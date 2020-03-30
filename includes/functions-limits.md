---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198346"
---
| 리소스 | [소비 계획](../articles/azure-functions/functions-scale.md#consumption-plan) | [프리미엄 플랜](../articles/azure-functions/functions-scale.md#premium-plan) | [앱 서비스 계획](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 확장 | 이벤트 기반 | 이벤트 기반 | [수동/자동 크기 조정](../articles/app-service/manage-scale-up.md) | 
| 최대 인스턴스 | 200 | 100 | 10-20 |
|기본 [시간 시간 시간(최소)](../articles/azure-functions/functions-scale.md#timeout) |5 | 30 |30<sup>2</sup> |
|최대 [시간 시간(최소)](../articles/azure-functions/functions-scale.md#timeout) |10 | 무한<sup>8</sup> | 무한<sup>3</sup> |
| 인스턴스당 최대 아웃바운드 연결 | 600 활성 (총 1200) | unbounded | unbounded |
| 최대 요청 크기(MB)<sup>4</sup> | 100 | 100 | 100 |
| 최대 쿼리 문자열 길이<sup>4</sup> | 4096 | 4096 | 4096 |
| 최대 요청 URL 길이<sup>4</sup> | 8192 | 8192 | 8192 |
| 인스턴스당 [ACU](../articles/virtual-machines/windows/acu.md) | 100 | 210-840 | 100-840 |
| 최대 메모리(인스턴스당 GB) | 1.5 | 3.5-14 | 1.75-14 |
| 계획별 기능 앱 |100 |100 |무한<sup>5</sup> |
| [앱 서비스 계획](../articles/app-service/overview-hosting-plans.md) | [지역당](https://azure.microsoft.com/global-infrastructure/regions/) 100개 |리소스 그룹당 100 |리소스 그룹당 100 |
| 저장 장치<sup>6</sup> |1 GB |250GB |50-1000 GB |
| 앱당 사용자 지정 도메인</a> |500<sup>7</sup> |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/configure-ssl-bindings.md) |언바운드 SNI SSL 연결 포함 | 언바운드 SNI SSL 및 1IP SSL 연결 포함 |언바운드 SNI SSL 및 1IP SSL 연결 포함 | 

<sup>1</sup> 다양한 앱 서비스 요금제 옵션에 대한 특정 제한은 [앱 서비스 요금제 한도를](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)참조하십시오.  
<sup>2</sup> 기본적으로 앱 서비스 계획의 함수 1.x 런타임에 대한 시간 설정은 바인딩되지 않습니다.  
<sup>3</sup> 앱 서비스 계획을 [항상 켜짐으로](../articles/azure-functions/functions-scale.md#always-on)설정해야 합니다. 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/)지불합니다.  
<sup>4</sup> 이러한 제한은 [호스트에 설정됩니다.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)  
<sup>5</sup> 호스트할 수 있는 함수 앱의 실제 수는 앱의 활동, 컴퓨터 인스턴스의 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>6</sup> 저장소 제한은 동일한 앱 서비스 계획의 모든 앱에서 임시 저장소의 총 콘텐츠 크기입니다. 소비 계획은 임시 저장소에 Azure 파일을 사용합니다.  
<sup>7</sup> 함수 앱이 소비 [계획에서](../articles/azure-functions/functions-scale.md#consumption-plan)호스팅되는 경우 CNAME 옵션만 지원됩니다. [프리미엄 요금제](../articles/azure-functions/functions-scale.md#premium-plan) 또는 앱 [서비스 플랜의](../articles/azure-functions/functions-scale.md#app-service-plan)기능 앱의 경우 CNAME 또는 A 레코드를 사용하여 사용자 지정 도메인을 매핑할 수 있습니다.  
<sup>8</sup> 최대 60분 동안 보장됩니다.