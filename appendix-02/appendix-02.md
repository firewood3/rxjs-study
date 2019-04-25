
https://itnext.io/how-javascript-works-in-browser-and-node-ab7d0d09ac2f

http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7CgpQcm9taXNlLnJlc29sdmUoKS50aGVuKGZ1bmN0aW9uKCl7CiAgICBjb25zb2xlLmxvZygicHJvbWlzZTEiKTsKfSkudGhlbihmdW5jdGlvbigpIHsKICAgIGNvbnNvbGUubG9nKCJwcm9taXNlMiIpOwp9KTs%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D

https://stackoverflow.com/questions/50283281/do-the-javascript-web-apis-run-on-a-different-thread-than-the-call-stack-thread

https://stackblitz.com/edit/rxjs-scheduler-demo

https://blog.strongbrew.io/what-are-schedulers-in-rxjs/

https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/
```js
console.log("script start")
setTimeout(function(){
    console.log("setTimeout");
}, 0)
Promise.resolve().then(function(){
    console.log("promise 1")
}).then(function(){
    console.log("promise 2")
})

requestAnimationFrame(function() {
    console.log("reqeustAnimationFrame");
});


console.log("script end");
```

call stack / web api / queue(Task Queue, Microtask Queue, Animation frames)


1. 
    - call stack 등록: [console.log("start")] 
2. 
    - 처리 : [console.log("start")]
3. 
    - call stack 등록: setTimeout(cb)
    - web api 등록: setTimeout(cb)
    - call stack 제거: setTimeout(cb)
4. 
    - task queue 등록: cb
   












