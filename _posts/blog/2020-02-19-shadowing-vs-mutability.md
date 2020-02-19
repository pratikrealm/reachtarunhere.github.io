---
layout: post
title: Shadowing vs Mutability
comments: true
---


Immutability has several advantages especially when it comes to areas of Parallel Programming and shared access so you don't have to deal with deadlocks and race conditions. However, that is not the topic of this post since there are tons or resources that explain that well.

One thing that tripped me a bit on first exposure to Clojure was the idea of immutability i.e. a variable cannot be modified vs that of shadowing bindings -> how the same name can refer to something different. An example would make it more clear. I use SML for the example but any language that makes similar promises will have the same behaviour:

    val x = 32;

> *val x = 32 : int*

My first understanding of the idea of the immutable concept was that `x` cannot be changed now. What if I want to try incrementing the value of `x`. I can't (unless I use constructs that support imperative programming in SML). But, what if I go ahead and do:

    val x = x + 1;

> *val x = 33 : int*

What?? `x` just got incremented. Isn't this exactly what we are trying to prevent? Nope, in reality, it is more like two different variables that are just spelt with the same ASCII name. One way to think about it is that after making the second binding the first binding hasn't gone away but we have only lost a way to get to it because having the same name allows us to only refer to the most recent one. The previous binding has been shadowed.

Here is some Python code which demonstrates the idea of shadowing:

    x = 32
    
    print("x is ", x)
    
    def my_fancy_fn(x):
        print("x is ", x)
    
    my_fancy_fn(33)

> *x is 32 <br> x is 33*

While inside the body of `my_fancy_fn` the global binding of `x` to 32 has been shadowed by the new local binding to 33. The original binding still exists but we have no direct way (there is the global keyword but it would result in an exception with the parameter of the same name) to refer to it.

Let's consider one final example. Lists in Python are mutable but tuples are not.

    our_immutable_collection = (1 ,2, 3, 4)
    our_mutable_collection = [1, 2, 3, 4]
    second_ref_immutable_collection = our_immutable_collection
    second_ref_mutable_collection = our_mutable_collection

For the list I can do something like:

    our_mutable_collection[0] = 100

Doing the same for the tuple would throw an error:

    our_immutable_collection[0] = 100

> TypeError: 'tuple' object does not support item assignment

Shadowing would still leave the underlying data unchanged:

    our_immutable_collection = (100, 2, 3, 4)
    print(second_ref_immutable_collection)
    
 

> *(1 ,2, 3, 4)*

Therefore some other piece of code (in the same or different thread) which had access to the underlying data would still function the same for the immutable case even though the original binding has been shadowed.

    print(sum(second_ref_immutable_collection))
    print(sum(second_ref_mutable_collection)) #bad

> *10  <br> 109*

Therefore in terms of consequences shadowing != mutation