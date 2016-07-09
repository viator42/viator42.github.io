---
layout: post
title:  "Android ContentProvider笔记"
date:   2016-07-04
categories: android
---

### 作用    
为应用提供对外的CURD接口.系统或者其他应用可以通过此接口进行数据的查询添加删除操作.

### 实现

#### 定义Provider类    

    public class TesterProvider extends ContentProvider {
        public TesterProvider() {
        }

        @Override
        public int delete(Uri uri, String selection, String[] selectionArgs) {
            // Implement this to handle requests to delete one or more rows.
            Log.v("Provider", "delete "+uri + selection);
            throw new UnsupportedOperationException("Not yet implemented");
        }

        @Override
        public String getType(Uri uri) {
            // TODO: Implement this to handle requests for the MIME type of the data
            // at the given URI.
            throw new UnsupportedOperationException("Not yet implemented");
        }

        @Override
        public Uri insert(Uri uri, ContentValues values) {
            // TODO: Implement this to handle requests to insert a new row.
            throw new UnsupportedOperationException("Not yet implemented");
        }

        @Override
        public boolean onCreate() {
            // TODO: Implement this to initialize your content provider on startup.
            return false;
        }

        @Override
        public Cursor query(Uri uri, String[] projection, String selection,
                            String[] selectionArgs, String sortOrder) {
            // TODO: Implement this to handle query requests from clients.
            Log.v("Provider", "query "+uri);

            return null;
        }

        @Override
        public int update(Uri uri, ContentValues values, String selection,
                        String[] selectionArgs) {
            // TODO: Implement this to handle requests to update one or more rows.
            throw new UnsupportedOperationException("Not yet implemented");
        }
    }

#### 在AndroidManifest.xml文件中注册Provider.

    </application>
        <provider
        android:name=".TesterProvider"
        android:authorities="com.viator42.provider.tester"
        android:enabled="true"
        android:exported="true"></provider>
    </application>


#### 使用ContentResolver调用Provider    

    ContentResolver contentResolver = getContentResolver();
    Uri uri = Uri.parse("content://com.viator42.provider.tester");
    contentResolver.query(uri, null, null, null, null);

###注意    
* 调用的url应该加上content://前缀
* ContentProvider无法传入Content对象,应用不一定处在运行状态
