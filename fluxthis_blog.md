
#FluxThis - AddThis’ Flux Framework

## About

In our [previous blog post](/blog/2014/11/11/scaling-the-single-page-application-with-react-and-flux/) we discussed some ways we scaled React via best practices and Flux. We quickly came to realize that Flux out-of-the-bo had very good patterns; however, it did not enfornce really any of these. On the flipside, when you look at React, whenever you do something wrong you are harassed with Invariant errors to make sure you keep doing the 'right' thing. As [Jeff Atwood](http://blog.codinghorror.com/falling-into-the-pit-of-success/) said, we should build platforms that lead developers to write great, high performance code such that developers just fall into doing the "right thing". With this in mind, we developed FluxThis with a few main goals in mind:

1. Enforce Flux design patterns
2. Make debugging extremely easy
3. Make testing a breeze, not a chore
4. Reduce boilerplate code and follow [KISS](http://en.wikipedia.org/wiki/KISS_principle)
5. Create simple, modular APIs

## Enforce Best Practices & Decrease Complexity

As you look through the Flux framework ecosystem you will come to notice there are many ways to accomplish tasks and none are necessarily wrong when you compare their approach with Flux. We noticed this externally and internally throughout our projects, so FluxThis was developed to standarize some of these different ideas into one standard way to make life easier on developers. Below are some of those standards and best paractices we baked into FluxThis.

#### Immutability

For those who don't know about Immutability: Immutable data cannot be changed once created, leading to much simpler application development, no defensive copying, and enabling advanced memoization and change detection techniques with simple logic ([ImmutableJS](https://github.com/facebook/immutable-js)). As you can tell by the explanation, immutability is an awesome concept especially when you are dealing with many actors (stores, views, actions) like Flux/React has. When you enforce Immutability, you decrease the surface area for accidently (or purposefully) mutating objects. It's not fun to try and debug mutated actions or props... trust me.

In FluxThis, we expose two different types of Stores: Immutable Stores & Object Oriented (Non-Immutable) Stores. 
