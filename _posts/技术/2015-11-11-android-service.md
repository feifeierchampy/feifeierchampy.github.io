---
layout: post
title: Android Service学习记录
category: 技术
tags: Android
keywords: 
description: 
---

#Android Service
Service与Activity相似，它们都是从 `Context` 派生出来的，因此它们都可以调用 `Context` 里定义的 `getResources()` 、`getContentResolver()` 等方法。
Activity与Service的选择标准是：如果某个程序组件需要在运行时向用户呈现某种界面，或者该程序需要与用户交互，就需要使用Activity，否则就应该考虑使用Service了。

``` java
public class BindService extends Service
{

    private int count;
    private boolean quit;

	//定义 onBinder方法所返回的对象
    private MyBinder binder = new MyBinder();

    //通过继承Binder来实现IBinder类
    public class MyBinder extends Binder
    {
        public int getCount()
        {
            //获取service的运行状态：count
            return count;
        }
    }


    /**
     * 该方法时Service子类必须实现的方法。
     * 该方法返回一个IBinder对象，应用程序可以通过该对象与Service组件通信。
     * 该对象将被传给Service的访问者
     */
    @Override
    public IBinder onBind(Intent intent)
    {
        // TODO: Return the communication channel to the service.
        System.out.println("Service is Binded");
        
        //返回IBuilder对象
        return binder;
    }

    /**
     * 当该Service第一次被创建后将立即回到该方法
     */
    @Override
    public void onCreate()
    {
        super.onCreate();
        System.out.println("Service is Created");

        //启动一条线程，动态地修改count状态值
        new Thread()
        {
            @Override
            public void run()
            {
                while(!quit)
                {
                    try
                    {
                        Thread.sleep(1000);
                    }
                    catch(InterruptedException e)
                    {

                    }
                    count++;
                }
            }
        }.start();
    }

    /**
     * 该方法的早期版本是void onStart(Intent intent, int startId),
     * 每次客户端调用startService(Intent)方法启动该Service时都会回调该方法
     * 可在此增加Service的相关业务代码
     */
    @Override
    public int onStartCommand(Intent intent, int flags, int startId)
    {
        System.out.println("Service is Started");
        return START_STICKY;
    }

    /**
     * 当该Service上绑定的所有客户端都断开连接时将会回调该方法
     */
    @Override
    public boolean onUnbind(Intent intent)
    {
        System.out.println("Service is Unbinded");
        return true;
    }

    /**
     * 当该Service被关闭之前将会回调该方法
     */
    @Override
    public void onDestroy()
    {
        super.onDestroy();
        this.quit = true;
        System.out.println("Service is Destroyed");
    }
}

```
##配置Service

``` xml
<service android:name=".BindService">
	<intent-filter>
		<action android:name="hello"/>
    </intent-filter>
</service>
```
需放在 `<application> </application>` 内
第一个name代表该service的位置，包括完成的包名和service名，如果报名就是你定义的程序包名，也就是和gen目录下那个包的名字一样的话，直接“.service名”就可以了。第二个name是你调用service时 `intent.setAction();` 中的参数，这个可随便定义。

##启动和停止Service
- 通过 `Context` 的 `startService()` 方法：通过该方法启动Service，访问者与Service之间没有关联， 即使访问者退出了，Service仍然运行。 停止时调用 `stopService()` 方法。该方法Service与访问者不存在太多的关联，因此Service和访问者之间也无法进行通信、数据交换。
- 通过 `Context` 的 `bindService()` 方法：使用该方法启用Service，访问者与Service绑定在了一起，访问者一旦退出，Service也就终止。停止时调用 `unbindService()` 方法。

`Context`的 `bindService()`方法的完整方法签名为：
`bindService(Intent service, ServiceConnection conn, int flags)`三个参数代表了：  
- **service** :该参数通过Intent指定要启动的Service。  
- **conn** :该参数是一个ServiceConnection对象，该对象用于监听访问者与Service之间的连接情况。当连接成功时调用该`ServiceConnection`对象的`onServiceConnected(ComponentName name, IBinder service)`方法，该IBinder对象即可实现与被绑定Service之间的通信；当Service所在的宿主进程由于异常中止或其他原因终止导致该Service与访问者之间断开连接时回调该`ServiceConnection`对象的`onServiceDisconnectd(ComponentName name)`方法。  
- **flags** :指定绑定时是否自动创建Service（如果Service还未创建）。该参数可指定为0（不自动创建）或`BIND_AUTO_CREATE`自动创建。


``` java
public class MainActivity extends Activity {

    Button bind, unbind, getServiceStatus;
    BindService.MyBinder binder;

    private ServiceConnection conn = new ServiceConnection()
    {
        //当该activity与service连接成功时回调该方法
        @Override
        public void onServiceConnected(ComponentName name, IBinder service)
        {
            System.out.println("--Service Connected--");

            binder = (BindService.MyBinder)service;
        }

        @Override
        public void onServiceDisconnected(ComponentName name)
        {
            System.out.println("--Service Disconnected");
        }
    };

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        bind = (Button)findViewById(R.id.bind);
        unbind = (Button)findViewById(R.id.unbind);
        getServiceStatus = (Button)findViewById(R.id.getServiceStatus);

        final Intent intent = new Intent(MainActivity.this, BindService.class);

        //intent.setAction("hello");

        bind.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //绑定指定Service
                bindService(intent, conn, Service.BIND_AUTO_CREATE);
            }
        });

        unbind.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //解除绑定
                unbindService(conn);
            }
        });

        getServiceStatus.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //获取、并显示Service的count值
                Toast.makeText(MainActivity.this, "Service的count值为：" + binder.getCount(), Toast.LENGTH_SHORT).show();
            }
        });

    }

}
```




