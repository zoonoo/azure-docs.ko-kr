---
title: LUIS를 사용하여 작성 및 런타임 키를 사용하는 방법
titleSuffix: Azure Cognitive Services
description: LUIS는 두 개의 키, 즉 작성 키를 사용하여 모델을 만들고 사용자 발언을 사용하여 예측 끝점을 쿼리하기 위한 런타임 키를 만듭니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221506"
---
# <a name="authoring-and-runtime-keys"></a>작성 및 런타임 키

언어 이해(LUIS)에는 두 가지 서비스와 API 집합이 있습니다. 

* 작성(이전의 프로그래밍 _방식)_
* 예측 런타임

작업할 서비스와 작업 방법에 따라 몇 가지 주요 유형이 있습니다.

## <a name="non-azure-resources-for-luis"></a>LUIS에 대한 Azure 이외의 리소스

### <a name="starter-key"></a>스타터 키

LUIS를 처음 사용할 때 **시작 키가** 만들어집니다. 이 리소스는 다음을 제공합니다.

* LUIS 포털 또는 API(SDK 포함)를 통한 무료 서비스 요청
* 브라우저, API 또는 SDK를 통해 매월 1,000개의 예측 엔드포인트 요청 무료

## <a name="azure-resources-for-luis"></a>LUIS용 Azure 리소스

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS는 세 가지 유형의 Azure 리소스를 허용합니다. 
 
|Key|목적|코그너티브 서비스`kind`|코그너티브 서비스`type`|
|--|--|--|--|
|[작성 키](#programmatic-key)|작성, 교육, 게시 및 테스트를 통해 응용 프로그램의 데이터에 액세스하고 관리합니다. 프로그래밍 방식으로 LUIS 앱을 작성하려는 경우 LUIS 작성 키를 만듭니다.<br><br>`LUIS.Authoring` 키의 목적은 다음을 허용하는 것입니다.<br>* 프로그래밍 방식으로 언어 이해 앱 과 모델, 교육 및 출판을 포함<br> * 기여자 역할에 사람을 할당하여 작성 [리소스에](#contributions-from-other-authors)대한 권한을 제어합니다.|`LUIS.Authoring`|`Cognitive Services`|
|[예측 키](#prediction-endpoint-runtime-key)| 쿼리 예측 끝점 요청입니다. 클라이언트 앱에서 시작 리소스에서 제공하는 1,000개 이상의 요청을 초과하여 예측을 요청하기 전에 LUIS 예측 키를 만듭니다. |`LUIS`|`Cognitive Services`|
|[코그너티브 서비스 멀티 서비스 리소스 키](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUIS 및 기타 지원되는 코그너티브 서비스와 공유된 쿼리 예측 끝점 요청입니다.|`CognitiveServices`|`Cognitive Services`|

리소스 생성 프로세스가 완료되면 LUIS 포털에서 앱에 [키를 할당합니다.](luis-how-to-azure-subscription.md)

게시 및 쿼리하려는 [지역에서](luis-reference-regions.md#publishing-regions) LUIS 앱을 작성해야 합니다.

> [!CAUTION]
> 편의를 위해 대부분의 샘플은 [할당량에서](luis-boundaries.md#key-limits)몇 가지 무료 예측 끝점 호출을 제공하기 때문에 [시작 키를](#starter-key) 사용합니다.  


### <a name="query-prediction-resources"></a>쿼리 예측 리소스

* 런타임 키는 모든 LUIS 앱 또는 특정 LUIS 앱에 사용할 수 있습니다. 
* LUIS 앱을 작성할 때 런타임 키를 사용하지 마십시오. 

LUIS 런타임 끝점은 두 가지 유형의 쿼리를 허용하며, 둘 다 예측 끝점 런타임 키를 사용하지만 다른 위치에 있습니다.

런타임에 액세스하는 데 사용되는 끝점은 다음 표에 표시된 `{region}` 리소스 의 지역에 고유한 하위 도메인을 사용합니다. 

## <a name="assignment-of-the-key"></a>키 할당

[LUIS 포털또는](https://www.luis.ai) 해당 API를 통해 런타임 키를 [할당할](luis-how-to-azure-subscription.md) 수 있습니다. 

## <a name="key-limits"></a>키 제한

구독당 리전당 최대 10개의 제작 키를 만들 수 있습니다. 

[키 제한](luis-boundaries.md#key-limits) 및 [Azure 지역](luis-reference-regions.md)을 참조하십시오. 

게시 지역은 작성 지역과 다릅니다. 클라이언트 응용 프로그램을 배치할 게시 영역에 해당하는 작성 영역에서 앱을 만들어야 합니다.

## <a name="key-limit-errors"></a>키 제한 오류
트랜잭션 초당(TPS) 할당량을 초과하면 HTTP 429 오류가 발생합니다. 월별 트랜잭션(TPS) 할당량을 초과하면 HTTP 403 오류가 발생합니다. 

## <a name="contributions-from-other-authors"></a>다른 저자의 기여

**[리소스 마이그레이션된](luis-migration-authoring.md) 앱 작성의 경우:** _기고자는_ **IAM(Access Control)** 페이지를 사용하여 작성 리소스에 대한 Azure 포털에서 관리됩니다. 공동 작업자의 전자 메일 주소와 _기여자_ 역할을 사용하여 [사용자를 추가하는 방법을](luis-how-to-collaborate.md)알아봅니다. 

**아직 마이그레이션되지 않은 앱의 경우**모든 _공동 작업자는_ -> 공동 작업자 관리 페이지에서 LUIS 포털에서 **관리됩니다.**

## <a name="move-transfer-or-change-ownership"></a>소유권 이동, 이전 또는 변경

앱은 소유자의 구독에 의해 결정되는 Azure 리소스에 의해 정의됩니다. 

LUIS 앱을 이동할 수 있습니다. Azure 포털 또는 Azure CLI에서 다음 설명서 리소스를 사용합니다.

* [LUIS 작성 리소스 간에 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [리소스를 새 리소스 그룹 또는 구독으로 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [동일한 구독 내에서 또는 구독 간에 리소스 이동](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

구독 [소유권을](../../cost-management-billing/manage/billing-subscription-transfer.md) 이전하려면 다음 을 수행합니다. 

**마이그레이션한 사용자의 경우 - [리소스 마이그레이션된](luis-migration-authoring.md) 앱 작성**: 리소스의 소유자로서 `contributor`을 추가할 수 있습니다.

**아직 마이그레이션하지 않은 사용자의 경우**: 앱을 JSON 파일로 내보냅니다. 다른 LUIS 사용자는 앱을 가져올 수 있어 앱 소유자가 됩니다. 새 앱에는 다른 앱 ID가 있습니다.  

## <a name="access-for-private-and-public-apps"></a>프라이빗 및 퍼블릭 앱에 대한 액세스

**개인** 앱의 경우 소유자 및 참여자가 런타임 액세스를 사용할 수 있습니다. **공용** 앱의 경우 자체 Azure [인지 서비스](../cognitive-services-apis-create-account.md) 또는 [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 런타임 리소스가 있고 공용 앱의 ID가 있는 모든 사용자가 런타임 액세스를 사용할 수 있습니다. 

현재 공용 앱 카탈로그가 없습니다.

### <a name="authoring-access"></a>액세스 작성
[LUIS](luis-reference-regions.md#luis-website) 포털 또는 [작성 API에서](https://go.microsoft.com/fwlink/?linkid=2092087) 앱에 대한 액세스는 Azure 작성 리소스에 의해 제어됩니다. 

소유자와 모든 참여자는 앱을 작성할 수 있습니다. 

|작성 액세스에 포함된 작업|메모|
|--|--|
|엔드포인트 키 추가 또는 제거||
|버전 내보내기||
|엔드포인트 로그 내보내기||
|버전 가져오기||
|앱 공개|앱이 공용이면 작성 또는 엔드포인트 키가 있는 모든 사용자가 앱을 쿼리할 수 있습니다.|
|모델 수정|
|게시|
|[활성 학습](luis-how-to-review-endpoint-utterances.md)의 엔드포인트 발화 검토|
|학습|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>예측 엔드포인트 런타임 액세스

예측 끝점을 쿼리하기 위한 액세스는 **관리** 섹션의 **응용 프로그램 정보** 페이지의 설정에 의해 제어됩니다. 

|[프라이빗 엔드포인트](#runtime-security-for-private-apps)|[공용 엔드포인트](#runtime-security-for-public-apps)|
|:--|:--|
|소유자 및 기여자가 사용할 수 있음|소유자, 기여자 및 앱 ID를 아는 모든 사람이 사용할 수 있습니다.|

LUIS 런타임 키가 서버 간 환경에서 호출하여 표시되는 사람을 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 측 API가 호출되고 인증되고 인증이 확인되면 LUIS에 호출을 전달합니다. 이 전략은 중간자 공격을 방지하지는 않지만 사용자로부터 키 및 끝점 URL을 난독화하고 액세스를 추적할 수 있으며 끝점 응답 로깅(예: [응용 프로그램 인사이트)을](https://azure.microsoft.com/services/application-insights/)추가할 수 있습니다.

#### <a name="runtime-security-for-private-apps"></a>개인 앱에 대한 런타임 보안

개인 앱의 런타임은 다음 에서만 사용할 수 있습니다.

|키 및 사용자|설명|
|--|--|
|소유자의 작성 키| 최대 1000회의 엔드포인트 적중|
|공동 작업자/기고자 작성 키| 최대 1000회의 엔드포인트 적중|
|작성자 또는 공동 작업자/기여자가 LUIS에 할당한 모든 키|키 사용량 계층 기반|

#### <a name="runtime-security-for-public-apps"></a>공용 앱에 대한 런타임 보안

앱이 공용으로 구성되면 키에 전체 엔드포인트 할당량이 사용되지 않는 한 ‘모든’ 유효한 LUIS 작성 키 또는 LUIS 엔드포인트 키가 앱을 쿼리할 수 있습니다.__

소유자 또는 기여자가 아닌 사용자는 앱 ID가 지정된 경우에만 공용 앱의 런타임에 액세스할 수 있습니다. LUIS에는 공용 앱을 검색할 수 있는 공개 ‘시장’이나 다른 방법이 없습니다.__  

공용 앱은 모든 지역에 게시되므로 지역 기반 LUIS 리소스 키를 가진 사용자가 해당 리소스 키와 연결된 지역에서 앱에 액세스할 수 있습니다.

## <a name="transfer-of-ownership"></a>소유권 이전

LUIS는 리소스의 소유권을 이전하는 개념이 없습니다. 

## <a name="securing-the-endpoint"></a>엔드포인트 보안 

LUIS 예측 런타임 키를 서버 간 환경에서 호출하여 볼 수 있는 사람을 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고 인증 및 권한 부여가 확인되면 LUIS에 해당 호출을 전달합니다. 이 전략은 가로채기 공격을 방지하지 않지만 사용자의 엔드포인트를 난독 처리하고, 액세스를 추적하도록 허용하고, 엔드포인트 응답 로깅(예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가하도록 허용합니다.  

## <a name="next-steps"></a>다음 단계

* [버전 관리](luis-concept-version.md) 개념을 이해합니다. 
* [키를 만드는 방법에](luis-how-to-azure-subscription.md)대해 알아봅니다.
