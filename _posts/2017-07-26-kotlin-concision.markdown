---
layout: post
title:  "How Kotlin named arguments might make your code more expressive"
date:   2017-07-28 14:00:00 +0700
---

One day, I was making unit tests and had faced with code like this:

```java
assertEquals(cells[0].left, 100)
assertEquals(cells[0].right, 120)
assertEquals(cells[0].bottom, 200)
assertEquals(cells[1].left, 130)
assertEquals(cells[1].right, 370)
assertEquals(cells[1].bottom, 200)
assertEquals(cells[2].left, 380)
assertEquals(cells[2].top, 380)
assertEquals(cells[2].right, 500)
assertEquals(cells[2].bottom, 200)
```

The intention of this test is to check the position of every cell. The test checks `left`, `right`, and `bottom` of first two cells. 
Also, it checks the coordinates of every side of the last cell. 
The code is clearly understandable, however it looks sloppy. Let's look how Kotlin can improve it.

```java
fun assertCellPosition(cellInfo: SectionLayoutView.CellInfo, left: Int? = null, top: Int? = null, right: Int? = null, bottom: Int? = null) {
    left?.let { assertEquals(it, cellInfo.left) }
    top?.let { assertEquals(it, cellInfo.top) }
    right?.let { assertEquals(it, cellInfo.right) }
    bottom?.let { assertEquals(it, cellInfo.bottom) }
}
```

What you see is a function with: 1) named arguments; 2) default arguments. 
In the function body, we only check those coordinates which were been passed to the function.

Now, we are able to write this

```java
assertCellPosition(cells[0], left = 100, right = 120, bottom = 200)
assertCellPosition(cells[1], left = 130, right = 370, bottom = 200)
assertCellPosition(cells[2], left = 380, top = 380, right = 500, bottom = 200)
```

This code stays clear, but moreover, get sexy and looks more human readable.