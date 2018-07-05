---
title: OMS를 사용하여 Azure Data Factory 모니터링 | Microsoft Docs
description: 분석을 위해 OMS(Operations Management Suite)로 데이터를 라우팅하여 Azure Data Factory를 모니터링하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303984"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>OMS(Operations Management Suite)를 사용하여 Azure Data Factory 모니터링

Azure Data Factory와 Azure Monitor의 통합을 사용하여 OMS(Operations Management Suite)로 데이터를 라우팅할 수 있습니다. 이 통합은 다음과 같은 시나리오에서 유용합니다.

1.  Data Factory에서 OMS에 게시하는 풍부한 메트릭 집합에 대해 복잡한 쿼리를 작성하려고 합니다. OMS를 통해 이러한 쿼리에서 사용자 지정 경고를 만들 수도 있습니다.

2.  데이터 팩터리를 모니터링하려고 합니다. 여러 데이터 팩터리에서 하나의 OMS 작업 영역으로 데이터를 라우팅할 수 있습니다.

## <a name="get-started"></a>시작

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>진단 설정 및 OMS 작업 영역 구성

데이터 팩터리에 대한 진단 설정 사용

1.  **Azure Monitor** -> **진단 설정**을 선택하고 -> 데이터 팩터리를 선택하고 -> 진단을 설정합니다.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  OMS 작업 영역의 구성을 포함한 진단 설정을 제공합니다.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics OMS 팩 설치

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

**만들기**를 클릭하고 OMS 작업 영역 및 OMS 작업 영역 설정을 선택합니다.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>OMS를 사용하여 Azure Data Factory 메트릭 모니터링

**Azure Data Factory Analytics** OMS 팩을 설치하면 다음과 같은 메트릭이 설정된 기본 뷰 집합이 생성됩니다.

- ADF 실행- 1) Data Factory의 파이프라인 실행

- ADF 실행- 2) Data Factory의 활동 실행

- ADF 실행- 3) Data Factory의 트리거 실행

- ADF 오류- 1) Data Factory의 상위 10개 파이프라인 오류

- ADF 오류- 2) Data Factory의 상위 10개 활동 실행

- ADF 오류- 3) Data Factory의 상위 10개 트리거 오류

- ADF 통계- 1) 형식별 활동 실행

- ADF 통계- 2) 형식별 트리거 실행

- ADF 통계- 3) 최대 파이프라인 실행 기간

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

위의 메트릭을 시각화하고, 이러한 메트릭에 숨겨진 쿼리를 확인하며, 쿼리를 편집하고, 경고를 생성하는 등의 작업을 수행할 수 있습니다.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>다음 단계

실행 중인 파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically)를 참조하세요.
