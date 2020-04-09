---
title: Azure Functions 크기 조정 및 호스팅
description: Azure 기능 소비 계획과 프리미엄 플랜 중에서 선택하는 방법을 알아봅니다.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3b000776c04550e1deb883039d94deeb735061ce
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985884"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 크기 조정 및 호스팅

Azure에서 함수 앱을 만들 때 앱에 대한 호스팅 계획을 선택해야 합니다. Azure 기능에 사용할 수 있는 세 가지 호스팅 계획: [소비 계획,](#consumption-plan) [프리미엄 계획](#premium-plan)및 [전용 (앱 서비스) 계획.](#app-service-plan)

선택한 호스팅 계획은 다음과 같은 동작을 지시합니다.

* 함수 앱의 크기를 조정하는 방법입니다.
* 각 함수 앱 인스턴스에서 사용할 수 있는 리소스입니다.
* Azure 가상 네트워크 연결과 같은 고급 기능에 대한 지원입니다.

소비 및 프리미엄 요금제모두 코드가 실행 중일 때 자동으로 컴퓨팅 성능을 추가합니다. 로드를 처리하는 데 필요할 때 앱의 규모가 확장되고 코드 실행이 중지될 때 크기가 조정됩니다. 소비 계획의 경우 유휴 VM에 대한 비용을 지불하거나 용량을 미리 예약할 필요가 없습니다.  

프리미엄 요금제는 프리미엄 컴퓨팅 인스턴스, 인스턴스를 무기한 따뜻하게 유지하는 기능 및 VNet 연결과 같은 추가 기능을 제공합니다.

앱 서비스 계획을 사용하면 관리하는 전용 인프라를 활용할 수 있습니다. 함수 앱은 이벤트에 따라 확장되지 않으므로 확장되지 않습니다. (항상 [켜져](#always-on) 있어야 합니다.)

## <a name="hosting-plan-support"></a>호스팅 계획 지원

기능 지원은 다음 두 가지 범주로 나뉩니다.

* _일반적으로 사용 가능 (GA)_: 완전히 지원및 생산 사용을 위해 승인.
* _미리 보기_: 아직 완전히 지원되거나 프로덕션 용으로 승인되지 않았습니다.

다음 표는 Windows 또는 Linux에서 실행할 때 세 가지 호스팅 계획에 대한 현재 지원 수준을 나타냅니다.

| | 소비 계획 | 프리미엄 플랜 | 전용 플랜 |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>소비 계획

소비 계획을 사용하는 경우 들어오는 이벤트 수에 따라 Azure Functions 호스트의 인스턴스가 동적으로 추가되고 제거됩니다. 이 서버리스 계획은 자동으로 규모를 조정하며, 함수를 실행하는 경우에만 컴퓨팅 리소스에 대한 요금이 청구됩니다. 소비 계획에서 구성 가능한 시간 후 함수 실행 시간이 초과됩니다.

청구는 실행 횟수, 실행 시간 및 사용된 메모리를 기반으로 하며, 함수 앱 내의 모든 함수에 대해 집계됩니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

소비 계획은 기본 호스팅 계획이며 다음과 같은 이점을 제공합니다.

* 함수를 실행 중인 경우에만 지불
* 높은 부하 기간 동안에도 자동으로 규모 확장

동일한 지역의 함수 앱을 동일한 소비 요금제에 할당할 수 있습니다. 동일한 소비 계획에서 여러 앱을 실행하는 데는 단점이나 영향이 없습니다. 동일한 소비 계획에 여러 앱을 할당하면 각 앱의 복원력, 확장성 또는 안정성에 영향을 미치지 않습니다.

소비 계획에서 실행할 때 비용을 추정하는 방법에 대한 자세한 내용은 [소비 계획 비용 이해를](functions-consumption-costs.md)참조하세요.

## <a name="premium-plan"></a><a name="premium-plan"></a>프리미엄 플랜

프리미엄 계획을 사용하는 경우 Azure Functions 호스트의 인스턴스가 소비 계획과 같은 들어오는 이벤트 수에 따라 추가되고 제거됩니다.  프리미엄 플랜은 다음과 같은 기능을 지원합니다.

* 차가운 시작을 피하기 위해 인스턴스를 영구적으로 따뜻하게 합니다.
* VNet 연결
* 무제한 실행 기간(60분 보장)
* 프리미엄 인스턴스 크기(코어 1개, 코어 2개, 코어 인스턴스 4개)
* 더 예측 가능한 가격 책정
* 여러 기능 앱이 있는 계획에 대한 고밀도 앱 할당

이러한 옵션을 구성하는 방법에 대한 정보는 [Azure Functions Premium 계획 문서에서](functions-premium-plan.md)찾을 수 있습니다.

사용된 실행 및 메모리당 청구 대신 Premium 요금제에 대한 청구는 필요한 인스턴스와 미리 준비된 인스턴스에서 사용되는 코어 초 및 메모리 수를 기반으로 합니다. 계획당 항상 한 인스턴스이상이 따뜻해야 합니다. 즉, 실행 횟수에 관계없이 활성 계획당 최소 월별 비용이 있습니다. 프리미엄 플랜의 모든 기능 앱은 미리 준비된 활성 인스턴스와 활성 인스턴스를 공유합니다.

다음과 같은 상황에서 Azure Functions 프리미엄 계획을 고려하십시오.

* 함수 앱을 계속해서 또는 거의 끊임없이 실행합니다.
* 작은 실행 수가 많고 실행 계획이 높지만 소비 계획에서 GB 두 번째 청구서가 낮습니다.
* 소비 계획에서 제공하는 것보다 더 많은 CPU 또는 메모리 옵션이 필요합니다.
* 코드는 소비 계획에서 [허용되는 최대 실행 시간보다](#timeout) 더 오래 실행되어야 합니다.
* 가상 네트워크 연결과 같은 프리미엄 요금제에서만 사용할 수 있는 기능이 필요합니다.

프리미엄 요금제에서 JavaScript 기능을 실행할 때vCPU 수가 적은 인스턴스를 선택해야 합니다. 자세한 내용은 단일 [코어 프리미엄 선택 계획을](functions-reference-node.md#considerations-for-javascript-functions)참조하십시오.  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>전용(앱 서비스) 플랜

기능 앱은 다른 앱 서비스 앱(기본, 표준, 프리미엄 및 격리된 SCO)과 동일한 전용 VM에서 실행할 수도 있습니다.

다음과 같은 상황에서 앱 서비스 계획을 고려하십시오.

* 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있습니다.
* 함수를 실행할 사용자 지정 이미지를 제공하려고 합니다.

앱 서비스 요금제의 함수 앱에 대해 웹 앱과 같은 다른 앱 서비스 리소스와 동일한 요금을 지불합니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.

앱 서비스 계획을 사용하면 VM 인스턴스를 더 추가하여 수동으로 확장할 수 있습니다. 자동 크기 조정을 활성화할 수도 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service/manage-scale-up.md)을 참조하세요. 

App Service 계획에서 JavaScript 함수를 실행 중인 경우 vCPU 수가 더 작은 계획을 선택해야 합니다. 자세한 내용은 [단일 코어 앱 서비스 계획 선택을](functions-reference-node.md#choose-single-vcpu-app-service-plans)참조하십시오. 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>항상 켜기

앱 서비스 계획에서 실행하는 경우 함수 앱이 올바르게 실행되도록 **Always on** 설정을 사용하도록 설정해야 합니다. App Service 계획에서 함수 런타임은 비활성화되고 몇 분 후 유휴 상태가 되므로 HTTP 트리거만 함수를 “다시 시작”합니다. Always On은 App Service 계획에서만 사용할 수 있습니다. 소비 계획에서 플랫폼은 함수 앱을 자동으로 활성화합니다.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Always On이 설정된 경우에도 개별 함수의 실행 시간 초과는 [host.json](functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 설정에 의해 제어됩니다.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>기존 응용 프로그램의 호스팅 계획 결정

함수 앱에서 사용하는 호스팅 계획을 결정하려면 [Azure Portal](https://portal.azure.com)의 함수 앱에 대한 **개요** 탭에서 **App Service 계획/가격 책정 계층**을 참조하세요. App Service 계획의 경우 가격 책정 계층도 표시됩니다.

![포털에서 크기 조정 계획 보기](./media/functions-scale/function-app-overview-portal.png)

또한 다음과 같이 계획을 확인하기 위해 Azure CLI를 사용할 수도 있습니다.

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

이 명령의 출력이 `dynamic`인 경우 함수 앱은 소비 계획 상태입니다. 이 명령의 출력이 `ElasticPremium`있으면 함수 앱이 프리미엄 요금제에 있습니다. 다른 모든 값은 앱 서비스 계획의 다른 계층을 나타냅니다.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

어떤 계획에서든 함수 앱에는 Azure Blob, 큐, 파일 및 테이블 저장소를 지원하는 일반 Azure Storage 계정이 필요합니다. Functions는 트리거 관리 및 함수 실행 기록과 같은 작업에 Azure Storage를 사용하지만 일부 스토리지 계정은 큐와 테이블을 지원하지 않기 때문입니다. Blob 전용 스토리지 계정(Premium Storage 포함) 및 영역 중복 스토리지 복제가 사용되는 범용 스토리지 계정을 포함한 계정은 함수 앱을 만들 때 기존 **스토리지 계정** 선택 항목에서 필터링됩니다.

함수 앱에서 사용하는 동일한 저장소 계정을 트리거 및 바인딩에서 사용하여 응용 프로그램 데이터를 저장할 수도 있습니다. 그러나 저장소 집약적인 작업의 경우 별도의 저장소 계정을 사용해야 합니다.  

여러 기능 앱이 문제 없이 동일한 저장소 계정을 공유할 수 있습니다. (이 것의 좋은 예는 하나의 저장소 계정처럼 작동하는 Azure Storage 에뮬레이터를 사용하여 로컬 환경에서 여러 앱을 개발하는 경우입니다.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#core-storage-services)를 참조하세요.

## <a name="how-the-consumption-and-premium-plans-work"></a>소비 및 프리미엄 요금제의 작동 방식

소비 및 프리미엄 계획에서 Azure Functions 인프라는 함수가 트리거되는 이벤트 수에 따라 Functions 호스트의 추가 인스턴스를 추가하여 CPU 및 메모리 리소스를 확장합니다. 소비 계획의 함수 호스트의 각 인스턴스는 1.5GB의 메모리와 하나의 CPU로 제한됩니다.  호스트의 인스턴스는 전체 함수 앱으로, 함수 앱 내의 모든 함수가 인스턴스 내의 리소스를 공유하고 동시에 확장됩니다. 동일한 소비 계획을 공유하는 함수 앱은 독립적으로 크기 조정됩니다.  프리미엄 요금제에서 계획 크기에 따라 해당 인스턴스의 모든 앱에 사용할 수 있는 메모리와 CPU가 결정됩니다.  

함수 코드 파일은 함수의 기본 저장소 계정에 Azure Files 공유에 저장됩니다. 함수 앱의 주 스토리지 계정을 삭제하면 함수 코드 파일이 삭제되고 복구할 수 없습니다.

### <a name="runtime-scaling"></a>런타임 크기 조정

Azure Functions는 *크기 조정 컨트롤러*라는 구성 요소를 사용하여 이벤트의 비율을 모니터링하고 규모를 확장하거나 감축할 것인지 결정합니다. 크기 조정 컨트롤러는 각 트리거 유형에 대해 추론을 사용합니다. 예를 들어 Azure Queue Storage 트리거를 사용하는 경우 큐 길이 및 가장 오래된 큐 메시지의 기간에 따라 트리거가 조정됩니다.

Azure 함수의 축척 단위는 함수 앱입니다. 함수 앱을 확장하면 Azure Functions 호스트의 여러 인스턴스를 실행하기 위해 추가 리소스가 할당됩니다. 반대로, 컴퓨팅 수요가 감소하면 크기 조정 컨트롤러에서 함수 호스트 인스턴스를 제거합니다. 함수 앱 내에서 실행되는 함수가 없을 때 인스턴스 수가 결국 0으로 *조정됩니다.*

![이벤트를 모니터링하고 인스턴스를 만드는 크기 조정 컨트롤러](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>크기 조정 동작 이해

크기 조정은 다양한 요인에 따라 다르고, 선택한 트리거 및 언어에 따라 달라질 수 있습니다. 다음과 같은 몇 가지 복잡한 크기 조정 동작에 대해 알아야 합니다.

* 단일 함수 앱은 최대 200개의 인스턴스로만 확장됩니다. 단일 인스턴스는 동시에 둘 이상의 메시지 또는 요청을 처리할 수 있지만 동시 실행 수를 제한하지 않습니다.
* HTTP 트리거의 경우 새 인스턴스는 초당 한 번씩 할당됩니다.
* HTTP가 아닌 트리거의 경우 30초마다 한 번씩 새 인스턴스가 할당됩니다. [프리미엄 요금제에서](#premium-plan)실행할 때 확장 속도가 빨라집니다.
* Service Bus 트리거의 경우 가장 효율적인 확장을 위해 리소스에 대한 권한 _관리를_ 사용합니다. _Listen_ 권한을 사용하면 큐 길이를 사용하여 크기 조정 결정을 알릴 수 없으므로 크기 조정이 정확하지 않습니다. 서비스 버스 액세스 정책의 권한 설정에 대한 자세한 내용은 [공유 액세스 권한 부여 정책을](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)참조하십시오.
* 이벤트 허브 트리거의 경우 참조 문서에서 [크기 조정 지침을](functions-bindings-event-hubs-trigger.md#scaling) 참조하십시오. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>확장성 있는 앱의 모범 사례 및 패턴

함수 앱에는 호스트 구성, 런타임 공간 및 리소스 효율성을 비롯하여 규모 조정에 영향을 주는 여러 측면이 있습니다.  자세한 내용은 [성능 고려 사항 문서의 확장성 섹션](functions-best-practices.md#scalability-best-practices)을 참조하세요. 또한 함수 앱의 확장에 따라 연결이 어떻게 작동하는지도 알고 있어야 합니다. 자세한 내용은 [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)을 참조하세요.

파이썬 및 Node.js의 크기 조정에 대한 자세한 내용은 [Azure Functions 파이썬 개발자 가이드 - 크기 조정 및 동시성](functions-reference-python.md#scaling-and-concurrency) 및 [Azure Functions Node.js 개발자 가이드 - 크기 조정 및 동시성](functions-reference-node.md#scaling-and-concurrency)을 참조하십시오.

### <a name="billing-model"></a>청구 모델

다른 요금제에 대한 청구는 [Azure Functions 가격 책정 페이지에서](https://azure.microsoft.com/pricing/details/functions/)자세히 설명합니다. 사용량은 함수 앱 수준에서 집계되며 함수 코드가 실행될 때만 계산됩니다. 다음은 요금 청구 단위입니다.

* **기가바이트-초 단위의 리소스 소비(GB-s)**. 함수 앱 내 모든 함수의 메모리 크기와 실행 시간 조합으로 계산됩니다. 
* **실행**. 이벤트 트리거에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

소비 청구서를 이해하는 방법에 대한 유용한 쿼리 및 정보는 [청구 FAQ에서](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)찾을 수 있습니다.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>서비스 제한

다음 표는 다양한 호스팅 계획에서 실행할 때 함수 앱에 적용되는 제한을 나타냅니다.

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
