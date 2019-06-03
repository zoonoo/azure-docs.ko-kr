---
title: Azure의 Cloudyn 개요 | Microsoft Docs
description: Cloudyn은 Azure 및 다른 클라우드 리소스를 사용하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 8b989e5cf5b66d21c19d58f2f64fbba1927f5d69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792794"
---
# <a name="what-is-the-cloudyn-service"></a>Cloudyn 서비스란?

Microsoft 자회사인 Cloudyn은 Azure 리소스와 AWS 및 Google을 포함한 다른 클라우드 공급자에 대한 클라우드 사용량 및 비용을 추적할 수 있게 해줍니다. 이해하기 쉬운 대시보드 보고서는 비용 할당 및 쇼백/환불을 도울 수 있습니다. Cloudyn은 사용률이 낮은 리소스를 식별하여 관리하고 조정함으로써 클라우드 소비를 최적화하도록 도와줍니다.

소개 비디오를 시청하려면 [Azure Cloudyn 소개](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo)를 참조하세요.

Azure Cost Management는 Cloudyn과 비슷한 기능을 제공합니다. Azure Cost Management는 기본 Azure 비용 관리 솔루션입니다. 이 솔루션을 사용하면 비용을 분석하고, 예산을 작성 및 관리하고, 데이터를 내보내고, 비용 절약을 위한 최적화 권장 사항을 검토하고 필요한 조치를 취할 수 있습니다. 자세한 내용은 [Azure Cost Management](overview-cost-mgt.md)를 참조하세요.

[Azure Cost Management 및 Cloudyn 비디오](https://www.youtube.com/watch?v=PmwFWwSluh8)를 보고 비즈니스 요구 사항에 따라 Azure Cost Management 또는 Cloudyn을 사용해야 하는 경우 권장 사항을 확인합니다.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Azure Cost Management로 이동하는 Cloudyn 기능

Microsoft는 Cloudyn을 인수했으며 비용 관리 기능을 Cloudyn 포털에서 Azure로 온전히 마이그레이션하고 있습니다. 새 기능을 사용하려면 Azure Portal에 로그인한 후 Azure 서비스 목록에서 [비용 관리 및 청구](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)로 이동하세요. Cloudyn에 비해, 기본 환경은 향상된 성능과 약 8시간의 낮은 데이터 대기 시간을 제공합니다.

기업계약, 종량제 및 MSDN 제품 범주에 대한 Azure Cost Management로의 주요 기능 마이그레이션은 완료되었습니다. CSP 구독은 Azure Cost Management로 마이그레이션이 진행 중입니다.

아직 마이그레이션되지 않은 제품 범주가 있는 경우에는 Cloudyn 포털을 계속 사용해야 합니다. 모든 사용자가 Azure Cost Management를 사용할 수 있습니다.

| Microsoft Azure 제품 및 기능 | 권장되는 비용 관리 서비스 |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct(PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| AWS에 대한 클라우드 간 비용 분석 지원(미리 보기로 제공) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS 추천 | [Cloudyn](https://azure.cloudyn.com) |

다음 기능 중 일부는 Cloudyn에서 사용 가능하지만, 현재 Azure Cost Management에서 모든 기능을 사용할 수 있습니다.

- API
- Azure Compute 추천
- Azure Reservation 추천
- 예산
- 비용 분석
- Azure Storage 계정으로 데이터 내보내기
- 낮은 대기 시간
- Power BI 콘텐츠 팩 및 커넥터
- 리소스 태그 지원

## <a name="monitor-usage-and-spending"></a>사용량 및 소비 모니터링

조직이 시간이 지남에 따라 사용하는 리소스를 지불하기 때문에 사용량 및 소비를 모니터링하는 작업은 클라우드 인프라에 매우 중요합니다. 사용량이 규약 임계값을 초과하는 경우 즉시 예상치 못한 비용 초과분이 발생할 수 있습니다. 몇 가지 중요한 요소로 인해 임시 모니터링이 어려울 수 있습니다. 첫째, 평균 사용량에 따른 비용을 예상하는 작업에서는 주어진 청구 기간 동안 소비가 일관적으로 유지된다고 가정합니다. 둘째, 비용이 예산에 가깝거나 예산을 초과할 때 지출을 조정하라는 알림을 사전에 받는 것이 중요합니다. 또한 클라우드 서비스 공급자는 비용 예상 및 임계값이나 기간 간 비교 보고서를 제공하지 않을 수 있습니다.

보고서를 통해 소비를 모니터링하여 클라우드 사용량, 비용 및 추세를 분석하고 추적할 수 있습니다. 시간에 따른 보고서를 사용하여 일반 추세와 다른 이상을 감지할 수 있습니다. 클라우드 배포의 비효율성은 최적화 보고서에 표시됩니다. 비용 분석 보고서에서 비효율성을 확인할 수도 있습니다.

## <a name="manage-costs"></a>비용 관리

시간에 따라 사용량 및 비용을 분석하여 추세를 식별하는 경우 기록 데이터로 비용을 관리할 수 있습니다. 추세는 향후 소비량을 예측하는 데 사용됩니다. Cloudyn에는 유용한 예상 비용 보고서도 포함되어 있습니다.

비용 할당은 태그를 지정한 정책에 따라 비용을 분석하여 비용을 관리합니다. 사용자 지정 계정, 리소스 및 엔터티에 태그를 사용하여 비용을 구체화할 수 있습니다. Category Manager는 추가 거버넌스를 제공할 수 있는 태그를 구성합니다. 또한 쇼백/환불에 비용 할당을 사용하여 소비 행동에 영향을 주거나 테넌트 고객에게 요금을 청구하는 리소스 사용률 및 관련 비용을 표시합니다.

액세스 제어를 통해 사용자 및 팀이 필요한 Cost Management 데이터에만 액세스하도록 하여 비용을 관리할 수 있습니다. 엔터티 구조, 사용자 관리 및 받는 사람 목록을 포함한 예약된 보고서를 사용하여 액세스를 할당합니다.

경고는 비정상적인 지출 또는 과도한 지출이 발생할 때 자동으로 알려주어 비용을 관리하는 데 도움이 됩니다. 또한 경고는 이해 관계자에게 비정상 지출 및 과다 지출에 대한 위험을 자동으로 알릴 수 있습니다. 다양한 보고서는 예산 및 비용 임계값에 따라 경고를 지원합니다. 단, 경고는 현재 CSP 파트너 계정 또는 구독에 지원되지 않습니다.

## <a name="improve-efficiency"></a>효율성 향상

최적의 VM 사용을 확인하고 유휴 VM을 식별하거나 유휴 VM 및 Cloudyn과 연결되지 않은 디스크를 제거할 수 있습니다. 크기 조정 최적화 및 비효율성 보고서의 정보를 사용하여 유휴 VM의 크기를 줄이거나 제거하도록 계획할 수 있습니다. 단, 최적화 보고서는 현재 CSP 파트너 계정 또는 구독에 지원되지 않습니다.

AWS 예약 인스턴스를 프로비전한 경우 구매 권장 사항을 보고, 사용하지 않는 예약을 수정하고, 프로비전을 계획할 수 있는 최적화 보고서에서 예약된 인스턴스 사용률을 향상시킬 수 있습니다.


## <a name="next-steps"></a>다음 단계

이제 Cloudyn에 대해 알아보았으므로 다음 단계는 클라우드 환경을 등록하고 데이터를 탐색하기 시작하는 것입니다.

- [개별 Azure 구독 등록](quick-register-azure-sub.md)
