---
title: 전용 호스팅 Azure Functions
description: 전용 App Service 호스팅 계획에서 Azure Functions를 실행 하는 이점에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: a9f976eda4a551c302ba7df92fbdbbf7a4fce1d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704568"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Azure Functions에 대 한 전용 호스팅 계획

이 문서에서는 ASE (App Service Environment)를 비롯 하 여 App Service 계획에서 함수 앱을 호스트 하는 방법에 대해 설명 합니다. 다른 호스팅 옵션은 [호스팅 계획 문서](functions-scale.md)를 참조 하세요.

App Service 계획은 앱에서 실행할 컴퓨팅 리소스 세트를 정의합니다. 이러한 계산 리소스는 기존 호스팅의 [_서버 팜과_](https://wikipedia.org/wiki/Server_farm) 유사 합니다. 웹 앱과 같은 다른 App Service 앱과 동일한 컴퓨팅 리소스 (App Service 계획)에서 실행 되도록 하나 이상의 함수 앱을 구성할 수 있습니다. 이러한 계획에는 Basic, Standard, Premium 및 격리 Sku가 포함 됩니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.

다음과 같은 경우 App Service 계획을 고려해 야 합니다.

* 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있습니다.
* 함수를 실행할 사용자 지정 이미지를 제공 하려고 합니다.

## <a name="billing"></a>결제

다른 App Service 리소스의 경우와 마찬가지로 App Service 계획에서 함수 앱에 대 한 비용을 지불 합니다. 이는 소비 기반 비용 구성 요소가 있는 Azure Functions [소비 계획](consumption-plan.md) 또는 [프리미엄 계획](functions-premium-plan.md) 호스팅과 다릅니다. 계획에서 실행 되는 함수 앱 또는 웹 앱 수에 관계 없이 계획에 대해서만 요금이 청구 됩니다. 자세히 알아보려면 [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/)를 참조 하세요. 

## <a name="always-on"></a><a name="always-on"></a> Always On

App Service 계획에서 실행 하는 경우 함수 앱이 올바르게 실행 되도록 **Always on** 설정을 사용 하도록 설정 해야 합니다. App Service 계획에서 함수 런타임은 비활성화되고 몇 분 후 유휴 상태가 되므로 HTTP 트리거만 함수를 “다시 시작”합니다. **Always on** 설정은 App Service 계획 에서만 사용할 수 있습니다. 소비 계획에서 플랫폼은 함수 앱을 자동으로 활성화합니다.

Always On이 설정된 경우에도 개별 함수의 실행 시간 초과는 [host.json](functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 설정에 의해 제어됩니다.

## <a name="scaling"></a>크기 조정

App Service 계획을 사용 하면 더 많은 VM 인스턴스를 추가 하 여 수동으로 확장할 수 있습니다. 자동 크기 조정을 사용 하도록 설정할 수도 있습니다 .이 경우 자동 크기 조정은 프리미엄 계획의 탄력적 확장 보다 느립니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service/manage-scale-up.md)을 참조하세요. 

> [!NOTE] 
> App Service 계획에서 JavaScript (Node.js) 함수를 실행 하는 경우 vCPUs 수가 작은 계획을 선택 해야 합니다. 자세한 내용은 [단일 코어 App Service 계획 선택](functions-reference-node.md#choose-single-vcpu-app-service-plans)을 참조 하세요. 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>App Service Environment

ASE (App Service Environment)에서를 실행 하면 함수를 완전히 격리 하 고 App Service 계획 보다 많은 수의 인스턴스를 활용할 수 있습니다. 시작 하려면 [App Service 환경 소개](../app-service/environment/intro.md)를 참조 하세요.

가상 네트워크에서 함수 앱을 실행 하려는 경우 [Premium 요금제](functions-premium-plan.md)를 사용 하 여이 작업을 수행할 수 있습니다. 자세히 알아보려면 [Azure Functions 개인 사이트 액세스 설정](functions-create-private-site-access.md)을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

+ [Azure Functions 호스팅 옵션](functions-scale.md)
+ [Azure App Service 계획 개요](../app-service/overview-hosting-plans.md)
