---
title: Android的Drawable汇总
date: 2016-03-10 19:25:37
tags: android drawable 
---
## 简介
Android把可绘制的对象抽象为Drawable,不同的图形图像资源就代表着不同的drawable类型，Android FrameWork提供了一些具体实现。Drawable的种类很多，最常见的颜色和图片都是一个Drawable。本章的内容会比较简单，但是由于Drawable具体实现比较多，从而导致许多开发者对不同的Drawable的理解比较会乱。熟悉掌握各个Drawable实现可以快捷的实现一些特殊的UI效果。

## 优点
* 使用简单，比自定义View的成本要低；
* 非图片类型的Drawable占用空间较小，减小安装包的大小；
* Drawable默认是内存共享的，也就是说不同的地方使用同一个drawable，都会指向相同的资源；

## Drawable的具体实现
BitmapDrawable、NinePatchDrawable、ShapeDrawable、LayerDrawable、StateListDrawable、LevelListDrawable、TransitionDrawable、InsetDrawable、ClipDrawable、ScaleDrawable、AnimationDrawable、自定义Drawable

#### BitmapDrawable
***
BitmapDrawable其实就是对Bitmap的包装，可以设置它包装的bitmap在BitmapDrawable区域内的绘制方式，如平铺填充、拉伸填充或者保持图片原始大小，也可以在BitmapDrawable区域内部使用gravity指定的对齐方式等,具体属性如下所示:

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<bitmap 
    	xmlns:android="http://schemas.android.com/apk/res/android"
    	android:src="@[package:]drawable/drawable_resource"
    	android:antialias=["true" | "false"]
    	android:dither=["true" | "false"]
    	android:filter=["true" | "false"]
    	android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
                      "fill_vertical" | "center_horizontal" | "fill_horizontal" |
                      "center" | "fill" | "clip_vertical" | "clip_horizontal"]
    	android:mipMap=["true" | "false"]
    	android:tileMode=["disabled" | "clamp" | "repeat" | "mirror"] /> 
    	```
    
下面是各个属性的含义

`` android:src ``
	图片的资源id

`` android:antialias ``
	是否开启图片抗锯齿功能，

`` android:dither ``
	是否开启抖动效果。当图片的像素配置和手机屏幕的像素配置不一致时，开启这个选项可以让高质量的图片在低质量的屏幕上还能保持较好的现实效果，比如图片的色彩模式为ARGB8888,但是设备屏幕所支持的色彩模式为RGB555，这个时候开启抖动选项可以让图片不会过于失真。在Android中创建的Bitmap一般会选用ARGB8888这个模式，即ARGB四个通道各占8位，在这种色彩模式下，一个像素所占的大小为4个字节，一个像素的位数总和越高，图像也就越逼真。因此抖动效果建议开启。

`` android:filter ``
	是否开启过滤效果。当图片尺寸被拉伸或者压缩时，开启过滤效果可以保持较好的显示效果。因此过滤效果建议开启。

`` android:gravity ``
	当图片小于View大小时，设置此选项可以对图片进行定位。

`` android:mipMap ``
	这是一种图像相关的处理技术，也可叫纹理映射，比较抽象，这里不对其深入研究了，默认值为false，在开发中此选项不常用。

`` android:titleMode ``
	平铺模式。这个选项有四种模式["disabled" | "clamp" | "repeat" | "mirror"],其中disabled表示关闭平铺模式，这也是默认值，当开启平铺模式后，gravity属性设置无用。repeat、mirror、clamp都表示平铺模式，但是它们的表现却不相同。repeat表示的是简单的水平和竖直方向的平铺效果；mirror表示一种在水平和竖直方向上的镜面投影效果；clamp表示图片四周的像素会扩展到周围区域。

#### NinePatchDrawable
***
NinePatchDrawable表示的是一张.9格式的图片，.9图片可以自动地根据view的宽高进行相应的缩放并保证不会失真。

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<nine-patch
    	xmlns:android="http://schemas.android.com/apk/res/android"
    	android:src="@[package:]drawable/drawable_resource"
    	android:dither=["true" | "false"] /> 
	```
NinePatchDrawable中的属性含义和BitmapDrawable对应的属性含义是相同的，这里就不再描述了。

#### ShapeDrawable
***
ShapeDrawable是一种很常见的Drawable，可以理解为通过颜色来构造的图形，它既可以是纯色的图形，也可以似乎具有渐变效果的图形，也可以是具有简便效果的图形。

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<shape
    	xmlns:android="http://schemas.android.com/apk/res/android"
    	android:shape=["rectangle" | "oval" | "line" | "ring"] >
    	<corners
        	android:radius="integer"
        	android:topLeftRadius="integer"
        	android:topRightRadius="integer"
        	android:bottomLeftRadius="integer"
       		android:bottomRightRadius="integer" />
    	<gradient
        	android:angle="integer"
        	android:centerX="integer"
        	android:centerY="integer"
        	android:centerColor="integer"
        	android:endColor="color"
        	android:gradientRadius="integer"
        	android:startColor="color"
        	android:type=["linear" | "radial" | "sweep"]
        	android:useLevel=["true" | "false"] />
    	<padding
        	android:left="integer"
        	android:top="integer"
        	android:right="integer"
        	android:bottom="integer" />
    	<size
        	android:width="integer"
        	android:height="integer" />
    	<solid
        	android:color="color" />
    	<stroke
        	android:width="integer"
        	android:color="color"
        	android:dashWidth="integer"
        	android:dashGap="integer" />
	</shape>
	```

需要注意的是<shape>标签创建的Drawable，其实体类实际上是GradientDrawable,下面介绍各个属性的含义。

`` android:shape ``
	表示图形的形状，有四个选项：rectangle(矩形)、oval(椭圆)、line(横线)和ring(圆环)。默认值是矩形，另外line和ring这两个选项必须要通过<stroke>标签来指定线的宽度和颜色等信息，否则将无法达到预期的显示效果。

`` <corners> ``
	表示shape的四个角的角度。它只适用于矩形shape，这里的角度是指圆角的角度。单位建议用dp。
	
`` <gradient> ``
它与<solid>标签是互相排斥的，其中 ** solid ** 表示纯色填充，而gradient则表示渐变效果，gradient有如下几个属性	
**android:angel** 渐变的角度，默认为0，其值必须为45的倍数，0表示从左向右，90表示从下到上;  
**android:centerX** 渐变的中心点的横坐标;  
**android:centerY** 渐变中心店的纵坐标;  
**android:centerColor** 渐变的中间色;  
**android:endColor** 渐变的结束色;  
**android:startColor** 渐变的起始色;  
**android:gradientRadius** 渐变半径，仅当 **android:type = “radial”** 时有效   
**android:useLevel** 一般为false，当Drawable作为StateListDrawable使用时为true  
**android:type** 渐变的类别，有linear(线性渐变)、radial(径向渐变)、sweep(扫描线渐变)三种，其中默认		值为线性渐变。 	
	
`` <padding> ``
	这个表示空白，但是它表示的不是shape的空白，而是包含它的view的空白
	
`` <size> ``
	shape的大小，但是最终一般不是最终显示的大小，shape作为view的背景，它会自适应view的宽高。
	
`` <stroke> ``
	shape的描边，有如下几个属性：  
	**android:width** 描边的宽度;  
	**android:color** 描边的颜色;  
	**android:dashWidth** 组成虚线的线段的宽度;  
	**android:dashGap** 组成虚线的线段之间的间隔，即虚线之间的空隙;   
	**注意** dashWidth和dashGap有任何一个为0，那么虚线效果将不能生效。
	
### LayerDrawable
LayerDrawable对应的xml根节点是<layer-list>,它表示一种层次化的Drawable集合，通过item子节点定义每一层的drawable，layer-list没有属性节点，只包含item子节点。语法如下所示:  

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<layer-list
    	xmlns:android="http://schemas.android.com/apk/res/android" >
    	<item
        	android:drawable="@[package:]drawable/drawable_resource"
        	android:id="@[+][package:]id/resource_name"
        	android:top="dimension"	
        	android:right="dimension"
        	android:bottom="dimension"
        	android:left="dimension" />
	</layer-list>
	```

	
每个item中常用的属性有``android:top``、``android:right``、``android:left``、``android:bottom``,分别表示Drawable相对于View的上下左右的偏移量。
``android:drawable``  
已有的drawable资源id，也可以在item中自定义drawable。默认情况下xml中的所有Drawable都会被缩放至View的大小，对于bitmap来说，需要使用``android:gravity``才能控制图片的显示效果

### StateListDrawable
StateListDrawable对应的xml根节点是\<selector\>标签，它也是Drawable的集合，每个Drawable都对应这View的一种状态，这样系统就会根据view的状态来选择相对应的Drawable。StateListDrawable主要用于设置可点击的View的背景，这种读者应该比较常用。它的语法如下所示:  

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<selector xmlns:android="http://schemas.android.com/apk/res/android"
    	android:constantSize=["true" | "false"]
    	android:dither=["true" | "false"]
    	android:variablePadding=["true" | "false"] >
    	<item
        	android:drawable="@[package:]drawable/drawable_resource"
        	android:state_pressed=["true" | "false"]
        	android:state_focused=["true" | "false"]
        	android:state_hovered=["true" | "false"]
        	android:state_selected=["true" | "false"]
        	android:state_checkable=["true" | "false"]
        	android:state_checked=["true" | "false"]
        	android:state_enabled=["true" | "false"]
        	android:state_activated=["true" | "false"]
        	android:state_window_focused=["true" | "false"] />
	</selector>
	```
	
``android:constanSize``  
StateListDrawable的固有大小是否不随着其状态的改变而改变，因为状态的改变导致StateListDrawable切换到具体的Drawable，而不同的Drawable具有不同的大小。True表示StateListDrawable的固有大小保持不变，这时它的固有大小是内部所有Drawable的固有大小的最大值，false则会随着状态的改变而改变。此选项默认值为false。

``android:dither``  
是否开启抖动效果，和BitmapDrawable中的dither属性相同。

``android:variablePadding``  
StateListDrawable的padding表示是否随着其状态的改变而改变，true表示会随着状态的改变而改变，false表示StateListDrawable的padding是内部所有Drawable的padding最大值。此选项默认值为false，不建议开启此选项。

``android:state_pressed``  
表示按下状态  

``android:state_focused``  
表示View获取到焦点

``android:state:state_hovered``  
光标是否停留在该View上，默认值为false。(支持Api 14以上)
	
``android:selected``
用户是否选择了View 

``android:state_checkable``  
用户是否选中了View，一般用在checkbox上  

``android:state_enabled``
View当前是否处于可用状态

``android:activated``  
是否被激活。(支持Api 11以上)android官方api描述如下：

	Boolean. "true" if this item should be used when the object is activated as the persistent 	selection (such as to "highlight" the previously selected list item in a persistent 	navigation view); "false" if it should be used when the object is not activated.
	
``android:state_window_focused``  
应用程序是否在前台，当有通知栏被拉下来或者一个对话框弹出的时候应用程序就不在前台了。

**注意**:如果有多个item，那么程序将自动从上到下进行匹配，最先匹配的将得到应用。（不是通过最佳匹配）
如果一个item没有任何的状态说明，那么它将可以被任何一个状态匹配。

### LevelListDrawable
LevelListDrawable对应的xml根节点是<level-list>标签，它同样表示一个Drawable集合，集合中的每个Drawable都有一个等级(level)的概念。根据不同的等级，LevelListDrawable会切换为对应的每个Drawable,它的语法如下所示：  

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<level-list
    	xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@drawable/drawable_resource"
        android:maxLevel="integer"
        android:minLevel="integer" />
	</level-list>
	```
每个item表示一个Drawable，并且有对应的等级范围，由``android:maxLevel``和``android:minLevel``来制定。

### TransitionDrawable  
TransitionDrawable对应的xml根节点是<transition>标签，它用于实现两个Drawable之间的淡入淡出效果，语法如下所示:  

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<transition
		xmlns:android="http://schemas.android.com/apk/res/android" >
    	<item
        	android:drawable="@[package:]drawable/drawable_resource"
        	android:id="@[+][package:]id/resource_name"
        	android:top="dimension"
        	android:right="dimension"
        	android:bottom="dimension"
        	android:left="dimension" />
	</transition>
	```
属性和上文重复的已经介绍过了，就不再重复介绍了。

### InsetDrawable  
InsetDrawable对应的xml根节点是<inset>标签，它可以将其它Drawable内签到自己当中，并可以在四周留出一定的间距。当一个View希望自己的背景比自己的时间区域小的时候，可以采用InsetDrawable来实现，同时我们知道，通过LayerDrawable也可以实现这种效果。语法如下所示：  

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<inset
    	xmlns:android="http://schemas.android.com/apk/res/android"
    	android:drawable="@drawable/drawable_resource"
    	android:insetTop="dimension"
    	android:insetRight="dimension"
    	android:insetBottom="dimension"
    	android:insetLeft="dimension" />
    ```
    
上面的属性可以按字面意思理解，其中``andorid:insetTop:``、``android:insetRight:``、``andorid:insetBottom:``和``android:insetLeft:``分别表示顶部、右边、底部和左边内凹的大小。

### ScaleDrawable
ScaleDrawable对应的xml根节点是<scale>标签,它可以根据自己的等级将指定的Drawable缩放到一定比例。语法如下所示：  

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<scale
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    android:drawable="@drawable/drawable_resource"
	    android:scaleGravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
	                          "fill_vertical" | "center_horizontal" | "fill_horizontal" |
	                          "center" | "fill" | "clip_vertical" | "clip_horizontal"]
	    android:scaleHeight="percentage"
	    android:scaleWidth="percentage" />
	```
	
上面的``android:scaleGravity``的含义等同于shape中的``andorid:gravity``,而``android:scaleWidth``和``android:scaleHeight``分别表示对指定Drawable宽和高的缩放比例，以百分比的形式表示，如25%.

**注意** ScaleDrawable所在的View等级必须``不等于``0，ScaleDrawable才可以显示。下图是ScaleDrawable的draw方法，如下所示：

	``` java
		@Override
	    public void draw(Canvas canvas) {
	        if (mState.mDrawable.getLevel() != 0)
	            mState.mDrawable.draw(canvas);
	    }
	```

很明显，由于ScaleDrawable的等级和mDrawable的等级是保持一致的，所以如果ScaleDrawable的等级为0，它内部的mDrawable的等级也必然为0，这时mDrawable就无法被绘制出来，也就是ScaleDrawable不可见。所以使用ScaleDrawable的时候一定要设置它的等级，等级范围是1~10000.  

### ClipDrawable
ClipDrawable对应的xml根节点是标签，它可以根据自己当前的等级来裁剪另一个Drawable，裁剪方向可以通过``android:clipOrientation``和``android:gravity``这两个属性来共同控制，语法如下:

	``` xml
	<?xml version="1.0" encoding="utf-8"?>
	<clip
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    android:drawable="@drawable/drawable_resource"
	    android:clipOrientation=["horizontal" | "vertical"]
	    android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" |
	                     "fill_vertical" | "center_horizontal" | "fill_horizontal" |
	                     "center" | "fill" | "clip_vertical" | "clip_horizontal"] />                     
	```
	
``android:clipOrientation``  
裁剪方向，有水平和竖直两个方向，gravity的值比较多，需要和clipOrentation一起才能发挥作用。

``android:gravity``  
**top** 将内部的Drawable放在容器的顶部，不改变它的大小。如果为竖直裁剪，则从底部开始裁剪  
**bottom** 将内部的Drawable放在容器的底部，不改变它的大小。如果为竖直裁剪，则从顶部开始裁剪 
**left** 将内部的Drawable放在容器的左边，不改变它的大小。如果为水平裁剪，则从右边开始裁剪（默认值）  
**right** 将内部的Drawable放在容器的右边，不改变它的大小。如果为水平裁剪，则从左边开始裁剪   
**center_vertical** 使内部的Drawable在容器中竖直居中，不改变它的大小。如果为竖直裁剪，那么从上下同时开始裁剪   
**fill_vertical** 使内部的Drawable在竖直方向上填充容器。如果为竖直裁剪，那么仅当ClipDrawable的等级为0(0表示ClipDrawable被完全裁剪，即不可见)时，才能有裁剪行为  
**center_horizontal** 	使内部的Drawable在容器中水平居中，不改变它的大小。如果为水平裁剪，那么从左右两边同时开始裁剪   
**fill_horizontal** 使内部的Drawable在水平方向上填充容器。如果为水平裁剪，那么仅当ClipDrawable的等级为0时，才能有裁剪行为  
**center** 使内部的Drawable在容器中居中，不改变它的大小。如果为竖直裁剪，那么从上下同时开始裁剪；如果为水平方向，那么从左右同时开始裁剪
**fill** 使内部的Drawable在水平和竖直方向上同时填充容器。仅当CLipDrawable的等级为0时，才能有裁剪行为  
**clip_vertical** 附加选项，表示竖直方向的裁剪，较少使用  
**clip_horizontal** 附加选项，表示水平方向的裁剪，较少使用




	


	











    
    
    

