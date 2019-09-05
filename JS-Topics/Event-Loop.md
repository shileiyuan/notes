### 浏览器端：
The microtask queue is processed after callbacks as long as no other JavaScript is mid-execution, and at the end of each task. 
Any additional microtasks queued during microtasks are added to the end of the queue and also processed. Microtasks include mutation observer callbacks, and as in the above example, promise callbacks.

