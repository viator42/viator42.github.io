---
layout: post
title:  "随取随用的Android代码片段"
date:   2016-09-13
categories: android
---

### 获取手机的网络状态

    public static int getCurrentNetType(Context context){
        ConnectivityManager connManager = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
        if(connManager.getActiveNetworkInfo().isAvailable())
        {
            NetworkInfo networkInfo = connManager.getActiveNetworkInfo();
            if(networkInfo != null)
            {
                switch (networkInfo.getType())
                {
                    case ConnectivityManager.TYPE_WIFI:
                        return StaticValues.NETWORK_TYPE_WIFI;

                    case ConnectivityManager.TYPE_MOBILE:
                        return StaticValues.NETWOKR_TYPE_MOBILE;

                    default:
                        return StaticValues.NETWOKR_TYPE_MOBILE;

                }
            }
        }
        else
        {
            return StaticValues.NETWORK_TYPE_NONE;
        }

        return StaticValues.NETWOKR_TYPE_MOBILE;
    }