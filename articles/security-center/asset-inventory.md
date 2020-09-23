---
title: Azure Security Center의 자산 인벤토리
description: 모든 Security Center 모니터링 되는 리소스에 대 한 완전 한 가시성을 제공 하는 Azure Security Center의 자산 관리 환경에 대해 알아봅니다.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: dfba8bc1713e14099413a6c01d0af8508ba0eb73
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895176"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Asset inventory 및 관리 도구를 사용 하 여 리소스 탐색 및 관리

Azure Security Center의 자산 인벤토리 페이지에서는 Security Center 연결 된 리소스의 보안 상태를 볼 수 있는 단일 페이지를 제공 합니다. 

Security Center는 Azure 리소스의 보안 상태를 주기적으로 분석 하 여 잠재적인 보안 취약성을 식별 합니다. 그런 다음 이러한 취약성을 해결 하는 방법에 대 한 권장 사항을 제공 합니다.

리소스에 해결 되지 않은 권장 사항이 있는 경우 인벤토리에 표시 됩니다.

이 뷰와 해당 필터를 사용 하 여 다음과 같은 질문을 해결 합니다.

- Azure Defender를 사용 하는 내 구독 중에는 권장 되는 권장 사항이 있나요?
- ' Production ' 태그가 있는 내 컴퓨터에 Log Analytics 에이전트가 누락 되었습니까?
- 특정 태그를 사용 하 여 태그가 지정 된 내 컴퓨터의 수가 해결 되지 않은 권장 사항은 몇 개입니까?
- 특정 리소스 그룹에 있는 리소스의 수는 취약성 평가 서비스의 보안 결과는 몇 개입니까?

이 도구에 대 한 자산 관리 가능성은 상당히 늘어나고 계속 증가 합니다. 

> [!TIP]
> 보안 권장 사항은 **권장 사항** 페이지의 경우와 동일 하지만 여기서는 선택한 특정 리소스 종류에 따라 필터링 됩니다. 권장 사항을 해결 하는 방법에 대 한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조 하세요.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|Free|
|필요한 역할 및 사용 권한:|모든 사용자가 액세스할 수 있습니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/no-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Asset inventory의 핵심 기능은 무엇 인가요?

인벤토리 페이지는 다음 도구를 제공 합니다.

- **요약** -필터를 정의 하기 전에 인벤토리 보기의 맨 위에 있는 값의 중요 한 스트립은 다음과 같이 표시 됩니다.

    - **Total resources**: Security Center에 연결 된 총 리소스 수입니다.
    - **비정상 리소스**: 활성 보안 권장 사항이 있는 리소스입니다. [보안 권장 사항에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - **모니터링**되지 않는 리소스: 에이전트 모니터링 문제를 포함 하는 리소스-Log Analytics 에이전트를 배포 했지만 에이전트가 데이터를 전송 하지 않거나 다른 상태 문제를 포함 하 고 있습니다.

- **필터** -페이지 맨 위의 여러 필터는 대답 하려는 질문에 따라 리소스 목록을 신속 하 게 구체화 하는 방법을 제공 합니다. 예를 들어 *' Production ' 태그가 있는 내 컴퓨터에 Log Analytics 에이전트가 누락 된* 경우 질문에 답변 하려면 다음 클립과 같이 **에이전트 모니터링** 필터를 **태그** 필터와 결합할 수 있습니다.

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="모니터링 되지 않는 프로덕션 리소스로 필터링":::

    필터를 적용 하는 즉시 요약 값은 쿼리 결과와 관련 하 여 업데이트 됩니다. 

- **내보내기 옵션** -인벤토리는 선택한 필터 옵션의 결과를 CSV 파일로 내보낼 수 있는 옵션을 제공 합니다. 또한 쿼리 자체를 Azure 리소스 그래프 탐색기로 내보내 KQL 쿼리를 구체화, 저장 또는 수정할 수 있습니다.

    ![인벤토리에 대 한 내보내기 옵션](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > KQL 설명서에서는 몇 가지 샘플 데이터와 함께 데이터베이스에 몇 가지 간단한 쿼리를 제공 하 여 언어에 대 한 "느낌"을 가져옵니다. [이 KQL 자습서에서 자세히 알아보세요](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **자산 관리 옵션** -인벤토리를 사용 하면 복잡 한 검색 쿼리를 수행할 수 있습니다. 쿼리와 일치 하는 리소스를 찾았으면 인벤토리는 다음과 같은 작업에 대 한 바로 가기를 제공 합니다.

    - 필터링 된 리소스에 태그 할당-태그를 지정할 리소스 옆에 있는 확인란을 선택 합니다.
    - Security Center에 새 서버 등록- **비 Azure 서버 추가** 도구 모음 단추를 사용 합니다.
    - Azure Logic Apps를 사용 하 여 작업을 자동화 합니다. **트리거 논리 앱** 단추를 사용 하 여 하나 이상의 리소스에서 논리 앱을 실행 합니다. 논리 앱을 미리 준비 하 고 관련 트리거 유형 (HTTP 요청)을 적용 해야 합니다. [논리 앱에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


## <a name="how-does-asset-inventory-work"></a>Asset inventory는 어떻게 작동 하나요?

Asset inventory는 여러 구독에서 Security Center의 보안 상태 데이터를 쿼리 하는 기능을 제공 하는 Azure 서비스인 [Azure 리소스 그래프 (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)를 활용 합니다.

ARG는 효율적인 리소스 탐색을 제공 하 여 대규모로 쿼리할 수 있도록 설계 되었습니다.

KQL ( [Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)를 사용 하 여 asset inventory는 다른 리소스 속성과 함께 ASC 데이터를 상호 참조 하 여 심층 통찰력을 신속 하 게 생성할 수 있습니다.


## <a name="how-to-use-asset-inventory"></a>Asset inventory를 사용 하는 방법

1. Security Center의 사이드바에서 **인벤토리**를 선택 합니다.

1. **이름으로 필터링** 상자를 사용 하 여 특정 리소스를 표시 하거나 아래에 설명 된 대로 필터를 사용 합니다.

1. 필터에서 관련 옵션을 선택 하 여 수행 하려는 특정 쿼리를 만듭니다.

    :::image type="content" source="./media/asset-inventory/inventory-filters.png" alt-text="인벤토리에 대 한 필터링 옵션" lightbox="./media/asset-inventory/inventory-filters.png":::

    기본적으로 리소스는 활성 보안 권장 사항의 수를 기준으로 정렬 됩니다.

    > [!IMPORTANT]
    > 각 필터의 옵션은 현재 선택한 구독의 리소스 **와** 다른 필터에서 선택한 항목에만 적용 됩니다.
    >
    > 예를 들어 구독을 하나만 선택 하 고 구독에 해결 되지 않은 보안 권장 사항 (비정상 리소스 0 개)이 포함 된 리소스가 없는 경우 **권장** 구성 필터에 옵션이 표시 되지 않습니다. 

1. 보안 검색에 **포함** 된 필터를 사용 하려면 ID, 보안 검사 또는 CVE name의 사용 가능한 텍스트를 검색 하 여 영향을 받는 리소스를 필터링 합니다.

    !["보안 결과 포함" 필터](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **보안 검색 결과** 에는 단일 값만 허용 하는 및 **태그** 필터가 포함 됩니다. 둘 이상의 필터를 사용 하려면 필터 **추가**를 사용 합니다.

1. **Azure Defender** 필터를 사용 하려면 하나 이상의 옵션 (해제, 설정 또는 부분)을 선택 합니다.

    - **외부** -Azure Defender 계획으로 보호 되지 않는 리소스입니다. 이러한 항목 중 하나를 마우스 오른쪽 단추로 클릭 하 고 업그레이드할 수 있습니다.

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="리소스를 마우스 오른쪽 단추로 클릭 하 여 Azure Defender로 업그레이드" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - Azure Defender 계획 **에** 의해 보호 되는 리소스
    - **부분** -일부 Azure Defender 계획을 사용 하지 않도록 설정 하는 **구독** 에 적용 됩니다. 예를 들어 다음 구독에는 5 개의 Azure Defender 계획이 사용 하지 않도록 설정 되어 있습니다. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Azure Defender에서 부분적으로 구독":::

1. 쿼리 결과를 자세히 검토 하려면 원하는 리소스를 선택 합니다.

1. 리소스 그래프 탐색기에서 현재 선택한 필터 옵션을 쿼리로 보려면 **리소스 그래프 탐색기에서 보기**를 선택 합니다.

    ![ARG의 인벤토리 쿼리](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 을 사용 하 여 이전에 정의 된 논리 앱을 실행 하려면 

1. 일부 필터를 정의 하 고 페이지를 연 경우에는 Security Center 결과를 자동으로 업데이트 하지 않습니다. 수동으로 페이지를 다시 로드 하거나 **새로 고침**을 선택 하지 않으면 리소스에 대 한 모든 변경 내용이 표시 된 결과에 영향을 주지 않습니다.


## <a name="faq---inventory"></a>FAQ-인벤토리

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>내 구독, 컴퓨터, 저장소 계정 등이 모두 표시 되지 않는 이유는 무엇 인가요?

인벤토리 보기는 클라우드 CSPM (보안 상태 관리) 관점에서 Security Center 연결 된 리소스를 나열 합니다. 필터는 사용자 환경의 모든 리소스를 반환 하지 않습니다. 해결 되지 않은 (또는 ' 활성 ') 권장 사항이 있는 항목만 있습니다. 

예를 들어 다음 스크린샷은 38 구독에 대 한 액세스 권한이 있는 사용자를 보여 주지만 현재 10 개만 권장 됩니다. 따라서 **리소스 유형 = 구독**을 기준으로 필터링 하는 경우 활성 권장 사항이 있는 10 개의 구독만 인벤토리에 표시 됩니다.

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="활성 권장 구성이 없는 경우 일부 sub가 반환 되지 않음":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>일부 리소스가 Azure Defender 또는 에이전트 모니터링 열에서 빈 값을 표시 하는 이유는 무엇 인가요?

일부 Security Center 모니터링 되는 리소스는 에이전트가 없습니다. 예를 들어 Azure Storage 계정 또는 PaaS 리소스 (예: 디스크, Logic Apps, Data Lake 분석 및 이벤트 허브)가 있습니다.

가격 책정 또는 에이전트 모니터링이 리소스와 관련이 없는 경우 해당 인벤토리의 열에는 아무것도 표시 되지 않습니다.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="일부 리소스는 에이전트 모니터링 또는 Azure Defender 열에서 빈 정보를 표시 합니다.":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Security Center의 자산 인벤토리 페이지에 대해 설명 했습니다.

관련 도구에 대 한 자세한 내용은 다음 페이지를 참조 하세요.

- [Azure 리소스 그래프 (인수)](https://docs.microsoft.com/azure/governance/resource-graph/)
- [Kusto Query Language(KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)