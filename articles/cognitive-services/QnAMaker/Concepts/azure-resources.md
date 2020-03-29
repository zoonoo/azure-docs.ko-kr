---
title: Azure 리소스 - QnA 메이커
description: QnA Maker는 서로 다른 용도로 각각 여러 Azure 원본을 사용합니다. 개별적으로 사용되는 방식을 이해하면 올바른 가격 책정 계층을 계획하고 선택하거나 가격 책정 계층을 변경할 시기를 알 수 있습니다. 이러한 사용 방식이 조합되어 있는 방식을 이해하면 문제가 발생할 때 문제를 찾아 해결할 수 있습니다.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 8a5cc0f4889e31470514015035a92d230c40ed43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284248"
---
# <a name="azure-resources-for-qna-maker"></a>QnA 메이커를 위한 Azure 리소스

QnA Maker는 서로 다른 용도로 각각 여러 Azure 원본을 사용합니다. 개별적으로 사용되는 방식을 이해하면 올바른 가격 책정 계층을 계획하고 선택하거나 가격 책정 계층을 변경할 시기를 알 수 있습니다. 이러한 사용 방식이 _조합되어_ 있는 방식을 이해하면 문제가 발생할 때 문제를 찾아 해결할 수 있습니다.

## <a name="resource-planning"></a>리소스 계획

프로토타입 단계에서 QnA Maker 기술 기반을 처음 개발할 때는 테스트 및 프로덕션 모두에 대해 단일 QnA Maker 리소스를 두는 것이 일반적입니다.

프로젝트의 개발 단계로 이동할 때 다음을 고려해야 합니다.

* 지식 기반 시스템이 보유할 언어 수
* 기술 자료가 필요한 지역 수에 대해
* 시스템이 보유할 각 도메인의 문서 수

단일 QnA Maker 리소스에 동일한 언어, 동일한 지역 및 동일한 주제 도메인 조합을 가진 모든 기술 자료가 보유하도록 계획합니다.

## <a name="pricing-tier-considerations"></a>가격 책정 계층 고려 사항

일반적으로 고려해야 하는 다음 세 가지 매개 변수가 있습니다.

* **서비스에서 필요한 처리량**:
    * 필요에 따라 App Service에 적합한 [앱 플랜](https://azure.microsoft.com/pricing/details/app-service/plans/)을 선택합니다. 앱을 [확장](https://docs.microsoft.com/azure/app-service/manage-scale-up)하거나 축소할 수 있습니다.
    * 이것은 또한 Azure **인지 검색** SKU 선택에 영향을 주어야 하며 자세한 내용은 [여기를](https://docs.microsoft.com/azure/search/search-sku-tier)참조하십시오. 또한 복제본을 사용하여 인지 검색 [용량을](../../../search/search-capacity-planning.md) 조정해야 할 수도 있습니다.

* **기술 자료의 크기 및 개수**: 시나리오에 적합한 [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/)를 선택합니다. 일반적으로 다양한 주제 도메인 의 수에 따라 필요한 기술 자료 수를 결정합니다. 일단 주제 도메인(단일 언어의 경우)은 하나의 기술 자료에 있어야 합니다.

    특정 계층에 N-1개의 기술 자료를 게시할 수 있습니다. 여기서 N은 계층에서 허용되는 최대 인덱스 개수입니다. 또한 계층당 허용되는 문서의 최대 크기 및 개수를 확인합니다.

    예를 들어 계층에 허용되는 인덱스가 15개 있으면 기술 자료 14개를 게시할 수 있습니다(게시되는 기술 자료당 인덱스 1개). 15번째 인덱스는 모든 기술 자료에서 작성 및 테스트용으로 사용됩니다.

* **원본 문서 수**: QnA Maker 관리 서비스의 체험 SKU는 포털 및 API를 통해 관리할 수 있는 문서 수를 3개(각각 1MB 크기)로 제한합니다. 표준 SKU는 관리할 수 있는 문서 수에 대한 제한이 없습니다. 자세한 내용은 [여기](https://aka.ms/qnamaker-pricing)를 참조하세요.

다음 표에서는 몇 가지 전반적인 지침을 제공합니다.

|                        | QnA Maker 관리 | App Service | Azure Cognitive Search | 제한 사항                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 실험        | 체험 SKU             | 체험 계층   | 체험 계층    | KB 최대 2개, 50MB 크기까지 게시  |
| 개발/테스트 환경   | 표준 SKU         | Shared      | Basic        | 최대 14KB, 2GB 크기까지 게시    |
| 프로덕션 환경 | 표준 SKU         | Basic       | Standard     | KB 최대 49개, 25GB 크기까지 게시 |

## <a name="when-to-change-a-pricing-tier"></a>가격 책정 계층을 변경하는 시기

|업그레이드|이유|
|--|--|
|[업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA 메이커 관리 SKU|기술 자료에 더 많은 QnA 집합 또는 문서 원본을 갖고 싶습니다.|
|[업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) 앱 서비스 SKU 및 인지 검색 계층을 확인하고 [인지 검색 복제본 만들기](../../../search/search-capacity-planning.md)|기술 자료는 챗봇과 같은 클라이언트 앱의 더 많은 요청을 제공해야 합니다.|
|[업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure 인지 검색 서비스|많은 지식 기반을 가질 계획입니다.|

[Azure 포털에서 앱 서비스를 업데이트하여](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)최신 런타임 업데이트를 가져옵니다.

## <a name="resource-naming-considerations"></a>리소스 명명 고려 사항

와 같은 `qna-westus-f0-b`QnA Maker 리소스의 리소스 이름은 다른 리소스의 이름을 지정하는 데도 사용됩니다.

Azure 포털 만들기 창을 사용하면 QnA Maker 리소스를 만들고 다른 리소스에 대한 가격 책정 계층을 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![QnA 메이커 리소스 생성을 위한 Azure 포털의 스크린샷](../media/concept-azure-resource/create-blade.png)

리소스를 만든 후 이름에 문자를 연기하는 선택적 Application Insights 리소스를 제외하고 는 동일한 이름을 갖습니다.

> [!div class="mx-imgBorder"]
> ![Azure 포털 리소스 목록의 스크린샷](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> QnA Maker 리소스를 만들 때 새 리소스 그룹을 만듭니다. 이렇게 하면 리소스 그룹별로 검색할 때 QnA Maker 리소스와 연결된 모든 리소스를 볼 수 있습니다.

> [!TIP]
> 명명 규칙을 사용하여 리소스 또는 리소스 그룹의 이름 내에서 가격 책정 계층을 나타냅니다. 새 기술 자료 만들기 또는 새 문서 추가로 오류가 발생하면 코그너티브 검색 가격 책정 계층 제한이 일반적인 문제입니다.

## <a name="resource-purposes"></a>리소스 목적

QnA Maker로 만든 각 Azure 리소스에는 특정 목적이 있습니다.

* QnA Maker 리소스
* 인지 검색 리소스
* App Service
* 앱 계획 서비스
* 애플리케이션 인사이트 서비스


### <a name="cognitive-search-resource"></a>인지 검색 리소스

[인지 검색](../../../search/index.yml) 리소스는 다음과 같은 데 사용됩니다.

* QnA 세트 저장
* 런타임시 QnA 세트의 초기 순위(순위 #1)를 제공합니다.

#### <a name="index-usage"></a>인덱스 사용

리소스는 하나의 인덱스가 테스트 인덱스역할을 하도록 유지하고 나머지 인덱스는 각각 게시된 기술 자료 하나와 상관 관계를 유지합니다.

15개의 인덱스를 보유하도록 책정된 리소스는 14개의 게시된 기술 기반을 보유하며 하나의 인덱스는 모든 기술 기반을 테스트하는 데 사용됩니다. 이 테스트 인덱스는 대화형 테스트 창을 사용하는 쿼리가 테스트 인덱스를 사용하지만 특정 기술 자료와 연결된 특정 파티션의 결과만 반환되도록 기술 자료별로 분할됩니다.

#### <a name="language-usage"></a>언어 사용

QnA Maker 리소스에서 만든 첫 번째 기술 자료는 인지 검색 리소스 및 모든 인덱스에 대한 _단일_ 언어 집합을 결정하는 데 사용됩니다. QnA Maker 서비스에 대해 _하나의 언어만 설정할_ 수 있습니다.

### <a name="qna-maker-resource"></a>QnA Maker 리소스

QnA Maker 리소스는 런타임에 QnA 세트의 저작 및 게시 API와 NLP 기반 의 두 번째 순위 계층(랭커 #2)에 대한 액세스를 제공합니다.

두 번째 순위에는 메타데이터 및 후속 프롬프트를 포함할 수 있는 지능형 필터가 적용됩니다.

#### <a name="qna-maker-resource-configuration-settings"></a>QnA 메이커 리소스 구성 설정

[QnA Maker 포털에서](https://qnamaker.ai)새 기술 기반을 만들 때 **언어** 설정은 리소스 수준에서 적용되는 유일한 설정입니다. 리소스에 대한 첫 번째 기술 기반을 만들 때 언어를 선택합니다.

### <a name="app-service-and-app-service-plan"></a>앱 서비스 및 앱 서비스 계획

[앱 서비스는](../../../app-service/index.yml) 클라이언트 응용 프로그램에서 런타임 엔드포인트를 통해 게시된 기술 자료에 액세스하는 데 사용됩니다.

게시된 기술 자료는 게시된 모든 기술 자료가 동일한 URL 끝점을 사용하지만 경로 내에서 **기술 자료 ID를** 지정합니다.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>애플리케이션 정보

[응용 프로그램 인사이트는](../../../azure-monitor/app/app-insights-overview.md) 채팅 로그 및 원격 분석을 수집하는 데 사용됩니다. 서비스에 대한 자세한 내용은 일반적인 [Kusto 쿼리를](../how-to/get-analytics-knowledge-base.md) 검토합니다.

## <a name="share-services-with-qna-maker"></a>QnA 메이커와 서비스 공유

QnA Maker는 여러 Azure 리소스를 만듭니다. 관리 및 비용 공유의 이점을 줄이려면 다음 표를 사용하여 공유할 수 있는 것과 공유할 수 없는 내용을 이해하십시오.

|서비스|공유|이유|
|--|--|--|
|Cognitive Services|X|디자인상 불가능|
|App Service 계획|✔|앱 서비스 계획에 할당된 디스크 공간을 고정했습니다. 동일한 앱 서비스 계획을 공유하는 다른 앱이 상당한 디스크 공간을 사용하는 경우 QnAMaker 앱 서비스 인스턴스에 문제가 발생합니다.|
|App Service|X|디자인상 불가능|
|애플리케이션 정보|✔|공유할 수 있습니다.|
|Search 서비스|✔|1. `testkb` QnAMaker 서비스의 예약된 이름입니다. 다른 사용자가 사용할 수 없습니다.<br>2. 이름으로 `synonym-map` 된 동의어 맵은 QnAMaker 서비스를 위해 예약되어 있습니다.<br>3. 게시된 기술 자료의 수는 검색 서비스 계층에 의해 제한됩니다. 사용 가능한 무료 인덱스가 있는 경우 다른 서비스에서 사용할 수 있습니다.|

### <a name="using-a-single-cognitive-search-service"></a>단일 코그너티브 검색 서비스 사용

포털을 통해 QnA 서비스와 해당 종속성(예: 검색)을 만드는 경우 검색 서비스가 만들어지고 QnA Maker 서비스에 연결됩니다. 이러한 리소스를 만든 후 앱 서비스 설정을 업데이트하여 기존 검색 서비스를 사용하고 방금 만든 리소스를 제거할 수 있습니다.

QnA [Maker를 구성하여](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker 리소스 생성 프로세스의 일부로 만든 것과 다른 코그너티브 서비스 리소스를 사용하도록 구성하는 방법을 알아봅니다.

## <a name="management-service-region"></a>관리 서비스 지역

QnA Maker의 관리 서비스는 QnA Maker 포털 및 초기 데이터 처리에만 사용됩니다. 이 서비스는 **미국 서부** 지역에서만 사용할 수 있습니다. 이 미국 서부 서비스에는 고객 데이터가 저장되지 않습니다.

## <a name="keys-in-qna-maker"></a>QnA 메이커의 키

QnA Maker 서비스는 앱 서비스에서 호스팅되는 런타임에 사용되는 **키** 작성 및 **쿼리 끝점 키의** 두 가지 키를 다룹니다.

**구독 키를**찾고 있는 경우 [용어가 변경되었습니다.](#subscription-keys)

API를 통해 서비스에 요청할 때 이러한 키를 사용합니다.

![키 관리](../media/qnamaker-how-to-key-management/key-management.png)

|이름|위치|목적|
|--|--|--|
|작성 키|[Azure 포털](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|이러한 키는 [QnA Maker 관리 서비스 API](https://go.microsoft.com/fwlink/?linkid=2092179)에 액세스하는 데 사용됩니다. 이러한 API를 사용하면 기술 자료의 질문과 답변을 편집하고 기술 자료게시할 수 있습니다. 이러한 키는 새 QnA Maker 서비스를 만들 때 만들어집니다.<br><br>**키** 페이지에서 **인지 서비스** 리소스에서 이러한 키를 찾습니다.|
|쿼리 끝점 키|[QnA Maker 포털](https://www.qnamaker.ai)|이러한 키는 게시된 기술 자료 끝점을 쿼리하여 사용자 질문에 대한 응답을 얻는 데 사용됩니다. 일반적으로 채팅 봇 또는 QnA Maker 서비스에 연결하는 클라이언트 응용 프로그램 코드에서 이 쿼리 끝점을 사용합니다. 이러한 키는 QnA Maker 기술 자료작성시 만들어집니다.<br><br>**서비스 설정** 페이지에서 이러한 키를 찾습니다. 드롭다운 메뉴의 페이지 오른쪽 상단에 있는 사용자 메뉴에서 이 페이지를 찾습니다.|

### <a name="subscription-keys"></a>구독 키

용어 작성 및 쿼리 끝점 키수정 용어입니다. 이전 기간은 **구독 키였습니다.** 구독 키를 참조하는 다른 설명서가 표시되면 런타임에 사용되는 끝점 키 작성 및 쿼리와 동일합니다.

어떤 키를 찾아야 하는지 알기 위해서는 키가 액세스하는 키, 기술 자료 관리 또는 기술 자료 쿼리를 알고 있어야 합니다.

## <a name="next-steps"></a>다음 단계

* QnA 메이커 [기술 자료에](knowledge-base.md) 대해 알아보기
* 기술 [자료 수명 주기](development-lifecycle-knowledge-base.md) 이해
* 서비스 및 기술 자료 [한도](../limits.md) 검토

