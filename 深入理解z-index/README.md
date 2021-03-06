# 深入理解z-index笔记
[视频教程地址](http://www.imooc.com/learn/643)

## 一、z-index的基础
1.定义：z-index属性指定了元素机器子元素的z顺序，而z顺序可以决定当元素发生覆盖的时候，哪个元素在上面。通常一个较大的z-index的值得元素会覆盖较低的那一个。  

2.属性值  
* auto 默认值
* <integer> 整数值
* inherit 继承

3.基本特性

* 支持负值
* 支持CSS3 animation动画
* 在CSS2.1时代，需要和定位元素(position:relative/absoulte/fixed/sticky)配合使用


## 二、z-index与CSS定位元素的关系
1.定位元素没有发生嵌套
* 后来居上原则
* 那个大，哪个上

2.定位元素发生嵌套
* 祖先优先原则（如果祖先的z-index相同，则参照后来居上原则，前提是z-index值是数值而不是auto）

解释：**(z-index:auto)当前层叠上下文的生成盒子层叠水平是0，盒子(除非是根元素)不会创建一个新的层叠上下文**


## 三、CSS中的层叠上下文和层叠水平
1.层叠上下文(stacking context)是HTML元素中的一个三维概念，表示元素在Z轴上面可以优先显示
* 页面的根元素天生具有层叠上下文(根层叠上下文)
* z-index值为数值的定位元素也具有层叠上下文
* 其他属性

#### 层叠上下文的特性：
* 层叠上下文可以嵌套，组合成一个分层次的层叠上下文。
* 每个层叠上下文和兄弟元素独立：当进行层叠变化或渲染的时候，只需要考虑后代元素。
* 每个层叠上下文的自成体系的：当元素的内容被层叠后，整个元素被定位是在父层的层叠顺序中。

2.层叠水平(stacking level)  
层叠上下文的每个元素都有一个层叠水平，决定了同一个层叠上下文中的元素在Z轴上的显示顺序。
同样遵循后来居上和谁大谁上的层叠原则。

注意：**层叠水平和z-index不是一个东西。普通元素也有层叠水平。**


## 四、元素的层叠顺序
元素发生层叠时候有着特定的垂直显示顺序。

著名的7阶层叠水平顺序：
1. 层叠上下文background/border   ~装饰
2. 负z-index
3. block块状水平盒子             ~布局
4. float浮动盒子
5. inline/inline-block水平盒子   ~内容（文字、图片）
6. z-index:auto或者z-index:0/不依赖z-index的层叠上下文
7. 正z-index

#### 1.为什么会有层叠水平顺序？
浏览器的规范
#### 2.为什么层叠水平顺序是这样的?
更符合页面加载的功能和视觉呈现
#### 3.为什么inline|inline-block元素会在浮动元素之上？
内容是页面中最重要的实体，因此，层叠水平要高！


## 五、z-index与层叠上下文
1.定位元素默认z-index:auto可以看成z-index:0  
2.z-index不为auto的定位元素会创建层叠上下文  
3.z-index层叠顺序的比较止步于父级层叠上下文

#### z-index创建层叠上下文:
从层叠顺序上讲，z-index:auto可以看成z-index:0  
但是 **从层叠上下文来讲，两者却有着本质差异！前者不能创建叠层上下文，后者能。**

注意：**IE7即使z-index:auto也会创建层叠上下文**

3.z-index受限于层叠上下文


## 六、其他CSS属性与层叠上下文
1. z-index值不为auto的flex项(父元素display:flex|inline-flex)
2. 元素的opacity值不是1
3. 元素的transform值不是none
4. 元素mix-blend-mode值不是normal(混合模式)
5. 元素的filter值不是none
6. 元素的isolation值是isloate(隔离mix-blend-mode元素的混合)
7. position:fixed声明(chrome等blink/webkit内核浏览器)
8. will-change制定的属性值为上面任意一个(will-change属性可以提前通知浏览器我们要对元素做什么动画，这样浏览器可以提前准备合适的优化设置。这样可以避免对页面响应速度有重要影响的昂贵成本。元素可以更快的被改变，渲染的也更快，这样页面可以快速更新，表现的更加流畅。)
9. 元素的-webkit-overflow-scrolling设为touch


## 七、z-index与其他CSS属性层叠上下文
1. 不支持z-index的层叠上下文元素的层叠顺序均是z-index:auto级别  
2. 依赖z-index的层叠上下文元素的层叠顺序取决于z-index值  

#### 依赖z-index值创建层叠上下文的情况：
1. position值为relative/absoulte/fixed(部分浏览器)
2. display:flex|inline-flex容器的子flex项


## 八、层叠上下文导致的有趣的现象
覆盖在图片上的文字在图片淡入淡出的时候文字在最后一刻才显示出来

## 九、z-index相关实践分享
#### 1.最小化影响原则  
**目的：** 避免z-index嵌套层叠关系混乱  
**原因：** 元素的层叠水平主要由所在的层叠上下文决定（IE7 z-index:auto也会新建层叠上下文）  
**做法：** 避免使用定位属性（定位属性从大容器平级分离为私有小容器）

#### 2.不犯二准则
**目的：** 避免z-index混乱，一山比一山高的样式问题  
**原因：** 多人协作以及后期维护  
**做法：** 对于非浮层元素，避免设置z-index值，z-index值没有任何道理需要超过2

#### 3.组件层级计数器
**目的：** 避免浮层组件因z-index被覆盖的问题  
**原因：** 总会遇到意想不到的高层级元素（组件的覆盖规则具有动态性）
**做法：** 组件层级计数器（通过JS获得body下子元素的最大z-index值）

#### 4.可访问性隐藏
z-index负值元素在层叠上下文的背景之上，其他元素之下submit类型的按钮
