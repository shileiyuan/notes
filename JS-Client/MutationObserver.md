`MutationObserver` 可以用来监听DOM树的变化
由于MutationObserver和Promise都会运行于microTask中，所以MutationObserver也用于实现promise

### 例子
```javascript
// Select the node that will be observed for mutations
var targetNode = document.getElementById('some-id');

// Options for the observer (which mutations to observe)
var config = { attributes: true, childList: true, subtree: true };

// Callback function to execute when mutations are observed
var callback = function (mutationsList) {
  for (var mutation of mutationsList) {
    if (mutation.type == 'childList') {
      console.log('A child node has been added or removed.');
    }
    else if (mutation.type == 'attributes') {
      console.log('The ' + mutation.attributeName + ' attribute was modified.');
    }
  }
};

// Create an observer instance linked to the callback function
var observer = new MutationObserver(callback);

// Start observing the target node for configured mutations
observer.observe(targetNode, config);

// Later, you can stop observing
observer.disconnect();
```

### promise中的实现
```javascript
function useMutationObserver() {
  let iterations = 0;
  const observer = new MutationObserver(() => {
    console.log('变化了')
  });
  const node = document.createTextNode('');
  observer.observe(node, { characterData: true });

  return () => {
    node.data = (iterations = ++iterations % 2);
  };
}
```