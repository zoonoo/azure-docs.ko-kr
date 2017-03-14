---
title: "Azure Functions 호스팅 계획 선택 | Microsoft Docs"
description: "Azure Functions에서 이벤트 기반 워크로드의 수요를 충족하도록 크기를 조정하는 방식을 이해합니다."
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 1c740ac1f98a07b08bdf922dde99ce54bac23ee5
ms.openlocfilehash: e41e246b081efbdf5edf70ee5de86cd2a68043b2
ms.lasthandoff: 03/01/2017


---
# <a name="choose-the-correct-service-plan-for-azure-functions"></a>Azure Functions에 대한 올바른 서비스 계획 선택

## <a name="introduction"></a>소개

Azure Functions에는 소비 계획 및 App Service 계획이라는 두 가지 서비스 계획이 있습니다. 소비 계획은 코드가 실행 중일 때 계산 용량을 자동으로 할당하고, 로드를 처리하는 데 필요한 만큼 확장한 다음 코드가 실행되지 않을 때 축소합니다. 즉 유휴 상태인 VM에 대해 비용을 지불하거나 필요하기 전에 용량을 예약해야 합니다. 이 문서에서는 소비 서비스 계획을 중점적으로 살펴봅니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 

아직 Azure Functions에 익숙하지 않으면 [Azure Functions 개요](functions-overview.md) 문서를 참조하세요.

## <a name="service-plan-options"></a>서비스 계획 옵션

함수 앱을 만들 때 앱에 포함된 함수에 대한 호스팅 계획을 구성해야 합니다. 사용 가능한 호스팅 계획은 **소비 계획** 및 [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)입니다. 현재는 함수 앱을 만드는 중에 이러한 선택이 수행되어야 합니다. 만든 후에는 이러한 두 옵션을 변경할 수 없습니다. [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에서는 계층 간에 크기를 조정할 수 있습니다. 크기가 동적으로 조정되므로 소비 계획에 대한 변경은 현재 지원되지 않습니다.

### <a name="consumption-plan"></a>소비 계획

**소비 계획**에서 함수 앱은 계산 처리 인스턴스에 할당됩니다. 필요한 경우 더 많은 인스턴스가 동적으로 추가되거나 제거됩니다. 또한 함수는 요청을 처리하는 데 필요한 총 시간을 최소화하기 위해 병렬로 실행됩니다. 각 함수의 실행 시간은 포함된 함수 앱에서 집계합니다. 비용은 함수 앱의 모든 함수에서 메모리 크기와 총 실행 시간으로 계산됩니다(기가바이트-초 단위). 이는 실제로 사용 중인 계산 리소스에 대해서만 요금이 청구되므로 계산 수요가 간헐적이거나 작업 시간이 매우 짧은 경우에 유용합니다. 다음 섹션에서는 소비 계획의 작동 원리에 대해 자세히 설명합니다.

### <a name="app-service-plan"></a>앱 서비스 계획

현재 기본, 표준 또는 프리미엄 SKU에서 Web Apps가 작동하는 것처럼 **App Service 계획**에서 함수 앱은 전용 VM에서 실행됩니다. 전용 VM은 App Service 앱과 함수 앱에 할당되며, 코드가 활발히 실행되고 있는지 여부에 관계없이 항상 사용할 수 있습니다. 이미 다른 코드를 실행하고 있는 기존의 활용도가 낮은 VM이 있거나 함수를 계속해서 또는 거의 끊임없이 실행할 예정인 경우에 유용한 옵션입니다. VM은 런타임 및 메모리에서 비용을 분리합니다. 결과적으로 많은 장기 실행 함수의 비용을 해당 함수가 실행되는 VM의 비용으로 제한할 수 있습니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 

## <a name="how-the-consumption-plan-works"></a>소비 계획의 작동 원리

소비 계획은 함수 앱에 있는 함수의 런타임 요구에 따라 추가 처리 인스턴스를 추가하여 CPU 및 메모리 리소스를 자동으로 조정합니다. 모든 함수 앱 처리 인스턴스에는 최대 1.5GB의 메모리 리소스가 할당됩니다.

소비 계획을 실행할 때 함수 앱이 유휴 상태가 되는 경우 새 BLOB 처리에 하루 최대 10분이 걸릴 수 있습니다. 함수 앱이 실행되면 BLOB이 더 신속하게 처리됩니다. 이 초기 지연을 방지하려면 Always On을 활성화하여 App Service 계획을 사용하거나 다른 메커니즘을 사용하여 BLOB 이름을 포함하는 큐 메시지처럼 BLOB 처리를 트리거합니다. 

### <a name="runtime-scaling"></a>런타임 크기 조정

함수는 중앙 수신기를 사용하여 구성된 트리거를 기반으로 하여 계산 요구 사항을 평가하고 확장하거나 축소하는 시기를 결정합니다. 중앙 수신기는 메모리 요구 사항에 대한 힌트를 지속적으로 처리하고 특정 데이터 요소를 트리거합니다. 예를 들어 Azure Queue Storage 트리거의 경우 데이터 요소에는 가장 오래된 항목의 큐 길이와 큐 시간이 포함됩니다.

![](./media/functions-scale/central-listener.png)

크기 조정 단위는 함수 앱입니다. 이 경우 크기를 확장하면 함수 앱의 인스턴스를 더 추가하는 것입니다. 반대로, 계산 수요가 감소하면 함수 앱 인스턴스가 제거됩니다. 실행 중인 항목이 없으면 인스턴스 수가 결국&0;으로 축소됩니다. 

### <a name="billing-model"></a>청구 모델

소비 계획의 요금 청구는 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions)에 자세히 설명되어 있습니다. 코드가 실행된 시간에 대해서만 함수 앱별로 사용량이 보고됩니다. 다음은 요금 청구 단위입니다. 
* **리소스 소비(GB-s(기가바이트-초))** - 함수 앱에서 실행 중인 모든 함수의 메모리 크기와 실행 시간의 조합으로 계산됩니다. 
* **실행 횟수** - 바인딩으로 트리거된 이벤트에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

