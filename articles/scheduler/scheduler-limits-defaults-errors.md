---
title: Azure Scheduler의 한도, 할당량 및 임계값
description: Azure Scheduler에 대한 한도, 할당량, 기본값 및 제한 임계값에 대해 알아보기
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78898517"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure Scheduler의 한도, 할당량 및 제한 임계값

> [!IMPORTANT]
> [Azure Scheduler](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)는 조만간 사용 중지되고 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 대체됩니다. 스케줄러에 설정 된 작업을 계속 하려면 가능한 한 빨리 [Azure Logic Apps로 마이그레이션](../scheduler/migrate-from-scheduler-to-logic-apps.md) 하세요. 
>
> Scheduler는 더 이상 Azure Portal에서 사용할 수 없지만 [REST API](/rest/api/scheduler) 및 [Azure Scheduler PowerShell cmdlet](scheduler-powershell-reference.md)은 현재 사용 가능하므로 작업 및 작업 컬렉션을 관리할 수 있습니다.

## <a name="limits-quotas-and-thresholds"></a>한도, 할당량 및 임계값

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 헤더

스케줄러 서비스에 대해 수행 된 모든 요청은 이름이 **x-y**인 응답 헤더를 반환 합니다. 이 헤더에는 요청을 고유 하 게 식별 하는 불투명 값이 포함 됩니다. 따라서 요청의 형식이 제대로 지정되었음을 확인했는데 요청이 지속적으로 실패하면 **x-ms-request-id** 응답 헤더 값을 제공하고 다음 세부 정보를 Microsoft에 오류를 보고할 수 있습니다. 

* **x-ms-request-id** 값
* 요청이 작성된 대략적인 시간 
* Azure 구독, 작업 컬렉션 및 작업에 대한 식별자 
* 요청이 시도한 작업의 유형

## <a name="next-steps"></a>다음 단계

* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [Azure Scheduler의 플랜 및 청구 방식](scheduler-plans-billing.md)
* [Azure Scheduler REST API 참조](/rest/api/scheduler)
* [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)