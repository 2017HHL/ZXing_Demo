### 这种UI效果是市场上的app不符，所以需要自定义扫码页面，如下图所示：![github](https://raw.githubusercontent.com/2017HHL/ZXing_Demo/master/NA%7BKJ37SVE3(8QRE9JXQITD.png "github")
# ZXing_Demo 
- app 扫描Activity
- camera 摄像头相关
- decode 解析二维码相关
- encode 生成二维码相关
- util 工具类
- view 扫描预览视图ViewfinderView

# 绘制扫描边框：
- 熟悉自定义View的人首先想到在ViewfinderView中的onDraw方法中完成绘制，首先来看它默认绘制的onDraw方法：
```
@Override
    public void onDraw(Canvas canvas) {
       //获得扫描框的矩形
        Rect frame = CameraManager.get().getFramingRect();
        if (frame == null) {
            return;
        }

        int width = canvas.getWidth();
        int height = canvas.getHeight();

        //绘制扫描框矩形
        // Draw the exterior (i.e. outside the framing rect) darkened
        paint.setColor(resultBitmap != null ? resultColor : maskColor);
        canvas.drawRect(0, 0, width, frame.top, paint);
        canvas.drawRect(0, frame.top, frame.left, frame.bottom + 1, paint);
        canvas.drawRect(frame.right + 1, frame.top, width, frame.bottom + 1,
                paint);
        canvas.drawRect(0, frame.bottom + 1, width, height, paint);

        Collection<ResultPoint> currentPossible = possibleResultPoints;
        Collection<ResultPoint> currentLast = lastPossibleResultPoints;
        if (currentPossible.isEmpty()) {
             lastPossibleResultPoints = null;
        } else {
        possibleResultPoints = new HashSet<ResultPoint>(5);
        lastPossibleResultPoints = currentPossible;
         paint.setAlpha(OPAQUE);
         paint.setColor(resultPointColor);
         for (ResultPoint point : currentPossible) {
              canvas.drawCircle(frame.left + point.getX(), frame.top
                     + point.getY(), 6.0f, paint);
                }
            }
          if (currentLast != null) {
               paint.setAlpha(OPAQUE / 2);
               paint.setColor(resultPointColor);
               for (ResultPoint point : currentLast) {
                    canvas.drawCircle(frame.left + point.getX(), frame.top
                            + point.getY(), 3.0f, paint);
                }
            }

          postInvalidateDelayed(ANIMATION_DELAY, frame.left, frame.top,
                    frame.right, frame.bottom);
        }
```
# 绘制移动的线
```
 //定义好扫描线每秒移动端的像素
            slideTop += SPEEN_DISTANCE;
            //判断扫描线的长度是否大于扫描框底部，若大于则重新回到扫描框头部再进行扫描
            if (slideTop >= frame.bottom) {
                slideTop = frame.top;
            }

            //动态定义出扫描线矩形
            Rect lineRect = new Rect();
            lineRect.left = frame.left;
            lineRect.right = frame.right;
            lineRect.top = slideTop;
            lineRect.bottom = slideTop + 18;
            //绘制上去
            canvas.drawBitmap(((BitmapDrawable) (getResources()
                            .getDrawable(R.drawable.fle))).getBitmap(), null, lineRect,
                    paint);
                    
```
# 二维码生成
```
   /**
     * 二维码生成
     */
    private void qrCodeGenerated() {

        String str = "点个赞噻";

        DisplayMetrics metric = new DisplayMetrics();
        getWindowManager().getDefaultDisplay().getMetrics(metric);
        int width = metric.widthPixels;
        try {

            Bitmap qrCode = Utils.createQRCode(str, width / 2);
            qrcodeImg.setImageBitmap(qrCode);

        } catch (WriterException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```
