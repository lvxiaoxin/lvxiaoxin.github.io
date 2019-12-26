---
layout: post
title: Reading Note on You don't know JS - Async & Performance
categories: JavaScript
description: 读 You don't know JS 的一些记录。
keywords: JavaScript Language
---

#  Async & Performance

## **Key Notes**

1. Now and Later, mind the gap.
2. console.*  this is not original JS but added by environment. So whether this is async/sync depends the env(browers). --> Some browers think it should be delayed as it is I/O operation.
3. There is no built-in async concept in JS until ES6, but only Event Loops. ---> Looks like async by inserting events into event loops.  --> "One common thread" --> like a queue --> the every iteratiwon of event loop is called a tick.
4. Since like a queue --> setTimeout(…) -> insert into the end of queue after your specific time, --> The event won't happen before your specific time, but may delay, due to the length of event loops queue.
5. async is about the gap between now and later. But parallel is about things being able to occur simultaneously.
6. JS never share data across threads.  --> JS is Run-To-Completion. It's single thread running.  --> There is function level race condition.
7. There is 1 thing that doesn't have the behavior of Run-To-Completion, it is Generator, introduced in ES6.
8. The single-threaded event loop is one of expressions about concurrency.
9. Through coordination, try to avoid race condition.
10. Job queue,  ---> right after the end of current tick, but before next tick in event loops queue, -> insert another job on the top of the event loop queue.
11. Callback is an old and classic way of expressing async.
12. In sync sequence, our brain works match the code, but for async, it's not. Since the callback doesn't like the way our brains work, it's difficult for our brains to do thinking inserting/flow. That's why it's hard to use callback to do async for us.
13. Callback hell. ---> It forces our brains to jump among all the functions to try to "see" a sequence of calling. --> Which means, it relay on you to pre-define all possible call paths. --> leads to complicate and unmaintainable code.
14. The most dangerous thing is that: most time you may have to call a async function which belongs to a third party, and give them a callback function. This means you have to leave part of you code's execution authority to a third party code. This is call inversion of control, which is very dangerous as they may call your callback more than once or even don't call at all.
15. For 14, actually we can create a gate way to avoid calling multiple times. But what about don't call, what about call too early/late … You have to deal with so much situations for unknow code…   --> Callback hell.
16. To mitigate callback hell, but not resolve --> success, error separate / error comes first(Node style) …
17. Instead of delivering the later of our program to others with callback, how about the let the others give us a promise that when they will finished --> This is called Promise.
18. Promise --> a kind of future value, it may be success or fail, or even never ready.
19. Promise.All([…]) and .then() ----> once a Promise is resolved, its result turns from future value to a const(non-changeable) value.
20. Revealing constructor. ---> new Promise( function(resolve, reject){ .. } ) --> the inside function will execute immediately, 
21. Event Complete: Think about calling a async function foo().  We do not care the details of foo(), it may finished immediately or may take a while. But we don't care. We just care about when it will finish. So we need a way to express that when it is finished, or we can get an notification that it is finished. --> The oldest JS Style of this is to use event -> monitor.  
22. It is the same with Event Complete and Go on, depends on which point or view you hold.
23. There is a good stuff: separation of concerns. 
24. For a specific Promise, it can be resolved multiple times, but the results are all the same.
25. Any object that contains .then() method, is seemed as a Promise for its thenable. --> Duck checking
26. This kind of duke typing check is dangerous, as before ES6, some library may already have function whose name is then, --> what a ridiculous thing to let past and future code not using then.
27. So be careful when using duck type checking, it may be useful and powerful, but also dangerous.
28. Promise.race([…]) ---> work with promise timeout pattern.
29. All callback function defined at then(), can and will only be called once.
30. In the process of creating Promise, any JS error will lead to reject --> .then(function fulfilled(){}, function rejected(){}) will only call rejected.
31. Promise can only be resolved with 1 single object value, by return obj; or return new Promise((resolve, reject) => {resolve(obj);});--> If you need more, use object or array or …
32. What if error occurred in the success callback ? --> like the fulfilled() function in #30. ===> Remember .then() itself returns another Promise, that promise will become reject.
33. Promise.resolve(foo(x)) --> We don’t know whether the return value of foo(x) is a Promise or not, but the Promise.resolve() can help regular the foo(x)'s return value as a standard Promise. --> If the return result is Promise originally, it just return the same object, if not, then it will look through the return object until found the non-thenable value, and use it to make a Promise, and return.
34. Chain flow
    a. Every time you call then() on a Promise, it creates and returns a new Promise, which we can chain with.
    b. Whatever the alue you return from the then() call's fulfillment callback, is automatically set as the fulfillment of the chained Promise.
35. p.then().then().then()…. ===> every step can defer next step or not.
    ```
    var p = Promise.resolve( 21 );
    p.then( function(v){
    console.log( v );	// 21
    //create a Promise and return
    return new Promise( function(resolve,reject){
        // async introduced
        setTimeout( function(){
            // return a closed Promise
            resolve( v * 2 );
        }, 100 );
    } );
    } )
    .then( function(v){
    // even timeout 
    console.log( v );	// 42
    } );
    ```
36. In any of the chain flow promise, if no resolve returned, a undefined will be passed to next step. Every step's resolve are just a signal to next step.
37. For the traditional try…catch structure --> it only works for sync pattern.
38. Unlike error-first style (if err … else …), Promise uses separation-callback style. .then(function fulfilled(){}, function rejected(err){});
39. So every error occurs in .then().then()… will as the reject result of that step, and will resolved by next step's rejected function. So how about the last one? Is there a .done() like API to identify the end of Promise chain ? No. Can we just add a .catch() to the end of chain ? Yes, but how about the code inside catch.
40. Promise.all()
    ```
    // `request(..)`是一个兼容Promise的Ajax工具
    // 就像我们在本章早前定义的
    var p1 = request( "http://some.url.1/" );
    var p2 = request( "http://some.url.2/" );
    Promise.all( [p1,p2] )
    .then( function(msgs){
    // `p1`和`p2`都已完成，这里将它们的消息传入
    return request(
        "http://some.url.3/?v=" + msgs.join(",")
    );
    } )
    .then( function(msg){
    console.log( msg );
    } );
    ```
    Any thenable, even a concrete value, can be passed into the array that of .all(). --> They are all converted to a Promise by low level Promise.resolve() function.
    But, if any of the promise in the array is rejected, the main/whole Promise will be marked as rejected, and abandon the unfinished Promise's result.
41. Promise.race() --- race, only want the 1st, no matter it is resolve or reject. --> Do not pass in an empty array, --> never resolve.
    ```
    // `foo()`是一个兼容Promise
    // `timeoutPromise(..)`在早前定义过，
    // 返回一个在指定延迟之后会被拒绝的Promise
    // 为`foo()`设置一个超时
    Promise.race( [
    foo(),					// 尝试`foo()`
    timeoutPromise( 3000 )	// 给它3秒钟
    ] )
    .then(
    function(){
        // `foo(..)`及时地完成了！
    },
    function(err){
        // `foo()`要么是被拒绝了，要么就是没有及时完成
        // 可以考察`err`来知道是哪一个原因
    }
    );
    ```
42. What about finally ? --> what happened to the rest Promise that never resolve. --> We care about the side-effect.  A .finaly(cleanup) may need, but not ready.
43. Promise.any() ---> it will ignore any reject, it just wait for the 1st resolve.
44. .catch(handleErrors) --> not like .then(), .catch() only receive 1 callback, used to deal with the unhandled error occurred in the chain.
45. Promise.wrap() ---> It doesn't create Promise, it just create a function which can return promise. This is called lifting/promisifying/promisory
    Old code 
    ```
    function foo(x,y,cb) {
    ajax(
        "http://some.url.1/?x=" + x + "&y=" + y,
        cb
    );
    }
    foo( 11, 31, function(err,text) {
    if (err) {
        console.error( err );
    }
    else {
        console.log( text );
    }
    } );
    ```
    New code
    ```
    // 为`ajax(..)`制造一个promisory
    var request = Promise.wrap( ajax );
    // 重构`foo(..)`，但是为了代码其他部分
    // 的兼容性暂且保持它对外是基于回调的
    // ——仅在内部使用`request(..)`'的promise
    function foo(x,y,cb) {
    request(
        "http://some.url.1/?x=" + x + "&y=" + y
    )
    .then(
        function fulfilled(text){
            cb( null, text );
        },
        cb
    );
    }
    // 现在，为了这段代码本来的目的，为`foo(..)`制造一个promisory
    var betterFoo = Promise.wrap( foo );
    // 并使用这个promisory
    betterFoo( 11, 31 )
    .then(
    function fulfilled(text){
        console.log( text );
    },
    function rejected(err){
        console.error( err );
    }
    );
    ```
    Or
    ```
    // 现在，`foo(..)`也是一个promisory
    // 因为它委托到`request(..)` promisory
    function foo(x,y) {
    return request(
        "http://some.url.1/?x=" + x + "&y=" + y
    );
    }
    foo( 11, 31 )
    .then( .. )
    ..
    ```
46. Generator is introduced in ES6, which means a new kind of function that does not follow run-to-complete principle. 
47. function* foo() {} ---> generator, inside should be one or more yield. Which means it will pause at there and continue at next time. Call by foo() will not execute the function, but return a iterator instead. You should call .next() to execute the function.
48.  Two way data flow between yield and next()
    ```
    function *foo(x) {
    var y = x * (yield "Hello");	// <-- 让出一个值！
    return y;
    }
    var it = foo( 6 );
    var res = it.next();	// 第一个`next()`，不传递任何东西
    res.value;				// "Hello"
    res = it.next( 7 );		// 传递`7`给等待中的`yield`
    res.value;				// 42
    ```
49. Always activate a generator with a non-parameters next(). (the first next)
50. Whenever you create a iterator by calling foo(); --> You create an instance of that generator. --> Which means, you can create multiple iterator and call them in parallel.
51. In addition, all these iterator can interact with each other. So --> We can even create a race condition without let JS support multiple thread! (Even it's bad to have race condition.)

52. Standard generator
    ```
    var something = (function(){
    var nextVal;
    return {
        // `for..of`循环需要这个
        [Symbol.iterator]: function(){ return this; },
    // 标准的迭代器接口方法
        next: function(){
            if (nextVal === undefined) {
                nextVal = 1;
            }
            else {
                nextVal = (3 * nextVal) + 6;
            }
    return { done:false, value:nextVal };
        }
    };
    })();
    something.next().value;		// 1
    something.next().value;		// 9
    something.next().value;		// 33
    something.next().value;		// 105
    ```

53. for…of is used to iterator over a iterable object. But for a normal object is does not contains iterator, you can use Object.keys(obj) to do the same thing:  
    for (var k of Object.keys(obj)) { … }    - do not apply for Prototype-chain
54. Use finally to clean up
    ```
    function *something() {
    try {
        var nextVal;
    while (true) {
            if (nextVal === undefined) {
                nextVal = 1;
            }
            else {
                nextVal = (3 * nextVal) + 6;
            }
    yield nextVal;
        }
    }
    // 清理用的从句
    finally {
        console.log( "cleaning up!" );
    }
    }
    var it = something();
    for (var v of it) {
    console.log( v );
    // 不要让循环永无休止！
    if (v > 500) {
        console.log(
            // 使generator得迭代器完成
            it.return( "Hello World" ).value
        );
        // 这里不需要`break`
    }
    }
    // 1 9 33 105 321 969
    // cleaning up!
    // Hello World
    ```
55. Use generator to express async, in a sync way
    ```
    function foo(x,y) {
    ajax(
        "http://some.url.1/?x=" + x + "&y=" + y,
        function(err,data){
            if (err) {
                // 向`*main()`中扔进一个错误
                it.throw( err );
            }
            else {
                // 使用收到的`data`来继续`*main()`
                it.next( data );
            }
        }
    );
    }
    function *main() {
    try {
        var text = yield foo( 11, 31 );
        console.log( text );
    }
    catch (err) {
        console.error( err );
    }
    }
    var it = main();
    // 使一切开始运行！
    it.next();
    ```
    Inside our generator *main, everything is sync style. We abstract and extract all the async details out to foo, and use .next(data) to pass back the async end data back to main.

56. Yield a promise to combine promise and generator. --> Combine the promise's trust and yield's next as callback.
Like, yield Promise.all(…
57. Asm.js. --> a highly optimized sub set of js.
Benchmark.js