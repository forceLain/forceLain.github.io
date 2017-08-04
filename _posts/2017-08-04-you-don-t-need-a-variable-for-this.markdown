---
layout: post
title:  "You don't need a variable for this"
date:   2017-08-04 20:00:00 +0700
---

We write variables a lot. Every time we write a new variable we should give it a name. Naming the variables is a hard thing, good naming is harder. Why don't solve both of the problems at the same time? With Kotlin, you can avoid creating variables if you don't need them. So, you don't have to name your variables if you don't have them.

Look at this code:

```java
@Test
fun title() {
    val modelWithTitle = fromJson(jsonWithTitle)
    assertEquals(modelWithTitle.title, "test-title")
    assertEquals(modelWithTitle.subTitle, "test-sub-title")

    val modelEmptyTitle = fromJson(jsonEmptyTitle)
    assertNull(modelEmptyTitle.title)
    assertNull(modelEmptyTitle.subTitle)

    val modelEmpty = fromJson("{}")
    assertNull(modelEmpty.title)
    assertNull(modelEmpty.subTitle)
}
```

There are three local variables: `modelWithTitle`, `modelEmptyTitle`, and `modelEmpty`. The only reason of their existence is that we need to refer to their content to check it.

In Kotlin, we can use the `apply(...)` function to avoid unnecessary variables:

```java
@Test
fun title() {
    fromJson(jsonWithTitle).apply {
        assertEquals(title, "test-title")
        assertEquals(subTitle, "test-sub-title")
    }

    fromJson(jsonEmptyTitle).apply {
        assertNull(title)
        assertNull(subTitle)
    }

    fromJson("{}").apply {
        assertNull(title)
        assertNull(subTitle)
    }
}
```

`apply(...)` is an extension function and you can call it on any object. Also, you pass a lambda to the `apply` function and the object is available inside the lambda by `this` keyword. And, as you know, we can omit the `this` keyword.

Here is another example:

```
fun getProfile(): Profile {
    val response = webApi.getUserDataRequest().execute()
    val userDataEntity = response.body().getUserDataResponse.userData
    return mapper.mapUserDataResponse(userDataEntity)
}
```

In this example, we save the result of the request into `response`, then we save `userData` from the response's body into `userDataEntity` just to immediately pass it to the mapper. Of course, we can just throw all the variables away and write this all in one line:

```
fun getProfile(): Profile {
    return mapper.mapUserDataResponse(webApi.getUserDataRequest().execute().body().getUserDataResponse.userData)
}
```

I don't know if someone could call this code readable. What you really want write is "make a request", then "get a response body", then "map the body to a model". What the previous code says is "map the body of the response of the request", which sounds weird.

In Kotlin, we can cope with both problems by using the 'run(...)' function. 'run' is pretty much like `apply`, but can return an object with another type. We can think of `run` like as a mapping or a transform function.

```
fun getProfile(): Profile {
    return webApi.getUserDataRequest().execute()
            .run { body().getUserDataResponse.userData }
            .run { mapper.mapUserDataResponse(this) }
}
```

The first `run` transforms the response into 'userData'. The second `run` transforms `userData` into `Profile` by using the `mapper`. This code expressivly says: "make a request" -> "get the response body" -> "transform into POJO".