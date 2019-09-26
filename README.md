# IoT-talk-session

介绍下IoT智能硬件控制领域的协议以及方法论，主要在wifi跟ble。



## BLE

BLE协议分为BLE Application和BLE Core两部分， BLE Core又分为Host和Controller两部分，如下图
![BLE](https://lgl88911.gitee.io/2018/10/16/BLE%E5%8D%8F%E8%AE%AE%E7%AE%80%E8%BF%B0/ble.png)
Controller负责定义物理规范,并基于硬件抽象逻辑链路层.
Host基于逻辑链路进行封装，对应用更为友好.
Application:应用层

# Controller

## Physical Layer

物理层描述硬件特性包含射频(RF)，调制方式，传输码率.

### RF

- 40个物理通道
- 每个通道带宽2M
- ISM频段2400M~2483.5M

### Modulation & Data Rate

- GFSK调制
- 1Mbps/2Mbps传输码率

## Link Layer

链路层对物理层进行逻辑划分和控制

- 将40个物理划分为3个广播(advertising)通道和37个数据(Data)通道
  ![channel](https://lgl88911.gitee.io/2018/10/16/BLE%E5%8D%8F%E8%AE%AE%E7%AE%80%E8%BF%B0/channel.png)
- 采用跳频(Hopping)通讯，进行通道管理
  ![hopping](https://lgl88911.gitee.io/2018/10/16/BLE%E5%8D%8F%E8%AE%AE%E7%AE%80%E8%BF%B0/hopping.png)
  ![ahopping](https://lgl88911.gitee.io/2018/10/16/BLE%E5%8D%8F%E8%AE%AE%E7%AE%80%E8%BF%B0/ahopping.png)
- 规定数据包格式
  ![packet](https://lgl88911.gitee.io/2018/10/16/BLE%E5%8D%8F%E8%AE%AE%E7%AE%80%E8%BF%B0/packet.png)
- 负责广播，发现，连接的创建和维护
- 提供校验重传机制，确保数据可靠

# HCI

Host Controller interface，Host和Controller之前的接口，当Host和Controller位于两个不同的物理实体时的通讯协议，HCI可以是uart/USB/spi. 例如我有一颗uart的Controller的BLE芯片，那么可以在PC上实现host，并通过uart访问控制Controller。

# Host

## L2CAP

Logical Link Control and Adaptation Protocol, 为了让上层更方便的使用Link layer. Link layer有组多限制，例如包的大小等等，通过L2CAP来适配，这里不做展开.

## SM

Security Manager, 安全管理(配对,认证，加密)

## GAP

Generic Access Profile,通用访问配置文件，定义蓝牙设备的角色(Role:Peripheral & Central)，**用于BLE device的连接**。

- Central: 连接发起者(Client 请求数据)
- Peripheral: 被连接者（Server 响应请求，提供数据）

## ATT

Attribute Protocol, 用于“发现，读，写”设备，所有的BLE Service都使用ATT做为应用层协议。
![ATT](https://lgl88911.gitee.io/2018/10/16/BLE%E5%8D%8F%E8%AE%AE%E7%AE%80%E8%BF%B0/att.png)

- Attribute Handle: 访问属性的handle
- Attribute Type: UUID(官方16bit, 自定义128bit)
- Attribute Value: 属性值
- Attribute Permissions： 属性的读写等权限

## GATT

Generic Attribute Profile 通用属性配置文件，用于**BLE device连接后的数据传输**。GATT在ATT之上规定了在service中使用ATT的方法。所有LE profile都必须基于GATT协议。
GATT使用ATT定义的Attribute来表示Descriptor/Characteristic/Service，由Descriptor组成Characteristics，由多个Characteristics组成Service，多个Service组成Profile。
![gatt](https://lgl88911.gitee.io/2018/10/16/BLE%E5%8D%8F%E8%AE%AE%E7%AE%80%E8%BF%B0/gatt.png)

# Application

## Gatt-base profile & serivce

BLE划分上APP内需要用基于GATT实现profiles & service，相关[规范](https://www.bluetooth.com/zh-cn/specifications/gatt)

## Application

基于GAP & GATT profiles的应用程序

# Zephyr BLE应用相关API

## GAP

相关API定义在include/bluetooth.h

## GATT

相关API定义在include/bluetooth/gatt.h