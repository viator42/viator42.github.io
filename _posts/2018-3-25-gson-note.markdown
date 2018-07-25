---
layout: post
title:  "JSON序列库Gson使用笔记"
date:   2018-3-25
categories: Java Android
---

引入gson库

    dependencies {
        compile 'com.google.code.gson:gson:2.8.2'
    }

## 使用

### 数据定义

Json字符串

    {
    "name": "Norman",
    "email": "norman@futurestud.io",
    "age": 26,
    "isDeveloper": true
    }

Java Bean类

    public class UserSimple {
        String name;
        String email;
        int age;
        boolean isDeveloper;
    }

### 相互转换 

__Java bean -> json__

    Gson gson = new Gson();
    String userJson = gson.toJson(userObject);

__json -> Java bean__

    String userJson = "{'age':26,'email':'norman@futurestud.io','isDeveloper':true,'name':'Norman'}";
    Gson gson = new Gson();
    UserSimple userObject = gson.fromJson(userJson, UserSimple.class);

嵌套类也可以直接相互转换

    public class UserNested {
        String name;
        String email;
        boolean isDeveloper;
        int age;

        // new, see below!
        UserAddress userAddress;
    }

    public class UserAddress {
        String street;
        String houseNumber;
        String city;
        String country;
    }

转换的json数据

    {
        "age": 26,
        "email": "norman@futurestud.io",
        "isDeveloper": true,
        "name": "Norman",

        "userAddress": {
            "city": "Magdeburg",
            "country": "Germany",
            "houseNumber": "42A",
            "street": "Main Street"
        }
    }

### List列表相互转换    

转换之前需要先定义列表类型    

    Type founderListType = new TypeToken<ArrayList<Founder>>(){}.getType();

使用这个类型进行转换

    String founderJson = "[{'name': 'Christian','flowerCount': 1}, {'name': 'Marcus', 'flowerCount': 3}, {'name': 'Norman', 'flowerCount': 2}]";
    Gson gson = new Gson();
    Type founderListType = new TypeToken<ArrayList<Founder>>(){}.getType();
    List<Founder> founderList = gson.fromJson(founderJson, founderListType);

或者把列表类型嵌套到一个model类中,这样可以直接进行转换

    public class PostList {
        public ArrayList<PostItem> postItems;
        public boolean success;
    }

Map,Set类型同理

__如果bean的属性名和转换后的json key不一致,解决方法是bean字段前面加注解__

    public class UserSimple {
        @SerializedName("fullName")
        String name;
        String email;
        boolean isDeveloper;
        int age;
    }

序列化之后的json就会变成这样

    {
        "age": 26,
        "email": "norman@futurestud.io",
        "fullName": "Norman",
        "isDeveloper": true
    }

序列化名称的注解还可以添加一个替代值,反序列化的时候没有找到value的字段就会去寻找alternate.序列化的时候使用value    

    @SerializedName(value = "full_name", alternate = "fullname")

__@Expose注解设置一个字段是否参与json的序列化/反序列化__

    @Expose(serialize = false, deserialize = false)

通过GsonBuilder创建gson对象    
FieldNamingPolicy 自定义转换的json命名格式    

    GsonBuilder gsonBuilder = new GsonBuilder();
    gsonBuilder.setFieldNamingPolicy(FieldNamingPolicy.LOWER_CASE_WITH_UNDERSCORES);
    gson = gsonBuilder.create();

命名方式

* IDENTITY 默认,json和java的命名相同
* LOWER_CASE_WITH_UNDERSCORES 全小写下划线分隔
* LOWER_CASE_WITH_DASHES 全小写短横线分隔
* UPPER_CAMEL_CASE 驼峰式,首字母大写

除了使用预设的还可以自定义命名格式,创建FieldNamingStrategy类重写其中的translateName方法   
返回修改后的名称    

    FieldNamingStrategy fieldNamingStrategy = new FieldNamingStrategy() {
        @Override
        public String translateName(Field f) {
            String name = f.getName();
            return name + "aaa";
        }
    };

    gsonBuilder = new GsonBuilder();
    gsonBuilder.setFieldNamingStrategy(fieldNamingStrategy);
    gson = gsonBuilder.create();

参考: https://futurestud.io/tutorials/gson-advanced-custom-serialization-part-1

## 自定义序列化,反序列化,TypeAdapter使用



