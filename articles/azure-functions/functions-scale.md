---
title: "Azure Functions 호스팅 계획 선택 | Microsoft Docs"
description: "Azure Functions에서 이벤트 기반 워크로드의 수요를 충족하도록 크기를 조정하는 방식을 이해합니다."
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "Azure 함수, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/04/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0feeca446a756a076461f381123f63079e0a13db
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="choose-the-correct-hosting-plan-for-azure-functions"></a>Azure Functions에 대한 올바른 호스팅 계획 선택

## <a name="introduction"></a>소개

Azure Functions에는 소비 계획 및 App Service 계획이라는 두 가지 서비스 계획이 있습니다. 소비 계획은 코드가 실행 중일 때 계산 용량을 자동으로 할당하고, 로드를 처리하는 데 필요한 만큼 확장한 다음 코드가 실행되지 않을 때 축소합니다. 즉 유휴 상태인 VM에 대해 비용을 지불하거나 필요하기 전에 용량을 예약해야 합니다. 이 문서에서는 소비 서비스 계획을 중점적으로 살펴봅니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 

아직 Azure Functions에 익숙하지 않으면 [Azure Functions 개요](functions-overview.md) 문서를 참조하세요.

## <a name="service-plan-options"></a>서비스 계획 옵션

함수 앱을 만들 때 앱에 포함된 함수에 대한 호스팅 계획을 구성해야 합니다. 사용 가능한 호스팅 계획은 **소비 계획** 및 [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)입니다. 현재는 함수 앱을 만드는 중에 이러한 선택이 수행되어야 합니다. 만든 후에는 이러한 두 옵션을 변경할 수 없습니다. [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에서는 계층 간에 크기를 조정할 수 있습니다. 크기가 동적으로 조정되므로 소비 계획에 대한 변경은 현재 지원되지 않습니다.

### <a name="consumption-plan"></a>소비 계획

**소비 계획**에서 함수 앱은 계산 처리 인스턴스에 할당됩니다. 필요한 경우 더 많은 인스턴스가 동적으로 추가되거나 제거됩니다. 또한 함수는 요청을 처리하는 데 필요한 총 시간을 최소화하기 위해 병렬로 실행됩니다. 각 함수의 실행 시간은 포함된 함수 앱에서 집계합니다. 비용은 함수 앱에서 모든 함수의 메모리 크기와 총 실행 시간으로 계산됩니다. 계산 요구가 간헐적으로 발생하는 경우 또는 작업 실행 시간이 짧은 경우 소비 계획을 사용합니다. 이 계획을 통해 사용한 계산 리소스에 대한 요금을 지불합니다. 다음 섹션에서는 소비 계획의 작동 원리에 대해 자세히 설명합니다.

### <a name="app-service-plan"></a>앱 서비스 계획

현재 기본, 표준 또는 프리미엄 SKU에서 Web Apps가 작동하는 것처럼 **App Service 계획**에서 함수 앱은 전용 VM에서 실행됩니다. 전용 VM은 App Service 앱과 함수 앱에 할당되며, 코드가 활발히 실행되고 있는지 여부에 관계없이 항상 사용할 수 있습니다. 이미 다른 코드를 실행하고 있는 기존의 활용도가 낮은 VM이 있거나 함수를 계속해서 또는 거의 끊임없이 실행할 예정인 경우에 유용한 옵션입니다. VM은 런타임 및 메모리에서 비용을 분리합니다. 결과적으로 많은 장기 실행 함수의 비용을 해당 함수가 실행되는 VM의 비용으로 제한할 수 있습니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 

App Service 계획을 사용하면 더 많은 단일 코어 VM 인스턴스를 추가하여 수동으로 확장하거나 크기 자동 조정을 사용하도록 설정할 수 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service-web/web-sites-scale.md)을 참조하세요. App Service 계획에서 JavaScript 함수를 실행하려는 경우 코어 수가 더 작은 계획을 선택해야 합니다. 자세한 내용은 [함수에 대한 JavaScript 참조](functions-reference-node.md#choose-single-core-app-service-plans)를 참조하세요.  

## <a name="how-the-consumption-plan-works"></a>소비 계획의 작동 원리

소비 계획은 함수 앱에서 실행되는 함수의 요구에 따라 추가 처리 인스턴스를 추가하여 CPU 및 메모리 리소스를 자동으로 조정합니다. 모든 함수 앱 처리 인스턴스에는 최대 1.5GB의 메모리 리소스가 할당됩니다.

소비 계획을 실행할 때 함수 앱이 유휴 상태가 되는 경우 새 BLOB 처리에 하루 최대 10분이 걸릴 수 있습니다. 함수 앱이 실행되면 BLOB이 더 신속하게 처리됩니다. 이 초기 지연을 방지하려면 Always On을 활성화하여 App Service 계획을 사용하거나 다른 메커니즘을 사용하여 BLOB 이름을 포함하는 큐 메시지처럼 BLOB 처리를 트리거합니다. 

함수 앱을 만들 때 Blob, 큐 및 Table storage을 지원하는 범용 Azure Storage 계정을 만들거나 연결해야 합니다. 내부적으로 Azure Functions는 트리거 관리 및 함수 실행 로깅 등의 작업을 위해 Azure Storage를 사용합니다. Blob 전용 저장소 계정(Premium Storage 포함) 및 범용 저장소 계정(ZRS 복제 사용)과 같은 일부 저장소 계정은 큐 및 같은 테이블을 지원하지 않습니다. 이러한 계정은 함수 앱을 만들 때 저장소 계정 블레이드에서 필터링됩니다.

소비 호스팅 계획을 사용할 경우 함수 앱 콘텐츠(예: 함수 코드 파일 및 바인딩 구성)는 기본 저장소 계정의 Azure Files 공유에 저장됩니다. 기본 저장소 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

저장소 계정 유형에 대한 자세한 내용은 [Azure Storage 서비스 소개](../storage/storage-introduction.md#introducing-the-azure-storage-services)를 참조하세요.

### <a name="runtime-scaling"></a>런타임 크기 조정

Functions는 규모 컨트롤러를 사용하여 구성된 트리거를 기반으로 계산 요구 사항을 평가하고 규모 확장 또는 규모 감축 시기를 결정합니다. 규모 컨트롤러는 메모리 요구 사항 및 트리거별 데이터 요소에 대한 힌트를 지속적으로 처리합니다. 예를 들어 Azure Queue Storage 트리거를 사용하는 경우 데이터 요소에는 가장 오래된 항목의 큐 길이와 큐 시간이 포함됩니다.

![](./media/functions-scale/central-listener.png)

크기 조정 단위는 함수 앱입니다. 이 경우 크기를 확장하면 함수 앱의 인스턴스를 더 추가하는 것입니다. 반대로, 계산 수요가 감소하면 함수 앱 인스턴스가 제거됩니다. 실행 중인 함수가 없으면 인스턴스 수가 결국 0으로 축소됩니다. 

### <a name="billing-model"></a>청구 모델

소비 계획의 요금 청구는 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions)에 자세히 설명되어 있습니다. 코드가 실행된 시간에 대해서만 함수 앱별로 사용량이 보고됩니다. 다음은 요금 청구 단위입니다. 
* **리소스 소비(GB-s(기가바이트-초))** - 함수 앱에서 실행 중인 모든 함수의 메모리 크기와 실행 시간의 조합으로 계산됩니다. 
* **실행 횟수** - 바인딩으로 트리거된 이벤트에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

