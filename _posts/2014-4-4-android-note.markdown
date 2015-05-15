---
layout: post
title:  "Android学习笔记"
date:   2015-04-04
categories: android
---

####HttpClient

获取界面组件

	<Button
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:text="New Button"
	android:id="@+id/btn1" />

	Button btn1 = findViewById(R.id.btn1);

activity跳转

	Intent intent = new Intent();
	intent.setClass(activity, MainActivity.class);
	startActivity(intent);
	finish();

输出log日志

	import android.util.Log;
	Log.v(“log title”, “value");

SharedReference

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

Fragment (Android4 以上)

Fragment类定义
HelloFragment.java

public class HelloFragment extends Fragment

自定义fragment类继承Fragment

包含以下方法

#### Activity跳转

	Intent intent = new Intent();
	intent.setClass(MyOrderActivity.this, OrderDetailActivity.class);
	startActivity(intent);

#### 跳转传递参数

方法1
传递方

	Intent intent = new Intent();
	intent.setClass(MyOrderActivity.this, OrderDetailActivity.class);
	intent.putExtra("id", id);
	startActivity(intent);

接收方

	int id = this.getIntent().getIntExtra("id", 0);

方法2, 用bundle

传递方

	//新建一个显式意图，第一个参数为当前Activity类对象，第二个参数为你要打开的Activity类
    Intent intent =new Intent(MainActivity.this,MainActivity2.class);
    
    //用Bundle携带数据
    Bundle bundle=new Bundle();
    //传递name参数为tinyphp
    bundle.putString("name", "tinyphp");
    intent.putExtras(bundle);
    
    startActivity(intent);

接收方

	//新页面接收数据
    Bundle bundle = this.getIntent().getExtras();
    //接收name值
    String name = bundle.getString("name");
    Log.i("获取到的name值为",name);

#### ListView自定义布局

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

####http连接库httpClient

	简单实例

	CloseableHttpClient httpclient = HttpClients.createDefault();
	HttpGet httpget = new HttpGet("http://localhost/");
	CloseableHttpResponse response = httpclient.execute(httpget);
	try {
	    <...>
	} finally {
		
	}

#### Async task

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

#### Application 全局类

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

#### Handler和Message

从网络上下载图片的示例.

AndroidManifest.xml添加网络访问权限

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

导入Hanler和Message类.

    import android.os.Handler;
    import android.os.Message;

Activity类和OnCreate方法.

	private String imgUrl = "http://imgsrc.baidu.com/forum/w%3D580/sign=c7e8beb832d12f2ece05ae687fc2d5ff/d017b051f81986188a7e30eb4eed2e738bd4e6b5.jpg";
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

#### Looper对象

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

#### 查看SHA1签名

(Mac OS)
    
    keytool -list -v -keystore ~/.android/debug.keystore

口令没有就直接enter

#### Spinner选择器

有dialog和dropdown两种显示方式.
需要使用适配器来完成填充数据.

    ArrayList<String> list= new ArrayList<String>();
    list.add("aaaa");
    list.add("bbbb");

    ArrayAdapter<String> adapter = new ArrayAdapter<String>(RegisterActivity.this, android.R.layout.simple_spinner_item, list);
    provinceSpinner.setAdapter(adapter);

#### 自定义选项布局

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

使用SimpleAddpter

    List listData = new ArrayList<Map<String,Object>>();

    Map line = new HashMap();
    line.put("id", 1);
    line.put("name", "beijing");
    listData.add(line);

    SimpleAdapter adapter = new SimpleAdapter(this, listData, R.layout.spinner_item, new String[] {"name"}, new int[] {R.id.name});
    provinceSpinner.setAdapter(adapter);

SimpleAdapter构造函数的参数分别为

1. 上下文,一般为Activity类
2. 列表数据, 类型是List,每个元素是Map, 每个Map对象包含key,value键值对表示属性名和属性值.
3. 布局xml文件.
4. 字符串数组, 表示数据中key的属性名.
5. int数组, 数据中每个key属性名所对应的样式xml中的组件id.

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

#### AVD 模拟器添加sd卡

使用mksdcard程序,位置如下(MacOS系统)

    /Users/viator42/Library/Android/sdk/tools/mksdcard

命令格式 label:标签  size:大小 file:文件位置

    mksdcard -l <label> <size> <file>

#### 获取相册图片

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

#### GPS获取当前位置

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

#### ImageView/ImageButton 设置缩放

设置自适应调整大小和最大宽度.

    itemBtn.setAdjustViewBounds(true);
    itemBtn.setMaxWidth(100);

### 根据权重设置控件宽度

    把view的宽度设置为0dp同时设置weight属性.
    
    android:layout_width="0dp"
    android:layout_weight="1"

