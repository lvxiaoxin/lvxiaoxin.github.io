---
layout: post
title: Reading Noth on You don't know JS - Scope & Closure
categories: JavaScript
description: 读 You don't know JS - Series 1 的一些记录。
keywords: JavaScript Language
---

#  Scope & Closure

## **Key Notes**

1. Compiler, Scope, Engine
2. LHS / RHS
3. Lexical Scope and Look-Up
4. Shadowing and **window**.x
5. Above rules, only for first class identifiers, then follow by object property-access rules. *(e.g. a.b.c)*
6. Lexical time and author time. Lexical scope is defined at author time, when the code is written.
7. Cheating lexical for its only declaration based principle, bad practice since poor performance. (
    a. ***eval()*** => change from lexical time to author time, to modify lexical scope
    b. ***with*** => to create a temporary new scope, so as to change scope
    ) => but both in strict-mode will not.
8. Scope-based hiding by wrapping a function declaration around it. -> inverse thinking way.  Principle of Least Privilege ==> to expose only what is minimally necessary, and hide everything else, such as API for a module/object.
9. Collision avoidance
    a. Always try to use ***var*** in nested scope.
    b. Use an specific object to enclose all your function/variables when you want to make it and API/Module, instead of just use the global scope and expose them.
    c. Module pattern and depedency manager injection
10. function declaration vs. function expression.
    ***(function foo () {***
        ***…***
    ***})()***
so foo is not a identifier of the outer globe scope, will not pollute the enclosing scope unnecessarily.
11. Anonymous function expression, no identified name so hard to show useful info in stack traces, hard to self-reference ==> the best practice is to always name your function, even it is a IIFE. (Immediately invoked function expression, just like above example, which is a pattern, for function which only call once right after declaration.)
12. ***var*** -- it's function scope only. And for block scope, in JavaScript, is actually an reminder for coder, not for compiler. It is written by coder to remind himself not using the variables outside that {} scope, but actually this is a fake-scope. Which means, JavaScript has no facility for block scope.
13. ***let*** -- it's block scope, introduced in ES6. => good to uses. For a code snip inside a code block, if all are let, then it is safe to move when refactoring.
14. ***const*** - it's block scope, with a fixed value, introduced in ES6.
15. Hoisting - Compiler comes before executor. -> All declarations comes first. (Just declarations, no variables assignment.), all undefined. This is called hoisting which means to move all declarations on top of that scope level, and left all other assignment and executable logic as where they are. (function declarations comes before variables declarations.)
==> so, the best practice is to write code as the order of hosting, instead of let the engine to adjust them, which will help you understand your code's execution logic.
16. For function declarations, ***function*** ***foo() {}*** ==> JS engine sees it like we see it, just a declaration. Function expressions are actually treated as assignment.
17. For variables declarations, ***var x = 1;*** ==> JS engine sees it as 2 separate statements***, var x;***  and ***x = 1;*** , which only the first one is declaration. 
18. Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.
No matter how we transport an inner function outside of its lexical scope, it will maintain a scope reference to where it was originally declared.
19. For the for-loop, with loop-variables i declared by ***let / var***, its difference is at whether to share scope between each iteration. And, whether the i exists or not after all loop. --> #3
20. Modules 
- An object with a function property on it alone is **not** really a module.
- An object that is returned from function invocation that only has data properties on it and no closured functions is **not** really a module.
- An object that is returned from function invocation that has closured functions (inner function ref to close over scope) **is** actually a module.
21. Modern library often use #4 pattern to make their public APIs.  ==> The function-based module are not statically recognized pattern, it is decided at runtime, which means you can modify a module's API at runtime. However, ES6 introduced module APIs which by using separate files, are static pattern, cannot be modified at runtime.
22. JavaScript doesn't have dynamic scope, which is unlike lexical scope that is based on places of declarations. Dynamic scope is based on places of executions, or we say call-stack. 


## **Some meaningful code snips.**

1. **Garbage collection & Scope using**

   bad

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/00.png)

   good

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/01.png)

2. **Hoisting logic & function expressions /
   declarations**

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/10.png)

3. **Closures & Scope**

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/20.png)

   This is just because every 5 new functions declared here actually shared the lexical scope ==> **The loop here has no difference with defining them one by one.** So only one i here.

   To make it print 1, 2, 3, 4, 5 with 1 second interval, we need to make them have separate lexical scope, by the following way,

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/21.png)

   Which create a new scope by IIFE pattern, and then pollute this new scope with ***j***.

Or

![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/22.png)

​	Which the ***let*** here make the ***k*** in each iteration turns into a new scope that can let the closure to close over.

==> this can actually just by 

![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/23.png)

Cool! -> this is because now ***i*** is a block-based scope variable, which means the for-loop will separate each iteration's scope.

==> So, for-loop, with loop-variables ***i***, ***let / var***, its difference is at whether to share scope between each iteration. 

4. **Modules & IIFE & Closures --> making a
   singleton**

![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/30.png)

5. **Lexical this**

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/40.png)

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/41.png)

   ![](https://raw.githubusercontent.com/lvxiaoxin/lvxiaoxin.github.io/master/_posts/JavaScript/pic/ydkjs1/42.png)