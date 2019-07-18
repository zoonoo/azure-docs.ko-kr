---
title: 플랜 및 청구 방식 - Azure Scheduler
description: Azure Scheduler의 플랜 및 청구 방식에 대해 알아봅니다.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 3a8664497d3d082ec1c7f584188854991e872d50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720436"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Azure Scheduler의 플랜 및 청구 방식

> [!IMPORTANT]
> Azure Scheduler는 사용이 중지되며 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)가 대신 제공됩니다. 작업을 예약하려는 경우 [Azure Logic Apps를 대신 사용해 보세요](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="job-collection-plans"></a>작업 컬렉션 플랜

Azure Scheduler에서 작업 컬렉션에는 특정 수의 작업이 포함됩니다. 작업 컬렉션은 청구 가능 엔터티이며 표준, P10 프리미엄 및 P20 프리미엄 플랜을 선택할 수 있습니다. 아래에 이러한 플랜의 설명이 나와 있습니다. 

| 작업 컬렉션 플랜 | 컬렉션당 최대 작업 수 | 최대 되풀이 횟수 | 구독당 최대 작업 컬렉션 수 | 제한 | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 컬렉션당 작업 50개 | 분당 1회. 작업을 1분에 2개 이상 실행할 수는 없습니다. | 각 Azure 구독은 표준 작업 컬렉션을 100개까지 포함할 수 있습니다. | Scheduler 전체 기능 집합 액세스 | 
| **P10 Premium** | 컬렉션당 작업 50개 | 분당 1회. 작업을 1분에 2개 이상 실행할 수는 없습니다. | 각 Azure 구독은 P10 프리미엄 작업 컬렉션을 10,000개까지 포함할 수 있습니다. 컬렉션을 더 많이 사용하려는 경우 <a href="mailto:wapteams@microsoft.com">Microsoft에 문의</a>하세요. | Scheduler 전체 기능 집합 액세스 |
| **P20 Premium** | 컬렉션당 작업 1,000개 | 분당 1회. 작업을 1분에 2개 이상 실행할 수는 없습니다. | 각 Azure 구독은 P20 프리미엄 작업 컬렉션을 5,000개까지 포함할 수 있습니다. 컬렉션을 더 많이 사용하려는 경우 <a href="mailto:wapteams@microsoft.com">Microsoft에 문의</a>하세요. | Scheduler 전체 기능 집합 액세스 |
|||||| 

## <a name="pricing"></a>가격

가격 세부 정보는 [Scheduler 가격 책정](https://azure.microsoft.com/pricing/details/scheduler/)을 참조하세요.

## <a name="upgrade-or-downgrade-plans"></a>플랜 업그레이드 또는 다운그레이드

언제든지 작업 컬렉션 플랜을 표준, P10 프리미엄 및 P20 프리미엄 플랜 간에 업그레이드하거나 다운그레이드할 수 있습니다.

## <a name="active-status-and-billing"></a>활성 상태 청구

전체 Azure 구독이 청구상의 문제로 일시적으로 비활성화되지 않는 한 작업 컬렉션은 항상 활성 상태입니다. 그리고 단일 작업을 통해 작업 컬렉션의 모든 작업을 비활성화할 수는 있지만, 이 작업을 수행해도 작업 컬렉션의 청구 상태가 변경되지는 않으므로 작업 컬렉션 요금은 *계속* 청구됩니다. 빈 작업 컬렉션도 활성 상태로 간주되므로 요금이 청구됩니다.

작업 컬렉션에 대해 요금이 청구되지 않도록 하려는 경우 작업 컬렉션을 삭제해야 합니다.

## <a name="standard-billable-units"></a>표준 청구 가능 단위

표준 청구 가능 단위는 표준 작업 컬렉션을 10개까지 포함할 수 있습니다. 표준 작업 컬렉션의 경우 컬렉션당 작업을 50개까지 포함할 수 있으므로, 표준 청구 단위가 1개이면 Azure 구독이 작업을 최대 500개까지 포함할 수 있습니다(매월 약 *2,200만 개*의 작업 실행 가능). 아래 목록에서는 표준 작업 컬렉션의 수에 따라 요금이 청구되는 방식을 설명합니다.

* 표준 작업 컬렉션 수가 1~10개이면 표준 청구 단위 하나에 대한 요금이 청구됩니다. 

* 표준 작업 컬렉션 수가 11~20개이면 표준 청구 단위 2개에 대한 요금이 청구됩니다. 

* 표준 작업 컬렉션 수가 21~30개이면 표준 청구 단위 3개에 대한 요금이 청구됩니다. 이러한 방식으로 컬렉션 수가 증가함에 따라 해당 요금이 청구됩니다.

## <a name="p10-premium-billable-units"></a>P10 프리미엄 청구 가능 단위

P10 프리미엄 청구 가능 단위는 P10 프리미엄 작업 컬렉션을 10,000개까지 포함할 수 있습니다. P10 프리미엄 컬렉션의 경우 컬렉션당 작업을 50개까지 포함할 수 있으므로, P10 프리미엄 청구 단위가 1개이면 Azure 구독이 작업을 최대 500,000개까지 포함할 수 있습니다(매월 약 *220억 개*의 작업 실행 가능). 

P10 프리미엄 작업 컬렉션도 표준 작업 컬렉션과 같은 기능을 제공하지만, 작업 컬렉션이 많이 필요한 앱의 경우 가격이 할인되며 확장성도 더 높습니다. 아래 목록에서는 P10 프리미엄 작업 컬렉션의 수에 따라 요금이 청구되는 방식을 설명합니다.

* P10 프리미엄 작업 컬렉션 수가 1~10,000개이면 P10 프리미엄 청구 단위 하나에 대한 요금이 청구됩니다. 

* P10 프리미엄 작업 컬렉션 수가 10,001~20,000개이면 P10 프리미엄 청구 단위 2개에 대한 요금이 청구됩니다. 이러한 방식으로 컬렉션 수가 증가함에 따라 해당 요금이 청구됩니다.

## <a name="p20-premium-billable-units"></a>P20 프리미엄 청구 가능 단위

P20 프리미엄 청구 가능 단위는 P20 프리미엄 작업 컬렉션을 5,000개까지 포함할 수 있습니다. P20 프리미엄 컬렉션의 경우 컬렉션당 작업을 1,000개까지 포함할 수 있으므로, P20 프리미엄 청구 단위가 1개이면 Azure 구독이 작업을 최대 5,000,000개까지 포함할 수 있습니다(매월 약 *2,200억 개*의 작업 실행 가능).

P20 프리미엄 작업 컬렉션은 P10 프리미엄 작업 컬렉션과 같은 기능을 제공하지만, 컬렉션당 더 많은 수의 작업을 지원하며 P10 프리미엄에 비해 총 작업 수도 더 많으므로 확장성도 높아집니다.

## <a name="plan-comparison"></a>플랜 비교

* 표준 작업 컬렉션이 100개(표준 청구 단위 10개)가 넘을 경우 모든 작업 컬렉션에 대해 프리미엄 플랜을 선택하는 것이 더 경제적입니다.

* 표준 작업 컬렉션과 프리미엄 작업 컬렉션이 각각 하나씩이라면 표준 청구 단위 *및* 프리미엄 청구 단위 하나씩에 대한 요금이 청구됩니다.

  Scheduler 서비스는 활성 작업 컬렉션(표준 또는 프리미엄)의 수에 따라 요금을 청구합니다.

## <a name="see-also"></a>참고 항목

* [Azure Scheduler란?](scheduler-intro.md)
* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [Azure Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)