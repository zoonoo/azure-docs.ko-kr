---
title: Azure Cost Management 개요 | Microsoft Docs
description: Azure Cost Management는 Azure 지출을 모니터링 및 제어하고 리소스 사용을 최적화하도록 지원하는 비용 관리 솔루션입니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 6615c6d8912195dbecda727bbdb31dcf04898ea2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792821"
---
# <a name="what-is-azure-cost-management"></a>Azure Cost Management란?

비용 관리는 비즈니스 관련 비용을 효율적으로 계획하고 관리하는 프로세스입니다. 비용 관리 태스크는 일반적으로 재무, 관리 및 앱 팀에서 수행합니다. Azure Cost Management는 조직에서 비용을 염두에 두고 계획을 세울 수 있도록 도와줍니다. 또한 비용을 효율적으로 분석하고 클라우드 지출을 최적화하기 위한 작업을 수행하는 데도 도움이 됩니다. 조직 차원에서 비용 관리에 접근하는 방법에 대한 자세한 내용은 [Azure Cost Management 모범 사례](cost-mgt-best-practices.md) 문서를 참조하세요.

Azure Cost Management가 Azure에서 비용을 절약하는 방법에 대한 간략한 개요를 보려면 [Azure Cost Management 개요 비디오](https://www.youtube.com/watch?v=el4yN5cHsJ0)를 시청하세요.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

관련은 있지만 청구와 비용 관리는 다릅니다. 청구는 상품이나 서비스에 대한 비용을 고객에게 청구하고 상업적 관계를 관리하는 프로세스입니다.  일반적으로 조달 및 재무 팀이 청구 작업을 수행합니다.

Cost Management는 고급 분석을 통해 조직의 비용 및 사용 패턴을 보여 줍니다. Cost Management의 보고서에는 Azure 서비스 및 타사 Marketplace 제품에서 사용된 사용량 기준 비용이 표시됩니다. 비용은 예약 및 Azure 하이브리드 혜택 할인의 협상된 가격 및 비율을 기준으로 결정됩니다. 전체적으로 이 보고서는 사용량 및 Azure Marketplace 요금에 대한 내부 및 외부 비용을 표시합니다. 예약 구매, 지원 및 세금과 같은 다른 요금은 보고서에 아직 표시되지 않았습니다. 이러한 보고서는 지출 및 리소스 사용을 이해하는 데 도움이 되고 지출 문제를 찾는 데도 도움이 될 수 있습니다. 예측 분석도 사용할 수 있습니다. Cost Management는 Azure 관리 그룹, 예산 및 권장 사항을 사용하여 지출이 구성되는 방식과 비용을 줄일 수 있는 방법을 명확히 보여 줍니다.

내보내기 자동화를 위해 Azure Portal 또는 다양한 API를 사용하여 비용 데이터를 외부 시스템 및 프로세스와 통합할 수 있습니다. 자동화된 청구 데이터 내보내기 및 예약된 보고서도 사용할 수 있습니다.

## <a name="plan-and-control-expenses"></a>비용 계획 및 제어

Cost Management를 통해 비용을 계획하고 제어하는 방법에는 원가 분석, 예산, 추천 및 Cost Management 데이터 내보내기가 포함됩니다.

비용 분석을 사용하여 조직 비용을 탐색하고 분석합니다. 조직의 집계된 비용을 확인하여 어느 부분에서 비용이 누적되었는지를 이해하고 지출 패턴을 식별할 수 있습니다. 또한 시간에 따라 누적된 비용을 확인하여 예산을 기준으로 월 단위, 분기 단위 또는 연 단위 비용 추세를 평가할 수 있습니다.

예산은 조직에서 재무 책임을 계획하고 충족하는 데 도움이 됩니다. 또한 비용 임계값 또는 한도를 초과하지 않도록 하는 데 도움이 됩니다. 또한 예산은 다른 사람에게 지출에 대한 정보를 제공하여 비용을 사전에 관리하는 데 도움을 줄 수 있습니다. 또한 시간이 지남에 따라 지출이 진행되는 방식을 확인할 수 있습니다.

권장 사항은 유휴 및 미달 사용 리소스를 식별하여 최적화를 수행하고 효율성을 개선하는 방법을 보여 줍니다. 또는 비용이 덜 드는 리소스 옵션을 표시할 수 있습니다. 권장 사항에 따라 작업을 수행하면서 리소스 사용 방식을 변경하여 금액을 절약할 수 있습니다. 작업을 수행하려면 먼저 비용 최적화 권장 사항을 검토하여 잠재적인 사용 비효율성을 확인합니다. 다음으로, 권장 사항에 따라 작업하여 Azure 리소스 사용을 좀 더 비용 효율적인 옵션으로 수정합니다. 그런 다음, 해당 작업을 확인하여 수행하는 변경이 성공적인지 검토합니다.

외부 시스템을 사용하여 비용 관리 데이터를 액세스 및 검토하는 경우 Azure에서 데이터를 쉽게 내보낼 수 있습니다. 또한 일별로 예약된 내보내기를 CSV 형식으로 설정하고 데이터 파일을 Azure Storage에 저장할 수 있습니다. 그런 다음, 외부 시스템에서 데이터에 액세스할 수 있습니다.

## <a name="consider-cloudyn"></a>Cloudyn 고려

[Cloudyn](overview.md)은 Cost Management와 관련된 Azure 서비스입니다. Cloudyn을 사용하면 Azure 리소스에 대한 클라우드 사용 및 지출을 추적할 수 있습니다. 또한 AWS 및 Google을 비롯한 다른 클라우드 공급자도 지원됩니다. 이해하기 쉬운 대시보드 보고서는 비용 할당 및 쇼백/환불을 도울 수 있습니다. 현재, Cost Management는 쇼백/차지백 또는 기타 클라우드 서비스 공급자를 지원하지 않습니다. 그러나 Cloudyn은 이러한 기능 및 공급자를 _지원하는_ 옵션입니다. 현재, Cost Management는 Microsoft CSP(클라우드 서비스 공급자) 계정을 지원하지 않지만 Cloudyn은 이 계정을 지원합니다. CSP 계정이 있거나 쇼백/차지백을 사용하려는 경우 Cloudyn을 사용하여 비용을 관리할 수 있습니다.

[Azure Cost Management 및 Cloudyn 비디오](https://www.youtube.com/watch?v=PmwFWwSluh8)를 보고 비즈니스 요구 사항에 따라 Azure Cost Management 또는 Cloudyn을 사용해야 하는 경우 권장 사항을 확인합니다.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="additional-azure-tools"></a>추가 Azure 도구

Azure에는 Azure Cost Management 기능 집합의 일부가 아닌 다른 도구가 있습니다. 그러나 비용 관리 프로세스에서 중요한 역할을 합니다. 이러한 도구에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) - 이 도구를 사용하여 선불 클라우드 비용을 예측합니다.
- [Azure Migrate](../migrate/migrate-overview.md) - 현재 데이터 센터 워크로드를 평가하여 Azure 대체 솔루션에서 필요한 항목을 이해합니다.
- [Azure Advisor](../advisor/advisor-overview.md) - 사용하지 않은 VM을 식별하고 Azure 예약 인스턴스 구매에 대한 권장 사항을 받습니다.
- [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) - Azure의 VM에 대해 현재 온-프레미스 Windows Server 또는 SQL Server 라이선스를 사용하여 비용을 절감합니다.


## <a name="next-steps"></a>다음 단계

이제 Cost Management를 숙지했으므로 다음 단계는 이 서비스 사용을 시작하는 것입니다.

- Azure Cost Management를 사용하여 [비용 분석](quick-acm-cost-analysis.md)을 시작하세요.
- [Azure Cost Management 모범 사례](cost-mgt-best-practices.md)에 대해 자세히 알아볼 수도 있습니다.
