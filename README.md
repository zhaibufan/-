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


