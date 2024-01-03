上完課之後要去看billing是否有被收費，被收費代表你有些東西沒有關閉


![Alt text](image-20.png)

老師被扣了1.多美金

http 提供了很多的方法，get很實用可以去獲取資料，post則可以上傳文章

今天要說的是Lambda

我們上個禮拜使用了Lambda role

我們需要新增Role

- 添加使用者權限
- 建立lambda Function

- 建立lambda Function讀取DynamoDB 跟寫入DynamoDB

- 

![Alt text](image-21.png)

![Alt text](image-22.png)

![Alt text](image-23.png)

![Alt text](image-24.png)

![Alt text](image-25.png)

`傳統來說你的虛擬機需要一臺一臺去配置,加設定一些網路它的結構還有很多東西 
，所以AWS提供了Lambda function讓你可以直接設定好代碼當達成事件的時候就自動觸發代碼`


![Alt text](image-26.png)

![Alt text](image-27.png)

![Alt text](image-28.png)

![Alt text](image-29.png)

![Alt text](image-123.png)

![Alt text](image-30.png)

![Alt text](image-31.png)

![Alt text](image-32.png)

![Alt text](image-33.png)

![Alt text](image-34.png)

![Alt text](image-35.png)

![Alt text](image-36.png)

click test

![Alt text](image-37.png)

![Alt text](image-38.png)

![Alt text](image-39.png)

![Alt text](image-40.png)

![Alt text](image-41.png)

![Alt text](image-42.png)

![Alt text](image-43.png)

![Alt text](image-44.png)

```py
import json
import boto3

client = boto3.client('dynamodb')

def lambda_handler(event, context):
  data = client.get_item(
    TableName='addrbook',
    Key={
        'name': {
          'S': 'tom'
        }
    }
  )
  print(data['Item'])
  response = {
      'statusCode': 200,
      'body': json.dumps(data['Item']),
      'headers': {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      },
  }
  return response
```

![Alt text](image-45.png)

# 圖1
![Alt text](image-124.png)

![Alt text](image-46.png)

![Alt text](image-47.png)
> 按下test


change to mary

![Alt text](image-48.png)

![Alt text](image-49.png)

![Alt text](image-50.png)

![Alt text](image-51.png)

## 讀取資料截一張圖

Create new Lambda function

![Alt text](image-52.png)

![Alt text](image-53.png)

![Alt text](image-54.png)

![Alt text](image-55.png)

![Alt text](image-56.png)

copy

![Alt text](image-57.png)

paste

```py
import json
import boto3
client = boto3.client('dynamodb')
def lambda_handler(event, context):
    PutItem = client.put_item(
        TableName='addrbook',
        Item={
            'name': {
              'S': 'john'
            },
            'phone': {
              'S': '0931987654'
            }
        }
      )
    response = {
      'statusCode': 200,
      'body': json.dumps(PutItem)
    }
    return response
```

![Alt text](image-58.png)

deploy

![Alt text](image-59.png)

test

![Alt text](image-60.png)

save

click test

![Alt text](image-61.png)


## 抓一張寫入john

![Alt text](image-125.png)

Stracture

![Alt text](image-62.png)


## 使用APIgetway連接lamda根據使用者調用API進行相對應操作

- 建立lambdaFunction (用於連接DynamoDB並顯示資料庫內容)

- 到API Gateway創建restAPI 建立資源配置路徑
- 關連 restAPI 與 lambda function
- 進行測試獲取資料

![Alt text](image-63.png)




![Alt text](image-64.png)

```py
import json
import boto3
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)

dynamodb = boto3.resource('dynamodb',region_name='us-east-1')

def lambda_handler(event, context):
  print(event)
  data = {
    'Items' : "Bad Request"
  }
  statusCode = 200
  path = event["path"]
  httpMethod = event["httpMethod"]
  table = dynamodb.Table('addrbook')
  if httpMethod == 'GET' and path == '/addrbook':
    data = table.scan()
  elif httpMethod == 'POST' and path == '/add_addrbook':
    if event['body'] is not None :
      body = json.loads(event['body'])

      table.put_item(
        Item={
          'name': body['name'],
          'phone': body['phone']
        },
      )

      data = {
        'Items' : "addrbook created statusCodeuccessfully"
      }
    else:
      data = {
        'Items' : "Invalid Payload"
      }
      statusCode = 400
  else:
    statusCode = 400

  print(data['Items'])
  response = {
    'statusCode': statusCode,
    'body': json.dumps(data['Items']),
    'headers': {
       'Content-Type': 'application/json',
    },
  }

  return response
```

![Alt text](image-65.png)

![Alt text](image-66.png)

![Alt text](image-67.png)

![Alt text](image-68.png)

![Alt text](image-69.png)
> deploy


![Alt text](image-70.png)

![Alt text](image-71.png)

Choice rest api build

![Alt text](image-72.png)

![Alt text](image-73.png)

![Alt text](image-74.png)

![Alt text](image-126.png)
> create resource


![Alt text](image-75.png)

![Alt text](image-76.png)

![Alt text](image-77.png)

![Alt text](image-78.png)

![Alt text](image-79.png)

![Alt text](image-80.png)

Create

![Alt text](image-81.png)

![Alt text](image-82.png)


![Alt text](image-83.png)

# 先做到這裡

![Alt text](image-127.png)

![Alt text](image-84.png)

先開一台linux的虛擬機


Deploy

# 部屬RestAPI

- Deploy API
- using chrome postman tool
- copy api url paste to postman tool and add /addrbook like `......./addrbook`

![Alt text](image-85.png)

![Alt text](image-86.png)

![Alt text](image-87.png)

![Alt text](image-88.png)

![Alt text](image-89.png)

![Alt text](image-90.png)

![Alt text](image-91.png)
> plus /addrbook

![Alt text](image-92.png)

![Alt text](image-93.png)


after deploy

![Alt text](image-94.png)

![Alt text](image-95.png)

# 貼一張圖

![Alt text](image-128.png)

![Alt text](image-96.png)

## Terraform

- DownLoad terraform for your linux
- In linux create tf file
- main.tf and variables.tf
- variable tf include your variable for main.tf
- change vairable ami content(if in us-east-1 using it!) you can in ec2 ami catalog to find ami content
- `terraform init` to initialize your file directory
- `terraform plan` like git add (you can preveiew what you add)
- `terraform apply` to push to aws
- `terraform destroy` to delete all terraform control (delete what you add this is help us easy to use aws)

![Alt text](image-97.png)

DownLoad

![Alt text](image-98.png)

crtl C & V

![Alt text](image-99.png)

![Alt text](image-100.png)

need install aws cli first

![Alt text](image-101.png)

![Alt text](image-102.png)

![Alt text](image-103.png)
main.tf and variables.tf copy paste from file

```TF
provider "aws" {
  region = var.region
}

resource "aws_instance" "mytest_vm" {
  ami = lookup(var.amis, var.region)
  instance_type=var.instance_type
  
  tags = {
   # Name = "mytest",
 }
}


```

![Alt text](image-104.png)
> ami要改

```tf
variable "region" {
    type = string
    default = "us-east-1"
    description="aws region"
}

variable "amis" {
    type=map
    default = {
        us-east-1 = "ami-06b09bfacae1453cb"
    }
    description = "ami id"

}

variable "instance_type" {
    type=string
    default = "t2.micro"
    description = "EC2 instance type"
}



```

![Alt text](image-105.png)
> ami source

![Alt text](image-106.png)

![Alt text](image-107.png)

## change (cache)

![Alt text](image-108.png)
> change

![Alt text](image-109.png)
> No change

## Push to aws

![Alt text](image-110.png)

![Alt text](image-111.png)

start create

![Alt text](image-112.png)

## success

![Alt text](image-113.png)

我遲遲不能夠成功直到我去刪除aws的default vpc再重新創建

![Alt text](image-114.png)

## 貼一張圖

![Alt text](image-129.png)

## Terraform 可以讓aws變得比較好去管理與維護

tf檔沒有寫的東西就會使用預設值

![Alt text](image-115.png)
> 可以創造多個虛擬機

![Alt text](image-116.png)

## destroy

![Alt text](image-117.png)
> 可以一鍵刪除你剛剛配置的東西不用一個一個去刪除

## 貼一下Destroy

![Alt text](image-130.png)

## 創建vpc跟subnet

開啟test1 資料夾當中有tf檔案，把裡面的內容增加到Linux tf當中.

![Alt text](image-118.png)

```tf
provider "aws" {
  region = var.region
}

resource "aws_vpc" "testvpc"{
    cidr_block="192.168.0.0/16"
    tags = {
        Name = "testvpc"
    }
}

resource "aws_subnet" "testvpc-web" {
    vpc_id = "${aws_vpc.testvpc.id}"
    cidr_block = "192.168.1.0/24"
    tags = {
        Name = "testvpc-web"
    }
}
```

testvpc1

![Alt text](image-119.png)
> subnet 要寫 vpc_id 使用$()獲取 vpc 的id

![Alt text](image-120.png)

 `terraform apply`

![Alt text](image-121.png)

![Alt text](image-122.png)

## 抓一下testvpc 跟 subnet 最後 destroy

![Alt text](image-131.png)

```sh
terraform destroy
```











