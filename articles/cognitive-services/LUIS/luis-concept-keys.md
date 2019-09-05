---
title: 제작 및 런타임 키-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 두 가지 키 인 제작 키를 사용 하 여 모델을 만들고 사용자 길이 발언를 사용 하 여 예측 끝점을 쿼리 하기 위한 런타임 키를 사용 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6c87da7ae62af54990e0a1a2c62065717a201a
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256945"
---
# <a name="authoring-and-runtime-keys"></a>제작 및 런타임 키


>[!NOTE]
>계속 하기 전에 Azure 제작 리소스를 사용 하지 않는 앱을 모두 [마이그레이션](luis-migration-authoring.md) 하세요.

LUIS는 두 가지 유형의 Azure 리소스를 사용 합니다. 각 유형에는 다음과 같은 키가 있습니다. 
 
* [작성](#programmatic-key) 하 여 의도, 엔터티 및 레이블 길이 발언, 학습 및 게시를 만들 수 있습니다. LUIS 앱을 게시할 준비가 되 면 앱에 할당 된 [런타임에 대 한 예측 끝점 키](luis-how-to-azure-subscription.md) 가 필요 합니다.
* [런타임에 대 한 예측 끝점 키](#prediction-endpoint-runtime-key)입니다. 채팅 봇과 같은 클라이언트 응용 프로그램은이 키를 통해 런타임의 **쿼리 예측 끝점** 에 액세스 해야 합니다. 

|Key|용도|인식 서비스`kind`|인식 서비스`type`|
|--|--|--|--|
|[작성 키](#programmatic-key)|작성, 학습, 게시, 테스트.|`LUIS.Authoring`|`Cognitive Services`|
|[예측 끝점 런타임 키](#prediction-endpoint-runtime-key)| 사용자 utterance를 사용 하 여 예측 끝점 런타임을 쿼리 하 여 의도 및 엔터티를 확인 합니다.|`LUIS`|`Cognitive Services`|

또한 LUIS는 월간 1000 트랜잭션 예측 끝점 할당량을 포함 하는 [시작 키](luis-how-to-azure-subscription.md#starter-key) 를 제공 합니다. 

두 키를 동시에 만들 필요는 없지만 이렇게 하면 훨씬 더 쉽습니다.

게시 하 고 쿼리 하려는 [지역](luis-reference-regions.md#publishing-regions) 에서 LUIS apps를 작성 하는 것이 중요 합니다.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>작성 키

LUIS 계정을 만들 때 제작 키가 자동으로 생성 되며 무료입니다. LUIS로 시작 하는 경우 각 제작 [지역](luis-reference-regions.md)에 대 한 모든 LUIS 앱에서 하나의 시작 키가 있습니다. 제작 키의 목적은 LUIS 앱을 관리 하거나 예측 끝점 쿼리를 테스트 하는 인증을 제공 하는 것입니다. 

Azure Portal에서 제작 키를 만들면 [참가자 역할](#contributions-from-other-authors)에 사용자를 할당 하 여 제작 리소스에 대 한 사용 권한을 제어할 수 있습니다. 참가자를 추가 하려면 Azure 구독 수준에서 권한이 필요 합니다. 

작성 키를 찾으려면 [LUIS](luis-reference-regions.md#luis-website)에 로그인하고 오른쪽 위 탐색 모음에 있는 계정 이름을 클릭하여 **계정 설정**을 엽니다.

![작성 키](./media/luis-concept-keys/authoring-key.png)

**런타임 쿼리**를 만들려면 Azure [LUIS 리소스](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)를 만듭니다. 

> [!CAUTION]
> 편의를 위해 대부분의 샘플은 [할당량](luis-boundaries.md#key-limits)에 몇 가지 무료 예측 끝점 호출을 제공 하므로 [스타터 키](#starter-prediction-endpoint-runtime-key) 를 사용 합니다.  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>예측 끝점 런타임 키 

**런타임 끝점 쿼리가**필요한 경우 LANGUAGE UNDERSTANDING (LUIS) 리소스를 만든 다음 LUIS 앱에 할당 합니다. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

리소스 만들기 프로세스가 완료 되 면 앱에 [키를 할당](luis-how-to-azure-subscription.md) 합니다. 

* 런타임 (쿼리 예측 끝점) 키는 런타임 키를 만들 때 지정한 사용량 계획에 따라 끝점 적중의 할당량을 허용 합니다. 가격 정보는 [Cognitive Services 가격](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h)을 참조하세요.

* 런타임 키는 모든 LUIS apps 또는 특정 LUIS 앱에 사용할 수 있습니다. 
* LUIS apps를 작성 하는 데 런타임 키를 사용 하지 마세요. 

### <a name="starter-prediction-endpoint-runtime-key"></a>시작 예측 끝점 런타임 키

**시작** 예측 끝점 키는 무료로 제공 되며 1000 예측 끝점 쿼리를 포함 합니다. 이러한 쿼리를 사용한 후에는 Language Understanding에 대 한 고유한 예측 끝점 리소스를 만들어야 합니다.  

특수 한 리소스를 생성 합니다. 이는 임시 시작 키로 서 Azure 리소스의 목록에 표시 되지 않습니다. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>쿼리에서 런타임 키 사용
LUIS runtime 끝점은 두 가지 유형의 쿼리를 수락 합니다 .이 두 가지는 모두 예측 끝점 런타임 키를 사용 하지만 다른 위치에서 사용 됩니다.

런타임에 액세스 하는 데 사용 되는 끝점은 다음 표에 나와 있는 `{region}` 것 처럼 리소스 지역에 고유한 하위 도메인을 사용 합니다. 

|동사|예제 URL 및 키 위치|
|--|--|
|[GET](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>`runtime-key`의 쿼리 문자열 값<br><br>LUIS 엔드포인트 키 할당량 요금을 사용하려면 작성(시작) 키에서 새 엔드포인트 키로 `runtime-key`의 엔드포인트 쿼리 값을 변경합니다. 키를 만들고 할당하지만 `runtime-key`의 엔드포인트 쿼리 값을 변경하지 않는 경우에는 엔드포인트 키 할당량을 사용하지 않습니다.|
|[POST](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> `Ocp-Apim-Subscription-Key`의 헤더 값<br>런타임 키를 만들고 런타임 키를 할당 하지만의 끝점 쿼리 값을 변경 하지 않는 경우에 `Ocp-Apim-Subscription-Key`는 런타임 키를 사용 하지 않습니다.|

이전 URL에 사용되던 앱 ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2`는 [대화형 데모](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)에 사용되는 공용 IoT 앱입니다. 

## <a name="assignment-of-the-runtime-key"></a>런타임 키 할당

[LUIS 포털](https://www.luis.ai) 에서 또는 해당 api를 통해 런타임 키를 [할당할](luis-how-to-azure-subscription.md) 수 있습니다. 

## <a name="key-limits"></a>키 제한

구독 당 지역 당 최대 10 개의 제작 키를 만들 수 있습니다. 

[키 제한](luis-boundaries.md#key-limits) 및 [Azure 지역](luis-reference-regions.md)을 참조 하세요. 

게시 지역은 작성 지역과 다릅니다. 클라이언트 응용 프로그램을 배치할 게시 영역에 해당 하는 제작 지역에 앱을 만들어야 합니다.

## <a name="key-limit-errors"></a>키 제한 오류
TPS (초당 트랜잭션 수) 할당량을 초과 하는 경우 HTTP 429 오류를 받게 됩니다. 월별 트랜잭션 (TPS) 할당량을 초과 하는 경우 HTTP 403 오류가 표시 됩니다. 

## <a name="contributions-from-other-authors"></a>다른 작성자의 기여



공동 작업자의 기여 관리는 앱의 현재 상태에 따라 달라 집니다.

**[리소스 마이그레이션된 앱 제작](luis-migration-authoring.md)** : _참가자_ 는 **액세스 제어 (IAM)** 페이지를 사용 하 여 제작 리소스에 대 한 Azure Portal에서 관리 됩니다. 협력자의 이메일 주소 및 _참가자_ 역할을 사용 하 여 [사용자를 추가 하는 방법을](luis-how-to-collaborate.md)알아봅니다. 

**아직 마이그레이션하지 않은 앱의 경우**: 모든 _공동 작업자_ 는 LUIS 포털에서 관리 **-> 협력자** 페이지를 통해 관리 됩니다.

### <a name="contributor-roles-vs-entity-roles"></a>참가자 역할 vs 엔터티 역할

[엔터티 역할](luis-concept-roles.md) 은 LUIS 앱의 데이터 모델에 적용 됩니다. 협력자/기여자 역할은 제작 액세스 수준에 적용 됩니다. 

## <a name="moving-or-changing-ownership"></a>소유권 이동 또는 변경

앱은 소유자의 구독에 의해 결정 되는 Azure 리소스에 의해 정의 됩니다. 

LUIS 앱을 이동할 수 있습니다. Azure Portal 또는 Azure CLI에서 다음 설명서 리소스를 사용 합니다.

* [LUIS authoring resources 간에 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/resource-group-move-resources.md)
* [동일한 구독 내 또는 구독 간에 리소스 이동](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* 구독의 [소유권 이전](../../billing/billing-subscription-transfer.md) 

## <a name="access-for-private-and-public-apps"></a>개인 및 공용 앱에 대 한 액세스

**개인** 앱의 경우 소유자 및 참가자에 대 한 런타임 액세스를 사용할 수 있습니다. **공용** 응용 프로그램의 경우 고유한 Azure [인식 서비스](../cognitive-services-apis-create-account.md) 또는 [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 런타임 리소스를 포함 하 고 공용 앱 ID를 가진 모든 사용자가 런타임 액세스를 사용할 수 있습니다. 

현재는 공용 앱의 카탈로그가 없습니다.

### <a name="authoring-access"></a>작성 액세스
[LUIS](luis-reference-regions.md#luis-website) 포털에서 앱에 액세스 하거나 [제작 api](https://go.microsoft.com/fwlink/?linkid=2092087) 는 Azure authoring resource에 의해 제어 됩니다. 

소유자와 모든 참가자는 앱을 제작할 수 있는 권한이 있습니다. 

|작성 액세스에 포함된 작업|참고|
|--|--|
|엔드포인트 키 추가 또는 제거||
|버전 내보내기||
|엔드포인트 로그 내보내기||
|버전 가져오기||
|앱 공개|앱이 공용이면 작성 또는 엔드포인트 키가 있는 모든 사용자가 앱을 쿼리할 수 있습니다.|
|모델 수정|
|게시|
|[활성 학습](luis-how-to-review-endpoint-utterances.md)의 엔드포인트 발화 검토|
|기차|

### <a name="prediction-endpoint-runtime-access"></a>예측 끝점 런타임 액세스

쿼리 액세스는 **관리** 섹션의 **응용 프로그램 정보** 페이지에 있는 설정에 의해 제어 됩니다. 

![앱을 공용으로 설정](./media/luis-concept-security/set-application-as-public.png)

|[프라이빗 엔드포인트](#runtime-security-for-private-apps)|[공용 엔드포인트](#runtime-security-for-public-apps)|
|:--|:--|
|소유자 및 참가자가 사용할 수 있음|소유자, 참가자 및 앱 ID를 알고 있는 다른 사람이 사용할 수 있습니다.|

서버 간 환경에서 LUIS 런타임 키를 호출 하 여 해당 키를 볼 수 있는 사용자를 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API를 호출 하 여 인증 하 고 권한 부여를 확인 한 경우에는에 대 한 호출을 LUIS에 전달 합니다. 이 전략은 메시지 가로채기 (man-in-the-middle) 공격을 방지 하지는 않지만 사용자의 키 및 끝점 URL을 난독 처리 액세스를 추적할 수 있으며 끝점 응답 로깅 (예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가할 수 있습니다.

#### <a name="runtime-security-for-private-apps"></a>전용 앱에 대 한 런타임 보안

개인 앱의 런타임은 다음에만 사용할 수 있습니다.

|키 및 사용자|설명|
|--|--|
|소유자의 작성 키| 최대 1000회의 엔드포인트 적중|
|협력자/기여자 제작 키| 최대 1000회의 엔드포인트 적중|
|작성자 또는 협력자/기여자에 의해 LUIS에 할당 된 모든 키|키 사용량 계층 기반|

#### <a name="runtime-security-for-public-apps"></a>공용 앱에 대 한 런타임 보안

앱이 공용으로 구성되면 키에 전체 엔드포인트 할당량이 사용되지 않는 한 ‘모든’ 유효한 LUIS 작성 키 또는 LUIS 엔드포인트 키가 앱을 쿼리할 수 있습니다.

소유자 또는 참가자가 아닌 사용자는 앱 ID가 지정 된 경우에만 공용 앱의 런타임에 액세스할 수 있습니다. LUIS에는 공용 앱을 검색할 수 있는 공개 ‘시장’이나 다른 방법이 없습니다.  

공용 앱은 모든 지역에 게시되므로 지역 기반 LUIS 리소스 키를 가진 사용자가 해당 리소스 키와 연결된 지역에서 앱에 액세스할 수 있습니다.

## <a name="transfer-of-ownership"></a>소유권 이전

**[리소스 마이그레이션](luis-migration-authoring.md) 앱을 제작 하는 경우**: 

**아직 마이그레이션하지 않은 앱의 경우**: 앱을 JSON 파일로 내보냅니다. 다른 LUIS 사용자는 앱을 가져올 수 있으므로 앱 소유자가 됩니다. 새 앱에는 다른 앱 ID가 있습니다.  

## <a name="securing-the-endpoint"></a>엔드포인트 보안 

서버 간 환경에서 호출 하 여 LUIS 예측 런타임 끝점 키를 볼 수 있는 사람을 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고 인증 및 권한 부여가 확인되면 LUIS에 해당 호출을 전달합니다. 이 전략은 가로채기 공격을 방지하지 않지만 사용자의 엔드포인트를 난독 처리하고, 액세스를 추적하도록 허용하고, 엔드포인트 응답 로깅(예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가하도록 허용합니다.  

## <a name="next-steps"></a>다음 단계

* [버전 관리](luis-concept-version.md) 개념을 이해합니다. 
* [키를 만드는 방법](luis-how-to-azure-subscription.md)에 대해 알아봅니다.
