---
layout: post
title:  "Android学习笔记"
date:   2015-04-04
categories: android
---

### 获取界面组件

	<Button
	android:id="@+id/btn1" />

	Button btn1 = findViewById(R.id.btn1);

## Activity 相关

Actitity之间跳转,分为显式跳转和隐式跳转

### 显式跳转

    Intent intent=new Intent(this,OtherActivity.class);  //方法1

    Intent intent2=new Intent();

    intent2.setClass(this, OtherActivity.class);//方法2
    intent2.setClassName(this, "com.zy.MutiActivity.OtherActivity");  //方法3 此方式可用于打开其它的应用
    intent2.setComponent(new ComponentName(this, OtherActivity.class));  //方法4
    startActivity(intent2);

### 隐式跳转（只要action、category、data和要跳转到的Activity在AndroidManifest.xml中设置的匹配就OK

隐式跳转不指定启动特定的Activity,需要判断action和category找到匹配的Actitity启动     
AndroidManifest.xml文件中为每一个Activity中设置intent-filter来指定

这个是指定启动App启动时首先启动的Actitity

    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>

指定目标Activity的filter, 包括name和category,name只有一个category可以设置多个

    <activity android:name=".SecondActivity" >
        <intent-filter>
            <action android:name="com.example.activitytest.ACTION_START" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="com.example.activitytest.MY_CATEGORY"/>
            <data android:scheme="ispring" android:host="blog.csdn.net" />
        </intent-filter>
    </activity>

创建Intent的时候设置name和category,只有name和所有的category都匹配的时候才会跳转到新的Activity.否则就会爆出ActivityNotFoundException异常

    Intent intent = new Intent("com.example.activitytest.ACTION_START");
    intent.addCategory("com.example.activitytest.MY_CATEGORY");
    startActivity(intent);

Android可以根据Intent所携带的信息去查找要启动的组件，Intent还携带了一些数据信息以便要启动的组件根据Intent中的这些数据做相应的处理。

### Intent的组成

Intent由6部分信息组成：Component Name、Action、Data、Category、Extras、Flags。根据信息的作用用于，又可分为三类:   

* Component Name、Action、Data、Category为一类，这4中信息决定了Android会启动哪个组件，其中Component Name用于在显式Intent中使用，Action、Data、Category、Extras、Flags用于在隐式Intent中使用。

* Extras为一类，里面包含了具体的用于组件实际处理的数据信息。
* Flags为一类，其是Intent的元数据，决定了Android对其操作的一些行为

Component Name是在显式Intent中指定目标Activity    
Action是表示了要执行操作的字符串，比如查看或选择，其对应着Intent Filter中的action标签<action />    
Data指的是Uri对象和数据的MIME类型，其对应着Intent Filter中的data标签<data />一个完整的Uri由scheme、host、port、path组成，格式是<scheme>://<host>:<port>/<path>，例如content://com.example.project:200/folder/subfolder/etc。    
Category包含了关于组件如何处理Intent的一些其他信息，虽然可以在Intent中加入任意数量的category，但是大多数的Intent其实不需要category。
Extras就是额外的数据信息，Intent中有一个Bundle对象存储着各种键值对，接收该Intent的组件可以从中读取出所需要的信息以便完成相应的工作。

参考链接: http://lib.csdn.net/article/android/62966

### Activity全屏    
    
    requestWindowFeature(Window.FEATURE_NO_TITLE);  //隐藏应用的ActionBar
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
            WindowManager.LayoutParams.FLAG_FULLSCREEN);    //应用全屏,隐藏android的标题栏

### activity的生命周期

![Activity Lifescycle](http://android.okhelp.cz/wp-content/uploads/lifecycle-activity-android.png "Activity Lifescycle")

### activity启动模式

* standard 默认的启动模式,每次启动一个新的activity都创建一个新的实例到栈中.
* singleTop 创建activity的时候如果栈顶已经是该活动,就直接使用不会重复创建.
* singleTask 如果栈中已经有此activity则把这个活动之上的所有活动出栈.

设置方法

    <activity
    android:launchMode="singleTop"/>

### activity跳转(带参数)

	Intent intent = new Intent(activity.this, MainActivity.class);
    Bundle bundle = new Bundle();
    Bundle bundle = new Bundle();
    bundle.putString("key", "value");
	startActivity(intent);
	finish();

    Bundle bundle = this.getIntent().getExtras();
    String value = bundle.getString("key", "");

### 输出log日志

	import android.util.Log;
	Log.v(“log title”, “value");

2个参数,一个是tag,另一个是日志内容.

输出日志类型

* Log.v();  日志信息,对应级别verbose
* Log.i();  调试信息,对应级别info
* Log.w();  警告信息,对应级别warning
* Log.e();  错误信息,对应级别error

### RelativeLayout属性

第一类:属性值为true或false
  
  Android:layout_centerHrizontal 水平居中    
  android:layout_centerVertical 垂直居中    
  android:layout_centerInparent 相对于父元素完全居中    
  android:layout_alignParentBottom 贴紧父元素的下边缘    
  android:layout_alignParentLeft 贴紧父元素的左边缘    
  android:layout_alignParentRight 贴紧父元素的右边缘    
  android:layout_alignParentTop 贴紧父元素的上边缘    
  android:layout_alignWithParentIfMissing 如果对应的兄弟元素找不到的话就以父元素做参照物    

第二类：属性值必须为id的引用名“@id/id-name”

  android:layout_below 在某元素的下方    
  android:layout_above 在某元素的的上方    
  android:layout_toLeftOf 在某元素的左边    
  android:layout_toRightOf 在某元素的右边    
  android:layout_alignTop 本元素的上边缘和某元素的的上边缘对齐    
  android:layout_alignLeft 本元素的左边缘和某元素的的左边缘对齐    
  android:layout_alignBottom 本元素的下边缘和某元素的的下边缘对齐    
  android:layout_alignRight 本元素的右边缘和某元素的的右边缘对齐    

第三类：属性值为具体的像素值，如30dip，40px

  android:layout_marginBottom 离某元素底边缘的距离    
  android:layout_marginLeft 离某元素左边缘的距离    
  android:layout_marginRight 离某元素右边缘的距离    
  android:layout_marginTop 离某元素上边缘的距离    

### SharedReference

	//获取SharedPreferences实例     第一个参数是名称, 第二个是作用域
	SharedPreferences ref = getSharedPreferences("user", Context.MODE_PRIVATE);
	//获取数据,参数分别为key值和默认值(未取到值时的返回值)
	int id = ref.getInt("id", 0);
	ref.getString("username", "")

	//向SharedPreferences存储数据
	SharedPreferences ref = activity.getSharedPreferences("user", Context.MODE_PRIVATE);
	//获取editor对象
	SharedPreferences.Editor editor = ref.edit();
	//赋值
	editor.putInt("id", 1);
	editor.putString("tel", “1234567890");
	//完成后提交修改
	editor.commit();

### Fragment (Android4 以上)

Fragment生命周期
![Fragment Lifescycle](http://indy-world.net/wp-content/uploads/2014/08/fragment_lifecycle.png "Fragment Lifescycle")

Fragment类定义
HelloFragment.java

public class HelloFragment extends Fragment

自定义fragment类继承Fragment

包含以下方法

    public class StatisticFragment extends Fragment {
        private OnFragmentInteractionListener mListener;
        private AppContext context;
        private Store store;

        private TextView productCountTextView;

        /**
         * Use this factory method to create a new instance of
         * this fragment using the provided parameters.
         *
         * @return A new instance of fragment StatisticFragment.
         */
        // TODO: Rename and change types and number of parameters
        public static StatisticFragment newInstance(String param1, String param2) {
            StatisticFragment fragment = new StatisticFragment();
            Bundle args = new Bundle();

            fragment.setArguments(args);
            return fragment;
        }

        public StatisticFragment() {
            // Required empty public constructor
        }

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            context = (AppContext) getActivity().getApplicationContext();
            store = context.getStore();

        }

        @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container,
                                 Bundle savedInstanceState) {
            // Inflate the layout for this fragment
            View view = inflater.inflate(R.layout.fragment_statistic, container, false);

            productCountTextView = (TextView) view.findViewById(R.id.product_count);
            
            return view;
        }
    }

Activity.java 使用 Fragment

先定义FragmentManager

    private FragmentManager manager;
    private FragmentTransaction transaction;

设置Fragment

    manager = getFragmentManager();
    transaction = manager.beginTransaction();
    transaction.replace(R.id.content, fragment );
    transaction.commit();

### fragment传递参数

创建对象时使用bundle传递参数

    fragment1 = new Fragment1();
    Bundle bundle = new Bundle();
    bundle.putParcelable("data", model01);

    fragment1.setArguments(bundle);

    transaction = manager.beginTransaction();
    transaction.replace(R.id.content, fragment1);
    transaction.commit();

然后fragment获取参数

    public static Fragment1 newInstance(Model01 model01) {
        Fragment1 fragment = new Fragment1();
        Bundle args = new Bundle();
        args.putParcelable("data", model01);
        fragment.setArguments(args);
        return fragment;
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            model01 = getArguments().getParcelable("data");
        }
    }

可以传递基本类型和parcelble对象

通过id获取fragment对象

    FragmentManager manager = getFragmentManager();
    Fragment1 fragment1 = (Fragment1) manager.findFragmentById(R.id.content);

获取到fragment实例之后就可以执行重新赋值的操作

--------

### ListView

简单列表,只显示一个textview,使用ArrayAdapter实现.
    
    private ArrayList<String> paymethodList;
    private ListView payMethodListView;

    payMethodListView = (ListView) view.findViewById(R.id.pay_method_list);

    paymethodList = new ArrayList<String>();
            paymethodList.add("支付宝");
            paymethodList.add("信用卡1");
            paymethodList.add("信用卡2");

    ArrayAdapter adapter = new ArrayAdapter(context, android.R.layout.simple_list_item_1, paymethodList);
            payMethodListView.setAdapter(adapter);
            payMethodListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
                @Override
                public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                    payMethod = paymethodList.get(position);
                    setPayConfirmMode();
                }
            });

带单选框的listview

    listview=new ListView(this);

    //android.R.layout.simple_list_item_single_choice设置ListView项目条带单选按钮

    listview.setAdapter(new ArrayAdapter<String>(this,android.R.layout.simple_list_item_single_choice,getData()));

    listview.setChoiceMode(ListView.CHOICE_MODE_SINGLE);//如果不使用这个设置，选项中的radiobutton无法响应选中事件

    listview.setItemChecked(2, true);

    payMethodListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
                @Override
                public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                    listview.setItemChecked(position, true);
                }
            });

动态添加数据

    adapter.add() 或者addAll()添加元素
    adapter.notifyDataSetChanged();      //通知listview数据改变


### ListView自定义布局

先创建一个layout resource file: layout.xml

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="match_parent" android:layout_height="match_parent">

	<TextView
	android:layout_width="100dp"
	android:layout_height="50dp"
	android:id="@+id/name" />

	<TextView
	android:layout_width="100dp"
	android:layout_height="50dp"
	android:id="@+id/price" />

	<TextView
	android:layout_width="100dp"
	android:layout_height="50dp"
	android:id="@+id/address" />
	</LinearLayout>

创建一个Adaper类继承自BaseAdapter

	public class ContentAdaper extends BaseAdapter {
	    //填充数据的List
	    List<Map<String, Object>> list = new ArrayList<Map<String, Object>>();

	    //上下文
	    private Context context;

	    //用来导入布局
	    private LayoutInflater inflater = null;

	    //构造器
	    public ContentAdaper(List<Map<String, Object>> list, Context context) {
	        this.context = context;
	        this.list = list;
	        inflater = LayoutInflater.from(context);
	    }

	    @Override
	    public int getCount() {
	        // TODO Auto-generated method stub
	        return list.size();
	    }

	    @Override
	    public Object getItem(int arg0) {
	        // TODO Auto-generated method stub
	        return list.get(arg0);
	    }

	    @Override
	    public long getItemId(int position) {
	        // TODO Auto-generated method stub
	        return position;
	    }

	    //核心部分，返回Listview视图
	    @Override
	    public View getView(int position, View convertView, ViewGroup parent) {
	        // TODO Auto-generated method stub
	        ViewHolder holder = null;

	        if (convertView == null) {
	            holder = new ViewHolder();
	            convertView = inflater.inflate(R.layout.layout, null);
	            holder.name = (TextView) convertView.findViewById(R.id.name);
	            holder.price = (TextView) convertView.findViewById(R.id.price);
	            holder.address = (TextView) convertView.findViewById(R.id.address);
	            //为view设置标签
	            convertView.setTag(holder);
	        } else {
	            holder = (ViewHolder) convertView.getTag();
	        }

	        holder.name.setText(list.get(position).get("name").toString());
	        holder.price.setText(list.get(position).get("price").toString());
	        holder.address.setText(list.get(position).get("address").toString());

	        return convertView;
	    }

	    static class ViewHolder {
	        TextView name;
	        TextView price;
	        TextView address;
	    }
	}

最后在Activity中调用Adaper.

	List<Map<String,Object>> data = new ArrayList<Map<String,Object>>();
	Map item1 = new HashMap();
	item1.put("name", "Name1");
	item1.put("price", "450")
	item1.put("address", "address 101");
	data.add(item1);
	ContentAdaper adaper = new ContentAdaper(data, this);
	listView.setAdapter(adaper);

listView onClick事件设置

	position

	orderlistView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {

            }
        });

参数id用来返回某一行的标识id, 默认返回的是行号.需要重写Adapter的getItemId方法来获取id属性.

	@Override
    public long getItemId(int position) {
        // TODO Auto-generated method stub
        return (long)list.get(position).get("id");

    }

--------

### 使用SimpleAddpter

    List listData = new ArrayList<Map<String,Object>>();

    Map line = new HashMap();
    line.put("id", 1);
    line.put("name", "beijing");
    listData.add(line);

    // 参数含义, context, 列表数据, 列表项布局, 显示的value名称, 名称对应的xml布局id
    SimpleAdapter adapter = new SimpleAdapter(this, listData, R.layout.spinner_item, new String[] {"name"}, new int[] {R.id.name});
    provinceSpinner.setAdapter(adapter);

SimpleAdapter构造函数的参数分别为

1. 上下文,一般为Activity类
2. 列表数据, 类型是List,每个元素是Map, 每个Map对象包含key,value键值对表示属性名和属性值.
3. 布局xml文件.
4. 字符串数组, 表示数据中key的属性名.
5. int数组, 数据中每个key属性名所对应的样式xml中的组件id.

最后两个参数数组项是一一对应的,把数据赋值给对应的控件显示

设置列表项选中时的事件

    provinceSpinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
                Toast.makeText(RegisterActivity.this, ((Map)provinceSpinner.getItemAtPosition(i)).get("id").toString(), Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onNothingSelected(AdapterView<?> adapterView) {

            }
        });

--------

### Async task

一般声明在Activity类中作为内内部类.标注三个参数的类型
第一个参数表示要执行的任务通常是网络的路径。第二个参数表示进度的刻度，第三个参数表示任务执行的结果。

重写方法完成操作.

*  onPreExecute 表示任务执行之前的操作.    
*  doInBackground方法实现耗时的任务。    
*  onPostExecute 主要是更新UI的操作.    

        public class ListAllTask extends AsyncTask<String, Void, String>
        {
            @Override
            protected void onPreExecute() {
                super.onPreExecute();
            }

            @Override
            protected String doInBackground(String... params) {
                return null;
            }

            @Override
            protected void onPostExecute(String s) {
                super.onPostExecute(s);
            }
        }

运行AsyncTask
new ListAllTask().execute("aaa");

### Application 全局类配置

定义一个类可以继承Application, onCreate方法在应用启动时运行.

	public class AppContext extends Application
	{
	    private User user;
	    private Store store;

	    @Override
	    public void onCreate() {
	        super.onCreate();

	    }
	}

必须在manifest中指定类名.

	<application
	    android:name=".AppContext" >

在Activity中可以直接调用Application类的方法和变量
	
	AppContext context =  (AppContext)activity.getApplication();

### Handler和Message

从网络上下载图片的示例.

AndroidManifest.xml添加网络访问权限

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

导入Hanler和Message类.

    import android.os.Handler;
    import android.os.Message;

Activity类和OnCreate方法.

	private String imgUrl = "http://image_url";
    private final int IS_END = 1;
    private ProgressDialog dialog;

	protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        img = (ImageView)findViewById(R.id.img);
        loadImgBtn = (Button)findViewById(R.id.load_image);
        loadImgBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            	//新建一个网络访问的线程.
                new Thread(new MyThread()).start();
                dialog.show();

            }
        });

        dialog = new ProgressDialog(this);
        dialog.setTitle("提示");
        dialog.setMessage("正在下载,请稍后....");
        dialog.setCancelable(false);

    }

Activity类中新建一个Handler类并重写handleMessage方法.

	public Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg)
        {
        	//获取Message消息
            byte[] data = (byte[])msg.obj;
            Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);
            img.setImageBitmap(bitmap);

            if(msg.what == IS_END)
            {
                dialog.dismiss();
            }

        }
    };


	public class MyThread implements Runnable
    {

        @Override
        public void run() {
            HttpClient httpClient = new DefaultHttpClient();
            HttpGet httpGet = new HttpGet(imgUrl);
            HttpResponse response = null;
            try{
                response = httpClient.execute(httpGet);
                if(response.getStatusLine().getStatusCode() == 200)
                {
                    byte[] data = EntityUtils.toByteArray(response.getEntity());
                    //Message.obtain()方法是从消息池中获取消息对象.
                    Message message = Message.obtain();
                    message.obj = data;
                    message.what = IS_END;
                    //发送Message消息
                    handler.sendMessage(message);

                }

            } catch (Exception e)
            {
                e.printStackTrace();
            }


        }
    }

发送Message消息方法

* 第一种方法

	Message message = Message.obtain();
	message.obj = data;
	message.what = IS_END;
	handler.sendMessage(message);

* 第二种方法, 新建message对象指定handler

    Message message = Message.obtain(handler);
    message.obj = data;
    message.what = IS_END;
    message.sendToTarget();

* 第三种方法, 新建message对象指定handler和what参数
	
	Message message = Message.obtain(handler, 1);
    message.obj = data;
    message.what = IS_END;
    message.sendToTarget();

### Looper对象

Activity中有一个默认的Looper对象,来处理子线程发送的消息.所以主线程接收子线程发送的消息就补需要定义looper
如果子线程需要获取主线程发送的消息就必须定义Lopper.

定义一个Handler对象

	private Handler handler;

主线程发送Message消息

	Message message = Message.obtain();
	message.obj = "Jack";
	handler.sendMessage(message);

子线程定义Loop消息队列并接收消息.
	public class MyThread implements Runnable
    {

        @Override
        public void run() {
            Looper.prepare();//循环消息队列

            handler = new Handler()
            {
                @Override
                public void handleMessage(Message msg) {
                    super.handleMessage(msg);
                    Log.v("--supai", "从UI主线程中获取消息-->" + msg.obj);

                }
            };

            Looper.loop();//直到消息队列循环结束

        }
    }

### 查看SHA1签名

(Mac OS)
    
    keytool -list -v -keystore ~/.android/debug.keystore

口令没有就直接enter

--------

### Spinner选择器

有dialog和dropdown两种显示方式.
需要使用适配器来完成填充数据.

获取数据, 设置显示内容name和值id.    

    ArrayList<Map<String, Object>> result= new ArrayList<Map<String, Object>>();
    Map province = new HashMap();
    province.put("id", jsonObject.getString("code"));
    province.put("name", jsonObject.getString("name"));
    result.add(province);


设置spinner    

    provinceSpinner = (Spinner)findViewById(R.id.province);
    
    SimpleAdapter adapter = new SimpleAdapter
        NewStoreActivity.this, result, R.layout.spinner_item, new String[] {"name"}, new int[] {R.id.alias});
    provinceSpinner.setAdapter(adapter);
    provinceSpinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
        @Override
        public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
            String code = ((Map) provinceSpinner.getItemAtPosition(i)).get("id").toString();
            new GetCityTask().execute(new Integer(code));

        }
        @Override
        public void onNothingSelected(AdapterView<?> adapterView) {

        }
    });

Spinner_item.xml布局文件

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent" android:layout_height="match_parent"
        android:orientation="horizontal">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:id="@+id/alias"
            android:gravity="center" />
    </LinearLayout>

### 自定义选项布局

/res/value下添加spinner_item.xml

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent" android:layout_height="match_parent"
        android:orientation="horizontal">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:id="@+id/name"
            android:gravity="center" />
    </LinearLayout>

--------

### AVD 模拟器添加sd卡

使用mksdcard程序,位置如下(MacOS系统)

    /Users/viator42/Library/Android/sdk/tools/mksdcard

命令格式 label:标签  size:大小 file:文件位置

    mksdcard -l <label> <size> <file>

--------

### 获取相册图片

方式1, 直接返回图片数据.

点击按钮触发打开相册的intent,此处putExtra("return-data", true)是直接返回图片数据,图片较小而且不需要剪裁的时候使用.否则使用临时文件存储.

    iconImageBtn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //打开相册
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
            intent.setType("image/*");
            intent.putExtra("crop", "true");
            intent.putExtra("return-data", true);

            startActivityForResult(intent, 1000);
        }
    });

定义onActivityResult方法,获取Bitmap图片.

    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == 1000 && resultCode == RESULT_OK) {
            if(data != null)
            {
                ContentResolver resolver = getContentResolver();
                Uri originalUri = data.getData();
                try {
                    MediaStore.Images.Media.getBitmap(resolver, originalUri);
    
                } catch (IOException e) {
                    e.printStackTrace();
                }
                if (logoBitmap != null)
                {
                    //获取Bitmap
                    iconImageBtn.setImageBitmap(logoBitmap);

                }

            }
            else
            {
                Toast.makeText(activity.getApplicationContext(), "图片读取失败", Toast.LENGTH_SHORT).show();
            }
        }

        super.onActivityResult(requestCode, resultCode, data);

    }

方式2, 使用临时文件.
如果图片需要剪裁则必须使用临时文件,否则会出异常.

定义文件

    private File sdcardTempFile = new File("/mnt/sdcard/", "tmp_pic_" + SystemClock.currentThreadTimeMillis() + ".jpg");

点击按钮触发打开相册的intent

    iconImageBtn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            //打开相册
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
            intent.setType("image/*");
            intent.putExtra("crop", "true");
            intent.putExtra("aspectX", 1);// 裁剪框比例
            intent.putExtra("aspectY", 1);
            intent.putExtra("outputX", StaticValues.ICON_WIDTH);// 输出图片大小
            intent.putExtra("outputY", StaticValues.ICON_HEIGHT);
            intent.putExtra("return-data", false);// 不返回图片数据(必须)
            intent.putExtra("output", Uri.fromFile(sdcardTempFile));

            startActivityForResult(intent, 1000);
            }
        });

定义onActivityResult方法,获取Bitmap图片.

    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == 1000 && resultCode == RESULT_OK) {
            if(data != null)
            {
                logoBitmap = BitmapFactory.decodeFile(sdcardTempFile.getAbsolutePath());
                if (logoBitmap != null)
                {
                    iconImageBtn.setImageBitmap(logoBitmap);
                }s
            }
            else
            {
                Toast.makeText(activity.getApplicationContext(), "图片读取失败", Toast.LENGTH_SHORT).show();
            }
        }
        super.onActivityResult(requestCode, resultCode, data);
    }

### 图片尺寸压缩

    /**
     * 图片进行压缩处理,宽度固定,高度自动适配
     * @param bm
     *
     * @return
     */
    public static Bitmap zoomImg(Bitmap bm){
        // 获得图片的宽高
        int width = bm.getWidth();
        int height = bm.getHeight();

        if(width > 0 && height > 0)
        {
            if(width <= StaticValues.WIDTH_LIMIT)
            {
                //原图小于规定尺寸的不用缩放
                return bm;
            }
            else
            {
                int newWidth = StaticValues.WIDTH_LIMIT;
                int newHeight = (int)((newWidth * height) / width);
                // 计算缩放比例
                float scaleWidth = ((float) newWidth) / width;
                float scaleHeight = ((float) newHeight) / height;

                // 取得想要缩放的matrix参数
                Matrix matrix = new Matrix();
                matrix.postScale(scaleWidth, scaleHeight);
                // 得到新的图片
                Bitmap newbm = Bitmap.createBitmap(bm, 0, 0, width, height, matrix, true);
                return newbm;
            }
        }
        else
        {
            return bm;

        }
    }

### 图片裁剪

    Intent intent = new Intent("com.android.camera.action.CROP");
    intent.setDataAndType(imageUri, "image/*");
    intent.putExtra("crop", "true");
    intent.putExtra("aspectX", 1);
    intent.putExtra("aspectY", 1);
    intent.putExtra("outputX", StaticValues.ICON_WIDTH);
    intent.putExtra("outputY", StaticValues.ICON_HEIGHT);
    intent.putExtra("scale", true);
    intent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri);
    intent.putExtra("return-data", false);
    intent.putExtra("outputFormat", Bitmap.CompressFormat.JPEG.toString());
    intent.putExtra("noFaceDetection", true); // no face detection
    startActivityForResult(intent, StaticValues.IMAGE_CROP);

    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if(requestCode == StaticValues.IMAGE_CROP && resultCode == RESULT_OK)
        {
            //裁剪
            if(imageUri != null){
                logoBitmap = decodeUriAsBitmap(imageUri);//decode bitmap
                iconImageBtn.setImageBitmap(logoBitmap);
            }
            else
            {
                Toast.makeText(NewStoreActivity.this, "图片裁剪失败", Toast.LENGTH_SHORT).show();
            }
        }
    }
    
--------

### GPS获取当前位置

AndroidManifest.xml文件添加以下权限.
    
    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="im.supai.supaimarketing" >
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    ...
    </manifest>

类必须继承LocationListener接口并重写相关函数.

    public class NewStoreActivity extends BaseAccivity implements LocationListener {
        
        //位置相关
        @Override
        public void onLocationChanged(Location location) {
            latitude = location.getLatitude();
            longitude = location.getLongitude();
        }

        @Override
        public void onStatusChanged(String s, int i, Bundle bundle) {
        }

        @Override
        public void onProviderEnabled(String s) {
        }

        @Override
        public void onProviderDisabled(String s) {
        }
    }

在Activity类中需要获取位置的地方新建一个LocationManager并发送请求即可.

    LocationManager locationManager = (LocationManager)getSystemService(Context.LOCATION_SERVICE);
    locationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, 0, 0, (android.location.LocationListener) this);

--------

### ImageView/ImageButton 设置缩放

设置自适应调整大小和最大宽度.

    itemBtn.setAdjustViewBounds(true);
    itemBtn.setMaxWidth(100);

--------

### 根据权重设置控件宽度

    把view的宽度设置为0dp同时设置weight属性.
    
    android:layout_width="0dp"
    android:layout_weight="1"

--------

### AutoCompleteTextView的使用方法

AutoCompleteTextView可以为输入框提供补全提示功能

在layout中定义widget

    <AutoCompleteTextView
    android:id="@+id/search_bar" />

activity中实现

    private AutoCompleteTextView searchEditText;
    searchEditText = (AutoCompleteTextView) findViewById(R.id.search_bar);

    String[] ary = new String[]{"aaa", "bbb"};  //定义候选词列表,可以使用字符串数组或者ArrayList

    //定义adaper
    ArrayAdapter<String> searchEditAdapter=new ArrayAdapter<String>(this, 
        android.R.layout.simple_dropdown_item_1line, context.searchList);
    searchEditText.setAdapter(searchEditAdapter);

--------

### ASyncTask相关

Android多线程编程主要使用的方法    
创建Thread,Handler Looper机制通信和使用异步框架ASyncTask    

Android 原生的 AsyncTask.java 是对线程池的一个封装，使用其自定义的 Executor 来调度线程的执行方式（并发还是串行），并使用 Handler 来完成子线程和主线程数据的共享。

ASyncTask需要继承父类并定义三个泛型类型

    private class MyTask extends AsyncTask<Void, Void, Void> { ... }

* Params  传入的参数,这里是可变长参数
* Progress    处理过程中的进度信息
* Result  返回的结果信息

ASyncTask需要重写三个方法    
* onPreExecute() 该方法将在执行实际的后台操作前被UI thread调用。可以在该方法中做一些准备工作，如在界面上显示一个进度条。
* doInBackground(Params...), 将在onPreExecute 方法执行后马上执行，该方法运行在后台线程中。这里将主要负责执行那些很耗时的后台计算工作。可以调用 publishProgress方法来更新实时的任务进度。此方法中不能进行修改UI操作
* onProgressUpdate(Progress...) UI thread将调用这个方法从而在界面上展示任务的进展情况，例如通过一个进度条进行展示。
* onPostExecute(Result), 在doInBackground 执行完成后，onPostExecute 方法将被UI thread调用，后台的计算结果将通过该方法传递到UI thread.

ASyncTask必须在UI线程中创建,execute方法必须在UI thread中调用,不要手动的调用onPreExecute(), onPostExecute(Result)，doInBackground(Params...), onProgressUpdate(Progress...)这几个方法

ASyncTask的缺点: 后台线程只有一个,多个任务线性执行

参考    
https://segmentfault.com/a/1190000002872278    
http://www.infoq.com/cn/articles/android-asynctask    

--------

### Android ToolBar使用

ToolBar是Android 5.0（API Level 21）之后用来取代ActionBar的

1.AndroidMenifest.xml 中修改theme为NoActionBar

    android:theme="@style/AppTheme.NoActionBar"

2.activity.xml中添加ToolBar

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme.AppBarOverlay">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:popupTheme="@style/AppTheme.PopupOverlay" >

            <!--自定义控件-->
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:text="自定义控件" />

        </android.support.v7.widget.Toolbar>
    </android.support.design.widget.AppBarLayout>

Toolbar中可以添加自定义控件

3.activity.java

    public class MainActivity extends AppCompatActivity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            //定义和初始化ToolBar
            Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
            setSupportActionBar(toolbar);

            toolbar.setNavigationIcon(R.mipmap.ic_launcher);//设置导航栏图标
            toolbar.setLogo(R.mipmap.ic_launcher);//设置app logo
            toolbar.setTitle("Title");//设置主标题
            toolbar.setSubtitle("Subtitle");//设置子标题

        }

        //设置菜单
        @Override
        public boolean onCreateOptionsMenu(Menu menu) {
            // Inflate the menu; this adds items to the action bar if it is present.
            getMenuInflater().inflate(R.menu.main, menu);
            return true;
        }

        @Override
        public boolean onOptionsItemSelected(MenuItem item) {
            switch (item.getItemId()) {
                case R.id.menu_item1:
                    Toast.makeText(MainActivity.this , "Menu Item 1 Clicked", Toast.LENGTH_SHORT).show();
                    break;
            }

            return super.onOptionsItemSelected(item);
        }
    }

--------

### Android 手势相关

首先定义GestureDetector

    GestureDetector mGestureDetector = new GestureDetector(this, new GestureDetector.OnGestureListener() {
        @Override
        //当手指按下的时候触发下面的方法
        public boolean onDown(MotionEvent e) {
            Toast.makeText(MainActivity.this, "Press Down", Toast.LENGTH_SHORT).show();
            return true;
        }

        @Override
        //当用户手指在屏幕上按下,而且还未移动和松开的时候触发这个方法
        public void onShowPress(MotionEvent e) {

        }

        @Override
        //当手指在屏幕上轻轻点击的时候触发下面的方法
        public boolean onSingleTapUp(MotionEvent e) {
            return false;
        }

        @Override
        //当手指在屏幕上滚动的时候触发这个方法
        public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
            return false;
        }

        @Override
        //当用户手指在屏幕上长按的时候触发下面的方法
        public void onLongPress(MotionEvent e) {
            Toast.makeText(MainActivity.this, "Long pressed", Toast.LENGTH_SHORT).show();
        }

        @Override
        //当用户的手指在触摸屏上拖过的时候触发下面的方法,velocityX代表横向上的速度,velocityY代表纵向上的速度
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            return false;
        }
    });

组件应用touch事件, 将touch事件交给gesture处理

    button = (Button) findViewById(R.id.test_btn);
    button.setOnTouchListener(new View.OnTouchListener() {
        @Override
        public boolean onTouch(View v, MotionEvent event) {
            mGestureDetector.onTouchEvent(event);
            return true;
        }
    });

