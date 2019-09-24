# Moviee Vue实战项目

# 在这个APP中实现（目标）
+ 通过设置manifest.json文件提供将Webapp保存至桌面的能力，便于实际调试和实现PWA的基础
1. 豆瓣电影评分获取功能 动态加载（预加载/懒加载）
   1. 增加过渡效果的动态搜索框 √
   + 增加正在热映的搜索及评分跳转逻辑
   2. 影片的展示及影评页的制作 √
   + 详细信息页的制作
   3. 根据获取到的观影情况绘制影片热度折线图
   4. 使用ECharts绘制电影评分图 √
   5. 点击详细信息后跳转路由，卡片组件的复用并自动移动至屏幕顶端 √
   6. Service Worker 基础配置及cache的使用
2. 哈登新闻、图片及视频的获取与展示
   1. 不同大小卡片镶嵌式列表展示 √
   2. 卡片的无限下拉实现 
      1. 考虑原生js实现的方法 
         1. 根据 scrollTop + clientHeight > offsetTop 判断
         2. 根据getBoundingClientRect直接获取元素的大小及位置         bound.top <= clientHeight 判断
      2. 注意增加节流，防止性能的损耗 √
   2. 点赞及消息提示的防抖功能 √
   3. 下拉动态卡片懒加载 √
3. 比赛及数据的可视化显示
4. 周边商品的展示
   1. 实现骨架图及内容预加载
5. 社区功能

*暂时用手动设置后的EasyMock上的端口来代替真实的数据*
  
  地址：https://www.easy-mock.com/project/5d4f6b52bfbd2538192571c7

# 技术栈
+ javaScript Vue Vue-router
+ CSS(3) Sass
+ axios 
+ ECharts
+ (plus) PWA: Manifest、Service Worker (未实现)

## 记录 
1. 完成footer的制作和跳转 完成首页的基本布局 制作item组件
2. 通过Easy Mock网站提供自定义的接口 
3. 完成社区页面中的标签页组件的制作,sticky定位,实现增删兴趣项的功能，并使用localStorage实现了状态的存储
4. 修改首页布局，用不对称的卡片式列表展示新闻动态，实现点赞按钮的防抖
5. 完成行程页面的基本布局，增加提醒按钮及过期提醒标志。
6. 为PWA应用预热，增加上映电影的豆瓣评分界面及影片热度可视化
7. 完善Moviee首页内容及无网状态下的表现，增加影评页面

## 开发中遇到的问题及思考

### 字体图标通过v-for遍历展示时，使用模板数据展示无效
+ 给元素加上v-html属性，进行转义显示
### 路由高亮的方式 路由重定向后高亮丢失的问题
+ 高亮的两种方式
   1. router自带的router-link-active类，在上面增加样式(注意router-link-exact-active是精确匹配,会导致路由高亮丢失)
   2. 给属性linkActiveClass赋值自定义的样式MyActive
### 过渡与动画效果 只能依赖于元素的插入、更新或销毁吗
### 在localStorage中的数据存入与读取
1. 注意需要通过JSON.stringify转化成字符串来存储
2. 读取时如果数据与计算属性绑定，会报错，因此需要给读出的数据一个默认值（通过||默认值的方式）
### 使用position:sticky时安卓设备的兼容存在问题 慎用！
### 在开发时注意按需加载插件 异步加载组件
1. 同级的组件在引入的时候需要需要增加地址前缀，否则回去node_modules中寻找而报错
### 两行式卡片展示 如果直接使用flex布局或者组件库中给出的栅格布局，不能直接实现卡片嵌入的效果
1. 解决方式：将单列ul变为两列ul，可以实现卡片嵌入的效果，但是也带来了额外的开销
### 在为图片点赞时避免频繁点击，需增加防抖
1. 采用从工具类utils文件夹中导入helper.js中自定义的Debounce方法(按需加载)
2. 根据点赞功能的基本需求，设置为先进行一次点赞再防抖
3. 注意防抖函数中的this指向问题，因为Debounce方法的返回值是函数
### 白屏时间2s 需优化
1. 引入骨架图 vant框架中的不好用（原因未知） 
2. 手动实现简易的骨架图，能先出现骨架图再出现组件，但并没有性能提升
### 切换至子子路由时 由子路由在mounted钩子函数中向父组件emit消息，将内容移至当前视口的顶端
1. 使用scrollIntoView方法 推荐
2. 直接DOM操作调整scrollTop的数值
### 在使用ref和$refs获取dom对象时，需要注意是否获取组件内部的dom（$refs中为空）！！！
### echarts的使用
1. 全局注入 类似总线的方式 Vue.prototype.$echarts = echarts
2. 在评分子组件通过props获取到最新的评分数据后，需要通过watch动态监控rating，动态更新评分图
3. **思考** 多个ECharts是否会影响性能？
### 真机调试时 IOS对于webp格式的图片支持不佳 图片无法显示
1. 考虑使用webpjs库将webp类型的图片转化成浏览器可识别的格式（貌似被墙）
2. 使用IP地址和IOS端及Android端均出现相同问题 可能并不是webp格式导致图片无法显示
### 注意组件化 减少耦合 如最热电影和Top10电影列表以组件形式编写 减少重复逻辑和代码的编写
### 非父子组件间传值时 也可以使用总线的方式来实现
1. Vue.prototype.bus = new Vue() **思考**有什么缺点？？？
2. this.bus.$emit("customevent", "data") 传递数据
3. this.bus.$on("customevent", (arg) => {}) 监听事件