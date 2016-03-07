<properties
pageTitle="논리 앱에서 Office 365 비디오 API 사용 | Microsoft Azure"
description="Microsoft Azure 앱 서비스 논리 앱에서 Office 365 비디오 API(커넥터)를 사용하여 시작"
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
ms.date="02/18/2016"
ms.author="deonhe"/>

# Office 365 비디오 API 시작

Office 365 비디오 API는 Office 365 채널 및 비디오를 사용하는 API를 제공합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전의 경우 [Office365 비디오 커넥터](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md)를 클릭합니다.

Office 365 비디오를 사용하여 다음을 수행할 수 있습니다.

* 논리 앱 빌드

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업에 대한 정보

Office 365 비디오 API를 작업으로 사용할 수 있습니다. 트리거는 없습니다. 모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

 Office365 비디오 API에서는 다음과 같은 동작 및/또는 트리거를 사용할 수 있습니다.

### Office 365 비디오 API 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|IsVideoPortalEnabled|비디오 포털 상태를 확인하여 비디오 서비스를 사용할 수 있는지 알아봅니다.|
|ListViewableChannels|사용자가 볼 수 있는 모든 채널을 가져옵니다.|
|ListVideos|채널에 있는 모든 Office 365 비디오를 나열합니다.|
|GetVideo|특정 Office 365 비디오에 대한 정보를 가져옵니다.|
|GetPlaybackUrl|비디오에 대한 Azure 미디어 서비스 매니페스트의 재생 URL을 가져옵니다.|
|GetStreamingKeyAccessToken|비디오 암호 해독 권한을 얻기 위해 전달자 토큰을 가져옵니다.|
ActionsTableReplaceMeLater## Office365 Video API에 대한 연결 만들기 Office365 비디오 API를 사용하려면 먼저 **연결**을 만든 후 다음 속성에 대한 세부 정보를 제공합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|SharePoint Online 자격 증명 제공|


>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Office365 비디오 REST API 참조
#### 이 문서 적용 버전: 1.0


### 비디오 포털 상태를 확인하여 비디오 서비스를 사용할 수 있는지 알아봅니다.
**```GET: /{tenant}/IsEnabled```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 사용자가 볼 수 있는 모든 채널을 가져옵니다.
**```GET: /{tenant}/Channels```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 채널에 있는 모든 Office 365 비디오를 나열합니다.
**```GET: /{tenant}/Channels/{channelId}/Videos```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|비디오를 가져와야 하는 채널 ID|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 특정 Office 365 비디오에 대한 정보를 가져옵니다.
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|채널 ID|
|videoId|string|yes|path|없음|비디오 ID|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 비디오에 대한 Azure 미디어 서비스 매니페스트의 재생 URL을 가져옵니다.
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|채널 ID|
|videoId|string|yes|path|없음|비디오 ID|
|streamingFormatType|string|yes|쿼리|없음|스트리밍 형식 유형입니다. 1 - 부드러운 스트리밍 또는 MPEG-DASH 0 - HLS 스트리밍|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 비디오 암호 해독 권한을 얻기 위해 전달자 토큰을 가져옵니다.
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**로 바꿉니다.



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|채널 ID|
|videoId|string|yes|path|없음|비디오 ID|


### 다음은 가능한 응답입니다.

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



## 개체 정의: 

 **Channel**: 채널 클래스

Channel에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|Id|string|
|제목|string|
|설명|string|



 **Video**: 비디오 클래스

Video에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|Id|string|
|제목|string|
|설명|string|
|CreationDate|string|
|소유자|string|
|ThumbnailUrl|string|
|VideoUrl|string|
|VideoDuration|정수|
|VideoProcessingStatus|정수|
|ViewCount|정수|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0224_2016-->