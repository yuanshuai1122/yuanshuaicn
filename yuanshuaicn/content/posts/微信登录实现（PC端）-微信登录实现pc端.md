---
title: 微信登录实现（PC端）
date: 2022-07-12 17:58:33.43
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- 微信
- OAuth2.0
---

# 微信登录实现（PC端）

### 中心思想：

`通过微信扫码和微信交互，最终拿到openid（相当于数据库主键id，是微信用户唯一标识），然后通过openid和业务交互。`

### 具体实现：

一共4个步骤，其实不论是微信授权登录，还是QQ授权登录,或者支付宝授权登录.....等只要是OAuth2.0协议都是这逻辑

>1 第一步：用户同意授权，获取code
>
>2 第二步：通过code换取网页授权access_token
>
>3 第三步：刷新access_token（如果需要）
>
>4 第四步：拉取用户信息(需scope为 snsapi_userinfo)

### 准备工作：

#### 1、注册账号

`https://open.weixin.qq.com` 注册微信开发平台

#### 2、开发者资质认证

准备营业执照，1-2个工作日审批，300元

#### 3、创建网站应用

提交审核，七个工作日审批

#### 4、开发流程

`https://developers.weixin.qq.com/doc/oplatform/Website_App/WeChat_Login/Wechat_Login.html`

>1. 第三方发起微信授权登录请求，微信用户允许授权第三方应用后，微信会拉起应用或重定向到第三方网站，并且带上授权临时票据 code 参数；
>2. 通过 code 参数加上 AppID 和AppSecret等，通过 API 换取access_token；
>3. 通过access_token进行接口调用，获取用户基本数据资源或帮助用户实现基本操作。

获取access_token时序图：

<img src="https://hexobbblog.oss-cn-beijing.aliyuncs.com/work/1.png?versionId=CAEQKBiBgIDDwPHXjxgiIDRlMDM2ZjA4MWYwNjQ0Yjk5NWRlNTk5NjY1NWRkYjdh" alt="1" style="zoom:75%;" />

### 前端微信登录二维码展示：

以`vue.js`为例：

需要在`loginApi.js`中配置接口，检查是否登录。

```javascript
checkLogin() {
    return request({
        url: `/user/wx/checklogin`,
        method: 'get',
    })
}
```

在`login.vue`中添加属性

```vue
// 需要在页面head中先引入如下 JS 文件（支持https）：
head: {
    script: [
    {src: 'http://res.wx.qq.com/connect/zh_CN/htmledition/js/wxLogin.js'}
    ]
},
// 定义对话框开启关闭的boolean类型
data() {
	return {
		wxDialog: false
}
}
```

添加微信登录对话框

```vue
// @opened 对话框打开后的回调
<el-dialog
    :visible.sync="wxDialog"
    @opened="wxOpen"
    width="30%",
    center
>
    <div id="qrCode" style="padding-left: 110px;"></div>
</el-dialog>
```

在微信图标添加点击事件

```vue
<a id="weixin" class="weixin" target="_blank" @click="wxDialog=true">
    <i class="iconfont icon-weixin"/>
</a>
```

在`methods`中添加弹出二维码方法，这里用到了之前申请的`appid`，`redirect_uri`等，注意了解下`scope`

```vue
methods: {
	wxCreate() {
		 var obj = new WxLogin({
         self_redirect:true,
         id:"qrCode", 
         appid: "", 
         scope: "", 
         redirect_uri: "",
         state: "",
         style: "black",
         href: ""
         });
},
	wxOpen() {
    // 生成登录二维码
    this.wxCreate();
    // 验证是否已经扫码
    loginApi.checkLogin().then(response => {
        if (response.success) {
            // 携带token跳转到到首页
            this.$router.push( path:'/',query:{token:response.data.token}})
        }else {
            this.$message({
                type: 'error',
                message: '登录失败，请重试'
            });
            this.wxOpen();
        }
    })
}
}
```

`前端的总体思路就是：点击微信icon打开对话框，此时不断检查是否登录，如果已经登录，跳转首页，登陆成功，未登录，等待扫码，扫码后回调`

### 后端验证用户是否扫码成功

`总体思路为：再死循环中不断检查isLogin标志位是否为TRUE`

```java
@RestController
@RequestMapping("/user")
public class WxLoginController {

    final String app_id = "wx92b66fgfg8c01fc87";
    final String app_secret = "d734ba63fgfggb3b573d7cb1cdcb958eea";
    final String redirect_url = "http://wwwxxxx.com/wechat/callBack";

    /**
     * 记录当前用户是否已经登录
     */
    private boolean isLogin;

    /**
     * 记录当前用户是否扫码登录失败
     */
    private boolean isLoginFail;
    

    /**
     * 检查是否登录
     * @return 是否登录
     */
    @GetMapping("/wx/checklogin")
    public ResponseEntity<Object> checkLogin() {
        ResponseEntity<Object> responseEntity;
        // 定义flag
        int flag = 1;
        // 一直查询是否登录
        while (true) {
            // 检查flag是否超过120 即60s
            if (flag > 120) {
                responseEntity = new ResponseEntity<>("登录超时",HttpStatus.INTERNAL_SERVER_ERROR);
                break;
            }
            // 登陆成功
            if (isLogin) {
                // 如果已经登录，返回用户token
                responseEntity = new ResponseEntity<>(token,HttpStatus.OK);
                // 登录状态复位
                isLogin = false;
                break;
            }
            // 登录失败
            if (isLoginFail) {
                responseEntity = new ResponseEntity<>("登录失败",HttpStatus.INTERNAL_SERVER_ERROR);
                break;
            }
            // 每隔0.5s查询一次状态
            try {
                Thread.sleep(500);
            }catch (Exception e) {
                e.printStackTrace();
            }
            flag++;
        }
        return responseEntity;
    }
}
```

### 微信登录后端回调业务

```java
    final String app_id = "wx92b66fgfg8c01fc87";
    final String app_secret = "d734ba63fgfggb3b573d7cb1cdcb958eea";
    final String redirect_url = "http://wwwxxxx.com/wechat/callBack";

    @GetMapping("/wx/callback")
    public ResponseEntity<Object> callback(String code, String state) {
        if ("fm".equals(state)) {
            try {
                // 1.换取access_token
                String getToken = "https://api.weixin.qq.com/sns/oauth2/access_token?" +
                        "appid=%s" +
                        "&secret=%s" +
                        "&code=%s" +
                        "&grant_type=authorization_code";
                // 拼接请求地址
                getToken = String.format(getToken,
                        app_id,
                        app_secret,
                        code);
                // 2.发送请求，换取access_token
                Map<String, Object> tokenMap = CommonUtils.doGet(getToken);
                Object rsStr = tokenMap.get("rsStr");
                Map resMap = (Map)rsStr;
                // 3.解析返回值,拿到access_token 和 open_id
                String accessToken = (String) resMap.get("access_token");
                String openid = (String) resMap.get("openid");
                JSONObject jsonObject = new JSONObject();
                jsonObject.put("accessToken",accessToken);
                jsonObject.put("openid",openid);
                // TODO 根据openid去换id
                // TODO 成功登录，返回token
                // 修改标志
                this.isLogin = true;
                // ......这里就可以和我们具体业务对接
                ......
                return new ResponseEntity<>(jsonObject,HttpStatus.OK);

            }catch (Exception e) {
                this.isLoginFail = true;
                e.printStackTrace();
                return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
            }
        }
        return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
    }
```

#### 这样就可以完成微信的扫码登录，qq微博同理，到此结束