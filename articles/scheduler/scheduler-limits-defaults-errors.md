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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898517"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure Scheduler의 한도, 할당량 및 제한 임계값

> [!IMPORTANT]
> [Azure 논리 앱이](../logic-apps/logic-apps-overview.md) [사용 중지되는](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure 스케줄러를 대체합니다. 스케줄러에서 설정한 작업을 계속 작업하려면 가능한 한 빨리 [Azure Logic Apps로 마이그레이션하십시오.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> 스케줄러는 Azure 포털에서 더 이상 사용할 수 없지만 작업 및 작업 컬렉션을 관리할 수 있도록 [현재 REST API](/rest/api/scheduler) 및 Azure [스케줄러 PowerShell cmdlet을](scheduler-powershell-reference.md) 사용할 수 있습니다.

## <a name="limits-quotas-and-thresholds"></a>한도, 할당량 및 임계값

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 헤더

스케줄러 서비스에 대해 이루어진 모든 요청은 **x-ms-request-id라는**응답 헤더를 반환합니다. 이 헤더에는 요청을 고유하게 식별하는 불투명 값이 포함되어 있습니다. 따라서 요청의 형식이 제대로 지정되었음을 확인했는데 요청이 지속적으로 실패하면 **x-ms-request-id** 응답 헤더 값을 제공하고 다음 세부 정보를 Microsoft에 오류를 보고할 수 있습니다. 

* **x-ms-request-id** 값
* 요청이 작성된 대략적인 시간 
* Azure 구독, 작업 컬렉션 및 작업에 대한 식별자 
* 요청이 시도한 작업의 유형

## <a name="next-steps"></a>다음 단계

* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [Azure Scheduler의 플랜 및 청구 방식](scheduler-plans-billing.md)
* [Azure Scheduler REST API 참조](/rest/api/scheduler)
* [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)