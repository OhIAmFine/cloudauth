# DetectFaceAttributes {#reference_cnr_qnv_4fb .reference}

人脸有效性检测。

## 描述 {#section_uv4_5nv_4fb .section}

对输入照片中人脸的有效性相关属性进行检测，便于业务方判定该照片是否满足自身业务留存或比对的要求。目前支持的人脸有效性相关属性有：是否人脸、是否模糊、是否戴眼镜、脸部姿态、性别、年龄、是否笑脸等。

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

## 请求参数 {#section_bgs_vnv_4fb .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|MaterialValue|String|是|待检测照片，格式描述见[关于上传图片地址的说明](#)。|
|RetAttributes|String|是|指定需要检测的内容，检测内容越多接口耗时越大。取值：-   Facetype：是否人脸
-   Headpose：人脸姿态
-   Glasses：是否戴眼镜
-   Blur：是否模糊
-   Smiling：是否笑脸
-   Gender：性别
-   Age：年龄

|
|MaxFaceNum|Integer|是|最多检测多少张人脸，默认传1。|
|DontSaveDB|Boolean|是|照片是否不入库，默认传true。|
|ClientTag|String|是|照片标签，默认传null。|
|MaxNumPhotosPerCategory|Integer|是|如果照片需要入库，相同ClientTag 保留的最大张数，新的照片会替换最老的，默认传1。|

## 返回参数 {#section_hcf_wnv_4fb .section}

|名称|类型|是否必需|描述|
|--|--|----|--|
|ImgWidth|Integer|是|原图宽度，单位为像素。|
|ImgHeight|Integer|是|原图高度，单位为像素。|
|FaceInfos|Map|是|人脸检测结果。具体结构描述见[FaceInfos](#)。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|FaceAttributesDetectInfo|Map|是|人脸属性检测结果。具体结构描述见[FaceAttributesDetectInfo](#)。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|FaceAttributes|Map|是|人脸属性。具体结构描述见[FaceAttributes](#)。|
|FaceRect|Map|是|人脸在原图中的位置。具体结构描述见[FaceRect](#)。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|Facetype|String|否|是否人脸，取值：-   None：不是人脸
-   Face：人脸
-   Profile：侧脸（左右转头超过30°）

**说明：** 若没有检测到脸，接口返回的faceInfos即为空；这里的None代表检测到脸，但认为是卡通或宠物等。

|
|Headpose|Map|否|人脸姿态。具体结构描述见[Headpose](#)。|
|Glasses|String|否|是否戴眼镜，取值：-   None：未戴眼镜
-   Wear：戴普通眼镜
-   Sunglass：戴墨镜

|
|Blur|Float|否|人脸模糊度，分值越大越模糊。通常≥2.0已较模糊，建议根据实际业务数据测试调整。|
|Smiling|Map|否|是否笑脸。具体结构描述见[Smiling](#)。|
|Gender|Map|否|性别。具体结构描述见[Gender](#)。|
|Age|Integer|否|年龄。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|PitchAngle|Float|是|抬头角度，单位为弧度。|
|RollAngle|Float|是|左右歪头角度，单位为弧度。|
|YawAngle|Float|是|左右转头角度，单位为弧度。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|Value|String|是|性别，取值：-   Female：女性
-   Male：男性

|
|Score|Float|是|性别置信度，分值越大越可信。通常以 Value 值为准即可。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|Value|Float|是|笑脸分值。|
|Threshold|Float|是|笑脸阈值。|

|名称|类型|是否必需|描述|
|--|--|----|--|
|Left|Integer|是|人脸矩形左上角距原图左边的距离，单位为像素。|
|Top|Integer|是|人脸矩形左上角距原图上边的距离，单位为像素。|
|Height|Integer|是|人脸矩形的高度。|
|Width|Integer|是|人脸矩形的宽度。|

## 错误码 {#section_ay3_xnv_4fb .section}

|错误代码|英文描述|中文描述|HTTP 状态码|
|----|----|----|--------|
|Error.InternalError|The request processing has failed due to some unknown error.|服务内部出现未知错误，请联系技术支持排查。|500|
|InvalidParam.ParamMissing|Please check if there is param empty in your imput.|参数错误，请检查参数是否为空。|400|
|Error.NoPermissionAccess|No permission to access our service.|没有权限访问服务，请确认是否服务已开通。|403|

## 示例 {#section_dvz_xnv_4fb .section}

关于**使用SDK开发包**的示例，请参考以下具体SDK 开发包使用文档中的认证方案说明：

以下是**拼接HTTPS请求**的相关示例：

**请求示例**

```
https://cloudauth.aliyuncs.com/?Action=DetectFaceAttributes
&RegionId=cn-hangzhou
&MaterialValue=http%3A%2F%2Fjiangsu.china.com.cn%2Fuploadfile%2F2015%2F0114%2F1421221304095989.jpg
&RetAttributes=gender%2cheadpose%2cblur%2cglasses%2cage%2cfacetype
&MaxFaceNum=1
&DontSaveDB=true
&ClientTag=
&MaxNumPhotosPerCategory=1
&<[公共请求参数]>
```

**返回示例**

-   JSON格式

    ```
    {
      "Data":{
        "ImgWidth":1080,
        "ImgHeight":1920,
        "FaceInfos":{
          "FaceAttributesDetectInfo":[
            {
              "FaceAttributes":{
                "Facetype":"Face",
                "Headpose":{
                  "PitchAngle":-1.5683923959732056,
                  "RollAngle":7.163370132446289,
                  "YawAngle":-6.925303936004639
                },
                "Glasses":"None",
                "Blur":0.1419367790222168,
                "Smiling":{},
                "Gender":{
                  "Score":0.7767378687858582,
                  "Value":"Female"
                },
                "Age":2
              },
              "FaceRect":{
                "Left":354,
                "Top":453,
                "Height":473,
                "Width":473
              }
            }
          ]
        }
      },
      "Code":"1",
      "Success":true
    }
    ```


