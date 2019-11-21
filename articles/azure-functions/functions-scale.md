---
title: Azure Functions 크기 조정 및 호스팅
description: Learn how to choose between Azure Functions Consumption plan and Premium plan.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b9644e89591d7d8b7642b5f381434357191d1711
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226596"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 크기 조정 및 호스팅

When you create a function app in Azure, you must choose a hosting plan for your app. There are three hosting plans available for Azure Functions: [Consumption plan](#consumption-plan), [Premium plan](#premium-plan), and [App Service plan](#app-service-plan).

The hosting plan you choose dictates the following behaviors:

* How your function app is scaled.
* The resources available to each function app instance.
* Support for advanced features, such as VNET connectivity.

Both Consumption and Premium plans automatically add compute power when your code is running. Your app is scaled out when needed to handle load, and scaled down when code stops running. For the Consumption plan, you also don't have to pay for idle VMs or reserve capacity in advance.  

Premium plan provides additional features, such as premium compute instances, the ability to keep instances warm indefinitely, and VNet connectivity.

App Service plan allows you to take advantage of dedicated infrastructure, which you manage. Your function app doesn't scale based on events, which means is never scales down to zero. (Requires that [Always on](#always-on) is enabled.)

> [!NOTE]
> You can switch between Consumption and Premium plans by changing the plan property of the function app resource.

## <a name="hosting-plan-support"></a>Hosting plan support

Feature support falls into the following two categories:

* _Generally available (GA)_ : fully supported and approved for production use.
* _Preview_: not yet fully supported and approved for production use.

The following table indicates the current level of support for the three hosting plans, when running on either Windows or Linux:

| | 사용량 과금 플랜 | 프리미엄 플랜 | Dedicated plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>사용량 과금 플랜

When you're using the Consumption plan, instances of the Azure Functions host are dynamically added and removed based on the number of incoming events. 이 서버리스 계획은 자동으로 규모를 조정하며, 함수를 실행하는 경우에만 컴퓨팅 리소스에 대한 요금이 청구됩니다. 소비 계획에서 구성 가능한 시간 후 함수 실행 시간이 초과됩니다.

청구는 실행 횟수, 실행 시간 및 사용된 메모리를 기반으로 하며, 함수 앱 내의 모든 함수에 대해 집계됩니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

소비 계획은 기본 호스팅 계획이며 다음과 같은 이점을 제공합니다.

* 함수를 실행 중인 경우에만 지불
* 높은 부하 기간 동안에도 자동으로 규모 확장

Function apps in the same region can be assigned to the same Consumption plan. There's no downside or impact to having multiple apps running in the same Consumption plan. Assigning multiple apps to the same consumption plan has no impact on resilience, scalability, or reliability of each app.

To learn more about how to estimate costs when running in a Consumption plan, see [Understanding Consumption plan costs](functions-consumption-costs.md).

## <a name="premium-plan"></a>Premium plan

When you're using the Premium plan, instances of the Azure Functions host are added and removed based on the number of incoming events just like the Consumption plan.  Premium plan supports the following features:

* Perpetually warm instances to avoid any cold start
* VNet 연결
* Unlimited execution duration
* Premium instance sizes (one core, two core, and four core instances)
* More predictable pricing
* High-density app allocation for plans with multiple function apps

Information on how you can configure these options can be found in the [Azure Functions premium plan document](functions-premium-plan.md).

Instead of billing per execution and memory consumed, billing for the Premium plan is based on the number of core seconds and memory used across needed and pre-warmed instances. At least one instance must be warm at all times per plan. This means that there is a minimum monthly cost per active plan, regardless of the number of executions. Keep in mind that all function apps in a Premium plan share pre-warmed and active instances.

Consider the Azure Functions premium plan in the following situations:

* 함수 앱을 계속해서 또는 거의 끊임없이 실행합니다.
* You have a high number of small executions and have a high execution bill but low GB second bill in the consumption plan.
* You need more CPU or memory options than what is provided by the Consumption plan.
* Your code needs to run longer than the [maximum execution time allowed](#timeout) on the Consumption plan.
* You require features that are only available on a Premium plan, such as VNET/VPN connectivity.

When running JavaScript functions on a Premium plan, you should choose an instance that has fewer vCPUs. For more information, see the [Choose single-core Premium plans](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedicated (App Service) plan

Your function apps can also run on the same dedicated VMs as other App Service apps (Basic, Standard, Premium, and Isolated SKUs).

Consider an App Service plan in the following situations:

* 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있습니다.
* You want to provide a custom image on which to run your functions.

You pay the same for function apps in an App Service Plan as you would for other App Service resources, like web apps. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.

With an App Service plan, you can manually scale out by adding more VM instances. You can also enable autoscale. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service/manage-scale-up.md)을 참조하세요. 

App Service 계획에서 JavaScript 함수를 실행 중인 경우 vCPU 수가 더 작은 계획을 선택해야 합니다. For more information, see [Choose single-core App Service plans](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

App Service 계획에서 실행하는 경우 함수 앱이 올바르게 실행되도록 **Always On** 설정을 사용해야 합니다. App Service 계획에서 함수 런타임은 비활성화되고 몇 분 후 유휴 상태가 되므로 HTTP 트리거만 함수를 “다시 시작”합니다. Always On은 App Service 계획에서만 사용할 수 있습니다. 소비 계획에서 플랫폼은 함수 앱을 자동으로 활성화합니다.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Always On이 설정된 경우에도 개별 함수의 실행 시간 초과는 [host.json](functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 설정에 의해 제어됩니다.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Determine the hosting plan of an existing application

함수 앱에서 사용하는 호스팅 계획을 결정하려면 [Azure Portal](https://portal.azure.com)의 함수 앱에 대한 **개요** 탭에서 **App Service 계획/가격 책정 계층**을 참조하세요. App Service 계획의 경우 가격 책정 계층도 표시됩니다.

![포털에서 크기 조정 계획 보기](./media/functions-scale/function-app-overview-portal.png)

또한 다음과 같이 계획을 확인하기 위해 Azure CLI를 사용할 수도 있습니다.

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

이 명령의 출력이 `dynamic`인 경우 함수 앱은 소비 계획 상태입니다. When the output from this command is `ElasticPremium`, your function app is in the Premium plan. All other values indicate different tiers of an App Service plan.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

On any plan, a function app requires a general Azure Storage account, which supports Azure Blob, Queue, Files, and Table storage. Functions는 트리거 관리 및 함수 실행 기록과 같은 작업에 Azure Storage를 사용하지만 일부 스토리지 계정은 큐와 테이블을 지원하지 않기 때문입니다. Blob 전용 스토리지 계정(Premium Storage 포함) 및 영역 중복 스토리지 복제가 사용되는 범용 스토리지 계정을 포함한 계정은 함수 앱을 만들 때 기존 **스토리지 계정** 선택 항목에서 필터링됩니다.

The same storage account used by your function app can also be used by your triggers and bindings to store your application data. However, for storage-intensive operations, you should use a separate storage account.   

<!-- JH: Does using a Premium Storage account improve perf? -->

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#azure-storage-services)를 참조하세요.

## <a name="how-the-consumption-and-premium-plans-work"></a>How the consumption and premium plans work

In the consumption and premium plans, the Azure Functions infrastructure scales CPU and memory resources by adding additional instances of the Functions host, based on the number of events that its functions are triggered on. Each instance of the Functions host in the consumption plan is limited to 1.5 GB of memory and one CPU.  An instance of the host is the entire function app, meaning all functions within a function app share resource within an instance and scale at the same time. Function apps that share the same consumption plan are scaled independently.  In the premium plan, your plan size will determine the available memory and CPU for all apps in that plan on that instance.  

Function code files are stored on Azure Files shares on the function's main storage account. 함수 앱의 주 스토리지 계정을 삭제하면 함수 코드 파일이 삭제되고 복구할 수 없습니다.

### <a name="runtime-scaling"></a>런타임 크기 조정

Azure Functions는 *크기 조정 컨트롤러*라는 구성 요소를 사용하여 이벤트의 비율을 모니터링하고 규모를 확장하거나 감축할 것인지 결정합니다. 크기 조정 컨트롤러는 각 트리거 유형에 대해 추론을 사용합니다. 예를 들어 Azure Queue Storage 트리거를 사용하는 경우 큐 길이 및 가장 오래된 큐 메시지의 기간에 따라 트리거가 조정됩니다.

The unit of scale for Azure Functions is the function app. 함수 앱을 확장하면 Azure Functions 호스트의 여러 인스턴스를 실행하기 위해 추가 리소스가 할당됩니다. 반대로, 컴퓨팅 수요가 감소하면 크기 조정 컨트롤러에서 함수 호스트 인스턴스를 제거합니다. 함수 앱 내에서 실행 중인 함수가 없으면 인스턴스 수가 결국 0으로 축소됩니다.

![이벤트를 모니터링하고 인스턴스를 만드는 크기 조정 컨트롤러](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>크기 조정 동작 이해

크기 조정은 다양한 요인에 따라 다르고, 선택한 트리거 및 언어에 따라 달라질 수 있습니다. There are a few intricacies of scaling behaviors to be aware of:

* 단일 함수 앱은 최대 200개의 인스턴스로만 강화됩니다. 단일 인스턴스는 동시에 둘 이상의 메시지 또는 요청을 처리할 수 있지만 동시 실행 수를 제한하지 않습니다.
* For HTTP triggers, new instances will only be allocated at most once every 1 second.
* For non-HTTP triggers, new instances will only be allocated at most once every 30 seconds.

다른 트리거에는 아래에 문서화된 대로 다른 규모 조정 제한이 있을 수도 있습니다.

* [이벤트 허브](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>확장성 있는 앱의 모범 사례 및 패턴

함수 앱에는 호스트 구성, 런타임 공간 및 리소스 효율성을 비롯하여 규모 조정에 영향을 주는 여러 측면이 있습니다.  자세한 내용은 [성능 고려 사항 문서의 확장성 섹션](functions-best-practices.md#scalability-best-practices)을 참조하세요. 또한 함수 앱의 확장에 따라 연결이 어떻게 작동하는지도 알고 있어야 합니다. 자세한 내용은 [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)을 참조하세요.

### <a name="billing-model"></a>청구 모델

Billing for the different plans is described in detail on the [Azure Functions pricing page](https://azure.microsoft.com/pricing/details/functions/). 사용량은 함수 앱 수준에서 집계되며 함수 코드가 실행될 때만 계산됩니다. 다음은 요금 청구 단위입니다.

* **기가바이트-초 단위의 리소스 소비(GB-s)** . 함수 앱 내 모든 함수의 메모리 크기와 실행 시간 조합으로 계산됩니다. 
* **실행 횟수**. 이벤트 트리거에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

Useful queries and information on how to understand your consumption bill can be found [on the billing FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>서비스 제한

The following table indicates the limits that apply to function apps when running in the various hosting plans:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
