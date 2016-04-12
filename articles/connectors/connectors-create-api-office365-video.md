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
ms.date="03/23/2016"
ms.author="deonhe"/>

# Office 365 비디오 API 시작
Office 365 비디오에 연결하여 Office 365 비디오에 대한 정보 가져오기, 비디오 목록 가져오기 등을 수행합니다. Office 365 비디오 API를 다음에서 사용할 수 있습니다.

- 논리 앱 

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 이 API는 이전 스키마 버전에서 지원되지 않습니다.

Office 365 비디오로 다음을 수행할 수 있습니다.

- Office 365 비디오에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 비디오 포털 상태 확인, 채널의 모든 비디오 목록 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Bing 검색 API를 사용하여 Office 365 비디오를 검색한 다음 Office 365 비디오 API를 사용하여 해당 비디오에 대한 정보를 가져올 수 있습니다. 비디오가 요구 사항을 충족하면 Facebook에 이 비디오를 게시할 수 있습니다. 

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

Office 365 비디오 API에서는 다음과 같은 작업을 사용할 수 있습니다. 트리거는 없습니다.

| 트리거 | actions|
| --- | --- |
| 없음 | <ul><li>비디오 포털 상태 확인</li><li>볼 수 있는 모든 채널 가져오기</li><li>비디오에 대한 Azure 미디어 서비스 매니페스트의 재생 URL 가져오기</li><li>비디오 암호를 해독하기 위해 액세스를 가져오는 전달자 토큰 가져오기</li><li>특정 Office365 비디오에 대한 정보 가져오기</li><li>채널에 있는 모든 Office365 비디오 나열</li></ul>

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## Office365 비디오 API에 대한 연결 만들기
논리 앱에 이 API를 추가할 때 Office 365 비디오 계정에 로그인하고 논리 앱을 계정에 연결해야 합니다.

1. Office 365 비디오 계정에 로그인합니다.
2. 논리 앱에서 Office 365 계정에 연결하고 사용할 수 있도록 허용합니다. 

연결을 만든 후 테넌트 이름 또는 채널 ID 등의 Office 365 비디오 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 Office 365 비디오 연결을 사용할 수 있습니다.

## Swagger REST API 참조
적용 버전: 1.0

### 비디오 포털 상태 확인 
비디오 포털 상태를 확인하여 비디오 서비스를 사용할 수 있는지 알아봅니다. ```GET: /{tenant}/IsEnabled```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|



### 볼 수 있는 모든 채널 가져오기 
사용자가 볼 수 있는 모든 채널을 가져옵니다. ```GET: /{tenant}/Channels```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|




### 채널에 있는 모든 Office 365 비디오를 나열합니다. 
채널에 있는 모든 Office 365 비디오를 나열합니다. ```GET: /{tenant}/Channels/{channelId}/Videos```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|비디오를 가져와야 하는 채널 ID|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|




### 특정 Office 365 비디오에 대한 정보를 가져옵니다. 
특정 Office 365 비디오에 대한 정보를 가져옵니다. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|채널 ID|
|videoId|string|yes|path|없음|비디오 ID|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|




### 비디오에 대한 Azure 미디어 서비스 매니페스트의 재생 URL을 가져옵니다. 
비디오에 대한 Azure 미디어 서비스 매니페스트의 재생 URL을 가져옵니다. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|채널 ID|
|videoId|string|yes|path|없음|비디오 ID|
|streamingFormatType|string|yes|쿼리|없음|스트리밍 형식 유형입니다. 1 - 부드러운 스트리밍 또는 MPEG-DASH 0 - HLS 스트리밍|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|




### 비디오 암호 해독 권한을 얻기 위해 전달자 토큰을 가져옵니다. 
비디오 암호 해독 권한을 얻기 위해 전달자 토큰을 가져옵니다. ```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|tenant|string|yes|path|없음|사용자가 속해 있는 디렉터리의 테넌트 이름|
|channelId|string|yes|path|없음|채널 ID|
|videoId|string|yes|path|없음|비디오 ID|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|404|찾을 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


## 개체 정의

#### 채널: 채널 클래스

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|Id|string|no|
|제목|string|no|
|설명|string|no|


#### 비디오 

| 이름 | 데이터 형식 |필수|
|---|---|---|
|Id|string|no|
|제목|string|no|
|설명|string|no|
|CreationDate|string|no|
|소유자|string|no|
|ThumbnailUrl|string|no|
|VideoUrl|string|no|
|VideoDuration|정수|no|
|VideoProcessingStatus|정수|no|
|ViewCount|정수|no|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0330_2016-->