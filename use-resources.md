# 使用资源 Resources

在前面的例子中，我们忽略了一个重要的原则，在代码和 Layout 中，直接使用了字符串常量，比如：

```
<Button android:text=”Pattern”  
android:id=”@+id/btnPattern”  
android:layout_width=”wrap_content”  
android:textColor=”@color/black”  
android:checked=”true”  
android:layout_height=”wrap_content”>
</Button>
```    

我们直接定义 Button 的显示内容为“Pattern”。如果你想你的应用支持多种设备，多种语言，那么直接使用字符串常量会给程序的移植带来很大的问题。因此设计应用是一个重要原则是尽可能的将 UI 相关的资源（如图像，文字等）以外部资源的形式来定义。

Android 支持多种资源类型，对应每一种资源，你可以定义一个缺省资源和多个可选资源（根据设备配置或语言类型等）。

缺省资源定义成与设备配置和语言无关，用在找不到与设备配置对应资源时使用。比如说你可以将缺省 UI Layout 定义在 res/layout 中， 而将屏幕横置（Landscape)定义在 res/layout-land 中。Android 在运行时会根据设备配置自动选择合适的资源。

下图显示两种不同配置的设备中没有定义可选资源时都使用缺省资源定义：

![](images/34.png)

下图应用定义给两种不同设备定义了两种资源，一是缺省资源，一是为横屏显示时的资源：

![](images/35.png)

在定义可选资源时，Android 对可选资源的命名方法有一定的规定，具体可以参见[http://developer.android.com/guide/topics/resources/providing-resources.html](http://developer.android.com/guide/topics/resources/providing-resources.html)

Tags: [Android](http://www.imobilebbs.com/wordpress/archives/tag/android)


