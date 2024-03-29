# GetVerifyToken {#reference_ys2_cqs_gfb .reference}

发起认证请求。

## 描述 {#section_r21_2qs_gfb .section}

每次开始认证前通过本接口获取认证Token（VerifyToken），用来串联认证请求中的各个接口。

**关于上传图片地址的说明**

在传入图片时，您需要上传其对应的HTTP/OSS地址或base64编码。

-   HTTP地址：可访问的公网HTTP地址。例如，http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg。
-   OSS地址：可公开访问的OSS文件地址，格式为oss://<bucket\>:<path\_to\_file\>。
-   base64编码：通过base64编码的图片，格式为base64://<图片 base64 字符串\>。

**说明：** 

-   不支持本地图片的相对路径或绝对路径。
-   单张图片大小请控制在2M内，避免算法拉取超时。
-   单个请求的Body有8M的大小限制，请计算好请求中所有图片和其他信息的大小，不要超限。
-   使用base64传递图片时，接口的请求方法需要改成 POST；图片base64字符串需要去掉头部描述，如`data:image/png;base64,`。

## 请求参数 {#section_lyt_kqs_gfb .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|Action|String|是|要执行的操作。取值：GetVerifyToken。|
|RegionId|String|是|服务所在地域。取值：cn-hangzhou。|
|Biz|String|是|使用实人认证服务的业务场景。请先参考[业务设置](https://help.aliyun.com/document_detail/59975.html)在控制台完成创建。|
|TicketId|String|是|标识一次认证任务的唯一ID。通常由业务使用方指定，方便关联业务场景的其他内容。一次认证任务，系统支持无限次发起提交，直到最终认证通过，该任务完结。**说明：** 发起不同的认证任务时需要更换不同的认证 ID。

|
|Binding|String|否|认证扩展材料（JSON 格式）。具体内容请参考[扩展参数Binding说明](#)。|
|UserData|String|否|业务数据（JSON 格式），通常用来传递业务上下文内容。|

**扩展参数Binding说明**

部分场景中，业务方在发起认证请求前已经收集了一部分认证资料，需要传递给实人认证服务，这种情况下就需要使用Binding扩展参数。目前支持Binding的认证资料包括：

**说明：** 具体传入哪些字段，视您选择的认证方案和实际业务需求而定，具体请参考[认证方案](https://help.aliyun.com/document_detail/61362.html)中的说明。

-   Name：姓名。字符串格式，例如，`李三`。
-   IdentificationNumber：身份证号。字符串格式，例如，`630546198009090099`。
-   IdCardFrontPic：身份证人像面照片。字符串格式，具体请参考[关于上传图片地址的说明](#)。
-   IdCardBackPic：身份证国徽面照片。字符串格式，具体请参考[关于上传图片地址的说明](#)。
-   FacePic：人像照。字符串格式，具体请参考[关于上传图片地址的说明](#)。
-   IDPic：二代身份证芯片中存储的头像照片。字符串格式，具体请参考[关于上传图片地址的说明](#)。

**应用示例**

业务方在自己的业务流程中已经采集了用户的姓名、身份证号、身份证人像面照片、身份证国徽面照片，而希望使用实人认证服务对用户进行活体检测并确认是本人操作。在这种情况下，就需要在发起认证请求时使用Binding参数指定这些采集到的数据。该场景下，Binding的内容示例如下：

```
{
    \"Name\": \"李三\",
    \"IdentificationNumber\": \"584129192830192233\",
    \"IdCardFrontPic\": \"http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg\",
    \"IdCardBackPic\": \"base64:///9j/4AAQSkZJRgABAQEASABIAAD/2...\"
}
```

## 返回参数 {#section_flr_lqs_gfb .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|VerifyToken|Map|是|该次认证的Token，用来串联认证请求中的各个接口。具体结构描述见[VerifyToken](#)。|
|StsToken|Map|否|如果业务方有额外的文件需要一并提交认证服务，可以使用STSToken将其上传到认证服务指定的地方。操作方法参考[使用STSToken 上传文件示例](#)。STSToken的具体结构描述见[STSToken](#)。|
|CloudauthPageUrl|String|否|认证流程页面入口URL。**说明：** H5+服务端接入的认证方案需要使用此字段，用作前端 H5 跳转到认证流程的 URL。

|

|名称|类型|是否必需|描述|
|--|--|----|--|
|Token|String|是|一次认证会话的标识。|
|DurationSeconds|Integer|是|认证会话超时时间，单位为秒。一般为1,800s。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|AccessKeyId|String|是|OSS AccessKey ID。|
|AccessKeySecret|String|是|OSS AccessKey密钥。|
|Expiration|String|是|STS token过期时间。|
|EndPoint|String|是|OSS endpoint。|
|BucketName|String|是|OSS bucket，认证服务指定的文件上传 Bucket。|
|Path|String|是|一个认证请求生成的用来上传的目录，业务方需要将文件上传到这个目录。|
|Token|String|是|STS 访问Token。|

**使用STSToken上传文件示例**

为了让业务方能够将图片上传到实人认证服务指定的OSS Bucket中，需要使用OSS提供的安全凭证管理（Security Token Service，简称 STS）功能。STS 能够依据bucket owner指定的访问策略生成一个临时的STS token给业务方，作为临时上传文件使用的凭据。关于STS的更多信息，请参考[OSS访问控制](https://help.aliyun.com/document_detail/31867.html)。

1.  使用OSS SDK上传图片。

    例如，有一张人脸图片名字为 face.jpg，需要上传到实人认证服务进行比对，可以参考以下流程（Java 语言）：

    ```
    String bucketName = uploadToken.bucketName;
    String endpoint = uploadToken.endpoint;
    String accessKeyId = uploadToken.accessKeyId;
    String accessKeySecret = uploadToken.accessSecret;
    String token = uploadToken.token;
    // 初始化客户端
    OSSClient ossClient = new OSSClient(endpoint, accessKeyId, accessKeySecret, token);
    String fileKey = uploadToken.path + "face.jpg";  //  c3fef70f36ca4b0d8203a499beaa9f80/face.jpg
    byte[] content = getBytesOf("face.jpg");
    // 上传图片
    ossClient.putObject(bucketName, fileKey, content);
    // 关闭client
    ossClient.shutdown();
    ```

2.  使用[SubmitMaterials](https://help.aliyun.com/document_detail/58176.html)接口上传 OSS 文件给实人认证服务。

    在与 OSS 对接成功上传图片后，获取到图片的文件路径（例如，`c3fef70f36ca4b0d8203a499beaa9f80/face.jpg`）。然后，需要将此图片的文件路径地址告知实人认证服务端。

    对于已成功上传至 OSS 的图片，传递给实人认证服务端的格式应为`oss://<uploadToken.bucketName>:c3fef70f36ca4b0d8203a499beaa9f80/face.jpg`。

    将此完整的图片文件名通过[SubmitMaterials](https://help.aliyun.com/document_detail/58176.html)接口提交给实人认证服务即可。


## 错误码 {#section_pdq_tdm_4fb .section}

|错误代码|英文描述|中文描述|HTTP 状态码|
|----|----|----|--------|
|Error.InternalError|The request processing has failed due to some unknown error.|服务内部出现未知错误，请联系技术支持排查。|500|
|InvalidParam.ParamMissing|Please check if there is param empty in your imput|参数错误，请检查参数是否为空。|400|
|Error.NoPermissionAccess|No permission to access our service|没有权限访问服务，请确认是否服务已开通。|403|
|InvalidParam.InvalidBiz|The biz is not valid|参数`Biz`不合法，请与控制台上创建的保持一致。|400|

## 示例 {#section_cvw_4qs_gfb .section}

关于**使用SDK开发包**的示例，请参考以下具体SDK 开发包使用文档中的认证方案说明：[Java](https://help.aliyun.com/document_detail/64074.html)、[PHP](https://help.aliyun.com/document_detail/64081.html)、[Python](https://help.aliyun.com/document_detail/64085.html)、[.NET](https://help.aliyun.com/document_detail/64086.html)、[Node.js](https://help.aliyun.com/document_detail/64088.html)、[Go](https://help.aliyun.com/document_detail/64087.html)。

以下是**拼接HTTPS请求**的相关示例：

**请求示例**

```
https://cloudauth.aliyuncs.com/?Action=GetVerifyToken
&RegionId=cn-hangzhou
&Biz=RPBasic
&TicketId=39ecf51e-2f81-4dc5-90ee-ff86125be683
&Binding=%7B%22Name%22%3A+%22%E6%9D%8E%E4%B8%89%22%7D
&UserData=%7B%22orderId%22%3A+%221391012388%22%7D
&<[公共请求参数]>
```

**返回示例**

-   XML格式

    ```
    <?xml version='1.0' encoding='UTF-8'?>
    <GetVerifyTokenResponse>
        <Data>
            <StsToken>
                <AccessKeySecret>OSSAccessKeySecret</AccessKeySecret>
                <BucketName>verify-external-image</BucketName>
                <AccessKeyId>OSSAccessKeyID</AccessKeyId>
                <Expiration>2017-09-14T02:28:20Z</Expiration>
                <EndPoint>https://oss-cn-shanghai.aliyuncs.com</EndPoint>
                <Token> CAIS2QJ1q6Ft5B2yfSjIpojNLPyNqehJ4I7dUWLptzMUfs1qvaDDujz2IHpFenBqBuActP8xmWFW5v0Slol5QZFLHhecNZop4pkLoQj8M9fL5Zfq4+Rf0sb6EDafAUZiUXtL7KarIunGc9KBNno2/00amMkGHFfPdlihNoLzxvwDR98LXw6+QCNbDdNNXGVLo9MbMn2jUpTINQXx0FLNEG1iuAd3lRl1i8KFz9ab9wDVgXDj1+YRvP6RGJW/aNR2N5oNfbWZ1edtJK3ay3wSuVoY6qpsjaJVvDrXotyHBVBVphSDOuXZtddlIgEiPfBiSvdIoaSsnqAo5+aNzNv5k00XZr5cDSjVA8KCuJKeQ7P1aI9nKOigaimSgoC1W8Or419+UxUyLxhXftctEHh0BCE3RyvSQq3dowqbPF75FPjfivpojMsrnw6457uQLl3KXK+YzSsI3he3n9aLxnQagAGuwJ7ORAZMwZm0UqNZ9uICFM2L39GX6/H22KDq2BYGO4bMcS49V+CuBuzTK1MEgRE5ELfBq6ttk4/Arah6gUomug6Yi8ZZCl0Zghgl36j5FEHeOYMS/RGnyiLVhRb+biHoncq/PsD/m8bv3VVQIcKQQ9gF71+WzebokSmhSw3A7w==
                </Token>
                <Path>bbff330091de48c585b637f03237a6db/</Path>
            </StsToken>
            <VerifyToken>
                <DurationSeconds>1800</DurationSeconds>
                <Token>bbff330091de48c585b637f03237a6db</Token>
              </VerifyToken>
              <CloudauthPageUrl>https://h5.m.taobao.com/user_verify/cloudauth.html?token=1234567890abcdefg</CloudauthPageUrl>
        </Data>
        <Success>true</Success>
        <Code>1</Code>
    </GetVerifyTokenResponse>
    ```

-   JSON格式

    ```
    {
      "Code": "1",
      "Data": {
        "StsToken": {
          "AccessKeyId": "OSSAccessKeyID",
          "AccessKeySecret": "OSSAccessKeySecret",
          "BucketName": "verify-external-image",
          "EndPoint": "https://oss-cn-shanghai.aliyuncs.com",
          "Expiration": "2017-09-09T13:01:32Z",
          "Path": "92790974cd384f29a949b47f1c4ca3a5/",
          "Token": "CAIS2QJ1q6Ft5B2yfSjIpKaGDuzDqJpP5vWfNhLY1mMPPfxphvPa1Dz2IHpFenBqBuActP8xmWFW5v0SlokiEcAUHR2bMcB8tcQL/1nwatud6sbpsrJdg8GuEDOaVkZoDUsP7aarIunGc9KBNno2/00amMkGHFfPdlihNoLzxvwDR98LXw6+QCNbDdNNXGVLo9MbMn2jUpTINQXx0FLNEG1iuAd3lRl1i8KFz9ab9wDVgXDj1+YRvP6RGJW/aNR2N5oNfbWZ1edtJK3ay3wSuVoY6qpsjaJVvDrXotyHBVBVphSDOuXZtYw1c14hN/dmEKIf/KTyz6xx67CCndioxU9GYepcCC2CA8KCuJKeQ7P1aI9nKOigaimSgoC1W8Or419+UxUyLxhXftctEHh0BCE3RyvSQq3dowqbPF75FPjfivpojMsrnw6457uQLl3KXK+YzSsI3he3n9aLxnQagAGferYB1UMADL0LyW3Bd91xmtePTfJD8QszrYAladv2D7CxsqP7jdBoJ6gRQoE9U0NEibHGjFeNZ3C8VN5kmnU+aY6lnwgcz+7nEXBnrh4hl56ubNXb8Tkx5LDEbfXrZmnfv5LbuUn+79OkvQPQvfmL3xM+7ciWyAzmeBBGTIdf8w=="
        },
        "VerifyToken": {
          "Expire": 1800,
          "Token": "92790974cd384f29a949b47f1c4ca3a5"
        },
        "CloudauthPageUrl": "https://h5.m.taobao.com/user_verify/cloudauth.html?token=1234567890abcdefg"
      },
      "Success": true
    }
    ```


