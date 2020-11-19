这篇文章主要记录一下 retrofit 的注解使用方法。

retrofit 只是一个网络请求风格，本质上请求的是okhttp。一般的，在排查问题时，可以多使用postman这个强大的工具，它可以把网络请求进行转化，让你看到最后的代码形式。

注解一共有

![image-20201102174559218](https://tva1.sinaimg.cn/large/0081Kckwgy1gkaz03v4gaj30e40tuach.jpg)

这些大致分为三类吧。

### 请求方法

#### @GET

@POST

@PUT

@DELETE

这里列举几个，其他的请求也基本用不太到。

像请求方法一般要添加请求相对路径或全路径；也可以通过@Url 作为请求参数来作为请求路径，不过这个@Url 要作为第一个请求参数。另外，当设置的路径为相对路径，要通过baseUrl 来设置 host。

```
A relative or absolute path, or full URL of the endpoint. This value is optional if the first
parameter of the method is annotated with {@link Url @Url}.
```



### 请求参数注解

请求参数的使用和使用的请求方法、还有其他注解有关。

#### Body

- 作用于方法的参数的注解 (属于Paramter 参数注解)
- 使用该注解定义的参数不可为null
- 当你发送一个post或put请求,但是又不想作为请求参数或表单的方式发送请求时,使用该注解定义的参数可以直接传入一个实体类,retrofit会通过convert把该实体序列化并将序列化后的结果直接作为请求体发送出去.

```
Use this annotation on a service method param when you want to directly control the request body
of a POST/PUT request (instead of sending in as request parameters or form-style request
body). The object will be serialized using the {@link Retrofit Retrofit} instance
{@link Converter Converter} and the result will be set directly as the
request body.
```

使用示例：

```
 @POST("dictation/submitWork")
    Observable<Result<ReportDictationSubmitResponse>> submitReportDictationWork(
            @Query("slat1") String slat1, @Query("slat2") String slat2,
            @Query("workId") String workId, @Body RequestBody submitJson); 
 
 // 这里直接使用了一个实体类作为参数值，
@POST("/v1/online-profiles/is_show")
Observable<HttpResult<OnlineProfileModel>> onlineProfilesCancel(@Body OnlineProfileModel params);           
```

注意：

1 使用Body 后，不能在使用表单注解 FormUrlEncoded ，否则报错。

2 使用Body注解，会把body 注解的参数作为请求的请求体，且不需要key作为请求体的。（因为只有一个请求体，所以不能添加key，和表单那种key-value形式不一样）

**requestBody 构建**

当我们使用了RequestBody 时，我们需要构建这种参数，如何构建呢？

```
 RequestBody.create(MediaType.parse("image/jpeg"), photos);
 RequestBody.create(MediaType.parse("text/plain"), photos);

组合通常有下面这些组合
Returns the high-level media type, such as "text", "image", "audio", "video", or
"application".

Returns a specific media subtype, such as "plain" or "png", "mpeg", "mp4" or "xml".

text/plain ，image/png ， audio/mpeg ，video/mp4 ， application/xml 这些组合。
```



#### Field

- 作用于方法的参数
- **==用于发送一个表单请求==** （一般和FormUrlEncoded结合使用）
- 用String.valueOf()把参数值转换为String,然后进行URL编码,当参数值为null值时,会自动忽略。

```
@FormUrlEncoded
@POST("/v2/account-kit/verify")
Observable<HttpResult<LoginModel>> akVerify(
        @Field("code") int code,  //请求参数可以为 int ，只不过框架会把这个int转换为String
        @Field("fcm_token") String fcm_token,
        @Field("code_input") String code_input,
        @Field("source") String source

);
```



#### FieldMap

- 作用于方法的参数
- **==用于发送一个表单请求==** （一般和FormUrlEncoded结合使用）
- map中每一项的键和值都不能为空,否则抛出IllegalArgumentException异常

```
@FormUrlEncoded
@POST("/v1/devices")
Observable<HttpResult<Object>> uploadDevice(@FieldMap Map<String, Object> params);

 Map<String, Object> params = new HashMap<>();
 params.put("mac", DeviceUtil.getMacAddress(context));
 params.put("net_mode", NetworkUtils.getNetType(context));
 params.put("memory_total", DeviceUtil.getTotalMemory());
 params.put("memory_available", DeviceUtil.getAvailableMemory(context));
 params.put("sd_card_total", DeviceUtil.getSDTotalSize());
 params.put("sd_card_available", DeviceUtil.getSDAvailableSize());
 params.put("up_time", DeviceUtil.getUpTimes());
 params.put("display_metrics", DeviceUtil.getDisplayMetrics(context));
 params.put("cpu_cores", CPUUtil.getCPUCores());
 params.put("cpu_name", CPUUtil.getCpuName());
 params.put("cpu_max_freq", CPUUtil.getMaxCpuFreq());
 params.put("cpu_min_freq", CPUUtil.getMinCpuFreq());
 params.put("cpu_cur_freq", CPUUtil.getCurCpuFreq());
 UploadPresenter presenter = new UploadPresenter(DanaCepatApp.getInstance());
 presenter.uploadDevice(params, new ServiceCallback);
```



### FormUrlEncoded

- 用于修饰Field注解和FieldMap注解
- 使用该注解,表示请求正文将使用表单URL编码。字段应该声明为参数，并用@Field注释或FieldMap注释。使用FormUrlEncoded注解的请求将具”application / x-www-form-urlencoded” MIME类型。字段名称和值将先进行UTF-8进行编码, 再根据RFC-3986进行URI编码.

这个注解前面已经展示示例了。看看它用postman 表示该如何填写

![image-20201102183744022](https://tva1.sinaimg.cn/large/0081Kckwgy1gkb0hyluduj314o0hwdhu.jpg)这里看到postman 中的body 请求，有很多种表现形式，这时候我们要想起来请求/响应报文，请求报文分三部分

请求行：包含方法（get、post）、请求资源url、版本

请求头：就是包含请求长度、编码方式、使用的浏览器、是否常连接、主机名（接上请求行的请求资源url就是整个url）

请求实体：就是Body部分，它可以有很多的表现形式，比如说表单、二进制文件、json文件等等。而表单只不过是其中的一种而已。



#### Header

请求头，就是各种请求细节描述 比如说请求语言、是否长连接、编码格式等等

- 作用于方法的参数, 用于添加请求头
- 使用该注解定义的请求头可以为空,当为空时,会自动忽略,当传入一个List或array时,为拼接每个非空的item的值到请求头中.
- **具有相同名称的请求头不会相互覆盖,而是会照样添加到请求头中**

```
@GET("/")
Call<ResponseBody> foo(@Header("Accept-Language") String lang);
```

这样的方式可以动态修改，方便。

#### Headers

同上

使用示例

```
@Headers("Content-Type:image/jpeg")
@POST("/v1/identity/ektp-img-raw")
Observable<HttpResult<StorageModel>> uploadEKTPCompress(@Body RequestBody img);
```

#### HeaderMap

同上，使用示例

```
@GET("/search")
   void list(@HeaderMap Map<String, String> headers);

   //map
   Map<String,String> headers = new HashMap()<>;
   headers.put("Accept","text/plain");
   headers.put("Accept-Charset", "utf-8");
```



#### Part

- 作用于方法的参数,用于定义Multipart请求的每个part

- 使用该注解定义的参数,参数值可以为空,为空时,则忽略

- 使用该注解定义的参数类型有以下3种方式可选:

  * 如果类型是okhttp3.MultipartBody.Part，内容将被直接使用。 省略art中的名称,即 @Part MultipartBody.Part part 

    ```
    MultipartBody.Part part = MultipartBody.Part.createFormData("selfie", "xxxx", fileBody)
    
    @POST("https://api.faceid.com/faceid/v1/detect")
    @Multipart
    Observable<DetectModel> detectPictureDegree(@Part("key") RequestBody key @Part MultipartBody.Part part);
    ```

    这个就是body里面的那个form-data形式，可以接收文件。这个MultipartBody.Part 在使用时不需要key 。

    

  * 如果类型是RequestBody，那么该值将直接与其内容类型一起使用。 在注释中提供part名称（例如，@Part（“foo”）RequestBody foo）

    ```
    @POST("https://api-idn.megvii.com/faceid/v3/indonesia_ocr_sdk/get_biz_token")
    @Multipart
    Observable<BizTokenModel> getBizToken(@Part("key") RequestBody key, @Part("secret") RequestBody secret);
    使用时 还是需要key的
    ```

  * 其他对象类型将通过使用转换器转换为适当的格式。 在注释中提供part名称（例如，@Part（“foo”）Image photo）。

```
@Multipart
@POST("/")
Call<ResponseBody> example(
       @Part("description") String description,
       @Part(value = "image", encoding = "8-bit") RequestBody image)
```

```
 private void uploadFaceidSideImage(final byte[] photos) {
        RequestBody fileBody = RequestBody.create(MediaType.parse("image/jpeg"), photos);
        RequestBody faceApiKey = RequestBody.create(MediaType.parse("text/plain"), BuildConfig.FACE_API_KEY);
        RequestBody faceApiSecret = RequestBody.create(MediaType.parse("text/plain"), BuildConfig.FACE_API_SECRET);
        RequestBody token = RequestBody.create(MediaType.parse("text/plain"), faceFrontValidateModel.getToken_front_face());
        MultipartBody.Part part = MultipartBody.Part.createFormData("selfie", "xxx", fileBody);

        FacePresenter facePresenter = new FacePresenter(DanaCepatApp.getInstance());
        facePresenter.uploadSideFace(faceApiKey, faceApiSecret, part, token,
                new FaceHttpCallback<FaceSideValidateModel>(mContext, false) {
                    @Override
                    public void onSuccess(FaceSideValidateModel result) {
                        if (!TextUtils.isEmpty(result.getToken_side_face()) && null != mFrontPhotoByte && null != mSidePhotoByte) {
                            iconChooseSelfSide.setImageResource(R.drawable.icon_picture_success);
                            tvSelfStatusLeft.setText(getResources().getString(R.string.upload_photo_success));
                            //上传照片
                            uploadFrontAndSidePhoto(mFrontPhotoByte, mSidePhotoByte);
                        }
                    }

                    @Override
                    public void onFailure(FaceHttpError error) {
                        if (null != error && null != error.getError_message()) {
                            ToastUtil.show(DanaCepatApp.getInstance().getApplicationContext()
                                    , error.getError_message());
                            UploadLogToServerKt.UploadThirdlibFailLog("Error_message:" + error.getError_message() + " RawErrorMsg:" + error.getRawErrorMsg() + " detail msg:" + error.getDetailMsg(),
                                    4 , 1);
                        }
                    }
                });
    }
    
public void uploadSideFace(
            @Part("api_key") RequestBody api_key,
            @Part("api_secret") RequestBody api_secret,
            @Part MultipartBody.Part face,
            @Part("token_front_face") RequestBody token_front_face, FaceHttpCallback<FaceSideValidateModel> httpCallback) {
        Observable observable = FaceHttpClient.getInstance()
                .create(FaceService.class)
                .uploadSideFace(api_key, api_secret, face, token_front_face);
        toSubscribe(observable).subscribe(httpCallback);
    }
    
      //侧脸验证
    @POST("lite/raw/validate_side_face")
    @Multipart
    Observable<FaceSideValidateModel> uploadSideFace(
            @Part("api_key") RequestBody api_key,
            @Part("api_secret") RequestBody api_secret,
            @Part MultipartBody.Part face,
            @Part("token_front_face") RequestBody token_front_face);
```

上面的例子是之前项目使用到的接口，包含了各种情况。



#### PartMap 、Part

和Part 类似，有点像Header、HeaderMap、Headers （Field、FiedMap）三者之间的关系。

```
@Multipart
@POST("/upload")
Call<ResponseBody> upload(
       @Part("file") RequestBody file,
       @PartMap Map<String, RequestBody> params);
```



#### Path

- 作用于方法的参数
- 在URL路径段中替换指定的参数值。使用String.valueOf()和URL编码将值转换为字符串。
- 使用该注解定义的参数的值不可为空
- 参数值默认使用URL编码

```
//标准使用方式,默认使用URL编码
@GET("/image/{id}")
Call<ResponseBody> example(@Path("id") int id);
//默认使用URL编码
@GET("/user/{name}")
Call<ResponseBody> encoded(@Path("name") String name);
//不使用URL编码
@GET("/user/{name}")
Call<ResponseBody> notEncoded(@Path(value="name", encoded=true) String name);
```

==可以实现动态路径==



#### Query

- 作用于方法的参数
- 用于添加查询参数,即请求参数
- 参数值通过String.valueOf()转换为String并进行URL编码
- 使用该注解定义的参数,参数值可以为空,为空时,忽略该值,当传入一个List或array时,为每个非空item拼接请求键值对,所有的键是统一的,如: name=张三&name=李四&name=王五.

**==对get、post 添加 问号后的请求参数（post 也能添加问号后的请求 key-value ）==**

```
  @Headers("Content-Type:image/jpeg")
    @POST("/v1/identity/ektp-img")
    Observable<HttpResult<StorageModel>> uploadEKTP(@Body RequestBody img, @Query("etk_name") String etk_name);
    
 @GET("/v1/divisions")
 Observable<HttpResult<List<RegionModel>>> getDivisions(@Query("type") String type);


```



#### Url 

- 作用于方法参数
- 用于添加请求的接口地址

```
@GET
Call<ResponseBody> list(@Url String url);
```

### 总结

- FormUrlEncoded注解和Multipart注解不能同时使用,否则会抛出methodError(“Only one encoding annotation is allowed.”);可在ServiceMethod类中parseMethodAnnotation()方法中找到不能同时使用的具体原因.

- Path注解与Url注解不能同时使用,否则会抛出parameterError(p, “@Path parameters may not be used with @Url.”),可在ServiceMethod类中parseParameterAnnotation()方法中找到不能同时使用的具体代码.其实原因也很好理解: Path注解用于替换url路径中的参数,这就要求在使用path注解时,必须已经存在请求路径,不然没法替换路径中指定的参数啊,而Url注解是在参数中指定的请求路径的,这个时候指定请求路径已经晚了,path注解找不到请求路径,更别提更换请求路径中的参数了.

- 对于FiledMap,HeaderMap,PartMap,QueryMap这四种作用于方法的注解,其参数类型必须为Map的实例,且key的类型必须为String类型,否则抛出异常(以PartMap注解为例):parameterError(p, “@PartMap keys must be of type String: ” + keyType);

- 使用Body注解的参数不能使用form 或multi-part编码,即如果为方法使用了FormUrlEncoded或Multipart注解,则方法的参数中不能使用Body注解,否则抛出异常parameterError(p, “@Body parameters cannot be used with form or multi-part encoding.”);

