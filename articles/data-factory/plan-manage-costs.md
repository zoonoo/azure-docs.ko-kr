---
title: Azure Data Factory의 비용 계획 및 관리
description: 이 문서에서는 Azure Data Factory의 비용을 계획하고 관리하는 방법을 설명합니다.
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: ca76563475dfbf8d35595c1de3cdee37f80e3ce1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83690239"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Azure Data Factory의 비용 계획 및 관리

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory는 클라우드 스케일링을 위한 탄력적 서버리스 데이터 통합 서비스입니다.  즉, 최대 부하에 대해 계획해야 하는 고정 크기 컴퓨팅이 없습니다. 대신, 요청 시 작업별로 할당할 리소스의 양을 지정하여 확장성이 훨씬 더 큰 ETL 프로세스를 설계할 수 있습니다. 또한 ADF는 사용량 기반 요금제로 청구됩니다. 즉, 사용자가 사용한 만큼만 요금을 지불합니다.

이 문서에서는 Azure Data Factory의 비용을 계획하고 관리하는 방법을 설명합니다.

*   먼저 ETL 프로젝트의 시작 단계에서 개념 증명을 수행하고 파이프라인별 사용량과 가격 계산기의 조합을 사용하여 비용을 예측합니다.
*   파이프라인을 프로덕션에 배포한 후에는 비용 관리 기능을 사용하여 예산을 설정하고 비용을 모니터링할 수 있습니다. 예상 비용을 검토하고 지출 추세를 파악할 수도 있습니다.
*   또한 파이프라인별 사용량과 활동별 사용량 정보를 확인하여 비용이 많이 드는 파이프라인과 작업을 파악하고 비용 절감 대상을 식별할 수 있습니다.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>파이프라인 및 활동 실행 사용량과 가격 계산기를 사용하여 비용 추정

[ADF 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=data-factory)를 사용하여 Azure Data Factory에서 ETL 워크로드를 실행하는 데 드는 비용을 예측할 수 있습니다.  계산기를 사용하려면 활동 실행 횟수, 데이터 통합 단위 수, Data Flow에 사용되는 컴퓨팅 유형, 코어 수, 인스턴스 수, 실행 기간 등의 세부 정보를 입력해야 합니다.

가격 계산기에 대해 자주 묻는 질문 중 하나는 입력으로 사용해야 하는 값입니다.  개념 증명 단계에서 샘플 데이터 세트를 통해 시운전을 수행하여 다양한 ADF 미터의 사용량을 이해할 수 있습니다.  그런 다음, 샘플 데이터 세트의 사용량을 기반으로 전체 데이터 세트의 사용량 및 운영화 일정을 예상할 수 있습니다.

> [!NOTE]
> 아래 예제에 사용된 가격은 가상이며 실제 가격 책정을 의미하지는 않습니다.

예를 들어 AWS S3에서 Azure Data Lake Gen2로 매일 1TB의 데이터를 이동해야 한다고 가정해 보겠습니다.  100GB 데이터 이동의 POC를 수행하여 데이터 수집 처리량을 측정하고 해당 청구 사용량을 이해할 수 있습니다.

다음은 복사 작업 실행 세부 정보 샘플입니다. 실제 진행 속도는 특정 데이터 세트의 모양, 네트워크 속도, S3 계정의 송신 제한, ADLS Gen2 수신 제한 및 기타 요인에 따라 달라질 수 있습니다.

![S3 복사 실행](media/plan-manage-costs/s3-copy-run-details.png)

[파이프라인 실행 수준의 사용량 모니터링](#monitor-consumption-at-pipeline-run-level)을 활용하여 해당 데이터 이동 미터 사용량을 확인할 수 있습니다.

![S3 복사 파이프라인 사용량](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

따라서 한 달 내내 매일 1TB를 이동하는 데 소요되는 총 DIU 시간은 다음과 같습니다.

1.2667(DIU 시간) * (1TB/100GB) * 30(한 달의 일수) = 380 DIU 시간

이제 30회의 활동 실행 및 380 DIU 시간을 ADF 가격 계산기에 연결하여 월간 예상 청구 금액을 구할 수 있습니다.

![S3 복사 가격 계산기](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>예산 및 비용 경고 사용

[예산](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다.  경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다.  예산을 만들 때 리소스 ID 및 미터 이름과 같은 필터를 추가하면 구독 수준이나 그보다 더 하위 수준에서 예산을 만들 수 있습니다.  그러나 센터 내 개별 파이프라인에 대한 예산을 만들 수는 없습니다.

## <a name="monitor-costs-at-factory-level"></a>센터 수준에서 비용 모니터링

Azure Data Factory 사용을 시작하면 Azure Portal의 [비용 분석](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis) 창에서 발생한 비용을 볼 수 있습니다.

1. [비용 분석](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis)를 보려면 **Cost Management + Billing** 창을 열고 메뉴에서 **Cost Management**를 선택한 다음, **비용 분석 열기**를 선택합니다.
2. 기본 보기에는 이번 달의 누적 비용이 표시됩니다.  다른 시간 범위, 다른 세분성(예: 일일 또는 월간)으로 전환할 수 있습니다.
3. Azure Data Factory와 같은 단일 서비스로 비용 범위를 좁히려면 **필터 추가**를 선택한 다음, **서비스 이름**을 선택합니다.  그런 다음, 목록에서 **Azure Data Factory v2**를 선택합니다.
4. 특정 센터 인스턴스와 특정 ADF 미터 세분성의 비용을 분석하는 필터를 더 추가할 수 있습니다.

   ![비용 분석](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>파이프라인 실행 수준에서 사용량 모니터링

파이프라인을 실행할 때는 파이프라인에 있는 활동의 유형, 이동하고 변환하는 데이터의 양 및 변환의 복잡도에 따라, Azure Data Factory에서 서로 다른 청구 미터가 작동합니다.

Azure Data Factory 사용자 환경에서 개별 파이프라인 실행에 대한 서로 다른 미터의 사용량을 볼 수 있습니다. 모니터링 환경을 열려면 [Azure Portal](https://portal.azure.com/) 데이터 팩터리 블레이드에서 **모니터링 및 관리** 타일을 선택합니다. ADF UX에 이미 있는 경우 왼쪽 사이드바에서 **모니터** 아이콘을 클릭합니다. 기본 모니터링 보기는 파이프라인 실행 목록입니다.

파이프라인 이름 옆에 있는 **사용량** 단추를 클릭하면 파이프라인 내의 모든 활동에서 집계된 파이프라인 실행의 사용량을 보여 주는 팝업 창이 표시됩니다.

![파이프라인 실행 사용량](media/plan-manage-costs/pipeline-run-consumption.png)

![파이프라인 사용량 세부 정보](media/plan-manage-costs/pipeline-consumption-details.png)

파이프라인 실행 사용량 보기에서는 특정 파이프라인 실행의 각 ADF 미터별 사용량을 보여 주지만 사용자에게 청구되는 금액은 보유하고 있는 Azure 계정의 유형과 사용하는 통화의 유형에 따라 달라지므로 실제 청구 금액은 표시되지 않습니다.  지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data)를 참조하세요.

## <a name="monitor-consumption-at-activity-run-level"></a>활동 실행 수준에서 사용량 모니터링
파이프라인 실행 수준에서 집계된 사용량을 이해한 후에는 필요에 따라 더 깊이 들어가서 파이프라인 내에서 가장 비용이 많이 드는 활동을 식별해야 합니다.

활동 실행 수준에서 사용량을 확인하려면 데이터 팩터리 **작성자 및 모니터** UI로 이동합니다. 파이프라인 실행 목록이 표시되는 **모니터** 탭에서 **파이프라인 이름** 링크를 클릭하여 파이프라인 실행의 활동 실행 목록에 액세스합니다.  활동 이름 옆의 출력 **단추**를 클릭하고 JSON 출력에서 **billableDuration** 속성을 찾습니다.

다음은 복사 작업 실행의 샘플입니다.

![출력 복사](media/plan-manage-costs/copy-output.png)

다음은 매핑 데이터 흐름 활동 실행의 샘플입니다.

![데이터 흐름 출력](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>다음 단계

Azure Data Factory에서 가격 책정이 작동하는 방식에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Data Factory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [예제를 통한 Azure Data Factory 이해](https://docs.microsoft.com/azure/data-factory/pricing-concepts)
- [Azure Data Factory 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
