# SubmitMaterials {#reference5455 .reference}

提交认证资料。

## 描述 {#section_z1s_wpm_4fb .section}

服务端提交认证材料到实人认证服务进行审核，结果同步返回。

通常SubmitMaterials接口会同步返回认证结果。但是如果出现内部服务超时等现象，可能无法实时获取到认证的最终状态，此时接口会返回“认证中”状态。您在开发接入设计中需要考虑到这种情况，并可以使用[GetStatus](https://help.aliyun.com/document_detail/57049.html)接口轮询最新的状态。

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

## 请求参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|Action|String|是|要执行的操作。取值：SubmitMaterials。|
|RegionId|String|是|服务所在地域。取值：cn-hangzhou。|
|VerifyToken|String|是|认证会话标识，通过[GetVerifyToken](https://help.aliyun.com/document_detail/57050.html)接口获取。|
|Material.n.MaterialType|材料类型|是|提交审核的认证材料的类型。取值：**说明：** 需要传入哪些字段取决于您接入时确定的认证方案，具体见[认证方案](https://help.aliyun.com/document_detail/61362.html)中说明。

-   Name： 姓名。对应材料值为字符串格式，例如，张三。
-   IdentificationNumber： 身份证号。对应材料值为字符串格式，例如，630546198009090099。
-   FacePic： 人像照。对应材料值为字符串格式，具体请参考[关于上传图片地址的说明](#)。
-   IdCardFrontPic： 身份证人像面照片。对应材料值为字符串格式，具体请参考[关于上传图片地址的说明](#)。
-   IdCardBackPic： 身份证国徽面照片。对应材料值为字符串格式，具体请参考[关于上传图片地址的说明](#)。
-   IDPic： 二代身份证芯片中存储的头像照片。对应材料值为字符串格式，具体请参考[关于上传图片地址的说明](#)。
-   Mobile： 手机号（该手机号用于辅助风险判断，而非与手机运营商实名信息进行核验。）对应取值为字符串格式，例如，13983822930。

n为整型可变参数，当需要传入多种类型的材料时，依次通过Material.1.MaterialTy、Material.2.MaterialTy、...、Material.10.MaterialTy传入类型值。n最大为 10，且必须和Material.n.Value中的n匹配。

|
|Material.n.Value|材料值|是|提交审核的认证材料的取值，与具体的材料类型对应，具体见Material.n.MaterialType描述。n为整型可变参数，最大为 10，且必须和Material.n.MaterialType中的n匹配。

|

## 返回参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|VerifyStatus|Map|是|提交认证资料后的认证状态。具体结构描述见[VerifyStatus](#)。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|StatusCode|Integer|是|一个认证任务所处的认证状态。取值： -   -1： 未认证。表示没有提交记录。
-   0： 认证中。表示已提交认证，系统正在审核认证资料。
-   1： 认证通过。表示最近一次提交的认证资料已通过审核，当前认证任务完结。
-   2： 认证不通过。表示最近一次提交的认证资料未通过审核，当前认证任务还可以继续发起提交。

|
|SimilarityScore|Float|是|认证过程中所提交的人脸照片和身份证上的头像的相似程度分值。取值范围为\[0,100\]，分数越大相似度越高。|

## 错误码 { .section}

|错误代码|英文描述|中文描述|HTTP状态码|
|----|----|----|-------|
|Error.InternalError|The request processing has failed due to some unknown error.|服务内部出现未知错误，请联系技术支持排查。|500|
|InvalidParam.ParamMissing|Please check if there is param empty in your imput.|参数错误，请检查参数是否为空。|403|
|Error.NoPermissionAccess|No permission to access our service.|没有权限访问服务，请确认是否服务已开通。|403|
|InvalidParam.VerifyTokenExpired|The verify token was expired.|VerifyToken 过期。VerifyToken 是有时效性的，请确保在 token 失效前进行提交。|400|
|InvalidParam.MaterialsEmpty|The materials list is empty.|Materials 列表为空。|400|
|InvalidParam.MaterialsNotValid|The param materials is not valid.|请确保提交的材料类型正确性。|403|
|Error.DuplicatedTicketId|A record associated with your ticketId has existed.|重复提交。一个TicketId参数已经提交，且认证状态为认证中或认证通过时，再次提交会出现此错误。|403|
|Error.SubmitMaterialsError|SubmitMaterials action failed due to internal error.|提交处理失败。通常是实人认证服务内部处理异常，请联系技术支持排查。|500|
|Error.MaterialsUploadError|The materials upload failed.|SubmitMaterials在上传认证资料中出现错误，请检查图片地址格式正确，图片大小未超限，使用 base64 传图的已设置请求方法为POST。|500|
|Error.MaterialsInsufficient|The number of materials does not meet the requirements for verification.|提交认证的材料种类不满足要求，请检查上传的材料种类是否符合业务场景要求。|403|

## 示例 { .section}

关于**使用SDK开发包**的示例，请参考以下具体SDK 开发包使用文档中的认证方案说明：[Java](https://help.aliyun.com/document_detail/64074.html?spm=a2c4g.11186623.2.17.7b462daemy7kyn)、[PHP](https://help.aliyun.com/document_detail/64081.html)、[Python](https://help.aliyun.com/document_detail/64085.html)、[.NET](https://help.aliyun.com/document_detail/64086.html)、[Node.js](https://help.aliyun.com/document_detail/64088.html)、[Go](https://help.aliyun.com/document_detail/64087.html)。

以下是**拼接HTTPS请求**的相关示例：

**请求示例**

```
https://cloudauth.aliyuncs.com/?Action=SubmitMaterials
&RegionId=cn-hangzhou
&VerifyToken=8eb57ce35d584cbb9724198cb0ac6b60
&Material.1.MaterialType=FacePic
&Material.1.Value=http%3A%2F%2Fjiangsu.china.com.cn%2Fuploadfile%2F2015%2F0114%2F1421221304095989.jpg
&Material.2.MaterialType=IDPic&SignatureNonce=c71f14e8-f2e7-45fd-9c5a-721cf6b38c4a
&Material.2.Value=oss%3A%2F%2Fverify-img%3A715559d79b8d49c4aabfdad3f6a40774OSS.JPG
&Material.3.MaterialType=IdCardFrontPic&Signature=azM4ZgJ7auesjlWQuMBF9X2Tnc8%3D
&Material.3.Value=oss%3A%2F%2Fverify-img%3A715559d79b8d49c4aabfdad3f6a40774OSS.JPG
&Material.4.MaterialType=IdCardBackPic
&Material.4.Value=oss%3A%2F%2Fverify-img%3A715559d79b8d49c4aabfdad3f6a40774OSS.JPG
&Material.5.MaterialType=IdentificationNumber
&Material.5.Value=630546198009090099
&Material.6.MaterialType=Name
&Material.6.Value=%E6%9D%8E%E4%B8%89
&<[公共请求参数]>

```

**返回示例**

-   XML格式

    ```language-xml
    <?xml version='1.0' encoding='UTF-8'?>
    <SubmitMaterialsResponse>
        <Data>
            <VerifyStatus>
                <StatusCode>1</StatusCode>
    	    <SimilarityScore>93.12</SimilarityScore>
            </VerifyStatus>
        </Data>
        <Success>true</Success>
        <Code>1</Code>
    </SubmitMaterialsResponse>
    
    ```

-   JSON格式

    ```language-json
    {
      "Code": "1",
      "Data": {
        "VerifyStatus": {
          "MaterialScore": 95.22905,
          "StatusCode": 1
        }
      },
      "Success": true
    }
    
    ```


