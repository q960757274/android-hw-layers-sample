   最初使用动画时，在低性能手机上显示效果会很不好，尤其是有复合动画时。
 在绘制动画时，你的view需要重绘每一层，如果你使用视图图层代替重绘，视图渲染只会渲染一次并会被重用。使用硬件加速会让动画绘制的更快，因为硬件会把图层缓存在GPU上。
 >用法
 
 用法很简单关键方法是` View.setLayerType()`
 

 1. 在开始动画时调用`View.setLayerType(View.LAYER_TYPE_HARDWARE, null) `
 2. 运行动画
 3. 动画结束时调用`View.setLayerType(View.LAYER_TYPE_NONE, null).`

**代码示例**

```
// 设置硬件加速 
myView.setLayerType(View.LAYER_TYPE_HARDWARE, null);

// 设置动画
ObjectAnimator animator = ObjectAnimator.ofFloat(myView, View.TRANSLATION_X, 150);

// 设置一个回调，在动画完成时取消硬件加速
animator.addListener(new AnimatorListenerAdapter() {  
  @Override
  public void onAnimationEnd(Animator animation) {
    myView.setLayerType(View.LAYER_TYPE_NONE, null);
  }
});

// 开始动画
animator.start();  
```
如果你的sdk版本在14以上你可以使用属性动画，可以更简洁的设置硬件加速，调用`withLayer()`

```
myView.animate()  
  .translationX(150)
  .withLayer()
  .start();
```
>注意

硬件加速可以提高动画的性能，但是切记不要滥用！

 1. 在某些情况下，硬件层会比普通view绘制多做很多的工作。缓存一个层到GPU需要花费一些时间，因为需要两个步骤：首先view绘制到GPU的一个层中，然后GPU再把这个层绘制到window上。如果只是简单的view绘制使用硬件加速会增加不必要的开销
 2. 与所有的缓存一样，GPU的硬件缓存也一样会有失败的几率。如果在动画进行中调用`View.invalidate（）`的话，缓存的层会不得不重新渲染。如果不断有无效的硬件层产生的话，还不如不使用硬件加速。因为增加硬件层缓存会增加额外的开销。
 
>例子

包括简单动画，复合动画，开启硬件加速等。以下是在各种情况下的GPU绘制情况（需在开发者选项中开启GPU呈现模式），需要注意的是一定要选一个性能较差的手机
**简单动画不开启硬件加速**

![](http://upload-images.jianshu.io/upload_images/1796102-77cdc4a07fd129cb?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**简单动画开启硬件加速**

![](http://upload-images.jianshu.io/upload_images/1796102-fc1980a002c0f681?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**复合动画开启硬件加速**

![](http://upload-images.jianshu.io/upload_images/1796102-827d7cc12425c41e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
