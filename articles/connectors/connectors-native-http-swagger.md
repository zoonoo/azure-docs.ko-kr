
<properties
	pageTitle="논리 앱에 HTTP + Swagger 동작 추가 | Microsoft Azure"
	description="HTTP + Swagger 동작 개요"
	services=""
	documentationCenter=""
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# HTTP + Swagger 동작 시작

HTTP + Swagger 동작을 사용하여 [Swagger 문서](https://swagger.io)를 통해 REST 끝점에 대한 최고급 커넥터를 만들 수 있습니다. 최고급 논리 앱 디자이너 환경이 있는 모든 REST 끝점을 호출하도록 논리 앱을 확장할 수도 있습니다.

논리 앱에서 HTTP + Swagger 동작 사용을 시작하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

---

## HTTP + Swagger를 트리거 또는 동작으로 사용합니다.

HTTP + Swagger 트리거 및 동작은 [HTTP 동작](connectors-native-http.md)과 동일하게 작동하지만 [swagger 메타데이터](https://swagger.io)의 API 모양 및 출력을 디자이너에 표시하여 더 나은 디자인 환경을 제공합니다. 또한 HTTP + Swagger를 트리거로 사용할 수 있습니다. 폴링 트리거를 구현하려면 [논리 앱과 함께 사용할 사용자 지정 API 만들기](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers)에 설명된 폴링 패턴을 따라야 합니다.

[논리 앱 트리거 및 동작에 대해 알아봅니다.](connectors-overview.md)

다음은 논리 앱에서 HTTP + Swagger 작업을 워크플로의 동작으로 사용하는 예제입니다.

1. **새 단계** 단추를 선택합니다.
2. **작업 추가**를 선택합니다.
3. 동작 검색 상자에 **swagger**를 입력하여 HTTP + Swagger 동작을 나열합니다.

	![HTTP + Swagger 동작 선택](./media/connectors-native-http-swagger/using-action-1.png)

4. Swagger 문서에 대한 URL을 입력합니다.
	- 논리 앱 디자이너에서 작동하려면 URL이 HTTPS 끝점이어야 하고 CORS를 사용할 수 있어야 합니다.
	- Swagger 문서가 이 요구 사항을 충족하지 않는 경우 [CORS가 설정된 Azure Storage를 사용](#hosting-swagger-from-storage)하여 문서를 저장할 수 있습니다.
5. **다음**을 클릭하여 Swagger 문서를 읽고 렌더링합니다.
6. HTTP 호출에 필요한 모든 매개 변수를 추가합니다.

	![HTTP 작업 완료](./media/connectors-native-http-swagger/using-action-2.png)

1. 도구 모음 왼쪽 위에서 **저장**을 클릭하면 논리 앱이 저장되고 게시됩니다(활성화).

### Azure Storage에서 Swagger 호스트

호스트되지 않거나 디자이너에 대한 보안 및 원본 간 요구 사항을 충족하지 않는 Swagger 문서를 참조할 수 있습니다. 이 문제를 해결하기 위해 Azure Storage에서 Swagger 문서를 저장하고 CORS를 사용하도록 설정하여 문서를 참조할 수 있습니다.

Azure Storage에서 Swagger를 생성, 구성 및 저장하는 단계는 다음과 같습니다.

1. [Azure Blob 저장소를 사용하여 Azure Storage 계정을 만듭니다](../storage/storage-create-storage-account.md). 이 작업을 수행하려면 사용 권한을 **공용 액세스**로 설정합니다.
2. Blob에 대해 CORS를 사용하도록 설정합니다. [이 PowerShell 스크립트](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)를 사용하여 설정을 자동으로 구성할 수 있습니다.
3. Blob에 Swagger 파일을 업로드합니다. [Azure 포털](https://portal.azure.com) 또는 [Azure Storage 탐색기](http://storageexplorer.com/)와 같은 도구에서 이 작업을 수행할 수 있습니다.
1. Azure Blob 저장소의 문서에 대한 HTTPS 링크를 참조합니다. 링크는 `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*` 형식을 따릅니다.



## 기술 세부 정보

이 HTTP + Swagger 커넥터가 지원하는 트리거 및 동작에 대한 세부 정보는 다음과 같습니다.

## HTTP + Swagger 트리거

트리거는 논리 앱에서 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](connectors-overview.md) HTTP + Swagger 커넥터에는 1개의 트리거가 있습니다.

|트리거|설명|
|---|---|
|HTTP + Swagger|HTTP 호출을 수행하고 응답 콘텐츠를 반환합니다.|

## HTTP + Swagger 동작

동작은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](connectors-overview.md) HTTP + Swagger 커넥터에는 1개의 가능한 동작이 있습니다.

|작업|설명|
|---|---|
|HTTP + Swagger|HTTP 호출을 수행하고 응답 콘텐츠를 반환합니다.|

### 작업 세부 정보

HTTP + Swagger 커넥터에는 1개의 가능한 동작이 있습니다. 다음은 각 동작, 해당 필수 및 옵션 입력 필드, 사용과 관련된 해당 출력 세부 정보에 대한 정보입니다.

#### HTTP + Swagger

Swagger 메타데이터를 지원하는 HTTP 아웃바운드 요청을 만듭니다. 별표(*)는 필수 필드를 의미합니다.

|표시 이름|속성 이름|설명|
|---|---|---|
|Method*|메서드|사용할 HTTP 동사|
|URI*|uri|HTTP 요청에 대한 URI|
|헤더|headers|포함할 HTTP 헤더의 JSON 개체|
|본문|body|HTTP 요청 본문|
|인증|authentication|요청에 사용할 인증 [자세한 내용은 HTTP를 참조하세요](./connectors-native-http.md#authentication).|

**출력 세부 정보**

HTTP 응답

|속성 이름|데이터 형식|설명|
|---|---|---|
|헤더|object|응답 헤더|
|본문|object|응답 개체|
|상태 코드|int|HTTP 상태 코드|

### HTTP 응답

다양한 작업을 호출할 때 특정 응답이 발생할 수 있습니다. 다음 표에서는 해당 응답 및 설명을 대략적으로 요약해서 보여 줍니다.

|Name|설명|
|---|---|
|200|확인|
|202|수락됨|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생.|

---

## 다음 단계

지금 플랫폼을 사용해 보고 [논리 앱을 만드세요](../app-service-logic/app-service-logic-create-a-logic-app.md). [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인할 수 있습니다.

<!---HONumber=AcomDC_0810_2016-->