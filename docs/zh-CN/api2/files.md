# 文件

- [上传检查](#上传检查)
- [上传文件](#上传文件)
- [获取文件](#获取文件)

## 上传检查

在上传一个文件前对文件进行检查，并创建一个 File with,如果文件存在，当文件存在，则创建一个 with id，不存在抛出 `404`。

```
GET /files/uploaded/:hash
```

##### 响应

```
Status: 200 OK
```
```json
{
    "message": "获取成功",
    "id": 1
}
```

## 上传文件

```
POST /files
```

## 输入

| 名称 | 类型 | 描述 |
|:----:|:----:|----|
| file | File | 待上传的文件 |

#### Response

```
Status: 201 Created
```
```json
{
    "message": [
        "上传成功"
    ],
    "id": 1
}
```

## 获取文件

```
GET /files/:file
```

> 这里的 :file 虽然叫做文件，实际是系统中的 file_with 标识，例如上传返回的都是 file_with 标识。

#### 参数

| 名称 | 类型 | 描述 |
|:----:|:---:|----|
| w | float | 需要获取的图片宽度，小数点后两位。|
| h | float | 需要获取的图片高度，小数点后两位。|
| q | integer | 可选，`1` <= `q` <= 100，可以和 `w`、`h` 配合使用，也可以单独使用，该参数表示图片质量 |
| json | mixed | 可选，不存在的情况下资源会使用 `302` 网络状态重定向到真实地址，如果存在则以 `json` 格式返回地址 |

> `q` 参数在「本地 CDN 驱动」中，只针对 `jpeg/jpg` 图像有效，因为 `png` 未无损图像。
> `w` & `h` 可以任意传一个，但是推荐都传递，只传一边的情况，则满足传递转换边，另一边按照图片比例自动设置。

#### 响应

```
Status: 200 OK
```

##### 如果是 `json`

```
{
    "url": "https://plus.io/storage/2017/cc/2.png"
}
```

#### 储存转换规则

不传递 `w`、`h` 的情况下，服务器会返回原是图片，如果传递，服务器则会拿出原始图像尺寸与获取尺寸进行计算，如果传递的获取最短边高于原图对应边，则直接返回原图，短于原图边的情况下取得获取边的最短边，并将对边使用原图尺寸自动设置尺寸。

> 在获取尺寸短于原图尺寸的情况下，处理后的图片永远只会满足获取遍的最低边，另一边自动尺寸，按照原图比例转换。

#### q 质量

`q` 参数值针对 `jpeg/jpg` 有效，服务器会有修正值 `90` 高于 `90` 的质量处理都将按照 `90` 进行处理。
 