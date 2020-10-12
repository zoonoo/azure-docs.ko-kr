---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 37a61e270f95ffdc420536046e0ce1eda1e489b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85391491"
---
| 리소스 |[사용 계획](../articles/azure-functions/functions-scale.md#consumption-plan)|[프리미엄 계획](../articles/azure-functions/functions-scale.md#premium-plan)|[전용 계획](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|기본 [시간 제한 기간](../articles/azure-functions/functions-scale.md#timeout)(분) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|최대 [시간 제한 기간](../articles/azure-functions/functions-scale.md#timeout)(분) |10 | 무제한<sup>7</sup> | 무제한<sup>2</sup> | unbounded | unbounded |
| 최대 아웃바운드 연결 수(인스턴스당) | 600개 활성(총 1,200개) | unbounded | unbounded | unbounded | unbounded |
| 최대 요청 크기(MB)<sup>3</sup> | 100 | 100 | 100 | 100 | 클러스터에 따라 다름 |
| 최대 쿼리 문자열 길이<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | 클러스터에 따라 다름 |
| 최대 요청 URL 길이<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | 클러스터에 따라 다름 |
|인스턴스당 [ACU](../articles/virtual-machines/windows/acu.md) | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [AKS 가격 책정](https://azure.microsoft.com/pricing/details/container-service/) |
| 최대 메모리(인스턴스당 GB) | 1.5 | 3.5-14 | 1.75-14 | 3.5 - 14 | 모든 노드가 지원됩니다. |
| 계획당 함수 앱 |100 |100 |무제한<sup>4</sup> | unbounded | unbounded |
| [App Service 계획](../articles/app-service/overview-hosting-plans.md) | [지역](https://azure.microsoft.com/global-infrastructure/regions/)당 100 |리소스 그룹당 100 |리소스 그룹당 100 | - | - |
| 스토리지<sup>5</sup> |5TB |250GB |50-1000GB | 1TB | 해당 없음 |
| 앱당 사용자 지정 도메인</a> |500<sup>6</sup> |500 |500 | 500 | 해당 없음 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/configure-ssl-bindings.md) |무제한 SNI SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 해당 없음 |

<sup>1</sup> 기본적으로 App Service 요금제의 Functions 1.x 런타임에 대한 제한 시간은 무제한입니다.  
<sup>2</sup> App Service 요금제를 [Always On](../articles/azure-functions/functions-scale.md#always-on)으로 설정해야 합니다. 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제하세요.  
<sup>3</sup> 이러한 한도는 [호스트에 설정](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)됩니다.  
<sup>4</sup> 호스트할 수 있는 함수 앱의 실제 수는 앱의 활동, 머신 인스턴스의 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>5</sup> 스토리지 제한은 동일한 App Service 요금제에 있는 모든 앱에서 임시 스토리지의 총 콘텐츠 크기입니다. 소비 계획은 임시 스토리지에 Azure Files를 사용합니다.  
<sup>6</sup> [소비 계획](../articles/azure-functions/functions-scale.md#consumption-plan)에서 함수 앱이 호스트되는 경우 CNAME 옵션만 지원됩니다. [프리미엄 플랜](../articles/azure-functions/functions-scale.md#premium-plan) 또는 [App Service 요금제](../articles/azure-functions/functions-scale.md#app-service-plan)에서 호스트되는 함수 앱의 경우 CNAME 또는 A 레코드 중 하나를 사용하여 사용자 지정 도메인을 매핑할 수 있습니다.  
<sup>7</sup> 최대 60분까지 보장됩니다.  
<sup>8</sup> 작업자는 고객 앱을 호스트하는 역할입니다. 작업자는 3가지 고정된 크기로 사용할 수 있습니다. vCPU/3.5GB RAM 1개, vCPU/7GB RAM 2개, vCPU/14GB RAM 4개
