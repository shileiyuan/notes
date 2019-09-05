```javascript
// JQuery的chaining模式
(function () {
  // 参数可以是原生的dom元素或者选择器
  function _$(elements) {
    this.elements = []
    for (var i = 0, len = elements.length; i < len; i++) {
      var element = elements[i]
      if (typeof element === 'string') {
        element = document.querySelector(element)
      }
      this.elements.push(element)
    }
  }
  _$.prototype = {
    each: function (fn) {
      for (var i = 0, len = this.elements.length; i < len; i++) {
        fn.call(this, this.elements[i])
      }
      return this
    },
    setStyle: function (prop, value) {
      this.each(function (element) {
        element.style[prop] = value
      })
      return this
    },
    show: function () {
      this.each(function (element) {
        this.setStyle('display', 'block')
      })
      return this
    },
    hide: function() {
      this.each(function(element) {
        this.setStyle('display', 'none')
      })
      return this
    },
    addEvent: function (type, fn) {
      this.each(function (element) {
        element.addEventListener(type, fn, false)
      })
    }
  }
  
  window.$ = function () {
    return new _$(arguments)
  }
  
}())

$('#test-1', '#test-2').setStyle('color', 'red').addEvent('click', function (e) {
  $(e.target).setStyle('color', 'green')
})


```

