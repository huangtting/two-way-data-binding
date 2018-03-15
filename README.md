# two-way-data-binding
A simple interpretation of how Vue.js realizes two-way data-binding.
#实现双向绑定的流程
#observer:
观察者。对对象中所有的属性key设置defineProperty，设置get和set。对这个属性的get和set都会调用这两个方法。
在get方法中添加watch（订阅者），在set中调用Dep的notify方法，notify方法会调用watch的update方法(更新视图)。由于可能会被多个watch调用所以需要Dep。

#compile:
渲染，将html中的{{}}替换成data中的数据。并绑定相应的更新函数（在这个例子中是input的listener）。在nodeToFragment中调用compile。
如果元素节点（nodeType===1），得到v-model，对设置了v-molde的节点添加input监听器，这样触发input时，也可以触发set属性。
```
node.addEventListener('input', function (e) {
    // 给相应的 data 属性赋值，进而触发该属性的 set 方法
    vm[name] = e.target.value;
});
```
处理完后，removeAttribute。
然后创建一个Watcher（订阅者），new Watcher(vm, node, name, 'input');Watcher的构造函数会调用update，update会触发相应属性的set和get,设置相应的value

如果是文本节点（nodeType===3），创建一个相应name的watcher，new Watcher(vm, node, name, 'text');
#watcher:
在watcher中更新视图，数据发生更新时会通过Dep调用watcher
##Dep:
watcher的容器

#DocumentFragment
DocumentFragment（文档片段）可以看作节点容器，它可以包含多个子节点，当我们将它插入到 DOM 中时，只有它的子节点会插入目标节点，所以把它看作一组节点的容器。使用 DocumentFragment 处理节点，速度和性能远远优于直接操作 DOM。Vue 进行编译时，就是将挂载目标的所有子节点劫持（真的是劫持，通过 append 方法，DOM 中的节点会被自动删除）到 DocumentFragment 中，经过一番处理后，再将 DocumentFragment 整体返回插入挂载目标。

#流程图
![MVVM](https://raw.githubusercontent.com/huangtting/Blog/master/images/MVVM.png)