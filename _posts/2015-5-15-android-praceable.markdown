---
layout: post
title:  "Android使用Parcelable在Activity之间传递对象"
date:   2015-05-15
categories: android
---

如果需要在Activity之间传递类对象的话,必须将类序列化.Android使用的是Parcelable接口.

被传递的类需要继承Parcelable接口.

    public class Store implements Parcelable
    {
        //此处变量的声明顺序必须和readFromParcel,writeToParcel的读写顺序一致,否则会出错
        private long id;
        private String name;
        private long userid;
        private double longitude;
        private double latitude;
        private String address;
        private String area;
        private String description;
        private String errMsg;
        private String logoUrl;
        private byte[] logo;    //图片数据,建议定义为byte[]类型.

        //这个方法不需要修改
        @Override
        public int describeContents() {
            return 0;
        }

        public static final Parcelable.Creator CREATOR = new Parcelable.Creator() {
            public Store createFromParcel(Parcel in) {
                return new Store(in);
            }

            public Store[] newArray(int size) {
                return new Store[size];
            }
        };

        public Store(Parcel in){
            readFromParcel(in);
        }

        public Store()
        {

        }

        public void readFromParcel(Parcel in){

            id = in.readLong();
            name = in.readString();
            userid = in.readLong();
            longitude = in.readDouble();
            latitude = in.readDouble();
            address = in.readString();
            area = in.readString();
            description = in.readString();
            errMsg = in.readString();
            logoUrl = in.readString();
            logo = new byte[in.readInt()];  
            in.readByteArray(logo); //读取byte流

        }

        @Override
        public void writeToParcel(Parcel parcel, int i) {
            parcel.writeLong(id);
            parcel.writeString(name);
            parcel.writeLong(userid);
            parcel.writeDouble(latitude);
            parcel.writeDouble(longitude);
            parcel.writeString(address);
            parcel.writeString(area);
            parcel.writeString(description);
            parcel.writeString(errMsg);
            parcel.writeString(logoUrl);
            parcel.writeInt(logo.length);   //图片byte流数据,先写入长度.
            parcel.writeByteArray(logo);    //再写入byte流

        }
    }




