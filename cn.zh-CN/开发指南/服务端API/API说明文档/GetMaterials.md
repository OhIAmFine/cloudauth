# GetMaterials {#reference5558 .reference}

获取认证资料 。

## 描述 { .section}

收到认证请求后，业务系统通过该接口获取用户上传的资料（人脸图、证件图）及算法识别的资料（OCR 的地址等）。

## 请求参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|Action|String|是|要执行的操作。取值：GetMaterials。|
|RegionId|String|是|服务所在地域。取值：cn-hangzhou。|
|Biz|String|是|使用实人认证服务的业务场景。请先参考[业务设置](https://help.aliyun.com/document_detail/59975.html)在控制台完成创建。|
|TicketId|String|是|标识一次认证任务的唯一ID。通常由业务使用方指定，方便关联业务场景的其他内容。**说明：** 需要与当前认证任务在[GetVerifyToken](https://help.aliyun.com/document_detail/57050.html)中指定的TicketId保持一致。

|

## 返回参数 { .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|Name|String|是|姓名。|
|IdentificationNumber|String|是|证件号。|
|IdCardType|String|是|证件类型。identityCard代表身份证。|
|IdCardStartDate|String|否|证件有效期起始日期。格式：`yyyyMMdd`。依赖于算法识别，非必返回字段，可能存在无法识别的情况。|
|IdCardExpiry|String|否|证件有效期截止日期。格式：`yyyyMMdd`。依赖于算法识别，非必返回字段，可能存在无法识别的情况。|
|Address|String|否|证件地址。通过OCR算法识别出的地址（JSON 数据格式），结构描述见[Address 参数示例](#)。证件地址依赖于算法识别，非必返回字段，可能存在无法识别或识别不全的情况。

|
|Sex|String|否|证件性别。返回值的集合为`{m, f}`，其中m代表男性，f代表女性。|
|IdCardFrontPic|String|否|证件照正面图片HTTP地址（调用本接口后1小时内可访问）。若证件类型为身份证，则为身份证人像面照片。|
|IdCardBackPic|String|否|证件照背面图片HTTP地址（调用本接口后1小时内可访问）。若证件类型为身份证，则为身份证国徽面照片。|
|FacePic|String|是|认证过程中拍摄的人像正面照图片HTTP地址（调用本接口后1小时内可访问）。|
|EthnicGroup|String|否|证件上的民族。依赖于算法识别，非必返回字段，可能存在无法识别的情况。|

**Address示例**

**说明：** 以下地址通过身份证OCR识别获得，可能存在识别错误或不全的情况。其中，`province`为省/直辖市，`city`为市，`area`为区/县/县级市，`town`为街道/镇，`detail`为详细地址。由于街道/镇 一级的行政划分经常匹配不到，`town`的返回值为 0。

```
{
  "address": {
    "area": {
      "text": "余杭区",
      "value": "330110",
      "key": "area"
    },
    "province": {
      "text": "浙江省",
      "value": "330000",
      "key": "province"
    },
    "town": {
      "value": "0",
      "key": "town"
    },
    "city": {
      "text": "杭州市",
      "value": "330100",
      "key": "city"
    }
  },
  "detail": "五常街道永福社区文一西路977号"
}

```

## 错误码 { .section}

|错误代码|英文描述|中文描述|HTTP 状态码|
|----|----|----|--------|
|Error.InternalError|The request processing has failed due to some unknown error.|服务内部出现未知错误，请联系技术支持排查。|500|
|InvalidParam.ParamMissing|Please check if there is param empty in your imput.|参数错误，请检查参数是否为空。|400|
|Error.NoPermissionAccess|No permission to access our service.|没有权限访问服务，请确认是否服务已开通。|403|
|InvalidParam.InvalidBiz|The biz is not valid|参数Biz不合法，请与控制台上创建的保持一致。|400|
|InvalidParam.RecordNotFound|There is not record associated with your ticketId.|没有找到和TicketId关联的记录。|404|
|Error.GetMaterialsError|GetMaterials action failed due to internal error.|查询认证资料过程中出现内部未知错误，请联系技术支持排查。|500|

## 示例 { .section}

关于**使用SDK开发包**的示例，请参考以下具体SDK 开发包使用文档中的认证方案说明：[Java](https://help.aliyun.com/document_detail/64074.html)、[PHP](https://help.aliyun.com/document_detail/64081.html)、[Python](https://help.aliyun.com/document_detail/64085.html)、[.NET](https://help.aliyun.com/document_detail/64086.html)、[Node.js](https://help.aliyun.com/document_detail/64088.html)、[Go](https://help.aliyun.com/document_detail/64087.html)。

以下是**拼接HTTPS请求**的相关示例：

**请求示例**

```
https://cloudauth.aliyuncs.com/?Action=GetMaterials
&Biz=RPBasic
&TicketId=1839171847
&<[公共请求参数]>

```

**返回示例**

-   XML格式

    ```
    <?xml version='1.0' encoding='UTF-8'?>
    <GetMaterialsResponse>
        <Data>
            <Name>李明</Name>
            <FacePic><![CDATA[http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg]]></FacePic>
            <IdCardType>identityCard</IdCardType>
            <Address>{ "detail": "2号大街18号", "address": { "area": { "text": "江干区", "key": "area" }, "province": { "text": "浙江省", "key": "province" }, "city": { "text": "杭州市", "key": "city" } } }</Address>
            <IdCardBackPic>
                <![CDATA[http://verify-img.cn-shanghai.img.aliyun-inc.com/542fd010b3b04ef18193a94d8739cf97OSS.JPG?Expires=1505348837&OSSAccessKeyId=IJ95qE4nJQY6t6Lk&Signature=WeIU6zzeXXpBhwf7uwig0p7I7Hk%3D]]></IdCardBackPic>
            <IdCardFrontPic>
                <![CDATA[http://verify-img.cn-shanghai.img.aliyun-inc.com/715559d79b8d49c4aabfdad3f6a40774OSS.JPG?Expires=1505348836&OSSAccessKeyId=IJ95qE4nJQY6t6Lk&Signature=R9bwCimlsXLGYaIZ8S%2BppxNAzJ0%3D]]></IdCardFrontPic>
            <IdentificationNumber>629212199002108316</IdentificationNumber>
            <IdCardStartDate>19900210</IdCardStartDate>
            <IdCardExpiry>19900210</IdCardExpiry>
            <Sex>m</Sex>
            <EthnicGroup>汉</EthnicGroup>
        </Data>
        <Success>true</Success>
        <Code>1</Code>
    </GetMaterialsResponse>
    
    ```

-   JSON格式

    ```
    {
      "Code": "1",
      "Data": {
        "Address": "{ \"detail\":\"2号大街1号\", \"address\":{ \"area\":{ \"text\":\"江干区\", \"value\":\"330104\", \"key\":\"area\" }, \"province\":{ \"text\":\"浙江省\", \"value\":\"330000\", \"key\":\"province\" }, \"city\":{ \"text\":\"杭州市\", \"value\":\"330100\", \"key\":\"city\" } } }",
        "FacePic": "http://image-demo.img-cn-hangzhou.aliyuncs.com/example.jpg",
        "IdCardBackPic": "http://verify-img.cn-shanghai.img.aliyun-inc.com/581fd010b3b04ef18193a94d8739cf07OSS.JPG?Expires=1505301155&OSSAccessKeyId=IJ95qE4nJQY6t6Lk&Signature=nYEZoBcdBro4Yfr4QQSv%2B3/rM24%3D",
        "IdCardStartDate": "19900210",
        "IdCardExpiry": "19900210",
        "IdCardFrontPic": "http://verify-img.cn-shanghai.img.aliyun-inc.com/915559f79b8d49c8aabfdad3f6a40774OSS.JPG?Expires=1505301155&OSSAccessKeyId=IJ95qE4nJQY6t6Lk&Signature=jgW4rqB5OSTbDDo1Xlf7rwL9N80%3D",
        "IdCardType": "identityCard",
        "IdentificationNumber": "545230199002108328",
        "Name": "李明",
        "Sex": "m",
        "EthnicGroup": "汉"
      },
      "Success": true
    }
    
    ```


