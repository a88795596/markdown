# VUE 列表过度

## \<transition-group> [官方文档地址](https://cn.vuejs.org/v2/guide/transitions.html#%E5%88%97%E8%A1%A8%E8%BF%87%E6%B8%A1)
  
在Vue中渲染DOM使用了Diff算法，该算法优化了DOM渲染性能，但是在使用动画处理单个生成的DOM时，由于Diff算法无法生效到目标DOM元素，导致动画效果变形。

## 列表过渡

在Vue中，经常会使用 `v-for` 来循环生成元素或组件，但是在使用列表过渡时会因为`vnode`和`diff`算法的优化，会出现过渡元素错误的情形。
官方案例：

```html
<div id="list-demo" class="demo">
  <button v-on:click="add">Add</button>
  <button v-on:click="remove">Remove</button>
  <transition-group name="list" tag="p">
    <span v-for="item in items" v-bind:key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>
</div>
```

```JavaScript
new Vue({
  el: '#list-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9],
    nextNum: 10
  },
  methods: {
    randomIndex: function () {
      return Math.floor(Math.random() * this.items.length)
    },
    add: function () {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove: function () {
      this.items.splice(this.randomIndex(), 1)
    },
  }
})
```

```css
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to
/* .list-leave-active for below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
```

上述案例解决了列表循环中新增或移除单个列表元素时的准确过渡效果

## 列表的排序过渡

虽然列表元素过渡已经完成了，但是相对整个列表来说整个list的改变并不平滑。

使用`v-move`特性可以做到列表变化的平滑过渡。

```html
  <div id="flip-list-demo" class="demo">
  <button v-on:click="shuffle">Shuffle</button>
  <transition-group name="flip-list" tag="ul">
    <li v-for="item in items" v-bind:key="item">
      {{ item }}
    </li>
  </transition-group>
</div>
```

```JavaScript
new Vue({
  el: '#flip-list-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9]
  },
  methods: {
    shuffle: function () {
      this.items = _.shuffle(this.items)
    }
  }
})
```

```css
.flip-list-move {
  transition: transform 1s;
}
```

>`<transition-group>` 组件还有一个特殊之处。不仅可以进入和离开动画，还可以改变定位。要使用这个新功能只需了解新增的 `v-move` 特性，它会在元素的改变定位的过程中应用。像之前的类名一样，可以通过 `name` 属性来自定义前缀，也可以通过 `move-class` 属性手动设置

## 过渡

内部的实现，Vue 使用了一个叫 FLIP 简单的动画队列
使用 transforms 将元素从之前的位置平滑过渡新的位置。

## 案例整合

```html
  <div id="list-complete-demo" class="demo">
    <button v-on:click="shuffle">Shuffle</button>
    <button v-on:click="add">Add</button>
    <button v-on:click="remove">Remove</button>
    <transition-group name="list-complete" tag="p">
      <span
        v-for="item in items"
        v-bind:key="item"
        class="list-complete-item"
      >
        {{ item }}
      </span>
    </transition-group>
  </div>
```

```JavaScript
new Vue({
  el: '#list-complete-demo',
  data: {
    items: [1,2,3,4,5,6,7,8,9],
    nextNum: 10
  },
  methods: {
    randomIndex: function () {
      return Math.floor(Math.random() * this.items.length)
    },
    add: function () {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove: function () {
      this.items.splice(this.randomIndex(), 1)
    },
    shuffle: function () {
      this.items = _.shuffle(this.items)
    }
  }
})
```

```css
.list-complete-item {
  transition: all 1s;
  display: inline-block;
  margin-right: 10px;
}
.list-complete-enter, .list-complete-leave-to
/* .list-complete-leave-active for below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
.list-complete-leave-active {
  position: absolute;
}
```

## 注意事项

>需要注意的是使用 FLIP 过渡的元素不能设置为 display: inline 。作为替代方案，可以设置为 display: inline-block 或者放置于 flex 中。
>
>内部元素 总是需要 提供唯一的 key 属性值,避免使用下标作为key
