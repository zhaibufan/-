# -
开发中遇到的零碎知识点
1. enqueue和execute的问题
    再用retrofit时遇到了这两个方法，其实这是okhttp中的方法，因为retrofit其实只是对于okHttp请求的一层封装，网络请求还是okHttp.
    execute的方法是同步方法，enqueue的方法是异步方法，
    
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


