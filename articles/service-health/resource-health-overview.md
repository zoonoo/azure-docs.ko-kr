---
title: "Azure 리소스 상태 개요 | Microsoft Docs"
description: "Azure 리소스 상태 개요"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 9912911d6ed43a70a7a0f9316079d8f66d063f64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-resource-health-overview"></a>Azure Resource Health 개요
 
Resource Health는 Azure 문제가 리소스에 영향을 주는 경우 진단 및 지원을 도와줍니다. 리소스의 현재 및 이전 상태에 대해 알려주고 문제를 완화하는 데 도움이 됩니다. Resource Health는 Azure 서비스 문제와 관련된 도움이 필요할 때 기술 지원을 제공합니다.

[Azure 상태](https://status.azure.com)는 광범위한 고객 집합에 영향을 주는 서비스 문제를 알려주지만, 리소스 상태는 리소스의 상태에 대한 개인 설정된 대시보드를 제공합니다. Resource Health에 Azure 서비스 문제로 인해 과거에 리소스를 사용할 수 없었던 모든 시간이 표시되기 때문에 SLA 위반 여부를 쉽게 파악할 수 있습니다. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>무엇이 리소스로 간주되고 리소스 상태에서는 리소스가 정상인지를 어떻게 결정하나요?
리소스는 Azure 서비스의 특정 인스턴스(예: 가상 머신, 웹앱 또는 SQL 데이터베이스)입니다.

Resource Health는 다양한 Azure 서비스에서 내보낸 신호에 의존하여 리소스의 정상 여부를 평가합니다. 리소스가 정상이 아니면 리소스 상태에서 추가 정보를 분석하여 문제 원인을 파악합니다. 또한 Microsoft가 문제를 해결하기 위해 수행하는 작업 또는 문제의 원인을 해결하기 위해 수행할 수 있는 작업도 파악합니다. 

상태를 평가하는 방법에 대한 자세한 내용은 [Azure Resource Health](resource-health-checks-resource-types.md)에서 리소스 유형 및 상태 검사에 대한 전체 목록을 확인하세요.

## <a name="health-status-provided-by-resource-health"></a>리소스 상태에서 제공하는 상태
리소스 상태는 다음 상태 중 하나입니다.

### <a name="available"></a>사용 가능
서비스에서 리소스의 상태에 영향을 주는 어떠한 이벤트도 감지되지 않았습니다. 리소스가 최근 24시간 동안 계획되지 않은 가동 중지 시간에서 복구된 경우 **최근 복구된** 알림이 표시됩니다.

![리소스 상태가 사용 가능한 가상 머신](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>사용할 수 없음
서비스에서 리소스 상태에 영향을 주는 진행 중인 플랫폼 또는 비-플랫폼 이벤트를 감지했습니다.

#### <a name="platform-events"></a>플랫폼 이벤트
이 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 여기에는 예정된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획에 없는 호스트 재부팅)가 모두 포함됩니다.

Resource Health에는 이벤트 및 복구 프로세스에 대한 추가 세부 정보가 제공됩니다. 또한 Microsoft 지원 계약이 유효하지 않은 경우에도 지원 담당자에게 문의할 수 있습니다.

![플랫폼 이벤트로 인해 리소스 상태가 사용 불가능한 가상 머신](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>비-플랫폼 이벤트
이 이벤트는 사용자가 수행한 작업에 의해 트리거됩니다. 예: 가상 머신 중지 또는 Redis Cache에 대한 최대 연결 수 도달.

![비-플랫폼 이벤트로 인해 리소스 상태가 사용 불가능한 가상 머신](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>알 수 없음
이 상태 정보는 Resource Health에서 이 리소스에 대한 정보를 10분 이상 수신하지 못했음을 나타냅니다. 이 상태는 리소스 상태에 대한 명확한 표시는 아니지만 문제 해결 프로세스의 중요한 데이터 요소입니다.
* 리소스가 예상한 대로 실행 중이면 몇 분 후 리소스 상태가 사용 가능으로 업데이트됩니다.
* 리소스에 문제가 발생하는 경우 알 수 없음 상태로 플랫폼의 이벤트에 의해 리소스가 영향을 받음을 나타낼 수 있습니다.

![리소스 상태가 알 수 없음인 가상 머신](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>성능 저하됨
이 상태는 리소스를 계속 사용할 수 있지만 성능 저하가 감지되었음을 나타냅니다.
다양한 리소스에는 리소스가 저하되었음을 나타내는 경우에 대한 고유 기준이 있습니다.

![리소스 상태가 저하된 가상 머신](./media/resource-health-overview/degraded.png)

## <a name="report-an-incorrect-status"></a>잘못된 상태 보고
어느 시점에서 현재 상태가 잘못되었다고 생각되면 **Report incorrect health status(잘못된 상태 보고)**를 클릭하여 알려주세요. Azure 문제의 영향을 받는 경우 Resource Health에서 지원 담당자에게 문의하는 것이 좋습니다. 

![리소스 상태에서 잘못된 상태 보고](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>기록 정보
Resource Health에서 **기록 보기**를 클릭하여 최대 14일 동안의 상태 기록에 할 수 있습니다. 

![리소스 상태 보고 기록](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>시작
하나의 리소스에 대한 리소스 상태를 열려면
1.  Azure Portal에 로그인합니다.
2.  리소스로 이동합니다.
3.  왼쪽에 있는 리소스 메뉴에서 **리소스 상태**를 클릭합니다.

![리소스 뷰에서 Resource Health 열기](./media/resource-health-overview/from-resource-blade.png)

**모든 서비스**를 클릭하고 필터 텍스트 상자에 **리소스 상태**를 입력하여 **도움말 + 지원** 블레이드를 열어서 리소스 상태에 액세스할 수도 있습니다. 마지막으로 [**리소스 상태**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)를 클릭합니다.

![모든 서비스에서 리소스 상태 열기](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>다음 단계

리소스 상태에 대한 자세한 내용은 다음 리소스를 확인하세요.
-  [Azure Resource Health에서 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)
-  [Azure Resource Health에 대한 질문과 대답](resource-health-faq.md)




