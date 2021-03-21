---
title: Azure Functions 크기 조정 및 호스팅
description: Azure Functions 소비 계획 및 프리미엄 계획 중에서 선택 하는 방법을 알아봅니다.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936767"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions 호스팅 옵션

Azure에서 함수 앱을 만들 때 앱에 대 한 호스팅 계획을 선택 해야 합니다. Azure Functions는 [소비 계획](consumption-plan.md), [프리미엄 계획](functions-premium-plan.md)및 [전용 (App Service) 계획](dedicated-plan.md)의 세 가지 기본 호스팅 계획을 사용할 수 있습니다. 모든 호스팅 요금제는 Linux 및 Windows 가상 머신 모두에서 GA (일반 공급)로 제공 됩니다.

선택한 호스팅 계획에 따라 다음과 같은 동작이 결정 됩니다.

* 함수 앱의 크기를 조정 하는 방법입니다.
* 각 함수 앱 인스턴스에 사용할 수 있는 리소스입니다.
* Azure Virtual Network 연결과 같은 고급 기능을 지원 합니다.

이 문서에서는 Kubernetes 기반 호스트와 함께 다양 한 호스팅 계획 간의 자세한 비교를 제공 합니다.

## <a name="overview-of-plans"></a>계획 개요

다음은 함수에 대 한 세 가지 주요 호스팅 계획의 이점에 대 한 요약입니다.

| | |
| --- | --- |  
|**[소비 계획](consumption-plan.md)**| 자동으로 크기를 조정 하 고 함수를 실행 하는 경우 계산 리소스에 대해서만 비용을 지불 합니다.<br/><br/>소비 계획에서 함수 호스트의 인스턴스는 들어오는 이벤트의 수에 따라 동적으로 추가 및 제거 됩니다.<br/><br/> 기본 호스팅 계획을 ✔ 합니다.<br/>함수를 실행 하는 경우에만 ✔ 요금을 지불 합니다.<br/>✔ 높은 부하 기간 동안에도 자동으로 크기가 조정 됩니다.|  
|**[프리미엄 플랜](functions-premium-plan.md)**|는 유휴 상태가 된 후에는 지연 없이 응용 프로그램을 실행 하는 사전 준비 작업자를 사용 하 여 수요에 따라 자동으로 크기를 조정 하 고, 더 강력한 인스턴스에서 실행 하며, 가상 네트워크에 연결 <br/><br/>다음과 같은 경우 Azure Functions 프리미엄 계획을 고려 합니다. <br/><br/>함수 앱이 계속 해 서 또는 거의 계속 실행 ✔.<br/>많은 수의 작은 실행 및 실행 청구서가 있지만 소비 계획에는 낮은 GB 초가 ✔.<br/>✔ 소비 계획에서 제공 하는 것 보다 더 많은 CPU 또는 메모리 옵션이 필요 합니다.<br/>소비 계획에서 허용 되는 최대 실행 시간 보다 오래 실행 되어야 하는 코드를 ✔ 합니다.<br/>✔ 가상 네트워크 연결과 같이 소비 계획에서 사용할 수 없는 기능이 필요 합니다.|  
|**[전용 계획](dedicated-plan.md)** |정기적 [App Service 요금제 속도로](https://azure.microsoft.com/pricing/details/app-service/windows/)App Service 계획 내에서 함수를 실행 합니다.<br/><br/>[Durable Functions](durable/durable-functions-overview.md) 를 사용할 수 없는 장기 실행 시나리오에 적합 합니다. 다음과 같은 경우 App Service 계획을 고려해 야 합니다.<br/><br/>다른 App Service 인스턴스를 이미 실행 중인 기존의 미달 사용 Vm이 있는 ✔ 합니다.<br/>함수를 실행 하는 데 사용할 사용자 지정 이미지를 제공할 ✔ 합니다. <br/>✔ 예측 크기 조정 및 비용이 필요 합니다.|  

이 문서의 비교 테이블에는 함수 앱을 실행할 수 있는 가장 높은 제어 및 격리를 제공 하는 다음과 같은 호스팅 옵션도 포함 되어 있습니다.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | ASE (App Service Environment)는 App Service 앱을 매우 대규모로 안전 하 게 실행 하기 위해 완전히 격리 된 전용 환경을 제공 하는 App Service 기능입니다.<br/><br/>Ase는 다음이 필요한 응용 프로그램 작업에 적합 합니다. <br/><br/>✔ 매우 높습니다.<br/>전체 계산 격리 및 보안 네트워크 액세스를 ✔ 합니다.<br/>높은 메모리 사용을 ✔ 합니다.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes는 Kubernetes 플랫폼을 기반으로 실행 되는 완전히 격리 된 전용 환경을 제공 합니다.<br/><br/> Kubernetes는 다음이 필요한 응용 프로그램 작업에 적합 합니다. <br/>사용자 지정 하드웨어 요구 사항을 ✔ 합니다.<br/>격리 및 보안 네트워크 액세스를 ✔ 합니다.<br/>하이브리드 또는 다중 클라우드 환경에서 실행할 수 있는 ✔.<br/>✔ 기존 Kubernetes 응용 프로그램 및 서비스와 함께 실행 됩니다.|  

이 문서의 나머지 표에서는 다양 한 기능 및 동작에 대 한 계획을 비교 합니다. 동적 호스팅 계획 (소비 및 프리미엄) 간의 비용 비교는 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조 하세요. 다양 한 전용 계획 옵션의 가격 책정에 대 한 자세한 내용은 [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/)를 참조 하세요. 

## <a name="operating-systemruntime"></a>운영 체제/런타임

다음 표에서는 호스팅 계획에 대해 지원 되는 운영 체제 및 언어 런타임 지원을 보여 줍니다.

| | Linux<sup>1</sup><br/>코드 전용 | Windows<sup>2</sup><br/>코드 전용 | Linux<sup>1, 3</sup><br/>Docker 컨테이너 |
| --- | --- | --- | --- |
| **[소비 계획](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | 지원되지 않음  |
| **[프리미엄 플랜](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[전용 계획](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | 해당 없음 | 해당 없음 |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux는 Python 런타임 스택에 대해 유일 하 게 지원 되는 운영 체제입니다. <br/>
<sup>2</sup> Windows는 PowerShell 런타임 스택에 대해 유일 하 게 지원 되는 운영 체제입니다.<br/>
<sup>3</sup> Linux는 Docker 컨테이너에 대해 유일 하 게 지원 되는 운영 체제입니다.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>확장

다음 표에서는 다양 한 호스팅 계획의 크기 조정 동작을 비교 합니다.

| | 확장 | 최대 인스턴스 개수 |
| --- | --- | --- |
| **[소비 계획](consumption-plan.md)** | [이벤트 기반](event-driven-scaling.md). 높은 부하 기간 동안에도 자동으로 규모 확장 Azure Functions 인프라는 들어오는 트리거 이벤트의 수에 따라 함수 호스트의 추가 인스턴스를 추가 하 여 CPU 및 메모리 리소스를 확장 합니다. | 200 |
| **[프리미엄 플랜](functions-premium-plan.md)** | [이벤트 기반](event-driven-scaling.md). 높은 부하 기간 동안에도 자동으로 규모 확장 Azure Functions 인프라는 해당 함수가 트리거되는 이벤트의 수에 따라 함수 호스트의 추가 인스턴스를 추가 하 여 CPU 및 메모리 리소스를 확장 합니다. |100|
| **[전용 요금제](dedicated-plan.md)**<sup>1</sup> | 수동/자동 크기 조정 |10-20|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | 수동/자동 크기 조정 |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | [Keda](https://keda.sh)를 사용 하 여 Kubernetes 클러스터에 대 한 이벤트 기반 자동 크기 조정 | &nbsp;클러스터에 따라 다름 &nbsp;&nbsp;&nbsp;|

<sup>1</sup> 다양 한 App Service 계획 옵션에 대 한 특정 한도는 [App Service 계획 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)을 참조 하세요.

## <a name="cold-start-behavior"></a>콜드 시작 동작

|    |    | 
| -- | -- |
| **[소비 &nbsp; 계획](consumption-plan.md)** | 응용 프로그램은 유휴 상태일 때 0으로 확장 될 수 있습니다. 즉, 시작 시 일부 요청에 추가 대기 시간이 있을 수 있습니다.  소비 계획에는 함수 호스트 및 언어 프로세스가 이미 실행 되 고 있는 사전 준비 자리 표시자 함수에서 끌어오기를 비롯 하 여 콜드 시작 시간을 줄일 수 있도록 최적화가 있습니다. |
| **[프리미엄 플랜](functions-premium-plan.md)** | 콜드 시작을 방지 하기 위해 웜 인스턴스를 영구적으로 합니다. |
| **[전용 계획](dedicated-plan.md)** | 전용 계획에서 실행 하는 경우 함수 호스트가 지속적으로 실행 될 수 있습니다. 즉, 콜드 시작은 문제가 되지 않습니다. |
| **[ASE](dedicated-plan.md)** | 전용 계획에서 실행 하는 경우 함수 호스트가 지속적으로 실행 될 수 있습니다. 즉, 콜드 시작은 문제가 되지 않습니다. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | KEDA 구성에 따라 콜드 부팅을 방지 하도록 앱을 구성할 수 있습니다. 0으로 크기를 조정 하도록 구성 된 경우 새 이벤트에 콜드 시작이 발생 합니다. 

## <a name="service-limits"></a>서비스 제한

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>네트워킹 기능

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>결제

| | | 
| --- | --- |
| **[소비 계획](consumption-plan.md)** | 함수가 실행 되는 시간에 대해서만 요금을 지불 합니다. 청구는 실행 횟수, 실행 시간 및 사용된 메모리를 기반으로 하며, |
| **[프리미엄 플랜](functions-premium-plan.md)** | 프리미엄 요금제는 필요한 코어 초와 준비 인스턴스 간에 사용 되는 메모리의 수를 기반으로 합니다. 계획 당 하나 이상의 인스턴스를 항상 웜으로 유지 해야 합니다. 이 계획은 가장 예측 가능한 가격 책정을 제공 합니다. |
| **[전용 계획](dedicated-plan.md)* | 웹 앱과 같은 다른 App Service 리소스의 경우와 마찬가지로 App Service 계획에서 함수 앱에 대해 동일한 비용을 지불 합니다.|
| **[ASE(App Service Environment)](dedicated-plan.md)** | 인프라에 대해 요금을 지불 하 고 ASE 크기를 변경 하지 않는 ASE에 대 한 고정 월별 요금이 있습니다. App Service 계획 vCPU 당 비용이 있습니다. ASE에 호스트되는 모든 앱은 격리 가격 책정 SKU에 해당합니다. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Kubernetes 클러스터의 비용만 지불 하면 됩니다. 함수에 대 한 추가 요금이 청구 되지 않습니다. 함수 앱은 일반 앱 처럼 클러스터 위에 응용 프로그램 워크 로드로 실행 됩니다. |

## <a name="next-steps"></a>다음 단계

+ [Azure Functions의 배포 기술](functions-deployment-technologies.md) 
+ [Azure Functions 개발자 가이드](functions-reference.md)