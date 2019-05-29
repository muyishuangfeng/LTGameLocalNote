# LTGameLocalNote
### 支付流程说明

    为了保证支付的安全，sdk采用了Android支付平台（Google Play和OneStore）验证和LT服务器验证两种方式，业务流程图如下：

![TIM图片20190118111718.png](https://upload-images.jianshu.io/upload_images/1716569-d609964d9efb2856.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### GooglePlay 接入

(1)、客户端调用支付模块代码，由SDK向LT服务器发起一个订单请求；
(2)、拿到服务器给的订单ID后，SDK发起Google Play支付；
(3)、Google Play支付返回结果后，拿到Google Play支付成功的token信息；
(4)、SDK把Google play返回的订单号token等信息发给服务端，LT服务器向Google Play服务器校验；
(5)、服务端对Google play服务器响应数据做处理和校验订单的合法性；
(6)、通过服务器验证的结果来确定支付的结果；
(7)、支付成功回调 支付成功回调的使用方法： 需要CP服务器建立一个API供乐推服务调用 回调地址所接受的参数格式
~~~
 METHOD: POST 
 HEADER:Content-Type:application/json
~~~
 + BODY:
__1、初始化参数说明__ 

|参数|类型|是否必须|说明|
|:------:|:------:|:-----:|:------|
|context| Context|是|上下文|
|publicKey|String|是|Google Play 生成的公钥|
|productID|String|是|商品ID|
|goodsList|List|是|商品集合|
|OnGoogleInitListener|Interface|是|初始化接口Google回调接口|

__2、支付请求参数说明__

|参数|类型|是否必须|说明|
|:-------:|:-------:|:-------:|:-----:|
|context| Context|是|上下文|
|LT-AppID|String|是|每个应用对应的appid|
|LTAppKey|String|是|每个应用对应的appKey|
|gid|String|是|服务器配置的id|
|packageId|String|是|每个应用的包名|
|custom|Map<String,Object>|是|游戏上自定义数据，可以包含充值的区服等信息|
|requestCode|String|是|支付请求码|
|goodsList|List<String>|是|内购商品集合|
|productID|String|是|内购商品唯一ID|
|OnGooglePayResultListener|Interface|是|支付接口回调接口|


__3、支付结果回调参数说明__

|参数|类型|是否必须|说明|
|:-------:|:-------:|:-------:|:----:|
|context| Context|是|上下文|
|requestCode|int|是|对应onActivityResult方法中的requestCode|
|resultCode|int|是|对应onAcitivityResult方法中的resultCode|
|data|Intent|是|对应onActivityResult方法中的data|
|selfRequestCode|int|是|自定义的请求码和支付请求中的requestCode保持一致|
|LT-AppID|String|是|每个应用对应的appid|
|LTAppKey|String|是|每个应用对应的appKey|
|goodsList|List|是|商品集合|
|productID|String|是|商品ID|
|OnGoogleResultListener|Interface|是|支付结果回调接口|

#### Google Play支付简介

+ 1、ltgamegoogle jar包；
+ 2、GooglePlayManager 封装类；
+ 3、 回调接口：
   + 1、 OnGoogleInitListener Google Play 初始化接口

         /**
          * 初始化成功
          *
          * @param success 成功回调信息
          */
         void onGoogleInitSuccess(String success);
        
         /**
          * 初始化失败回调
          *
          * @param result 失败回调信息
          */
         void onGoogleInitFailed(String result);

 + 2、OnGooglePayResultListener 
 
     支付结果回调接口

      /**
       * 支付成功 
       *
       * @param result 成功信息
       */

        void onPaySuccess(String result); 


       /**
         * 支付失败
         *
         * @param result 失败信息 
         */

        void onPayFailed(Throwable ex);
        
      /**
        * 支付完成
        *
        */
      void onPayComplete();

      /**
       * 支付错误
       *
       * @param result 错误信息
       */
      void onPayError(String result);

+ 3、OnGoogleResultListener Google Play 服务器返回信息

      /**
       * Google play 返回信息（成功）
       * 
       * @param result 成功信息
       */ 
      void onResultSuccess(String result);

       /** 
        * 上传到服务器错误 
        *
        * @param ex 错误信息 
        */

      void onResultError(Throwable ex);
      /** 
       * Google play 返回信息（失败）
       * 
       * @param failedMsg 失败内容 
       */

       void onResultFailed(String failedMsg);

#### Google Play 支付接入（Android studio）

  + 1、接入前提 
    __手机必须支持Google 服务并且已经登录__

2、在项目的build下面引用仓库地址

     maven { url 'https://jitpack.io' }
![4](https://upload-images.jianshu.io/upload_images/1716569-84f44d0667d0283a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



  + 3、在所使用的moule的 app.build中添加项目引用

         implementation 'com.github.muyishuangfeng:LTGameLocalGooglePlay:1.0.1'




 + 4、在需要调用google play支付的Activity或者Fragment中调用 __初始化__、__支付和__支付回调__的方法

        1、初始化方法
           GooglePlayManager.init(this, "公钥", 商品ID, 商品集合, new OnGoogleInitListener() {
            @Override
            public void onGoogleInitSuccess(String success) {
                
            }

            @Override
            public void onGoogleInitFailed(String result) {
                
            }
        });
         2、支付方法
         
                 Map<String, Object> params = new WeakHashMap<>();
                 params.put("xx", "xx");
                 params.put("xx", "xx");
                //所有内购商品集合
                List<String> oldSkus = new ArrayList<>();
                oldSkus.add("xxx");
                oldSkus.add("xxx");
                oldSkus.add("xxx");
                GooglePlayManager.recharge(this, LTAppID, LTAppKey, gid, packageName,
                params, selfRequestCode, oldSkus, 商品ID,
                new OnGooglePlayResultListener() {
                    @Override
                    public void onPlaySuccess(String result) {
                        
                    }

                    @Override
                    public void onPlayFailed(Throwable ex) {
                        
                    }

                    @Override
                    public void onPlayComplete() {
                        
                    }

                    @Override
                    public void onPlayError(String result) {
                       
                    }
                });
          3、支付回调方法
            
            @Override
            protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            //所有内购商品集合
                List<String> oldSkus = new ArrayList<>();
                oldSkus.add("xxx");
                oldSkus.add("xxx");
                oldSkus.add("xxx");
            GooglePlayManager.onActivityResult(this, requestCode, resultCode, data, selfRequestCode,
                LTAppID, LTAppKey, oldSkus, productID, new OnGoogleResultListener() {
                    @Override
                    public void onResultSuccess(String result) {
                        
                    }

                    @Override
                    public void onResultError(Throwable ex) {
                        
                    }

                    @Override
                    public void onResultFailed(String failedMsg) {
                       
                    }
                });
        }

         4、释放资源
         
         @Override
         protected void onDestroy() {
         super.onDestroy();
         GooglePlayManager.release();
       }

 + 5 、Google play 配置参考
 [Google Play 配置参考](https://blog.csdn.net/alex_my/article/details/82984706#2__8)

 + 6、结果码（具体以接口回调结果为准）

       1、 init Success 初始化成功
       2、 Order creation failed 订单创建失败
       3、 Purchase successful 支付成功
       4、 Purchase Failed 支付失败
       5、 ltOrderID is null 乐推订单是空
       6、 200成功
    
----
****
 __分割线__
****
----



### Facebook登录接入说明
  + 1、配置

      1）、在项目的build下面引用仓库地址

         maven { url 'https://jitpack.io' }
![4](https://upload-images.jianshu.io/upload_images/1716569-84f44d0667d0283a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
       2）、在项目的app.build中引用facebook的网络包如下所示

     implementation 'com.github.muyishuangfeng:LTGameLocalFaceBook:1.0.1'

   3）、在项目的清单文件（Manifest）中配置
     
         <!-- facebook登录 -->
        <meta-data
            android:name="com.facebook.sdk.ApplicationId"
            android:value="@string/facebook_app_id" />
    
        <activity
            android:name="com.facebook.FacebookActivity"
            android:configChanges="keyboard|keyboardHidden|screenLayout|screenSize|orientation"
            android:label="@string/app_name" />
        <activity
            android:name="com.facebook.CustomTabActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
    
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
    
                <data android:scheme="@string/fb_login_protocol_scheme" />
            </intent-filter>
        </activity>

__注意:facebook_app_id为facebook平台申请的appID，fb_login_protocol_scheme为facebook平台申请的scheme__
​      4）、在app.build中配置appID和scheme


+ 2、参数说明

|参数|类型|是否必须|说明|
|:------:|:------:|:-----:|:------|
|context| Context|是|上下文|
|LT-AppID|String|是|每个应用对应的appid|
|LTAppKey|String|是|每个应用对应的appKey|
|mAdID|String|是|广告ID（获取方法看文档）|
|mPackageID|String|是|应用包名|
|OnLoginSuccessListener|Interface|是|登录回调接口|

+ 3、接口说明

       /**
        * 登录回调
        */
      public interface OnLoginSuccessListener {
      /**
   ​    * 成功回调
   ​    *
   ​    * @param result 成功信息
   ​    */
      void onSuccess(BaseEntry<ResultData> result);

      /**
   ​    * 请求失败
   ​    *
   ​    * @param ex 失败信息
   ​    */
      void onFailed(Throwable ex);

      /**
   ​    * 完成时
   ​    */
      void onComplete();

      /**
   ​    * 参数错误
   ​    *
   ​    * @param result 错误信息
   ​    */
      void onParameterError(String result);

      /**
   ​    * 错误回调
   ​    *
   ​    * @param error 错误信息
   ​    */
      void onError(String error);

+ 4、登录方法说明

       1、初始化
       FaceBookLoginManager.getInstance().initFaceBook(this, 
                "", "",mADID,"",
                new OnLoginSuccessListener() {
                    @Override
                    public void onSuccess(BaseEntry<ResultData> result) {
                        Log.e("facebook", result.getResult());
                    }
        
                    @Override
                    public void onFailed(Throwable ex) {
                        Log.e("facebook", ex.getMessage());
                    }
        
                    @Override
                    public void onComplete() {
                        Log.e("facebook", "===onComplete");
                    }
        
                    @Override
                    public void onParameterError(String result) {
                        Log.e("facebook", result);
                    }
        
                    @Override
                    public void onError(String error) {
                        Log.e("facebook", error);
                    }
                });
             2、facebook登录
             FaceBookLoginManager.getInstance().faceBookLogin(FaceBookActivity.this);
             3、facebook回调（在onActivityResult下引用）
           FaceBookLoginManager.getInstance().setOnActivityResult(requestCode, resultCode, data);

+ 5、结果码说明（具体以返回结果为准）
     ​    "OK"或者200表示成功
     ​    "NO"表示登录失败
     ​    "onCancel"表示登录取消
     ​    
-----
****
  分割线
****
----
### Google登录接入说明

  + 1、配置
      1）、在项目的build下面引用仓库地址

         maven { url 'https://jitpack.io' }
![4](https://upload-images.jianshu.io/upload_images/1716569-84f44d0667d0283a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
       2）、在项目的app.build中引用google的网络包如下所示：

        implementation 'com.github.muyishuangfeng:LTGameLocalGoogle:1.0.1'



+ 2、参数说明

     1）、登录参数

|参数|类型|是否必须|说明|
|:------:|:------:|:-----:|:------|
|context| Context|是|上下文|
|server_client_id| String|是|google平台申请的clientID|
|selfRequestCode|int|是|请求码（自定义）|

   2）、回调参数
|参数|类型|是否必须|说明|
|:------:|:------:|:-----:|:------|
|requestCode| int|是|onActivityResult方法中对应的requestCode|
|data| Intent|是|onActivityResult方法中对应的data|
|selfRequestCode| int|是|和上面的自定义selfRequestCode对应|
|content|Context|是|上下文|
|LT-AppID|String|是|每个应用对应的appid|
|LTAppKey|String|是|每个应用对应的appKey|
|mAdID|String|是|广告ID（获取方法看文档）|
|mPackageID|String|是|应用包ID|
|OnLoginSuccessListener|Interface|是|登录回调接口|

+ 3、接口说明

      /**
       * 登录回调
       */
       public interface OnLoginSuccessListener {
      /**
       * 成功回调
       *
       * @param result 成功信息
       */
      void onSuccess(BaseEntry<ResultData> result);
      
      /**
       * 请求失败
       *
       * @param ex 失败信息
       */
      void onFailed(Throwable ex);
      
      /**
       * 完成时
       */
      void onComplete();
      
      /**
       * 参数错误
       *
       * @param result 错误信息
       */
      void onParameterError(String result);
      
      /**
       * 错误回调
       *
       * @param error 错误信息
       */
      void onError(String error);
      
       }

  + 4、接口说明

         /**
          * 登录回调
          */
          public interface OnLoginSuccessListener {
         /**
          * 成功回调
          *
          * @param result 成功信息
          */
          void onSuccess(BaseEntry<ResultData> result);
          
         /**
          * 请求失败
          *
          * @param ex 失败信息
          */
         void onFailed(Throwable ex);
          
          /**
           * 完成时
           */
        void onComplete();

        /**
     ​    * 参数错误
     ​    *
     ​    * @param result 错误信息
     ​    */
     ​    void onParameterError(String result);

         /**
          * 错误回调
          *
          * @param error 错误信息
          */
         void onError(String error);

        }
     
   + 5、方法引用
        ​          
           1、登录方法
           GooglePlayLoginManager.initGoogle(this,clientID,REQUEST_CODE);
        ​       
           2、回调方法
        ​    GooglePlayLoginManager.onActivityResult(requestCode, data, REQUEST_CODE,
        ​    this,  LTAppID, LTAppKey,mADID,mPackageID,
        ​    new OnLoginSuccessListener() {
        ​        @Override
        ​        public void onSuccess(BaseEntry<ResultData> result) {
        ​            Log.e(TAG,result.getMsg()+"=="+result.getCode()+"=="+
        ​            result.getData().getApi_token());
        ​        }

                @Override
                public void onFailed(Throwable ex) {
                    Log.e(TAG,ex.getMessage());
                }
            
                @Override
                public void onComplete() {
            
                }
            
                @Override
                public void onParameterError(String result) {
                    Log.e(TAG,result);
                }
            
                @Override
                public void onError(String error) {
                    Log.e(TAG,error);
                }
            });

    + 6、结果码说明（具体可查看返回值）

                 "OK"或者200表示成功
                 "NO"表示登录失败
                 "12500"表示在google配置的sha1错误
    + 7、google登录配置
      
         [Google登录配置](https://blog.csdn.net/liutong123987/article/details/79417652)

  ### UI包配置说明

     1）、在项目的build下面引用仓库地址

         maven { url 'https://jitpack.io' }
  ![4](https://upload-images.jianshu.io/upload_images/1716569-84f44d0667d0283a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
     2）、在项目的app.build中引用UI的网络包如下所示：

       implementation 'com.github.muyishuangfeng:LTGameLocalUI:1.0.1'





+ 说明
  点击facebook开始facebook登录
  点击 google 开始 google登录 登录成功后跳转到协议页面，登录失败显示错误页面，之后再返回登录页面
 ![登录失败页面.png](https://upload-images.jianshu.io/upload_images/1716569-c12acecf7ff15284.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![协议页面.png](https://upload-images.jianshu.io/upload_images/1716569-aeab4b96bfb0c98e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


+ 协议传递方法说明
  
+ 1、登录方法

  |参数|类型|是否必须|说明|
  |:------:|:------:|:-----:|:------|
  |activity| Context|是|上下文|
  |mAgreementUrl| String|是|用户协议url|
  |mPrivacyUrl| String|是|隐私政策url|
  |googleClientID|String|是|google客户端ID|
  |LTAppID|String|是|乐推AppID|
  |LTAppKey|String|是|乐推AppKey|
  |mAdID|String|是|广告ID（获取方法看文档）|
  |mPackageID|String|是|应用包名|
  |OnReLoginInListener|Interface|是|第二次登录数据回调接口|

+ 2、登出方法

  |参数|类型|是否必须|说明|
  |:------:|:------:|:-----:|:------|
  |activity| Context|是|上下文|
  |mAgreementUrl| String|是|用户协议url|
  |mPrivacyUrl| String|是|隐私政策url|
  |googleClientID|String|是|google客户端ID|
  |LTAppID|String|是|乐推AppID|
  |LTAppKey|String|是|乐推AppKey|
  |mAdID|String|是|广告ID（获取方法看文档）|
  |mPackageID|String|是|应用包名|

+ 3、接口说明

      public interface OnReLoginInListener {
         //第二次登录成功结果回调
       void OnLoginResult(ResultData result);
       }

 
+ 3、使用说明

              //登录方法
              LoginUIManager.getInstance().loginIn(MainActivity.this, mAgreementUrl,
                      mPrivacyUrl, GoogleClientID, LTAppID, LTAppKey,mADID,mPackageID,oginInListener() {
                          @Override
                          public void OnLoginResult(ResultData result) {

                          }
                      });
               //登出方法
               LoginUIManager.getInstance().loginOut(MainActivity.this, mAgreementUrl,
                      mPrivacyUrl, GoogleClientID, LTAppID, LTAppKey,mADID,mPackageID);

  
  
    
     
   + 使用方法
     1、在onCreate中注册

            EventUtils.register(this);
     2、在onStop或者onDestory方法中反注册

            EventUtils.unregister(this);
     3、接收方法
     

      @Subscribe
      public void receiveEvent(Event event) {
        switch (event.getCode()) {
            case BaseResult.MSG_RESULT_GOOGLE_SUCCESS: {//获取到信息后做登录等操作
                ResultData mData = (ResultData) event.getData();
                

                break;
            }
            
            case BaseResult.MSG_RESULT_FACEBOOK_SUCCESS: {//获取到信息后做登录等操作
                ResultData mData = (ResultData) event.getData();

                break;
            }
        }
    
    }
    
    
**注意:广告ID必须写，获取广告ID的方法如下**

 Executors.newSingleThreadExecutor().execute(new Runnable() {
            @Override
            public void run() {
                try {
                  String  mAdID = DeviceUtils.getGoogleAdId(Context上下文);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
  

====================================================================

 __注意：兼容Android9.0 Http请求的网络请求方式__

 + 1、在res目录下创建xml文件夹
 + 2、在xml文件夹下创建network_security_config.xml文件夹
  
        <?xml version="1.0" encoding="utf-8"?>
        <network-security-config>
        <base-config cleartextTrafficPermitted="true" />
        </network-security-config>
 + 3、在清单文件（AndroidManifest）中的Application节点下配置引用
       
         <application
          android:networkSecurityConfig="@xml/network_security_config"
         />
