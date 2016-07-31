---
layout: post
title:  "Android图片的下载和展示"
date:   2016-07-13
categories: android
---

最近做了App的动态加载闪屏Splash的功能,研究了图片的下载,存储,读取一系列功能,步骤和代码记录如下.

#### 图片下载    

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

#### 下载的图片是二进制流,需要通过解码转换成Bitmap

    byte[] data = new ImageAction(MainActivity.this).getImage(url);
    Bitmap mBitmap = BitmapFactory.decodeByteArray(data, 0, data.length);

#### 图片显示到ImageView

    imageView.setImageBitmap(bitmap);

#### 下载的图片保存到存储中    

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

#### 从存储中读取图片

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
