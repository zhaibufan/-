# 零碎知识点
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
   
10.Glide的Target对象的使用

    https://blog.csdn.net/qq_18242391/article/details/72832538
    
11.Retrofit的get方法和post方法注解的使用

    get方法：
        1.@Query和@QueryMap:用于 @GET 方法的查询参数（Query = Url 中 ‘?’ 后面的 key-value）
        2.@Path:URL地址的缺省值
    post方法：
        1.@Body：以 Post方式 传递自定义数据类型（比如javaBean对象）给服务器（通常是将javaBean对象转化为json传递为服务器）
                @POST("/wisdomWater/create")
                Call<JsonObject> addWisdomWater(@Body RequestBody body);
        2.@Field & @FieldMap
                作用：发送 Post请求 时提交请求的表单字段
                具体使用：与 @FormUrlEncoded 注解配合使用
                @POST("/form")
                @FormUrlEncoded
                Call<ResponseBody> testFormUrlEncoded1(@Field("username") String name, @Field("age") int age);
                
                @POST("/form")
                @FormUrlEncoded
                Call<ResponseBody> testFormUrlEncoded2(@FieldMap Map<String, Object> map);
        3.@Part & @PartMap
                作用：发送 Post请求 时提交请求的表单字段
                与@Field的区别：功能相同，但携带的参数类型更加丰富，包括数据流，所以适用于 有文件上传 的场景
                具体使用：与 @Multipart 注解配合使用
                @POST("/form")
                @Multipart
                Call<ResponseBody> testFileUpload1(@Part("name") RequestBody name, @Part("age") RequestBody age, @Part MultipartBody.Part file);
                
                @POST("/form")
                @Multipart
                Call<ResponseBody> testFileUpload2(@PartMap Map<String, RequestBody> args, @Part MultipartBody.Part file);
                
    3.@Header & @Headers:添加请求头 &添加不固定的请求头
    
    详见：https://www.jianshu.com/p/a3e162261ab6
    
4.ViewPager的PageTransformer属性

    1.clipChildren属性:表示是否限制子控件在该容器所在的范围内，clipChildren属性配合layout_gravity属性，可以用来设置多余部分的显示位置
    2.一个页面显示多个ViewPager的Item
    3.初识PagerTransformer
    4.进一步了解PagerTransformer
    5.ViewPager结合CardView
    https://blog.csdn.net/u012702547/article/details/52334161
    
5.自定义view的一些知识点

    1.canvas.drawCircle()：绘制实心圆、圆环，取决于Paint
    2.canvas.drawArc():绘制圆弧(同理也包括实心和非实心)。一般第一个参数是RectF对象，该对象指的是圆弧对应的矩形的大小
    3.view的渐变效果：Paint.setShader(new Shader());
        new SweepGradient(width/2, height/2, processColorStart, processColorEnd) 扫描式渐变,一般用于弧形或圆形
        new LinearGradient(0, 0, getWidth(), getHeight(), startColor, endColor, Shader.TileMode.MIRROR); 线性渐变
    4.绘制图片
        1.canvas.drawBitmap();
        2.Bitmap bitmap = Bitmap.createBitmap();
          BitmapShader circleImgBitmapShader = new BitmapShader(bitmap, ...)
          paint.setShader(circleImgBitmapShader);
          canvas.drawXXX(..., paint);
    5.矩阵Matrix：使用矩阵进行平移 缩放 旋转等操作
    
6.属性动画相关知识点

    1.Interpolator:是控制动画的完成进度变化快慢的
        加减速插值器AccelerateDecelerateInterpolator；
        线性插值器LinearInterpolator；
        加速插值器AccelerateInterpolator；
        减速插值器DecelerateInterpolator；
        弹跳效果插值器BounceInterpolator
    2.TypeEvaluator:改变动画最终的属性值，比如颜色 位置
        https://blog.csdn.net/l_wwbs/article/details/53388924
        
7.关于HTTP和HTTPS的知识点:

    一、网络层结构:
    
        网络结构有两种主流的分层方式：OSI七层模型和TCP/IP四层模型。
            七层模型:
                    应用层:应用程序
                    表示层:数据的表示，压缩和加密（数据格式化，代码转换，数据加密）
                    会话层:建立管理终止会话
                    传输层:提供端到端可靠报文段传递和错误恢复(TCP/UDP)
                    网络层:提供数据包从源到宿的传递和网际交互
                    链路层:将比特组装成帧和点到点传递
                    物理层:传输比特流，以二进制数据形式在物理媒体上传输数据
             TCP/IP是四层模型:没有真正的定义，只是概念性的描述
             
    二.HTTP协议:Http是基于TCP/IP协议的应用程序协议，不包括数据包的传输，主要规定了客户端和服务器的通信格式，默认使用80端口。
    三.区别:
        https协议需要到CA申请证书，大多数情况下需要一定费用
        Http是超文本传输协议，信息采用明文传输，Https则是具有安全性SSL加密传输协议
        Http和Https端口号不一样，Http是80端口，Https是443端口
        Http连接是无状态的，而Https采用Http+SSL构建可进行加密传输、身份认证的网络协议，更安全。
        Http协议建立连接的过程比Https协议快。因为Https除了Tcp三次握手，还要经过SSL握手。连接建立之后数据传输速度，二者无明显区别。
        
8.项目开发中途添加忽略文件

    由于项目开发中途才添加忽略文件，所以本该忽略的文件肯定已经提交到远程仓库。以下步骤可以删除提交的忽略文件并添加最新忽略文件
    git rm -r --cached .    //删除追踪状态 或者  git rm -r --cached file_path
    git add .  //采用上面第二种方法则忽略此步骤
    git commit -m "fixed untracked files"
    git pull origin master //此步骤合作开发时防止冲突的 亦可以git fetch 和git rebase代替
    git push origin master
    
    https://www.jianshu.com/p/34f446747a94
                    
                    
                    
        
    
          
        
    
   
        
