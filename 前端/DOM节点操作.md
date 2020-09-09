1、DOM节点获取

| 节点关系       | 获取方法                | 说明 |
| -------------- | ----------------------- | ---- |
| 所有子节点     | element.childNodes      |      |
| 第一个子节点   | element.firstChild      |      |
| 最后一个子节点 | element.lastChild       |      |
| 弟节点         | element.nextSibling     |      |
| 父节点         | element.parentNode      |      |
| 兄节点         | element.previousSibling |      |

2、DOM节点操作

| 节点操作                                 | 操作方法                  |                                                 |
| ---------------------------------------- | ------------------------- | ----------------------------------------------- |
| 元素后添加节点                           | element.appendChild()     |                                                 |
| 克隆节点                                 | element.cloneNode()       | 默认参数为false不克隆内容，true为连内容一起克隆 |
| 添加原生内容                             | element.innerHTML         |                                                 |
| 元素前插入节点                           | element.insertBefore()    |                                                 |
| 合并元素中相邻的文本节点，并移除空的内容 | element.normalize()       |                                                 |
| 删除子节点                               | element.removeChild()     |                                                 |
| 删除指定属性的节点                       | element.removeAttribute() |                                                 |
| 替换元素的子节点                         | element.replaceChild()    |                                                 |

