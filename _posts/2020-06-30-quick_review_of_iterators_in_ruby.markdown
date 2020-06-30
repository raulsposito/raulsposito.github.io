---
layout: post
title:      "Quick review of Iterators in Ruby."
date:       2020-06-30 19:45:33 +0000
permalink:  quick_review_of_iterators_in_ruby
---


As coders, we like to keep our code clean and concise. DRY (Don’t Repeat Yourself) as we commonly refer. So if you have to perform a repeated action on a collection of objects, you would use an iterator! 

Iterators are the methods we use to handle our objects collections such as hashes or arrays.

Iterators are just that, they perform an action on a block of code once and again but there are different types of iterators and each one does a specific task and has a specific return value. 

Let's talk about that:

In case you wish to perform an action into each one of the elements of the block we would use **each.**

Each returns all the elements of a collection. It will return each value of the collection, one by one, to the block. 

Each always returns the original object or collection it is called on. Unlike… Map!

Another iterator to be known is **Map** or **Collect.**

Map collects the elements it is called on and creates a new array with the transformed elements. It goes one step further and it returns a new array of the data we just handled. 

As opposed to Each, the Map method would collect the results as a new array. 

And also we have: **Detect** or **Select.**

In this case the method works as a kind of filter to the original array. It goes to each element on the block it is passed on and “collects” the ones that are truthy.  

In this case we will also be creating a new array but only with the values on which the block evaluates to true. That would be our “filter”.

So **Each**, returns all elements in the original block untouched. **Map** makes a new array collecting the new elements as we modify them from the original block. And **Detect** would only return the values that make the block evaluate to true. 

There are a lot more ways to create loops on Ruby. As you work with them and code just keep in mind the action you are trying to perform. Whether you want the information you go over to be untouched or if you want to create new collections of data with the actions your code is performing. 

Always keep in mind as you code: What am I doing here? What do I expect to happen?

Asking yourself that might point you in the right direction to be where you want to be… not only in code.

