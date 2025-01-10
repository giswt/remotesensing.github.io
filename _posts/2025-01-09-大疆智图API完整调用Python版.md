---
layout:     post
title:      大疆智图API完整调用Python版
subtitle:   
date:       2025-01-09
author:     WT
header-img: img/DJI_0294.JPG
catalog: true
tags:
    - 大疆
    - 大疆智图
    - Python          
---  

## 背景
大疆智图API目前处于免费开放阶段，但[官网](https://developer.dji.com/doc/terra_api_tutorial/cn/quick-start.html)的快速入门文档的代码是Python和Shell脚本混合的，我尝试写了全PYthon版本，有利于后续开发过程中的集成。   
<font size=2 color=Red>本文只涉及二维可见光重建。 </font>   

## 步骤及代码

### 1 获取 Token
从本地设备向服务器发送请求时，需要在大疆智图 API 中使用 AK/SK 认证，借助 HMAC 签名算法为开发者的请求消息添加签名。  
返回值包含了accessKeyID、secretAccessKey、sessionToken、region、cloudBucketName、storePath、和callbackParam等参数。
``` python
import hmac
import hashlib
import base64
import requests
from datetime import datetime
import pytz

# DJI APP Key 和 Secret Key
DJI_APP_KEY="B"
DJI_SECRET_KEY="q"

# API 请求参数
HOST = "https://openapi-cn.dji.com"
URI = "/terra-rescon-be/v2/store/obtain_token"
HTTP_METHOD = "POST"

# 构造请求 Body (payload)
payload = """
{
    "example_key": "example_value"
}
"""

def calculate_signature(content, secret_key):
    """
    计算 HMAC-SHA256 签名
    """
    signature = hmac.new(
        secret_key.encode('utf-8'),
        content.encode('utf-8'),
        hashlib.sha256
    ).digest()
    return base64.b64encode(signature).decode('utf-8')

def generate_digest(payload):
    """
    生成请求 Body 的 SHA-256 Digest
    """
    digest = base64.b64encode(hashlib.sha256(payload.encode('utf-8')).digest()).decode('utf-8')
    return digest

# 当前 UTC 时间
utc_now = datetime.now(pytz.utc)
x_date = utc_now.strftime("%a, %d %b %Y %H:%M:%S GMT")

# HTTP 方法小写化
lower_method = HTTP_METHOD.lower()

# 生成 Digest
digest = generate_digest(payload)

# 构造签名内容
signature_content = f"date: {x_date}\n@request-target: {lower_method} {URI}\ndigest: SHA-256={digest}"
request_signature = calculate_signature(signature_content, DJI_SECRET_KEY)

# 完整请求 URL
url = f"{HOST}{URI}"

# 构造请求头
headers = {
    "Date": x_date,
    "Digest": f"SHA-256={digest}",
    "Authorization": f'hmac username="{DJI_APP_KEY}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{request_signature}"',
    "Content-Type": "application/json;charset=UTF-8"
}

# 发送 POST 请求
response = requests.post(url, headers=headers, data=payload, verify=False)  # 设置 verify=False 忽略 SSL 证书验证

# 输出结果
print("请求 URL:", url)
print("请求头:", headers)
print("请求 Body:", payload)
print("响应状态码:", response.status_code)
print("响应内容:", response.text)
``` 
将需要的参数从response.text中提取出来，以备后用。

``` python
import json

# 示例响应内容
response_content = response.text

# 将响应内容解析为 JSON
response_json = json.loads(response_content)

# 提取指定字段
cloud_name = response_json["data"]["cloudName"]
access_key_id = response_json["data"]["accessKeyID"]
access_key_secret= response_json["data"]["secretAccessKey"]
session_token = response_json["data"]["sessionToken"]

cloudBucketName=response_json["data"]["cloudBucketName"]
callback_param = response_json["data"]["callbackParam"]
store_path = response_json["data"]["storePath"]

# 打印提取的字段
print("Cloud Name:", cloud_name)
print("Access Key ID:", access_key_id)
print("access_key_secret:", access_key_secret)
print("Session Token:", session_token)

print("cloudBucketName:", cloudBucketName)
print("Callback Param:", callback_param)
print("Store Path:", store_path)
```

### 2 上传素材

数据需要上传到阿里云，中国的一个网址，其他地区的一个网址，因此需要安装对应的Python包。  

``` 
pip install oss2
```    
素材通过 Bucket和 store_path 建立与第一步的联系。  
安装完毕后，设置本地目录，以及上一步Token中获取的值，然后将数据上传到对应的文件夹中。
``` Python
#上传素材

import os
import oss2

endpoint = 'http://oss-cn-hangzhou.aliyuncs.com'

# 配置阿里云 OSS 访问信息
access_key_id = 'S'
access_key_secret = '2'
bucket_name = cloudBucketName
token=session_token

store_path = store_path

store_root_path = store_path[:store_path.rfind('/{fileName}')]

# 创建 STS 访问凭证
sts_auth = oss2.StsAuth(access_key_id, access_key_secret, token)

# 创建 OSS 客户端
bucket = oss2.Bucket(sts_auth, endpoint, bucket_name)
bucket.timeout = 600

# 本地文件夹路径
local_folder = r'M:\WT\DATA\DJI3_huailai'

# 遍历本地文件夹
uploaded_files = []
for root, dirs, files in os.walk(local_folder):
    for file_name in files:
        local_file_path = os.path.join(root, file_name)
        relative_path = os.path.relpath(os.path.join(root, file_name), local_folder)
        oss_file_path = os.path.join(store_root_path, relative_path)
        oss_file_path = oss_file_path.replace("\\", "/")
        put_result = bucket.put_object_from_file(oss_file_path, local_file_path)
        etag = put_result.etag
        # 保存结果的 etag，需要在 `关联文件` 步骤里用到
        print(f"Uploaded: {local_file_path} -> {oss_file_path}, etag: {etag}")
        container_file_path = relative_path.replace("\\", "/")
        uploaded_files.append({"name": container_file_path, "etag": etag, "checksum": etag})

if uploaded_files:
    import json
    with open("./uploaded_files.json", "w") as f:
        json.dump(uploaded_files, f)
```
上传后，会在本地目录生成一个文件 uploaded_files.json，保存着文件名和etag。    

### 3 创建资源
返回值中的 resource uuid 将在后续使用。   
```Python

#创建resource

import hmac
import hashlib
import base64
import requests
from datetime import datetime
import pytz

# DJI APP Key 和 Secret Key
DJI_APP_KEY="B"
DJI_SECRET_KEY="q"

# API 请求参数
HOST = "https://openapi-cn.dji.com"
URI = "/terra-rescon-be/v2/resources"
HTTP_METHOD = "POST"

# 请求 Body (payload)
payload = '{"name": "test_resource", "type": "map"}'

def calculate_signature(content, secret_key):
    """
    计算 HMAC-SHA256 签名
    """
    signature = hmac.new(
        secret_key.encode('utf-8'),
        content.encode('utf-8'),
        hashlib.sha256
    ).digest()
    return base64.b64encode(signature).decode('utf-8')

def generate_digest(payload):
    """
    生成请求 Body 的 SHA-256 Digest
    """
    digest = base64.b64encode(hashlib.sha256(payload.encode('utf-8')).digest()).decode('utf-8')
    return digest

# 当前 UTC 时间
utc_now = datetime.now(pytz.utc)
x_date = utc_now.strftime("%a, %d %b %Y %H:%M:%S GMT")

# HTTP 方法小写化
lower_method = HTTP_METHOD.lower()

# 生成 Digest
digest = generate_digest(payload)

# 构造签名内容
signature_content = f"date: {x_date}\n@request-target: {lower_method} {URI}\ndigest: SHA-256={digest}"
request_signature = calculate_signature(signature_content, DJI_SECRET_KEY)

# 完整请求 URL
url = f"{HOST}{URI}"

# 构造请求头
headers = {
    "Date": x_date,
    "Digest": f"SHA-256={digest}",
    "Authorization": f'hmac username="{DJI_APP_KEY}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{request_signature}"',
    "Content-Type": "application/json;charset=UTF-8"
}

# 发送 POST 请求
response = requests.post(url, headers=headers, data=payload, verify=False)  # 设置 verify=False 忽略 SSL 证书验证

# 输出结果
print("请求 URL:", url)
print("请求头:", headers)
print("请求 Body:", payload)
print("响应状态码:", response.status_code)
print("响应内容:", response.text)
```

解析response.text，获取resource uuid
```Python

response_json = json.loads(response.text)
# 提取指定字段
resource_uuid = response_json["data"]["uuid"]
# 打印提取的字段
print("resource_uuid:", resource_uuid)
```

### 4 关联文件
将上传的素材和资源关联。  
关系：资源的UUID与Token的callback_param

``` Python
#关联文件


import base64
import hashlib
import hmac
import json
import time

import requests

dji_app_key = DJI_APP_KEY
dji_secret_key = DJI_SECRET_KEY.encode('utf-8') 


uri = "/terra-rescon-be/v2/store/upload_callback"
resource_uuid = "7"
callback_param = "ek1"

host = "https://openapi-cn.dji.com"
url = host + uri
method = "POST"


def bind_batch_files(files):
    payload = {
        "resourceUUID": resource_uuid,
        "callbackParam": callback_param,
        "files": files,
    }

    digest = (
        lambda x: base64.b64encode(hashlib.sha256(x.encode("utf-8")).digest()).decode(
            "utf-8"
        )
    )(json.dumps(payload))

    gmt_time = time.strftime("%a, %d %b %Y %H:%M:%S GMT", time.gmtime(time.time()))
    signing_string = f"date: {gmt_time}\n@request-target: {method.lower()} {uri}\ndigest: SHA-256={digest}"
    signature = (
        lambda secret, x: base64.b64encode(
            hmac.new(secret, x.encode("utf-8"), hashlib.sha256).digest()
        ).decode("utf-8")
    )(dji_secret_key, signing_string)

    headers = {
        "Content-Type": "application/json",
        "Date": gmt_time,
        "Digest": f"SHA-256={digest}",
        "Authorization": f'hmac username="{dji_app_key}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{signature}"',
    }

    r = requests.post(url, json=payload, headers=headers)
    print(r.request.headers)
    print(r.content)
    print(r.headers)


if __name__ == "__main__":
    with open("./uploaded_files.json", "r") as f:
        uploaded_files = json.load(f)

    mini_batch = []
    for item in uploaded_files:
        mini_batch.append(item)
        if len(mini_batch) >= 50:
            bind_batch_files(mini_batch)
            mini_batch = []

    if mini_batch:
        bind_batch_files(mini_batch)

```

### 5 创建JOB
返回值中的 job uuid 将在将在后续使用。  
```Python 
import hmac
import hashlib
import base64
import requests
from datetime import datetime
import pytz

# DJI APP Key 和 Secret Key


# API 请求参数
HOST = "https://openapi-cn.dji.com"
URI = "/terra-rescon-be/v2/jobs"
HTTP_METHOD = "POST"

# 请求 Body (payload)
payload = '{"name": "test_name"}'

def calculate_signature(content, secret_key):
    """
    计算 HMAC-SHA256 签名
    """
    signature = hmac.new(
        secret_key.encode('utf-8'),
        content.encode('utf-8'),
        hashlib.sha256
    ).digest()
    return base64.b64encode(signature).decode('utf-8')

def generate_digest(payload):
    """
    生成请求 Body 的 SHA-256 Digest
    """
    digest = base64.b64encode(hashlib.sha256(payload.encode('utf-8')).digest()).decode('utf-8')
    return digest

# 当前 UTC 时间
utc_now = datetime.now(pytz.utc)
x_date = utc_now.strftime("%a, %d %b %Y %H:%M:%S GMT")

# HTTP 方法小写化
lower_method = HTTP_METHOD.lower()

# 生成 Digest
digest = generate_digest(payload)

# 构造签名内容
signature_content = f"date: {x_date}\n@request-target: {lower_method} {URI}\ndigest: SHA-256={digest}"
request_signature = calculate_signature(signature_content, DJI_SECRET_KEY)

# 完整请求 URL
url = f"{HOST}{URI}"

# 构造请求头
headers = {
    "Date": x_date,
    "Digest": f"SHA-256={digest}",
    "Authorization": f'hmac username="{DJI_APP_KEY}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{request_signature}"',
    "Content-Type": "application/json;charset=UTF-8"
}

# 发送 POST 请求
response = requests.post(url, headers=headers, data=payload, verify=False)  # 设置 verify=False 忽略 SSL 证书验证

# 输出结果
print("请求 URL:", url)
print("请求头:", headers)
print("请求 Body:", payload)
print("响应状态码:", response.status_code)
print("响应内容:", response.text)

```
解析返回的内容，获取 job uuid
``` Python
response_json = json.loads(response.text)

# 提取指定字段
job_uuid = response_json["data"]["uuid"]

# 打印提取的字段
print("job_uuid:", job_uuid)
```

### 6 启动 2D JOB 

将JOB UUID 与 Resource UUID建立关联，启动JOB进行运算
``` Python
{% raw %}
import hmac
import hashlib
import base64
import requests
from datetime import datetime
import pytz

# DJI APP Key 和 Secret Key


# API 请求参数
HOST = "https://openapi-cn.dji.com"
URI = f"/terra-rescon-be/v2/jobs/{job_uuid}/start"
HTTP_METHOD = "POST"

# 请求 Body (payload)
payload = f"""
{{
    "type": 14,
    "resourceUuid": "{resource_uuid}",
    "parameters": "{{\\"parameter\\":{{\\"map_mode\\":1,\\"quality_level\\":1,\\"output_geo_desc\\":{{\\"cs_type\\":\\"GEO_CS\\",\\"geo_cs\\":\\"EPSG:32650\\",\\"geo_cs_wkt\\":\\"\\",\\"override_vertical_cs\\":\\"\\"}}}}}}"
}}
"""

def calculate_signature(content, secret_key):
    """
    计算 HMAC-SHA256 签名
    """
    signature = hmac.new(
        secret_key.encode('utf-8'),
        content.encode('utf-8'),
        hashlib.sha256
    ).digest()
    return base64.b64encode(signature).decode('utf-8')

def generate_digest(payload):
    """
    生成请求 Body 的 SHA-256 Digest
    """
    digest = base64.b64encode(hashlib.sha256(payload.encode('utf-8')).digest()).decode('utf-8')
    return digest

# 当前 UTC 时间
utc_now = datetime.now(pytz.utc)
x_date = utc_now.strftime("%a, %d %b %Y %H:%M:%S GMT")

# HTTP 方法小写化
lower_method = HTTP_METHOD.lower()

# 生成 Digest
digest = generate_digest(payload)

# 构造签名内容
signature_content = f"date: {x_date}\n@request-target: {lower_method} {URI}\ndigest: SHA-256={digest}"
request_signature = calculate_signature(signature_content, DJI_SECRET_KEY)

# 完整请求 URL
url = f"{HOST}{URI}"

# 构造请求头
headers = {
    "Date": x_date,
    "Digest": f"SHA-256={digest}",
    "Authorization": f'hmac username="{DJI_APP_KEY}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{request_signature}"',
    "Content-Type": "application/json;charset=UTF-8"
}

# 发送 POST 请求
response = requests.post(url, headers=headers, data=payload, verify=False)  # 设置 verify=False 忽略 SSL 证书验证

# 输出结果
print("请求 URL:", url)
print("请求头:", headers)
print("请求 Body:", payload)
print("响应状态码:", response.status_code)
print("响应内容:", response.text)
{% endraw %}
```

### 7 查询JOB状态
根据JOB UUID 查询JOB是否运算完成，运算完成会返回 结果资源的UUID（outputResourceUuid）  
``` Python
import hmac
import hashlib
import base64
import requests
from datetime import datetime
import pytz

# DJI APP Key 和 Secret Key
DJI_APP_KEY="B"
DJI_SECRET_KEY="q"

# API 请求参数
HOST = "https://openapi-cn.dji.com"
URI = f"/terra-rescon-be/v2/jobs/{job_uuid}"
HTTP_METHOD = "GET"

def calculate_signature(content, secret_key):
    """
    计算 HMAC-SHA256 签名
    """
    signature = hmac.new(
        secret_key.encode('utf-8'),
        content.encode('utf-8'),
        hashlib.sha256
    ).digest()
    return base64.b64encode(signature).decode('utf-8')

def generate_digest(payload=""):
    """
    生成请求 Body 的 SHA-256 Digest (GET 请求通常没有 payload)
    """
    digest = base64.b64encode(hashlib.sha256(payload.encode('utf-8')).digest()).decode('utf-8')
    return digest

# 当前 UTC 时间
utc_now = datetime.now(pytz.utc)
x_date = utc_now.strftime("%a, %d %b %Y %H:%M:%S GMT")

# HTTP 方法小写化
lower_method = HTTP_METHOD.lower()

# 生成 Digest (GET 请求没有 payload)
payload = ""
digest = generate_digest(payload)

# 构造签名内容
signature_content = f"date: {x_date}\n@request-target: {lower_method} {URI}\ndigest: SHA-256={digest}"
request_signature = calculate_signature(signature_content, DJI_SECRET_KEY)

# 完整请求 URL
url = f"{HOST}{URI}"

# 构造请求头
headers = {
    "Date": x_date,
    "Digest": f"SHA-256={digest}",
    "Authorization": f'hmac username="{DJI_APP_KEY}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{request_signature}"',
    "Content-Type": "application/json;charset=UTF-8"
}

# 发送 GET 请求
response = requests.get(url, headers=headers, verify=False)  # 设置 verify=False 忽略 SSL 证书验证

# 输出结果
print("请求 URL:", url)
print("请求头:", headers)
print("响应状态码:", response.status_code)
print("响应内容:", response.text)
```
解析response.text，获得outputResourceUuid  
``` Python
response_json = json.loads(response.text)

# 提取指定字段
output_resource_uuid = response_json["data"]["outputResourceUuid"]

# 打印提取的字段
print("output_resource_uuid:", output_resource_uuid)
```

### 8 获取结果文件列表
根据output_resource_uuid 获取 结果文件列表files_uuid
``` Python
import hmac
import hashlib
import base64
import requests
from datetime import datetime
import pytz

# DJI APP Key 和 Secret Key


# API 请求参数
HOST = "https://openapi-cn.dji.com"
URI = f"/terra-rescon-be/v2/resources/{output_resource_uuid}"
HTTP_METHOD = "GET"

def calculate_signature(content, secret_key):
    """
    计算 HMAC-SHA256 签名
    """
    signature = hmac.new(
        secret_key.encode('utf-8'),
        content.encode('utf-8'),
        hashlib.sha256
    ).digest()
    return base64.b64encode(signature).decode('utf-8')

def generate_digest(payload=""):
    """
    生成请求 Body 的 SHA-256 Digest (GET 请求通常没有 payload)
    """
    digest = base64.b64encode(hashlib.sha256(payload.encode('utf-8')).digest()).decode('utf-8')
    return digest

# 当前 UTC 时间
utc_now = datetime.now(pytz.utc)
x_date = utc_now.strftime("%a, %d %b %Y %H:%M:%S GMT")

# HTTP 方法小写化
lower_method = HTTP_METHOD.lower()

# 生成 Digest (GET 请求没有 payload)
payload = ""
digest = generate_digest(payload)

# 构造签名内容
signature_content = f"date: {x_date}\n@request-target: {lower_method} {URI}\ndigest: SHA-256={digest}"
request_signature = calculate_signature(signature_content, DJI_SECRET_KEY)

# 完整请求 URL
url = f"{HOST}{URI}"

# 构造请求头
headers = {
    "Date": x_date,
    "Digest": f"SHA-256={digest}",
    "Authorization": f'hmac username="{DJI_APP_KEY}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{request_signature}"',
    "Content-Type": "application/json;charset=UTF-8"
}

# 发送 GET 请求
response = requests.get(url, headers=headers, verify=False)  # 设置 verify=False 忽略 SSL 证书验证

# 输出结果
print("请求 URL:", url)
print("请求头:", headers)
print("响应状态码:", response.status_code)
print("响应内容:", response.text)
```
解析 response.text 获得files_uuid  
``` Python
response_json = json.loads(response.text)

# 提取指定字段
files_uuid = response_json["data"]["fileUuids"]

# 打印提取的字段
print("files_uuid:", files_uuid)
```

### 9 下载结果文件
遍历files_uuid的文件列表，下载到本地的download_path路径。
```Python
import hmac
import hashlib
import base64
import requests
from datetime import datetime
import pytz
import os
import urllib.parse

# DJI APP Key 和 Secret Key


# API 主机地址
HOST = "https://openapi-cn.dji.com"

# 文件保存路径
download_path = "./downloaded_files2"
os.makedirs(download_path, exist_ok=True)

# UUID 列表（替换为实际的 UUID 列表）
file_uuids = files_uuid

def calculate_signature(content, secret_key):
    """
    计算 HMAC-SHA256 签名
    """
    signature = hmac.new(
        secret_key.encode('utf-8'),
        content.encode('utf-8'),
        hashlib.sha256
    ).digest()
    return base64.b64encode(signature).decode('utf-8')

def get_file_url(uuid):
    """
    获取文件的 URL
    """
    URI = f"/terra-rescon-be/v2/files/{uuid}"
    HTTP_METHOD = "GET"

    # 当前 UTC 时间
    utc_now = datetime.now(pytz.utc)
    x_date = utc_now.strftime("%a, %d %b %Y %H:%M:%S GMT")

    # 构造 lowercased HTTP method
    lower_method = HTTP_METHOD.lower()

    # 构造 Digest 值
    payload = ""  # GET 请求没有 body，因此 payload 为空
    digest = base64.b64encode(hashlib.sha256(payload.encode('utf-8')).digest()).decode('utf-8')

    # 构造签名内容
    signature_content = f"date: {x_date}\n@request-target: {lower_method} {URI}\ndigest: SHA-256={digest}"

    # 计算签名
    request_signature = calculate_signature(signature_content, DJI_SECRET_KEY)

    # 完整请求 URL
    url = f"{HOST}{URI}"

    # 构造请求头
    headers = {
        "Date": x_date,
        "Digest": f"SHA-256={digest}",
        "Authorization": f'hmac username="{DJI_APP_KEY}", algorithm="hmac-sha256", headers="date @request-target digest", signature="{request_signature}"',
        "Content-Type": "application/json;charset=UTF-8"
    }

    # 发送 GET 请求
    response = requests.get(url, headers=headers, verify=False)  # 设置 verify=False 忽略 SSL 证书验证
    if response.status_code == 200:
        # 提取返回 JSON 中的 URL 字段
        response_data = response.json()
        url = response_data.get("data", {}).get("url")  # 提取 JSON 中的 "url" 字段
        if url:
            return url
        else:
            print(f"未找到 URL (UUID: {uuid})，响应数据: {response_data}")
            return None
    else:
        print(f"请求失败 (UUID: {uuid})，HTTP 状态码: {response.status_code}")
        print("响应内容：", response.text)
        return None

def extract_path_from_url(url):
    """
    从 URL 中提取文件夹和文件名信息
    """
    # 解码 URL 中的路径部分
    parsed_url = urllib.parse.urlparse(url)
    path = urllib.parse.unquote(parsed_url.path)
    
    # 去掉 OSS 中 bucket 名称的前缀（例如 `/74c50efc-...`）
    path_parts = path.split("/", 2)
    if len(path_parts) > 2:
        relative_path = path_parts[2]  # 从第三部分开始是相对路径
    else:
        relative_path = path
    
    return relative_path

def download_file(file_url, root_path):
    """
    根据文件路径信息创建嵌套文件夹并下载文件
    """
    try:
        # 从 URL 提取文件相对路径（包括文件夹和文件名）
        relative_path = extract_path_from_url(file_url)
        full_path = os.path.join(root_path, relative_path)
        
        # 创建嵌套文件夹
        os.makedirs(os.path.dirname(full_path), exist_ok=True)

        # 下载文件
        response = requests.get(file_url, stream=True)
        response.raise_for_status()  # 检查 HTTP 请求是否成功

        # 保存文件
        with open(full_path, "wb") as file:
            for chunk in response.iter_content(chunk_size=1024):
                file.write(chunk)
        print(f"文件已成功下载: {full_path}")
    except requests.RequestException as e:
        print(f"下载文件时发生错误 (URL: {file_url}): {e}")

# 遍历 UUID 列表，获取文件 URL 并下载
for uuid in file_uuids:
    print(f"正在处理文件 UUID: {uuid}")
    file_url = get_file_url(uuid)  # 获取文件 URL
    if file_url:
        print(f"获取到的文件 URL: {file_url}")
        download_file(file_url, download_path)  # 下载文件并保存到嵌套文件夹
    else:
        print(f"未能获取文件 URL: {uuid}")

```


## 引用文献：  

[1] [大疆智图API](https://developer.dji.com/doc/terra_api_tutorial/cn/quick-start.html)  
