# -
开发中遇到的零碎知识点

1. enqueue和execute的问题

    再用retrofit时遇到了这两个方法，其实这是okhttp中的方法，因为retrofit其实只是对于okHttp请求的一层封装，网络请求还是okHttp.
    execute的方法是同步方法，enqueue的方法是异步方法。
    
2.Retrofit中addConverterFactory和addCallAdapterFactory区别

    Call<String> login( @Body User ueser);
    Observable<String> login(@Body User ueser);
    从上面可以看到，Retrofit接口的返回值分为两部分，一部分是前面的Call或者Observable，另一部分是String(或其他JsonObject、具体的Bean对象等)
    addCallAdapterFactory影响的就是第一部分的Call或者Observable，Call类型是默认支持的(内部由DefaultCallAdapterFactory支持)，而如果要支持Observable，我们就需要自己添加：  addCallAdapterFactory(RxJavaCallAdapterFactory.create())
    addConverterFactory影响的就是第二部分以及我们的请求参数，如上面的User
    
3.今日头条适配相关知识点

    android中的dp在渲染前会将dp转为px，计算公式：
        px = density * dp;
        density = dpi / 160;
        px = dp * (dpi / 160);
    而dpi是根据屏幕真实的分辨率和尺寸来计算的，每个设备都可能不一样的。
    
    屏幕尺寸、分辨率、像素密度三者关系
        dpi = 根号下height平方+width平方 / 屏幕尺寸
        举个例子：屏幕分辨率为：1920*1080，屏幕尺寸为5吋的话，那么dpi为440。
        说明：dpi就是像素密度  1920:高(px) 1080：宽(px)  
        
4.git拉取远程分支代码

    git fetch origin 远程分支名x:本地分支名x
    然后在checkout到改分支
    
5.禁止软键盘将底部view顶上去

    android:windowSoftInputMode="adjustPan|stateHidden"
    
6.java序列化和反序列化

        序列化：将对象转化成字节序列
        反序列化：将对象转化成对象
        序列化主要的用途：
            1.用于对象长久保存到本地，因为对象是保存在内存中的，所以需要将其转化为字节才能保存到本地或者硬盘
            2.用于对象的传递，比如网络间进程间或者Intent传递数据。因为网络通信时一般都是以二进制的字节在网络上进行传输
        android中序列化api:(Serializable和Parcelable)
           1.java.io.ObjectOutputStream代表对象输出流，它的writeObject(Object obj)方法可对参数指定的obj对象进行序列化，把得到的字节序列写到一个目标输出流中。
          2.java.io.ObjectInputStream代表对象输入流，它的readObject()方法从一个源输入流中读取字节序列，再把它们反序列化为一个对象，并将其返回。
        选择序列化方法的原则：
            1.在使用内存的时候，Parcelable比Serializable性能高，所以推荐使用Parcelable。
            2.Serializable在序列化的时候会产生大量的临时变量，从而引起频繁的GC
            3.Parcelable不能使用在要将数据存储在磁盘上的情况，因为Parcelable不能很好的保证数据的持续性在外界有变化的情况下。尽管Serializable效率低点，但此时还是建议使用Serializable 
        实现Parcelable步骤：
            1.implements Parcelable
            2.重写writeToParcel方法，将你的对象序列化为一个Parcel对象，即：将类的数据写入外部提供的Parcel中，打包需要传递的数据到Parcel容器保存，以便从 Parcel容器获取数据
            3.重写describeContents方法，内容接口描述，默认返回0就可以
            4.实例化静态内部对象CREATOR实现接口Parcelable.Creator:public static final Parcelable.Creator<T> CREATOR
            注：其中public static final一个都不能少，内部对象CREATOR的名称也不能改变，必须全部大写。需重写本接口中的两个方法：createFromParcel(Parcel in) 实现从Parcel容器中读取传递数据值，封装成Parcelable对象返回逻辑层，newArray(int size) 创建一个类型为T，长度为size的数组，仅一句话即可（return new T[size]），供外部类反序列化本类数组使用。
            简而言之：通过writeToParcel将你的对象映射成Parcel对象，再通过createFromParcel将Parcel对象映射成你的对象。也可以将Parcel看成是一个流，通过writeToParcel把对象写到流里面，在通过createFromParcel从流里读取对象，只不过这个过程需要你来实现，因此写的顺序和读的顺序必须一致。
            
7.最全面总结 Android WebView与 JS 的交互方式

    https://www.jianshu.com/p/345f4d8a5cfa

8.MVP

    步骤1：UI实现View方法，引用Presenter
    步骤2：Presenter调用Model，走Model具体逻辑
    步骤3：Model逻辑实现，回调Presenter方法
    步骤4：Presenter回调View，即回到UI，回调View方法
    
9.OkHttp实现缓存的策略

    https://blog.csdn.net/qq_30553235/article/details/79282113
    https://blog.csdn.net/u010256388/article/details/68491509/
    https://blog.csdn.net/wangkeke1860/article/details/52084869
    https://www.jianshu.com/p/dbda0bb8d541
    https://github.com/yale8848/RetrofitCache(注：如果想实现有网络时不走缓存就不要添加addNetworkInterceptor(new CacheInterceptorOnNet())拦截器)
