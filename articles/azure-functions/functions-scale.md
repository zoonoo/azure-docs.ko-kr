---
title: "Azure Functions 소비 및 App Service 계획 | Microsoft Docs"
description: "Azure Functions에서 이벤트 기반 워크로드의 수요를 충족하도록 크기를 조정하는 방식을 이해합니다."
services: functions
documentationcenter: na
author: lindydonna
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
ms.date: 05/15/2017
ms.author: donnam, glenga
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 25df99203ccb194d75985fca42d3d05609dc7d7c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="azure-functions-consumption-and-app-service-plans"></a>Azure Functions 소비 및 App Service 계획 

## <a name="introduction"></a>소개

Azure Functions는 소비 계획 및 App Service 계획이라는 두 가지 모드에서 실행될 수 있습니다. 소비 계획은 코드가 실행 중일 때 계산 용량을 자동으로 할당하고, 로드를 처리하는 데 필요한 만큼 확장한 다음 코드가 실행되지 않을 때 축소합니다. 따라서 유휴 VM에 대한 요금을 지불하고 용량을 미리 예약할 필요가 없습니다. 이 문서에서는 소비 계획을 중점적으로 살펴봅니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 

아직 Azure Functions에 익숙하지 않으면 [Azure Functions 개요](functions-overview.md) 문서를 참조하세요.

함수 앱을 만들 때 앱에 포함된 함수에 대한 호스팅 계획을 구성해야 합니다. 사용 가능한 호스팅 계획은 **소비 계획** 및 [**App Service 계획**](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)입니다. 두 모드에서 함수는 *Azure Functions 호스트*의 인스턴스에서 실행됩니다. 계획 컨트롤의 형식은 1) 호스트 인스턴스가 확장되는 방법 및 2) 각 호스트에 사용할 수 있는 리소스입니다.

현재 선택한 계획 형식은 함수 앱을 만드는 동안 만들어져야 하며 나중에 변경할 수 없습니다. [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에서는 계층 간에 크기를 조정할 수 있습니다. 소비 계획에서 Azure Functions는 모든 리소스 할당을 자동으로 처리합니다.

## <a name="consumption-plan"></a>소비 계획

**소비 계획**을 사용하는 경우 Azure Functions 호스트의 인스턴스는 들어오는 이벤트의 수에 따라 동적으로 추가되고 제거됩니다. 이 계획은 자동으로 조정하고 함수를 실행하는 경우에만 계산 리소스에 대한 요금이 청구됩니다. 소비 계획에서 함수는 최대 5분 동안 실행될 수 있습니다. 

대금 청구는 실행 시간 및 사용되는 메모리를 기반으로 하며 함수 앱 내에서 모든 함수에 대해 집계됩니다. 자세한 내용은 [Azure Functions 가격 책정 페이지]를 참조하세요.

소비 계획은 기본값이며 다음과 같은 이점을 제공합니다.
- 함수를 실행 중인 경우에만 지불
- 높은 부하 기간 동안에도 자동으로 규모 확장

## <a name="app-service-plan"></a>앱 서비스 계획

기본, 표준, 프리미엄 SKU에서 Web Apps와 유사하게 **App Service 계획**에서 함수 앱은 전용 VM에서 실행됩니다. 전용 VM은 App Service 앱에 할당됩니다. 즉, 함수 호스트는 항상 실행됩니다.

다음과 같은 경우에 App Service 계획을 고려합니다.
- 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있습니다.
- 함수 앱을 계속해서 또는 거의 끊임없이 실행할 예정입니다.
- 소비 계획에서 제공하는 것보다 많은 CPU 또는 메모리 옵션이 필요합니다.
- 소비 계획에 허용된 최대 실행 시간보다 오래 실행해야 합니다.

VM은 런타임 및 메모리에서 비용을 분리합니다. 결과적으로, 할당하는 VM 인스턴스의 비용보다 더 지불하지 않습니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요. 

App Service 계획을 사용하면 더 많은 VM 인스턴스를 추가하여 수동으로 확장하거나 크기 자동 조정을 사용하도록 설정할 수 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service-web/web-sites-scale.md)을 참조하세요. App Service 계획에서 JavaScript 함수를 실행하려는 경우 코어 수가 더 작은 계획을 선택해야 합니다. 자세한 내용은 [함수에 대한 JavaScript 참조](functions-reference-node.md#choose-single-core-app-service-plans)를 참조하세요.  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### 무중단

App Service 계획에서 실행하는 경우 함수 앱이 올바르게 실행되도록 **무중단** 설정을 활성화해야 합니다. App Service 계획에서 함수 런타임은 비활성화되고 몇 분 후 유휴 상태가 되므로 HTTP 트리거만 실제로 함수를 "다시 시작"합니다. 이는 WebJobs에서 무중단을 활성화해야 하는 방식과 유사합니다. 

무중단은 App Service 계획에서만 사용할 수 있습니다. 소비 계획에서 플랫폼은 함수 앱을 자동으로 활성화합니다.

## <a name="storage-account-requirements"></a>저장소 계정 요구 사항

소비 또는 App Service 계획에서 함수 앱에는 Blob, 큐 및 테이블 저장소를 지원하는 Azure Storage 계정이 필요합니다. 내부적으로 Azure Functions는 트리거 관리 및 함수 실행 로깅 등의 작업을 위해 Azure Storage를 사용합니다. Blob 전용 저장소 계정(Premium Storage 포함) 및 범용 저장소 계정(ZRS 복제 사용)과 같은 일부 저장소 계정은 큐 및 같은 테이블을 지원하지 않습니다. 이러한 계정은 함수 앱을 만들 때 저장소 계정 블레이드에서 필터링됩니다.

저장소 계정 유형에 대한 자세한 내용은 [Azure Storage 서비스 소개](../storage/storage-introduction.md#introducing-the-azure-storage-services)를 참조하세요.

## <a name="how-the-consumption-plan-works"></a>소비 계획의 작동 원리

소비 계획은 해당 함수가 트리거되는 이벤트의 수에 따라 함수 호스트의 추가 인스턴스를 추가하여 CPU 및 메모리 리소스를 자동으로 조정합니다. 함수 호스트의 각 인스턴스는 메모리의 최대 1.5GB로 제한됩니다.

소비 호스팅 계획을 사용할 경우 함수 코드 파일은 주 저장소 계정의 Azure 파일 공유에 저장됩니다. 기본 저장소 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

> [!NOTE]
> 소비 계획에서 Blob 트리거를 사용할 때 함수 앱이 유휴 상태가 되는 경우 새 Blob 처리에 하루 최대 10분이 걸릴 수 있습니다. 함수 앱이 실행되면 Blob이 즉시 처리됩니다. 이 초기 지연을 방지하려면 다음 옵션 중 하나를 고려합니다.
> - 무중단을 활성화하여 App Service 계획을 사용합니다.
> - Blob 이름을 포함하는 큐 메시지와 같은 다른 메커니즘을 사용하여 Blob 처리를 트리거합니다. 예를 들어 [Blob 입력 바인딩을 사용하여 큐 트리거](functions-bindings-storage-blob.md#input-sample)를 참조하세요.

### <a name="runtime-scaling"></a>런타임 크기 조정

Azure Functions는 *크기 조정 컨트롤러*라는 구성 요소를 사용하여 이벤트의 비율을 모니터링하고 규모를 확장하거나 축소할 것인지 결정합니다. 크기 조정 컨트롤러는 각 트리거 유형에 대해 추론을 사용합니다. 예를 들어 Azure Queue Storage 트리거를 사용하는 경우 큐 길이 및 가장 오래된 큐 메시지의 기간에 따라 조정합니다.

크기 조정 단위는 함수 앱입니다. 확장하는 경우 Azure Functions 호스트의 여러 인스턴스를 실행하기 위해 더 많은 리소스가 할당됩니다. 반대로, 계산 수요가 감소하면 함수 호스트 인스턴스가 제거됩니다. 함수 앱 내에서 실행 중인 함수가 없으면 인스턴스 수가 결국 0으로 축소됩니다.

![](./media/functions-scale/central-listener.png)

### <a name="billing-model"></a>청구 모델

소비 계획의 요금 청구는 [Azure Functions 가격 책정 페이지]에 자세히 설명되어 있습니다. 사용량은 함수 앱 수준에서 집계되며 함수 코드가 실행될 때만 계산됩니다. 다음은 요금 청구 단위입니다. 
* **리소스 소비(GB-s(기가바이트-초))**. 함수 앱 내 모든 함수의 메모리 크기와 실행 시간의 조합으로 계산됩니다. 
* **실행 횟수**. 바인딩으로 트리거된 이벤트에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

[Azure Functions 가격 책정 페이지]: https://azure.microsoft.com/pricing/details/functions
