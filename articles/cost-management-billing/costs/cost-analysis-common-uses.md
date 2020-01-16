---
title: 에서 사용 하는 일반적인 비용 분석 Azure Cost Management
description: 이 문서에서는 Azure Cost Management에서 일반적인 비용 분석 작업에 대 한 결과를 가져오는 방법을 설명 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 756e2f275a92a31a99604aaf7ef880bfdce8149b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988514"
---
# <a name="common-cost-analysis-uses"></a>일반적인 비용 분석 사용

Azure Cost Management 사용자는 자주 질문 하는 질문에 대 한 답변을 원합니다. 이 문서에서는 Cost Management에서 일반적인 비용 분석 작업에 대 한 결과를 가져오는 과정을 안내 합니다.

## <a name="view-cost-breakdown-by-azure-service"></a>Azure 서비스별 비용 분석 보기

Azure 서비스를 기준으로 비용을 보면 인프라의 일부를 가장 효율적으로 이해 하는 데 도움이 될 수 있습니다. 예를 들어 VM 계산 비용은 작을 수 있습니다. 그러나 Vm에서 내보내는 정보의 양에 의해 상당한 네트워킹 비용이 발생할 수 있습니다. 필요에 따라 서비스 사용을 조정할 수 있도록 Azure 서비스의 기본 비용 드라이버를 이해 하는 것이 중요 합니다.

1. Azure Portal에서 해당 범위에 대 한 비용 분석으로 이동 합니다. 예: **Cost Management + 청구** > **Cost Management** > **비용 분석**
1. **서비스별 비용** 을 선택한 다음 **서비스 계층**별로 그룹화 합니다.
1. 뷰를 **테이블로**변경 합니다.

![Azure 서비스별 비용 분석](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Azure 리소스 별로 비용 분석 보기

서비스는 Azure 리소스를 사용 하 여 빌드됩니다. 리소스를 기준으로 비용을 검토 하면 기본 비용 참가자를 빠르게 식별할 수 있습니다. 서비스에 비용이 많이 드는 리소스가 있는 경우 비용을 줄이기 위해 변경을 수행 하는 것이 좋습니다.

1. Azure Portal에서 해당 범위에 대 한 비용 분석으로 이동 합니다. 예: **Cost Management + 청구** > **Cost Management** > **비용 분석**
1. **리소스 비용을**선택 합니다.
1. 뷰를 **테이블로**변경 합니다.

![Azure 리소스 별로 비용 분석 보기](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>선택한 차원 별로 비용 분석 보기

차원을 통해 요금에 표시 된 다양 한 메타 데이터 값에 따라 비용을 구성할 수 있습니다. 예를 들어, 위치 별로 비용을 그룹화 할 수 있습니다.

1. Azure Portal에서 해당 범위에 대 한 비용 분석으로 이동 합니다. 예: **Cost Management + 청구** > **Cost Management** > **비용 분석**
1. **그룹화 방법** 필터를 선택 합니다.  
    항목을 기준으로 그룹을 선택 ![](./media/cost-analysis-common-uses/group-by.png)
1. 필요에 따라 나중에 사용할 수 있도록 보기를 저장 합니다.
1. 그래프 아래에 있는 원형 차트를 클릭 하 여 자세한 데이터를 봅니다.  
    ![선택한 차원 별로 비용 분석 보기](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>일별 또는 월별 비용 보기

매일 및 매월 비용을 살펴보면 비용이 더 높은 주 또는 연간 시간이 있는지 파악 하는 데 도움이 될 수 있습니다. 명절 기간에 고객 트래픽이 더 많은 경우 Azure 비용이 해당 하는 증가를 야기 하나요? 금요일은 월요일 보다 비용이 더 많이 듭니다.

1. Azure Portal에서 해당 범위에 대 한 비용 분석으로 이동 합니다. 예: **Cost Management + 청구** > **Cost Management** > **비용 분석**
1. **세분성** 을 **월별** 또는 **매일**로 설정 합니다.

![일별 비용 보기](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>특정 태그에 대 한 비용 보기

많은 Azure 사용자가 비용을 범주화 하기 위해 비용 센터 또는 개발 환경 (프로덕션 및 테스트)과 같은 리소스에 태그를 적용 합니다. 태그는 비용 분석에서 차원으로 표시 됩니다. 차원을 사용 하 여 사용자 지정 태그 분류에 대 한 통찰력을 얻을 수 있습니다.

태그에 대한 지원은 리소스에 적용된 *후*에 보고된 사용량에 적용됩니다. 비용 롤업에는 태그가 소급 적용되지 않습니다.

1. Azure Portal에서 해당 범위에 대 한 비용 분석으로 이동 합니다. 예: **Cost Management + 청구** > **Cost Management** > **비용 분석**
1. 태그에 대해 **Group by** 를 선택 합니다.

![특정 태그에 대 한 비용 보기](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>사용 세부 정보 다운로드

사용 정보 보고서 파일 (CSV 형식)은 송장에 대해 발생 하는 모든 요금에 대 한 분석을 제공 합니다. 보고서를 사용 하 여 송장과 비교 하 고 더 잘 이해할 수 있습니다. 청구서에 대 한 각 청구 요금은 사용량 보고서에서 중단 된 요금에 해당 합니다.

1. Azure Portal에서 청구 계정 또는 구독에 대 한 **사용량 및 요금** 탭으로 이동 합니다. 예: **Cost Management + 청구** > **청구** > **사용량 + 요금**
1. 다운로드할 항목을 선택 하 고 다운로드 기호를 클릭 합니다.  
    ![사용량 및 요금 다운로드](./media/cost-analysis-common-uses/download1.png)
1.  다운로드할 사용 파일을 선택 합니다.  
    ![다운로드할 사용 파일 선택](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>월간 EA 비용 분석 보기

EA 등록은 전체 조직에 대 한 비용을 계산 합니다. 시간이 지남에 따라 비용을 계산 하 고 송장을 발부 하는 방법을 이해 하면 적절 한 이해 관계자와 협력 하 여 비용을 관리 하는 데 도움이 됩니다.

1. Azure Portal에서 **Cost Management + 청구** > **개요**로 이동 합니다.
1. 이번 달에 대 한 **분석** 을 클릭 하 고 금액 약정을 표시 합니다.  
    ![EA 비용 개요-분석 요약](./media/cost-analysis-common-uses/breakdown1.png)
1.  **사용량 및 요금** 탭을 클릭 하 고 선택한 timespan에서 이전 달의 분석 결과를 확인 합니다.  
    ![사용량 및 요금 탭](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>약관 별 등록 월별 비용 보기

등록의 월별 비용에 대 한 그래픽 보기를 사용 하 여 지정 된 기간 동안 비용 추세와 송장이 발부 된 금액을 파악 합니다.

1. Azure Portal에서 해당 범위에 대 한 비용 분석으로 이동 합니다. 예: **Cost Management + 청구** > **Cost Management** > **비용 분석**
1. 등록을 선택 하 고 등록 기간을 설정 합니다.
1. 세분성을 월별으로 설정한 다음 뷰를 **열 (누적)** 로 설정 합니다.

데이터를 그룹화 하 고 필터링 하 여 보다 자세한 분석을 수행할 수 있습니다.

![용어 별 월간 등록 비용](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>EA 등록 누적 비용 보기

시간에 따른 순 누적 요금을 확인 하 여 지정 된 기간 동안 조직의 전반적인 비용을 파악 합니다.

1. Azure Portal에서 해당 범위에 대 한 비용 분석으로 이동 합니다. 예: **Cost Management + 청구** > **Cost Management** > **비용 분석**
1. 등록을 선택 하 고 현재 누적 비용을 확인 합니다.

![등록 누적 비용](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>다음 단계
- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
- [Cost Management 설명서](../index.yml)를 참조 하세요.
