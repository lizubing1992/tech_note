### 1.saveLayer会创建一个全透明的Bitmap，大小和指定保存的区域一致，其后的绘图操作都放在这个Bitmap上面进行，在绘制结束，会直接盖在上一层的Bitmap上显示

###2.画布Bitmap,图层Layer,Canvas之间的概念关系：

   图层Layer:每一次调用canvas.drawXXX系列函数时，都会生成一个透明的图层来专门画这个图形；

   画布bitmap:每个画布都是一个Bitmap，所有的图像都是在Bitmap上，，我知道每一次调用canvas.drawXXX函数时，都会生成一个专用的透明图层来画这个图形，画完以后，就盖在画布上，画布有两种，第一种是view的原始画布，是通过onDraw(Canvas canvas)函数传进来的，其中参数的canvas就对应view的原始画布，控件的背景就是画在这个画布上的；另一种是人造画布，通过saveLayer()，new Canvas(bitmap)新建的一个画布，saveLayer调用以后，所有的draw操作都是这个画布上，只有restore ,restoreToCount,才会回到原始画布上

   Canvas：理解成画板，Bitmap理解成透明画纸，Layer理解成图层，每个draw函数对应一个图层，图形画完之后就会放在画纸上。而一张张画纸则是一层层的叠加在画板上面，画板canvas裁小则所有的画纸都会裁小，而对应的canvas。rotate（30）所有的画纸都会旋转30，画板裁剪，唯一恢复的操作是canvas。revert把上一次的操作取消掉