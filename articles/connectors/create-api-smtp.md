<properties
	pageTitle="논리 앱에 SMTP API 추가 | Microsoft Azure"
	description="REST API 매개 변수를 사용하는 SMTP API 개요"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/11/2016"
   ms.author="mandia"/>

# SMTP API 시작
SMTP 서버에 연결하여 전자 메일을 보냅니다.

논리 앱에서 SMTP API를 사용할 수 있습니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전에 대한 내용을 보려면 [SMTP 커넥터](../app-service-logic/app-service-logic-connector-smtp.md)를 클릭하세요.

SMTP를 사용하여 다음을 수행할 수 있습니다.

- SMTP를 사용하여 전자 메일 보내기가 포함된 비즈니스 흐름을 빌드합니다. 
- 전자 메일을 보내는 작업을 사용합니다. 이 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 FTP 서버에 새 파일이 있는 경우 이 파일을 가져와 SMTP를 사용하여 전자 메일 첨부 파일로 보낼 수 있습니다. 

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업
SMTP API를 사용하여 다음 작업을 수행할 수 있습니다. 트리거는 없습니다.

|트리거 | actions|
|--- | ---|
|없음 | 메일 보내기|

모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

## SMTP에 대한 연결 만들기
논리 앱에 이 API를 추가할 때 다음과 같은 값을 입력합니다.

|속성| 필수|설명|
| ---|---|---|
| SMTP 서버 이름 | 예 | SMTP 서버의 IP 주소나 FQDN(정규화된 도메인 이름)을 입력합니다.|
| 사용자 이름 |예 |SMTP 서버에 연결할 사용자 이름을 입력합니다. |
| 암호 | 예|사용자 이름 암호를 입력합니다. |

연결을 만든 후 To 또는 CC 값 등의 SMTP 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 SMTP 연결을 사용할 수 있습니다.

## Swagger REST API 참조

### 전자 메일 보내기
하나 이상의 받는 사람에 게 전자 메일을 보냅니다.  
```POST: /SendEmail```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|emailMessage| 다양함|yes|body|없음 |전자 메일 메시지|

## 개체 정의

#### 전자 메일: SMTP 전자 메일

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|받는 사람|string|no|
|CC|string|no|
|제목|string|no|
|본문|string|no|
|원본|string|no|
|IsHtml|부울|no|
|Bcc|string|no|
|중요도|string|no|
|첨부 파일|array|no|


#### 첨부 파일: 전자 메일 첨부 파일

| 이름 | 데이터 형식 |필수|
|---|---|---|
|FileName|string|no|
|ContentId|string|no|
|ContentData|string|yes|
|ContentType|string|yes|
|ContentTransferEncoding|string|yes|


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0224_2016-->
