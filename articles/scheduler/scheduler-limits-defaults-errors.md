---
title: Azure Scheduler의 한도, 할당량 및 임계값
description: Azure Scheduler에 대한 한도, 할당량, 기본값 및 제한 임계값에 대해 알아보기
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300915"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure Scheduler의 한도, 할당량 및 제한 임계값

> [!IMPORTANT]
> 사용이 [중지](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)되는 Azure Scheduler를 교체 하는 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) . 스케줄러에 설정 된 작업을 계속 하려면 가능한 한 빨리 [Azure Logic Apps로 마이그레이션](../scheduler/migrate-from-scheduler-to-logic-apps.md) 하세요.

## <a name="limits-quotas-and-thresholds"></a>한도, 할당량 및 임계값

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 헤더

Scheduler 서비스에 대한 모든 요청에서는 이름이 **x-ms-request-id**인 응답 헤더를 반환합니다. 이 헤더는 요청을 고유하게 식별하는 불투명 값을 포함합니다. 따라서 요청의 형식이 제대로 지정되었음을 확인했는데 요청이 지속적으로 실패하면 **x-ms-request-id** 응답 헤더 값을 제공하고 다음 세부 정보를 Microsoft에 오류를 보고할 수 있습니다. 

* **x-ms-request-id** 값
* 요청이 작성된 대략적인 시간 
* Azure 구독, 작업 컬렉션 및 작업에 대한 식별자 
* 요청이 시도한 작업의 유형

## <a name="see-also"></a>참조

* [Azure Scheduler란?](scheduler-intro.md)
* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
