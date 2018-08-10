
```
# 这就是一个vue的组件，直接引入

# 父组件如下使用
<style scoped>

</style>

<template>
  <div>
    <ScrollTab :titleArr="list">
      <div slot="item1">11</div>
      <div slot="item2">222</div>
      <div slot="item3">333</div>
    </ScrollTab>
  </div>
</template>

<script>
import ScrollTab from './ScorllTab.vue'
export default {
  components:{ScrollTab},
  data () {
    return{
      list:['第一页','第二页','第三页']
    }
  }
}
</script>

#可设置height

#也可以自己修改源码

```