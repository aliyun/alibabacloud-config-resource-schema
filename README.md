## Alibaba Cloud Config Resource Schema

Alibaba Cloud Config资源类型属性文件列举了可用于搜索查询的属性和类型，方便用户使用SQL更准确地查询特定资源类型

此库具有以下目录结构：

```
└── config
    ├── properties
    │   ├── ACS.properties.json
    │   └── resource-types
    │       ├── ACS_ACK_Cluster.properties.json
    │       ├── ACS_ADB_DBCluster.properties.json
    ...     ...
```

### 资源属性

资源属性 ( .properties.json) 文件采用 JSON 编码并具有以下形状：

```json
{
  "...": "...",
  "sample.property.name": "string",
  "...": "..."
}
```

这里sample.property.name是一个属性的名称和string它的类型。目前支持以下类型：

* `boolean`: 一个布尔值
* `date`: 一个日期
* `float/double`: 一个浮点值
* `integer/long`:  一个整数值
* `ip`: 一个ip地址 (比如, `192.168.1.1`)
* `string`: 一个字符串

Config 支持的每种资源类型都存在资源属性文件；它们位于config/properties/resource-types目录中；它们根据对应的 Config 资源类型名称进行命名（例如，资源类型的属性文件ACS::ECS::Instance是ACS_ECS_Instance.properties.json）。包含所有 ACS 资源类型的资源属性的合并属性文件位于config/properties/ACS.properties.json 中。

#### 示例用法 1

假设我们要上海区域中搜索ECS实例，要求ECS实例具有标签key为business，标签value为online。要查找相应的属性，首先打开资源类型的资源属性文件，找到其中相关属性：

```json
{
  "...": "...",
  "ResourceId": "string",
  "RegionId": "string",
  "...": "...",
  "ResourceType": "string",
  "ResourceName": "string",
  "...": "...",
  "Tags.Kvpair": "string",
  "...": "..."
}
```
查询方式:
```sql
SELECT ResourceId, ResourceName  WHERE ResourceType='ACS::ECS::Instance' AND RegionId='cn-shanghai' AND Tags.Kvpair='business:online'
```

(注意：Tags.Kvpair属性是Tags.Key和Tags.Value属性的串联，两者之间通过“:”相连。例如，Tags.Key值为test，Tags.Value值为111，Tags.Kvpair则为test:111（用冒号连接）。)

#### 示例用法 2

假设我们要查询使用的ECS实例数量，要求ECS实例具有标签key为product，内存配置为1G。要查找相应的属性，首先打开资源类型的资源属性文件，找到其中相关属性：

```json
{
  "...": "...",
  "ResourceId": "string",
  "...": "...",
  "ResourceType": "string",
  "ResourceName": "string",
  "...": "...",
  "Memory":"integer",
  "...": "...",
  "Tags.Key": "string",
  "...": "..."
}
```

查询方式:
```sql
SELECT COUNT(1) WHERE ResourceType='ACS::ECS::Instance' AND Memory=1024 AND Tags.Key='product'
```

#### 示例用法 3

假设我们要查询创建于2021/01/01之后的ECS实例。要查找相应的属性，首先打开资源类型的资源属性文件，找到其中相关属性：

```json
{
  "...": "...",
  "ResourceId": "string",
  "...": "...",
  "ResourceType": "string",
  "ResourceName": "string",
  "ResourceCreationTime": "date",
  "...": "..."
}
```

查询方式:
```sql
SELECT ResourceId, ResourceName WHERE ResourceType = 'ACS::ECS::Instance' AND ResourceCreationTime > '2021-01-01 00:00:00'
```

（注意：ResourceCreationTime属性的格式为yyyy-MM-dd HH:mm:ss）

#### 示例用法 4

假设我们要查询ip为192.168.128.1的ECS弹性网卡。要查找相应的属性，首先打开资源类型的资源属性文件，找到其中相关属性：

```json
{
  "...": "...",
  "ResourceId": "string",
  "...": "...",
  "ResourceType": "string",
  "ResourceName": "string",
  "...": "...",
  "PrivateIpAddress": "ip",
  "...": "..."
}
```

查询方式:
```sql
SELECT * WHERE ResourceType = 'ACS::ECS::NetworkInterface' AND PrivateIpAddress = '192.168.128.1'
```

注意：ip类型的字段可以为以下形式
* 具体的ipv4地址，如：192.168.128.1
* 具体的ipv6地址，如：2001:db8::/48
* ip段，如：192.168.0.0/16


#### 示例用法 5

假设我们要查询每种资源的数量，

查询方式:
```sql
SELECT ResourceType, COUNT(1) GROUP BY ResourceType ORDER BY COUNT(1) DESC
```


## License

This library is licensed under the MIT License.