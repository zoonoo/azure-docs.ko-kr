---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 8f30d9fb2fcfe8f55af13d7726aa8458f8733b3f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236010"
---
| Resource | [소비 계획](../articles/azure-functions/functions-scale.md#consumption-plan) | [프리미엄 요금제](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) | [App Service 계획](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 확장 | 이벤트 기반 | 이벤트 기반 | [수동/자동 크기 조정](../articles/app-service/web-sites-scale.md) | 
|기본 [시간 초과 기간](../articles/azure-functions/functions-scale.md#timeout) (분) |5 | 30 |30<sup>2</sup> |
|최대 [시간 초과 기간](../articles/azure-functions/functions-scale.md#timeout) (분) |10 | unbounded | unbounded<sup>3</sup> |
| 최대 아웃 바운드 연결 (인스턴스당) | 600 active (1200 합계) | unbounded | unbounded |
| 최대 요청 크기 (MB)<sup>4</sup> | 100 | 100 | 100 |
| 최대 쿼리 문자열 길이로<sup>4</sup> | 4096 | 4096 | 4096 |
| 최대 요청 URL 길이<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) 인스턴스당 | 100 | 210-840 | 100-840 |
| 최대 메모리 (GB 인스턴스당) | 1.5 | 3.5-14 | 1.75-14 |
| 계획 당 함수 앱 |100 |100 |unbounded<sup>5</sup> |
| [App Service 계획](../articles/app-service/overview-hosting-plans.md) | 당 100 개 [지역](https://azure.microsoft.com/global-infrastructure/regions/) |리소스 그룹당 100 |리소스 그룹당 100 |
| Storage<sup>6</sup> |1 GB |250GB |50 ~ 1000 GB |
| 앱당 사용자 지정 도메인</a> |500<sup>7</sup> |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |지원 되지 않음, 와일드 카드 인증서에 대 한 *. azurewebsites.net 기본적으로 사용 가능| 무제한 SNI SSL 및 1 IP SSL 연결 포함 |무제한 SNI SSL 및 1 IP SSL 연결 포함 | 

<sup>1</sup>다양 한 App Service 계획 옵션에 대 한 특정 제한에 대 한 참조를 [App Service 계획 제한](../articles/azure-subscription-service-limits.md#app-service-limits)합니다.  
<sup>2</sup>기본적으로 App Service 계획의 Functions 1.x 런타임 시간 제한은 제한 합니다.  
<sup>3</sup>App Service 계획으로 설정 해야 [Always On](../articles/azure-functions/functions-scale.md#always-on)합니다. 표준 요금을 지불 [요금](https://azure.microsoft.com/pricing/details/app-service/)합니다.  
<sup>4</sup> 이러한 제한은 [호스트에서 설정](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)합니다.  
<sup>5</sup> 컴퓨터 인스턴스 및 해당 리소스 사용률의 크기, 앱의 활동에 호스트할 수 있는 함수 앱의 실제 수에 따라 달라 집니다.   
<sup>6</sup>저장소 용량 한도 총 콘텐츠 크기를 임시 저장소의 모든 앱에서 동일한 App Service 계획 합니다. 임시 저장소에 대 한 Azure Files를 사용 하는 소비 계획 합니다.  
<sup>7</sup>함수 앱에서 호스트 되는 경우는 [소비 계획](../articles/azure-functions/functions-scale.md#consumption-plan), CNAME 옵션만 지원 됩니다. 함수 앱에 대 한는 [프리미엄 요금제](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) 요소나 [App Service 계획](../articles/azure-functions/functions-scale.md#app-service-plan), CNAME 또는 A 레코드를 사용 하 여 사용자 지정 도메인을 매핑할 수 있습니다. 
