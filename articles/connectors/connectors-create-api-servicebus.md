<properties
pageTitle="논리 앱에서 Azure 서비스 버스 API 사용 | Microsoft Azure"
description="Microsoft Azure 앱 서비스 논리 앱에서 Azure 서비스 버스 API(커넥터)를 사용하여 시작"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/16/2016"
ms.author="deonhe"/>

# Azure 서비스 버스 API 시작

Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 작업을 수행할 수 있습니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

Azure 서비스 버스를 사용하여 다음을 수행할 수 있습니다.

* 논리 앱 빌드  

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업에 대한 정보

Azure 서비스 버스 API는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

 Azure 서비스 버스 API에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### Azure 서비스 버스 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|SendMessage|Azure 서비스 버스 큐나 항목으로 메시지를 보냅니다.|
### Azure 서비스 버스 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|GetMessageFromQueue|Azure 서비스 버스 큐에서 새 메시지를 가져옵니다.|
|GetMessageFromTopic|Azure 서비스 버스 항목 구독에서 새 메시지를 가져옵니다.|


## Azure 서비스 버스에 대한 연결 만들기
Azure 서비스 버스 API를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다.

|속성| 필수|설명|
| ---|---|---|
|ConnectionString|예|Azure 서비스 버스 연결 문자열 제공|  

다음 단계에 따라 논리 앱에서 사용할 수 있는 서비스 버스 **연결**을 만듭니다.

1. **되풀이**를 선택합니다.
2. **빈도**를 선택하고 **간격**을 입력합니다.
![서비스 버스 구성][1] 
3. **작업 추가**를 선택합니다.![서비스 버스 구성][2]   
4. 검색 상자에 **서비스 버스**를 입력하고 이름에 서비스 버스가 있는 모든 항목이 반환될 때까지 검색을 기다립니다.
5. **서비스 버스 - 메시지 보내기**를 선택합니다.![서비스 버스 구성][3]
7. **연결 이름** 및 **연결 문자열**을 입력한 다음 **연결 만들기**를 선택합니다.
![서비스 버스 구성][4]
7. 연결을 만들면 **메시지 보내기** 대화 상자가 표시됩니다. 메시지를 보내는 데 필요한 모든 정보를 입력합니다.![서비스 버스 구성][5]
8. 위의 메뉴에서 **저장** 단추를 선택하여 작업을 저장합니다.    

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Azure 서비스 버스 REST API 참조
#### 이 문서 적용 버전: 1.0


### Azure 서비스 버스 큐나 항목으로 메시지를 보냅니다.
**```POST: /{entityName}/messages```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|message| |yes|body|없음|서비스 버스 메시지|
|entityName|string|yes|path|없음|큐 또는 항목의 이름입니다.|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### Azure 서비스 버스 큐에서 새 메시지를 가져옵니다.
**```GET: /{queueName}/messages/head```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|queueName|string|yes|path|없음|큐의 이름입니다.|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### Azure 서비스 버스 항목 구독에서 새 메시지를 가져옵니다.
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|topicName|string|yes|path|없음|항목의 이름입니다.|
|subscriptionName|string|yes|path|없음|항목 구독의 이름입니다.|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



## 개체 정의: 

 **ServiceBusMessage**: 메시지는 콘텐츠와 속성으로 구성됩니다.

ServiceBusMessage에 대한 필수 속성:

ContentTransferEncoding

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|ContentData|string|
|ContentType|string|
|ContentTransferEncoding|string|
|속성|object|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-servicebus/connectionconfig1.png
[2]: ./media/connectors-create-api-servicebus/connectionconfig2.png
[3]: ./media/connectors-create-api-servicebus/connectionconfig3.png
[4]: ./media/connectors-create-api-servicebus/connectionconfig4.png
[5]: ./media/connectors-create-api-servicebus/connectionconfig5.png
[6]: ./media/connectors-create-api-servicebus/connectionconfig6.png

<!---HONumber=AcomDC_0413_2016-->