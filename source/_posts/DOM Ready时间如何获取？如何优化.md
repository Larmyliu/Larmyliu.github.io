## DOM Ready时间如何获取？如何优化

高级浏览器通过DOMContentLoaded事件获取。

低版本webkit内核浏览器可以通过轮询document.readyState来实现

ie中可以通过setTimeout不断调用documentElement的doScroll方法，直到其可用来实现

优化建议：

减少dom结构的复杂度，节点尽可能少，嵌套不要太深

优化关键呈现路径
