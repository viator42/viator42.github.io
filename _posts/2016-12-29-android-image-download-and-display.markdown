---
layout: post
title:  "Android图片的下载和展示"
date:   2016-12-29
categories: android
---

最近做了App的动态加载闪屏Splash的功能,研究了图片的下载,存储,读取一系列功能,步骤和代码记录如下.

#### 开启一个新的Thread进行图片下载操作

##### DownloadSplashThread.java

    public class DownloadSplashThread extends Thread
    {
        private String url;
        private Context context;

        public DownloadSplashThread(Context context, String url)
        {
            this.url = url;
            this.context = context;
        }
        @Override
        public void run() {
            super.run();

            byte[] data = null;
            try {
                data = new ImageAction(context).getImage(url);

            } catch (Exception e) {
                e.printStackTrace();
            }

            if(data != null)
            {
                Bitmap mBitmap = null;
                try {
                    mBitmap = BitmapFactory.decodeByteArray(data, 0, data.length);
                }catch (OutOfMemoryError e)
                {
                    System.gc();
                    System.runFinalization();
                }

                if(mBitmap != null)
                {
                    try {
                        new ImageAction(context).saveFile(context, mBitmap, "splash.jpg");
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }

            return;
        }
    }


#### 图片下载,保存操作类

    public class ImageAction
    {
        //根据url获取图片
        public byte[] getImage(String path) throws Exception{
            URL url = new URL(path);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setConnectTimeout(5 * 1000);
            conn.setRequestMethod("GET");
            InputStream inStream = conn.getInputStream();
            if(conn.getResponseCode() == HttpURLConnection.HTTP_OK){
                return readStream(inStream);
            }
            return null;
        }

        public static byte[] readStream(InputStream inStream) throws Exception{
            ByteArrayOutputStream outStream = new ByteArrayOutputStream();
            byte[] buffer = new byte[1024];
            int len = 0;
            while( (len=inStream.read(buffer)) != -1){
                outStream.write(buffer, 0, len);
            }
            outStream.close();
            inStream.close();
            return outStream.toByteArray();
        }

        public void saveFile(Context context, Bitmap bm, String fileName) throws IOException {
            // 这是将文件保存到内部存储中
            File file = new File(context.getFilesDir(), "FileName.png");
            OutputStream outStream = new FileOutputStream(file);
            bm.compress(Bitmap.CompressFormat.PNG, 80, outStream);
            outStream.flush();
            outStream.close();

            //或者也可以考虑存储到外部存储(SD卡)中,这需要申请外部存储读写权限,用户可能会拒绝
            //而且App删除后这些文件依然保留,不建议使用
            private final static String ALBUM_PATH
            = Environment.getExternalStorageDirectory() + "/com.brand.ushopping/";

        public void saveFile(Bitmap bm, String fileName) throws IOException {
            File dirFile = new File(ALBUM_PATH);
            if(!dirFile.exists()){
                dirFile.mkdir();
            }
            File myCaptureFile = new File(ALBUM_PATH + fileName);
            BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(myCaptureFile));
            bm.compress(Bitmap.CompressFormat.JPEG, 80, bos);
            bos.flush();
            bos.close();
        }
        
    }

#### 从存储中读取图片

    //读取内部存储的图片
    public Bitmap loadImage(Context context)
    {
        try
        {
            File file = new File(context.getFilesDir(), "Filename");
            if (file.exists()) {
                FileInputStream fileInputStream = new FileInputStream(file);
                Bitmap bitmap = BitmapFactory.decodeStream(fileInputStream);
                return bitmap;
            }

        }catch (Exception e)
        {
            e.printStackTrace();
        }

        return null;
    }

    //读取SD卡中的图片
    public Bitmap loadImage(String fileName)
    {
        String url = ALBUM_PATH + fileName;
        try
        {
            File file = new File(url);
            if (file.exists()) {
                Bitmap bitmap = BitmapFactory.decodeFile(url);
                return bitmap;
            }

        }catch (Exception e)
        {
            e.printStackTrace();
        }
        return null;
    }

#### 图片显示到ImageView

    Bitmap bitmap = loadImage(SplashAdActivity.this);
    imageView.setImageBitmap(bitmap);

