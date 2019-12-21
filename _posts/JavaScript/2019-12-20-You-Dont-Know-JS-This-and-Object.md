---
layout: post
title: Reading Note on You don't know JS - this & Object Prototypes
categories: JavaScript
description: 读 You don't know JS 的一些记录。
keywords: JavaScript Language
---

#  this & Object Prototypes

## **Key Notes**

1. Misconceptions about how it doesn't work.
    - X - ITSELF: this inside a function is not ref to the function object itself at all. If you want to ref a function inside that function, like recursion, use that function's name, like foo. So in this point, anonymous function will not be able to ref to itself. Avoid to use anonymous function.
    note: foo.call(foo, …)  then the this here inside foo will be the function itself. 
    - X - ITS Scope: To be clear, this does not, in any way, refer to a function’s lexical scope. It is true that internally, scope is kind of like an object with properties for each of the available identifiers. But the scope “object” is not accessible to JavaScript code. It’s an inner part of the engine’s implementation.
2. this, is not an author-time binding, but a runtime binding. It's not associated with where the function is declared, but relates to where it is called.
3. When a function is invoked, an activation record, otherwise known as an execution context, is created. This record contains information about where the function was called from (the call-stack), how the function was invoked, what parameters were passed, etc. One of the properties of this record is the this reference, which will be used for the duration of that function’s execution.
4. call-stack and call-site. call-stack is a nested layers relationship which descripts the chain of functions call in order, and call-site is about each of its parent layer. And call-site, is the only thing that matters this binding.
5. this binding Rules with order #1 
    - New binding
    - Explicit binding ( call() and apply(), they both take, as their first parameter, an object to use for the this, and then invoke the function with that this specified. )   ====> Hard binding and .bind(…)   --- put null or undefined will not make effect.
    - Implicitly lost (calls in function alias, even though there is a context object for call-site.)
    - Implicit binding (context object for its call-site)
    - Default binding (global object is only eligible for this rule when running in non-strict mode, otherwise it will be undefined.)
6. new: Unlike other language which will invoke constructor function that is attached to Class. In JS, constructor is just a function called whenever there is a new, and has nothing to do with class-oriented functionality. So, new is just a function call, a construction function call, e.g. Number(), Boolean()…    #2
7. new, 4 steps.
    1) A brand new object is created (aka constructed) out of thin air.
    2) The newly constructed object is [[Prototype]]-linked.
    3) The newly constructed object is set as the this binding for that function call.
    4) Unless the function returns its own alternate object, the new-invoked function call will automatically return the newly constructed object.
8. Arrow function, which is introduced in ES6, use logic as Lexical this. Arrow function do not use the above rules, but adopt the this binding from the author-time lexical enclosing (parent) scope. --> Often used in callback function, the this inside the callback arrow function ref to the enclosing function. #4
9. 6 primitives, including string, number, boolean, null, undefined, and object. 
10. So object is just one of the above 6 primitive types. It's incorrect to say "Everything in JavaScript is an object.". Objects have subtypes.
11. Built-in Functions: special object subtypes, like String, Number, Boolean, Object, Function, Array, Date, RegExp, and Error.
12. Actually, var str = "I am a string."; is just a string primitive. And var strObj = new String("I am a string."); is the one that use constructor functions and created and returned an object, which has property/method like .length or .charAt(). But, JS engine just auto convert primitive string to string object, which is just to be convenience. The same for Number, and Boolean.
So, for String / Number / Boolean, it's ok to create in no matter primitive way or object new way. JS Engine will do the convert job to make them all Objects.
13. But for null and undefined, they do not have object wrapper, but only primitive values. Date, by contrast, can only be created with constructed object form, the new Date() way.
14. Objects only contain contents' name, the properties' name, not their value. Then there is a pointer to get the value.
15. "Property access" (objectX.a) and "Key access" (objectX["a"]). Usually we use "property access", but when we have to compute the properties name, then we have to use "Key access".
16. Array, .length --> only return index level properties. And for var a = []; a["0"] = 1; ---> this will lead to a[0] --> is 1. there is an auto-convert work.
17. Shallow copy is to just reference the same places. Deep copy should duplicate everything. (Deep copy may lead to infinite circular duplication problem because of the circular reference. What's exactly the "duplication" should be like is unclear.)
18. The objects which are JSON-safe (that is, can be serialized to a JSON string and then reparsed to an object with the same structure and values.), should be easy to duplicated: var newObj = JSON.parse(JSON.stringify(oldObj));
19. Object.assign(targetObj, sourceObj); is introduced in ES6 for shallow copy for non-special properties.
20. Object.getOwnPropertyDescriptor(myObject, "key");  -> property descriptor characteristics (data descriptor)
    - value
    - writable
    - enumerable
    - configurable 
    Object.defineProperty(myObject, "key", {
        value: xxx,
        writable: true,
        …
    });
    Some one-way action, take care.  (I don't like it at all. Weird design.)
21. To do Immutability, with higher and higher order: Object.preventExtensions(myObject); / Object.seal(myObject); / Object.freeze(myObject);
22. [[GET]] is an operation that will be executed when "property access" is requested. It inspects the object for property of that requested name, return the value if found and do "traversal of the [[Prototype]] chain" if not found. Finally if nothing right, undefined is returned. ==> unable to identify it is "property exists but is undefined", or it is "property is just not exists." / ==> opposite, [[PUT]]
23. Accessor descriptor: getter / setter. Will override [[GET]] / [[PUT]], with a hidden function call, get() / set().
24. To deal with #22's identify problem, in operator(will do traversal of [[Prototype]] chain for property name) and .hasOwnProperty("key") method (only check object) is introduced. 
25. As in is to check property name, (4 in [2, 4, 6]) will return false. ===> which means, index is the perperty name of array. That's why myArray["0"] === myArray[0] ==> true, there is auto-convert.
26. Do not use for … in loops with array, as it will not only applies to numeric indices, but also any enumerable properties including its [[Prototype]] chain. Use forEach((item, index) => {}); or for…of: for (var item of myArray) {};.
27. enumerable will affects whether a property will show up in for…in loop. It also affects Object.Keys(myObject);, but has no effect on Object.getOwnPropertyNames(myObject); -- currently, there is no built-in way to return the list of all keys will be traveled when in is executed on [[Prototype]] chain. You could approximate such a utility by recursively traversing the [[Prototype]] chain of an object, and for each level, 
capturing the list from Object.keys(..);—only enumerable properties.
28. forEach(..) will iterate over all values in the array, and it ignores any callback return values. every(..) keeps going until the end or the callback returns a false (or “falsy”) value, whereas some(..) keeps going until the end or the callback returns a true (or “truthy”) value.
29. for…of with array is a way to enumerable, so it's not good to use it with index, as it call next() every time with iteration, begin with Symbol.iterator. var it = myArray[Symbol.iterator]();
30. Class, it is just a very fundamental foundation of all other design patterns and code. It is one of several common design patterns. It is just an optional abstraction on top of code. Although for language like Java and C#, you don't have other choice. Languages like C/C++, you have both procedural and class-oriented syntaxes.
31. JavaScript actually does Not have classes. It's just something syntaxes trying to make you design code like class, but the behinds are totally different with other languages.
32. Multiple inheritance, diamond problem. Class A. Class B and Class C inherited from A and both override its function foo. Then we have Class D inherited from both B and C. What will D's foo function be like ? B's foo, C's foo, or A's foo ? --> For JavaScript, it does not provide a native mechanism for multiple inheritance. 
33. JavaScript doesn't have "classes" to instantiate (or copy), but only objects. Objects use linked way to link together.
34. JavaScript functions can't really be duplicated (in a standard, reliable way), so what you end up with instead is a duplicated reference to the same shared function object.
35. Explicit mixin, which is to add duplicated reference to another object's function to an object. This is so called extend. But, avoid using this if it makes your code hard to maintain due to mixined from multiple object. 
36. Implicit mixin, which is to call another object's this-assign function in an object's constructor function with .call(this). Then the assign will apply to the new object which this refs to.
37. "I don't like mixin pattern at all. It's confuse and hard to maintain." by Xin.
38. Faking classes in JS often sets more landmines for future coding than solving present real problems. -- Author's opinion.
39. [[GET]], "property access", if the prototype chain ends and still not found, then undefined is returned. The same to "for…in".
40. The top end of normal [[Prototype]] chain is Object.prototype.
41. The lower [[Prototype]] chain, it contains the properties it has directly, then in its .__proto__., it contains the upper level object's properties, and its .__proto__., then go upper.   Just like a single-direction links chain.
42. Function, it use .prototype.xxx to make modifies on .__proto__.xxx, but for simple object, modify .__proto__.xxx directly. Way to use that to do things like inherited. #8
43. [[Prototype]] chain shadowing. Chain level, descriptor characteristics - read only, setter. All take effects on shadowing.
The presence of a read-only property prevents a property of the same name from being implicitly created (shadowed) at a lower level of a [[Prototype]] chain.  ==> This is mainly to imitate class-inherited. 
44. Try avoid [[Prototype]] chain shadowing as much as possible. It's dangerous. Shadowing is happened when you want to modify a higher level's property through calling it from lower level. #6
45. Unlike other OOP languages, which use Class to describe objects' behaviors, JavaScript let the object defines its own behavior directly. No classes, just objects.
46. JavaScript uses the mechanism called prototypal inheritance. Inheritance implies a copy operation, but JavaScript don't copy object properties at all. It  use links, which works as that one object can essentially delegate property/function access to another object. So, delegation is a much more accurate term for JavaScript's object-linking mechanism.
47. A function Foo, Foo.prototype.constructor === Foo ---> true. The Foo.prototype object by default gets a public, nonenumerable property called .constructor, and this property is a reference back to the function Foo that the object is associated with.
48. Constructor call. --> kind of side-effect call. #7. 
49. Functions aren't constructors. In JavaScript, it's better to say constructor is any function called with the new keyword in front of it.
50. Inspecting an instance (or object in JS) for its inheritance ancestry (delegation linkage in JS) is often called introspection (or reflection) in traditional class-oriented environments.   In JS, x instanceof Foo --> boolean, which means: in the entire [[Prototype]] chain of x, whether there is an object that is pointed to, by Foo.prototype, or not.
51. The above is for x--> simple object, Foo --> function. If for arbitrary objects a & b (suppose b is lower), you should use a.isPrototypeOf(b);
52. To get an object's prototype, Object.getPrototypeOf(b); --> this actually === a.    The same to .__proto__
53. Magic words called dunder proto after ES6: .__proto__, as you can .__protp__.__proto__.....which returns the prototype. It is no something exist on the object, but something inside prototype, like .toString(), .isPrototypeOf()…..
54. Object.create(sourceObj, {…additional object info}) --> Introduced in ES5, it make JS ability to create linkage relationship with concept like Classes.
55. If you are going to expose an API, delegation will tend to be less surprising/confusing if it’s an internal implementation detail rather than plainly exposed in your API interface design. See #9
56. Using JavaScript in a behavior delegation design pattern. We need to change our thinking from the class/inheritance design pattern to the behavior delegation design pattern.
57. Define an object(not a class, nor a function), it will have concrete behaviors. Then create another object and link this object to the previous object. This could be done by Object.create(…). Use the new object to delegate to the previous object's methods/behaviors when called. --> This is called delegate, since no class copies happened. The specific behaviors/methods are separated in different objects.
58. During the [[Prototype]]-chain delegation, calling the delegate function which had this inside, they are all refs to the one you called, not the one that above the chain or where the function belongs to. They are just the one object that you call directly.
59. unlike polymorphism in class design pattern, we should always try to avoid have the same function names in different level of [[Prototype]]-chain. Specific to the type of behavior each object is doing.
60. Cycle delegation is disallowed, which will have errors.
61. Purely JS style, operate on prototype chain --> ES6 fake class sugar introduced --> Delegation design pattern. #10
62. OLOO(objects linked to other objects) style supports better on separation of concerns of creation and initialization, which are done in same step when OO design pattern.
63. // NOW, link `AuthController` to delegate to `LoginController`
Object.setPrototypeOf( AuthController, LoginController );
64. A less robust pattern for type introspection, which many devs seem to prefer over instanceof, is called “duck typing.” This term comes from the adage, “if it looks like a duck, and it quacks like a duck, it must be a duck.”
    if (a1.something) {
a1.something();
}
65. You are right to use the ES6 class syntax, but you should know that it is just a new syntax, there is no class mechanism in JS. And if you modify a method of parent class, the child class will be impacted immediately, since there is no copies at all. It is still prototype delegation.
66. What class syntax actually implies is that it tells you to code like in a static environment, which is not consist with JS. It let you not to modify or touch a class once you have defined it, and let you forget that it is actually a concrete object, which you can interact with. 
"The You don't know JS author thought this is not a good idea. The class syntax raises more question than it answers. I agree this idea, but I think the reason why I like the ES6 class syntax is just because I'm used to work with OO design pattern. This is indeed not the way JS's dynamic thinking. However, with TypeScript, more and more class syntax are using. Is it right? …" -- Xin.