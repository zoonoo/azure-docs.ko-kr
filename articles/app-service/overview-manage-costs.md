---
title: App Service에 대 한 비용 관리 계획
description: Azure Portal에서 비용 분석을 사용 하 여 Azure App Service에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: ada4c1991a57c8252247c9617e097dc82cb3b4a9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593984"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Azure App Service에 대 한 비용 계획 및 관리

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

이 문서에서는 Azure App Service에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 Azure 가격 계산기를 사용 하 여 비용을 예측 하는 서비스에 대 한 리소스를 추가 하기 전에 App Service 비용을 계획할 수 있습니다. 그런 다음 Azure 리소스를 추가할 때 예상 비용을 검토 합니다. App Service 리소스 사용을 시작한 후에는 [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링 합니다. 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure App Service 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 App Service에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 하지만, 타사 서비스를 비롯 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다.

## <a name="relevant-costs-for-app-service"></a>App Service 관련 비용

App Service는 비용을 계산 하는 Azure 인프라에서 실행 됩니다. 추가 인프라가 비용을 계산 하는 것을 이해 하는 것이 중요 합니다. 배포 된 리소스를 변경할 때이 비용을 관리 해야 합니다.

### <a name="costs-that-accrue-with-azure-app-service"></a>Azure App Service로 계산 되는 비용

App Service에서 사용 하는 기능에 따라 다음과 같은 비용 발생 리소스가 만들어질 수 있습니다.

- **App Service 계획**  App Service 앱을 호스트 하는 데 필요 합니다.
- **격리 계층**  App Service 환경에 [Virtual Network](../virtual-network/index.yml) 필요 합니다.
- **백업**  백업을 수행 하려면 [저장소 계정이](../storage/index.yml) 필요 합니다.
- **진단 로그**  [저장소 계정을](../storage/index.yml) 로깅 옵션으로 선택 하거나 [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)와 통합할 수 있습니다.
- **인증서 App Service**  Azure에서 구입한 인증서는 [Azure Key Vault](../key-vault/index.yml)에서 유지 관리 해야 합니다.

App Service에 대 한 다른 비용 리소스 (자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/) 참조):

- [App Service 도메인](manage-custom-dns-buy-domain.md)  자동 갱신을 사용 하도록 설정 하는 경우 구독에는 매년 도메인 등록 요금이 청구 됩니다.
- [인증서 App Service](configure-ssl-certificate.md#import-an-app-service-certificate)  구매 시점의 일회성 요금입니다. 보호할 하위 도메인이 여러 개 있는 경우 여러 표준 인증서 대신 하나의 와일드 카드 인증서를 구매 하 여 비용을 절감할 수 있습니다.
- [IP 기반 인증서 바인딩](configure-ssl-bindings.md#create-binding)  바인딩은 앱 수준에서 인증서에 대해 구성 됩니다. 비용은 각 바인딩에 대해 계산 됩니다. **표준** 계층 이상의 경우 첫 번째 IP 기반 바인딩에는 요금이 부과 되지 않습니다.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>리소스 삭제 후 발생할 수 있는 비용

App Service 계획의 모든 앱을 삭제 하는 경우 계획은 구성 된 가격 책정 계층 및 인스턴스 수에 따라 요금이 계속 청구 됩니다. 원치 않는 요금을 방지 하려면 계획을 삭제 하거나 **무료** 계층으로 축소 합니다.

Azure App Service 리소스를 삭제 한 후에는 관련 Azure 서비스의 리소스가 계속 존재할 수 있습니다. 삭제할 때까지 비용이 계속 해 서 계산 됩니다. 다음은 그 예입니다. 

- **격리** 된 계층 App Service 계획에 대해 만든 Virtual Network
- 백업 또는 진단 로그를 저장 하기 위해 만든 저장소 계정
- App Service 인증서를 저장 하기 위해 만든 Key Vault
- 진단 로그를 발송 하기 위해 만든 분석 네임 스페이스 기록
- 아직 만료 되지 않은 App Service에 대 한 [인스턴스 또는 스탬프 예약](#azure-reservations)

### <a name="using-monetary-credit-with-azure-app-service"></a>Azure App Service에서 금액 크레딧 사용

Azure 선불 (이전에는 금액 약정 이라고 함) 크레딧을 사용 하 여 Azure App Service 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용 하 여 Azure Marketplace에서 비롯 한 타사 제품 및 서비스에 대 한 요금을 지불할 수 없습니다.

## <a name="estimate-costs"></a>비용 예측

사전에 App Service 비용을 예측 하 고 최적화 하는 쉬운 방법은 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용 하는 것입니다.

가격 계산기를 사용 하려면 **제품** 탭에서 **App Service** 를 클릭 합니다. 그런 다음 아래로 스크롤하여 계산기를 사용 합니다. 다음 스크린샷에서는 예제 이며 현재 가격은 반영 되지 않습니다.

![Azure 가격 계산기의 예상 비용을 보여 주는 예제](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

App Service 앱 또는 App Service 계획을 만드는 경우 예상 비용을 볼 수 있습니다.

앱을 만들고 예상 가격을 보려면 다음을 수행 합니다.

1. 만들기 페이지에서 **App Service 계획** 으로 스크롤하고 **새로 만들기** 를 클릭 합니다.
1. 이름을 지정 하 고 **확인** 을 클릭 합니다.
1. **Sku 및 크기** 옆에 있는 **크기 변경** 을 클릭 합니다.
1. 요약에 표시 된 예상 가격을 검토 합니다. 다음 스크린샷에서는 예제 이며 현재 가격은 반영 되지 않습니다.

    ![포털에서 각 가격 책정 계층에 대 한 예상 비용 검토](media/overview-manage-costs/pricing-estimates.png)

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과 하 여 지출 하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용 됩니다. 신용 한도에 도달 하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용 하지 않도록 설정 됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대 한 자세한 내용은 [Azure 지출 한도](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조 하세요.

## <a name="optimize-costs"></a>비용 최적화

기본 수준에서 App Service 앱은 해당 앱을 호스팅하는 App Service 요금제에 의해 부과 됩니다. App Service 배포와 관련 된 비용은 몇 가지 주요 요소에 따라 다릅니다.

- **가격 책정 계층**  그렇지 않으면 App Service 계획의 SKU 라고도 합니다. 상위 계층은 더 많은 CPU 코어, 메모리, 저장소 또는 기능을 제공 합니다.
- **인스턴스 수**  전용 계층 (기본 이상)을 확장할 수 있으며, 확장 된 각 인스턴스는 비용을 발생 시킬 수 있습니다.
- **스탬프 요금**  격리 계층에서는 호스트 되는 앱 또는 작업자 인스턴스 수에 관계 없이 App Service 환경에서 플랫 요금이 발생 합니다.

App Service 계획은 둘 이상의 앱을 호스트할 수 있습니다. 배포에 따라 한 App Service 계획에서 더 많은 앱을 호스트 하는 비용을 절감할 수 있습니다 (즉, 응용 프로그램을 더 작은 App Service 계획에 호스트).

자세한 내용은 [App Service 계획 개요](overview-hosting-plans.md) 를 참조 하세요.

### <a name="non-production-workloads"></a>비프로덕션 작업

저렴 하거나 최소한의 비용을 발생 하는 동시에 App Service 또는 솔루션을 테스트 하려면 공유 인스턴스에 호스트 되는 두 개의 항목 수준 가격 책정 계층, **무료** 및 **공유** 를 사용 하 여 시작할 수 있습니다. 더 나은 성능으로 전용 인스턴스에서 앱을 테스트 하려면 Windows 및 Linux 앱을 모두 지 원하는 **기본** 계층으로 업그레이드할 수 있습니다. 

> [!NOTE]
> **Azure 개발/테스트 가격 책정**  더 높은 계층 ( **격리** 를 제외한 모든 계층)을 요구 하는 프리 프로덕션 워크 로드를 테스트 하기 위해 Visual Studio 구독자는 상당한 할인을 제공 하는 [Azure 개발/테스트 가격](https://azure.microsoft.com/pricing/dev-test/)을 활용할 수도 있습니다.
>
> Azure 개발/테스트 가격 할인 뿐만 아니라 **무료** 및 **공유** 계층은 모두 재정적 지원 SLA를 제공 하지 않습니다.

### <a name="production-workloads"></a>프로덕션 워크로드

프로덕션 워크 로드는 전용 **표준** 가격 책정 계층의 권장 사항과 함께 제공 됩니다. 가격은 상위 계층에 대해 증가 하지만, 더 많은 메모리와 저장소와 더 높은 성능의 하드웨어를 제공 하 여 계산 인스턴스당 앱 밀도를 더 많이 제공 합니다. 그러면 동일한 수의 앱에 대해 더 낮은 인스턴스 수로 변환 되므로 비용이 줄어듭니다. 실제로 **프리미엄 V3** (**격리** 되지 않은 가장 높은 계층)은 대규모 앱을 제공 하는 가장 비용 효율적인 방법입니다. 절감 액에 추가 하기 위해 [Premium V3 예약](#azure-reservations)에 대 한 심층 할인을 얻을 수 있습니다.

> [!NOTE]
> **Premium V3** 은 Windows 컨테이너와 Linux 컨테이너를 모두 지원 합니다. 

원하는 가격 책정 계층을 선택한 후에는 유휴 인스턴스를 최소화 해야 합니다. 스케일 아웃 배포에서는 미달 사용 계산 인스턴스에 대 한 비용을 낭비 하 게 됩니다. **표준** 계층 이상에서 사용할 수 있는 자동 크기 [조정을 구성](../azure-monitor/autoscale/autoscale-get-started.md)해야 합니다. 메트릭 기반 스케일 아웃 규칙 뿐만 아니라 스케일 아웃 일정을 만들어 지정 된 시간에 정말 필요한 인스턴스에만 요금을 지불 하면 됩니다.

### <a name="azure-reservations"></a>Azure Reservations

1 년 이상에 대해 알려진 최소 수의 계산 인스턴스를 활용할 계획인 경우 **Premium V3** 계층을 활용 하 고 해당 인스턴스를 1 년 또는 3 년 단위로 예약 하 여 인스턴스 비용을 크게 줄일 수 있습니다. 월별 비용 절감은 인스턴스당 55% 일 수 있습니다. 두 가지 유형의 예약이 가능 합니다.

- **Windows (또는 플랫폼에 관계 없음)**  구독에서 Windows 또는 Linux 인스턴스에 적용할 수 있습니다.
- **Linux 전용**  구독의 Linux 인스턴스에만 적용 됩니다.

예약 된 인스턴스 가격은 예약 된 인스턴스 수까지 구독의 해당 인스턴스에 적용 됩니다. 예약 된 인스턴스는 청구에 관련 된 것 이며 특정 계산 인스턴스와 연결 되지 않습니다. 예약 기간 중 언제 든 지 예약 된 것 보다 많은 인스턴스를 실행 하는 경우 예약 된 인스턴스에 대 한 요금이 계속 청구 됩니다. 예약 기간 중 임의의 시점에 예약 된 것 보다 더 많은 인스턴스를 실행 하는 경우 추가 인스턴스에 대해 발생 하는 정상적인 비용을 지불 합니다.

**격리** 계층 (App Service 환경)은 가격 책정에서 1 년 및 3 년 예약도 지원 합니다. 자세한 내용은 [Azure App Service에 예약 할인이 적용 되는 방법](../cost-management-billing/reservations/reservation-discount-app-service.md)을 참조 하세요.

## <a name="monitor-costs"></a>비용 모니터링

App Service에서 Azure 리소스를 사용 하는 경우 비용이 발생 합니다. Azure 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일)에 따라 달라 집니다. App Service를 시작 하는 즉시 비용이 발생 하며 비용 [분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 볼 수 있습니다.

비용 분석을 사용 하면 서로 다른 시간 간격에 대 한 그래프 및 테이블의 App Service 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재 및 이전 달 및 연도가 있습니다. 또한 예산 및 예상 비용에 대 한 비용도 볼 수 있습니다. 시간이 지남에 따라 더 긴 뷰로 전환 하면 지출 추세를 식별 하는 데 도움이 됩니다. 그리고 과도 한 지출이 발생 한 위치를 확인할 수 있습니다. 예산을 만든 경우에는 해당 위치를 쉽게 확인할 수도 있습니다.
    
비용 분석에서 App Service 비용을 보려면 다음을 수행 합니다.

1. Azure Portal에 로그인합니다.
2. Azure Portal에서 범위를 열고 메뉴에서 **비용 분석** 을 선택 합니다. 예를 들어 **구독** 으로 이동 하 여 목록에서 구독을 선택한 다음 메뉴에서  **비용 분석** 을 선택 합니다. 비용 분석에서 다른 범위로 전환 하려면 **범위** 를 선택 합니다.
3. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시 됩니다. App Service 레이블 차트에서 영역을 선택 합니다.

처음에는 비용 분석을 열 때 실제 월별 비용이 표시 됩니다. 모든 월간 사용 비용을 보여 주는 예제는 다음과 같습니다.

![구독에 대 한 누적 비용을 보여 주는 예제](media/overview-manage-costs/all-costs.png)

App Service와 같은 단일 서비스에 대 한 비용을 좁히려면 **필터 추가** 를 선택 하 고 **서비스 이름** 을 선택 합니다. 그런 다음 **App Service** 를 선택 합니다.

App Service에 대 한 비용을 보여 주는 예제는 다음과 같습니다.

![ServiceName의 누적 비용을 보여 주는 예제](media/overview-manage-costs/service-specific-costs.png)

앞의 예제에서 서비스에 대 한 현재 비용이 표시 됩니다. Azure 지역 (위치) 및 리소스 그룹별 비용 App Service 비용도 표시 됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

## <a name="create-budgets"></a>예산 만들기

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

[예산을](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 만들어 비용을 관리 하 고 관련자에 게 잘못 된 지출 및 과도 한 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 

모니터링에 세분성이 더 필요한 경우 Azure의 특정 리소스 또는 서비스에 대 한 필터를 사용 하 여 예산을 만들 수 있습니다. 필터를 통해 실수로 추가 비용을 절감할 수 있는 새 리소스를 만들지 않도록 할 수 있습니다. 예산을 만들 때 사용할 수 있는 필터 옵션에 대 한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

[비용 데이터](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수도 있습니다. 이는 또는 다른 사용자가 비용에 대 한 더 많은 데이터 분석을 수행 해야 하는 경우에 유용 합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 대 한 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.

## <a name="next-steps"></a>다음 단계

- Azure Storage에서 가격 책정을 사용 하는 방법에 대해 자세히 알아보세요. [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.
- [Azure Cost Management를 사용 하 여 클라우드 투자를 최적화 하는 방법을](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)으로 비용 관리에 대해 자세히 알아보세요.
- [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법에 대해 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행 하세요.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->