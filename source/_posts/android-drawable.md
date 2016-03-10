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
	

	











    
    
    

