Component
-------

这个组件化方案的设计尽可能的贴近系统原生,不会扩展出系统不支持的技术<br/>
参考了 `ARouter`,`WMRouter`,`ActivityRouter`,`CC`,`DDComponent`等开源的组件化框架
结合了其中的一些优点并且扩展了几个很好用的特性：

- 跳转
    - 自定义跳转(这样子就支持了第三方和系统的界面)
    - 自定义 `Intent`
- 目标界面的参数注入
- 无缝对接 `H5`
- 配套的 `Idea Plugin` 方便快速浏览
- 对 `Activity` 和目标 `Activity` **0** 配置实现 `ActivityResult` 的获取
    - 支持 `Dialog` `Context`, 因为 `dialog` 实际上是附着在 `Activity` 上的
    - 不支持系统 `Service` `Context`, 因为 `Android` 原生就不支持
    - 不支持 `Application` `Context`, 因为 `Android` 原生就不支持
- 路由取消
    - 可以手动用代码取消
    - 路由自动取消,当发起路由的 `Fragment` 或者 `Activity` 销毁的时候会取消
- 路由拦截器
    - 主线程中执行,允许你进行 `UI` 操作
    - Callback机制,可以做任何事情
    - 提供了三种拦截器(具体看wiki)
        - 自定义拦截器
        - 页面拦截器
        - 全局拦截器
- 服务发现和路由分开设计
    - 其实这两块本来就是两个方面,我不清楚为什么很多方案中都柔和在一块
- Rx扩展库扩展完美结合了 RxJava2

**了解更多请看 [wiki](https://github.com/xiaojinzi123/Component/wiki/) 更多功能等你来发现**

```
@RouterApiAnno()  // 标记这个接口,注解处理器会生成实现类
@HostAnno("user") // 所有接口的默认 host 为 'user'
public interface Api {
    
    @HostAnno("user") // 你可以重新为该方法定义 host
    @PathAnno("login")
    // @HostAndPathAnno("user/login") // 你也可以使用 @HostAndPathAnno 去代替 @HostAnno 和 @PathAnno
    // @HostAndPathAnno 和 (@HostAnno & @PathAnno) 不能同时存在
    void toLoginView(Context context);
    
    // Callback 允许你接受到本次路由完成(成功、取消、出错)的回调
    @UseInterceptorAnno(names={"user.login"}) // 使用了一个登录的拦截器,跳转会自动完成登录整个过程
    @PathAnno("personCenter")
    void toPersonCenterView(Context context,Callback callback); // 去个人中心
    
    // 当你路由有回调的返回值的时候,需要用 BiCallback 并且泛型中填写需要的参数类型
    // 返回值你可以写 void 不关心返回值的类型,你也可以写 NavigationDisposable 拿到一个可取消的对象
    // forResult = true --> BiCallback<ActivityResult>
    // forResult = true,resultCodeMatch = <需要匹配的resultCode> --> BiCallback<ActivityResult>
    // forIntent = true --> BiCallback<Intent>
    // forResultCode = true --> BiCallback<Integer>
    // resultCodeMatch = <需要匹配的resultCode> --> Callback
    @NavigateAnno(forIntent = true)
    @PathAnno("address")
    @RequestCodeAnno() // value 不写表示系统自动生成一个可用的
    NavigationDisposable toAddressView(Context context, BiCallback<Intent> callback); // 去地址选择界面
    
    // 你可以不使用 NavigateAnno 注解而返回一个 Call 对象,这样子你可以拿到 Call 对象决定任何时候去路由以及路由的方式
    // @NavigateAnno 注解和 Call 返回值是冲突的,不可以同时存在!
    @PathAnno("userInfo")
    Call toUserInfoView(Context context)
    
}
```

<img src="imgs/rxGetData.png" width="640px" height="360px"/>

<img src="imgs/componentDesc.gif" width="250px" height="400px"/>

<img src="imgs/fieldInject.png" width="600px" height="360px"/>

RouterGoPlugin
-------

[一个帮助您识别路由和拦截器的库,减少您查找的时间,配合组件化使用](https://github.com/xiaojinzi123/RouterGoPlugin)

如何使用见wiki 
-------

[wiki使用介绍](https://github.com/xiaojinzi123/Component/wiki/)

- [名词解释](https://github.com/xiaojinzi123/Component/wiki/%E5%90%8D%E8%AF%8D%E8%A7%A3%E9%87%8A)
- [基本介绍](https://github.com/xiaojinzi123/Component/wiki/%E5%9F%BA%E6%9C%AC%E4%BB%8B%E7%BB%8D%E5%92%8C%E6%9E%B6%E6%9E%84%E4%BB%8B%E7%BB%8D)
- [依赖和配置](https://github.com/xiaojinzi123/Component/wiki/%E4%BE%9D%E8%B5%96%E5%92%8C%E9%85%8D%E7%BD%AE)
- [跳转](https://github.com/xiaojinzi123/Component/wiki/%E8%B7%B3%E8%BD%AC)
- [跳转目标注解标记](https://github.com/xiaojinzi123/Component/wiki/RouterAnno%E6%B3%A8%E8%A7%A3%E6%A0%87%E8%AE%B0%E7%9B%AE%E6%A0%87%E7%9A%84%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95)
- [拦截器](https://github.com/xiaojinzi123/Component/wiki/%E6%8B%A6%E6%88%AA%E5%99%A8)
- [服务发现实现跨模块调用](https://github.com/xiaojinzi123/Component/wiki/%E8%B7%A8%E6%A8%A1%E5%9D%97%E8%B0%83%E7%94%A8%E7%9A%84%E6%9C%8D%E5%8A%A1%E5%8F%91%E7%8E%B0%E5%8A%9F%E8%83%BD)

为了更好的能交流,这里新建了一个QQ群：870981195
或者扫描二维码来进群

<div>
<img src="imgs/qq_group1.JPG" width="260px" height="360px" />
<img src="imgs/qq_group2.JPG" width="260px" height="360px" />
</div>

License
-------

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
