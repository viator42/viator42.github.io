---
layout: post
title:  "Android 使用DownloadManager升级APK功能实现"
date:   2016-07-05
categories: android
---

下载新版APK并打开安装包的功能实现.

我把功能写在了ApplicationContext中,使用的时候直接调用downloadApp(url)方法即可.

    public class AppContext extends Application
    {
        private DownloadManager dMgr;
        private long downloadId;

        public void onCreate() {
            super.onCreate();
            
            dMgr = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
        }
        public void downloadApp(String url)
        {
            DownloadManager.Request request = new DownloadManager.Request(
                    Uri.parse(url)
            );

            // 设置下载路径和文件名
            request.setTitle("升级包下载中...");
            // request.setDescription(“MeiLiShuo desc”); //设置下载中通知栏提示的介绍
            request.setDestinationInExternalPublicDir("download", "UShopping.apk");
            request.setAllowedNetworkTypes(DownloadManager.Request.NETWORK_WIFI);
            request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
            request.setMimeType("application/vnd.android.package-archive");
            request.allowScanningByMediaScanner();    // 设置为可被媒体扫描器找到
            request.setVisibleInDownloadsUi(true);  // 设置为可见和可管理
            IntentFilter filter = new IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE);
            registerReceiver(receiver, filter);

            downloadId = dMgr.enqueue(request);
        }

        //------------------新版app下载------------------
        private BroadcastReceiver receiver = new BroadcastReceiver() {
            //下载完成的回调方法
            @Override
            public void onReceive(Context context, Intent intent) {
                try{
                    //下载完成后打开APK
                    Intent install = new Intent(Intent.ACTION_VIEW);
                    Uri downloadFileUri = dMgr.getUriForDownloadedFile(downloadId);
                    install.setDataAndType(downloadFileUri, "application/vnd.android.package-archive");
                    install.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    startActivity(install);

                }catch (Exception e)
                {
                    e.printStackTrace();
                }

            }
        };

        public void downloadApp(String url)
        {
            DownloadManager.Request request = new DownloadManager.Request(
                    Uri.parse(url)
            );

            // 设置下载路径和文件名
            request.setTitle("升级包下载中...");
            // request.setDescription(“MeiLiShuo desc”); //设置下载中通知栏提示的介绍
            request.setDestinationInExternalPublicDir("download", "UShopping.apk");
            request.setAllowedNetworkTypes(DownloadManager.Request.NETWORK_WIFI);
            request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
            request.setMimeType("application/vnd.android.package-archive");
            request.allowScanningByMediaScanner();    // 设置为可被媒体扫描器找到
            request.setVisibleInDownloadsUi(true);  // 设置为可见和可管理
            IntentFilter filter = new IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE);
            registerReceiver(receiver, filter);

            downloadId = dMgr.enqueue(request);
        }
    
    }