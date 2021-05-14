---
title: Azure Functions 전용 호스팅
description: 전용 App Service 호스팅 계획에서 Azure Functions를 실행하는 장점에 대하여 알아봅니다.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: a9f976eda4a551c302ba7df92fbdbbf7a4fce1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704568"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Azure Functions 전용 호스팅 계획

본 문서에서는 ASE(App Service Environment)를 포함하는 App Service 플랜에 함수 앱을 호스팅하는 내용을 다룹니다. 다른 호스팅 옵션에 대하여서는 [호스팅 계획 문서](functions-scale.md)를 확인하세요.

App Service 계획은 앱에서 실행할 컴퓨팅 리소스 세트를 정의합니다. 이러한 컴퓨팅 리소스는 기존 호스팅의 [_서버 팜_](https://wikipedia.org/wiki/Server_farm)과 유사합니다. 하나 이상의 함수 앱을 구성하여 웹앱 같은 기타 App Service 앱에서와 같이 동일한 컴퓨팅 리소스(App Service 플랜)에서 실행되도록 할 수 있습니다. Basic, Standard, Premium, 격리 SKU가 이런 플랜에 속합니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.

다음 상황에서 App Service 플랜을 고려합니다.

* 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있습니다.
* 함수를 실행할 사용자 지정 이미지를 제공하고자 합니다.

## <a name="billing"></a>결제

다른 App Service 리소스에서와 마찬가지로 App Service 플랜에 맞춰 함수 앱에 대한 비용을 지불합니다. 이는 소비 기반 비용 구성 요소가 있는 Azure Functions [사용량 플랜](consumption-plan.md)나 [Premium 플랜](functions-premium-plan.md)와는 다릅니다. 플랜에서 실행되는 함수 앱이나 웹앱의 개수와 상관 없이 해당 플랜에만 맞춰서 요금이 청구됩니다. 자세한 내용은 [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/)를 확인하세요. 

## <a name="always-on"></a><a name="always-on"></a> Always On

App Service 계획에서 실행하는 경우 함수 앱이 올바르게 실행되도록 **Always On** 설정을 사용해야 합니다. App Service 계획에서 함수 런타임은 비활성화되고 몇 분 후 유휴 상태가 되므로 HTTP 트리거만 함수를 “다시 시작”합니다. **Always on** 설정은 App Service 플랜에서만 사용할 수 있습니다. 소비 계획에서 플랫폼은 함수 앱을 자동으로 활성화합니다.

Always On이 설정된 경우에도 개별 함수의 실행 시간 초과는 [host.json](functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 설정에 의해 제어됩니다.

## <a name="scaling"></a>크기 조정

App Service 플랜을 사용하면 VM 인스턴스를 더 많이 추가하여 수동으로 스케일 아웃할 수 있습니다. Premium 플랜처럼 탄력적으로 크기를 조정하는 것보다는 느리지만, 자동 크기 조정도 사용할 수는 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service/manage-scale-up.md)을 참조하세요. 

> [!NOTE] 
> App Service 플랜에서 JavaScript(Node.js) 함수를 실행할 때는 vCPU가 적은 플랜을 선택하여야 합니다. 자세한 내용은 [단일 코어 App Service 플랜 선택하기](functions-reference-node.md#choose-single-vcpu-app-service-plans)를 참조하세요. 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>App Service Environment

ASE(App Service Environment)에서 실행하면 함수를 격리한 채로 App Service 플랜보다 많은 인스턴스를 활용할 수 있습니다. 시작하려면 [App Service Environments 소개](../app-service/environment/intro.md)를 참조하세요.

가상 네트워크에서 함수 앱을 실행시키려면 [Premium 플랜](functions-premium-plan.md)을 통하여 할 수 있습니다. 자세한 내용은 [Azure Functions 프라이빗 사이트 액세스 구축](functions-create-private-site-access.md)을 확인하세요. 

## <a name="next-steps"></a>다음 단계

+ [Azure Functions 호스팅 옵션](functions-scale.md)
+ [Azure App Service 계획 개요](../app-service/overview-hosting-plans.md)
