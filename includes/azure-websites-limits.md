---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 800dc50f82fa47228f1a88a143c5b515168812a6
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755713"
---
| 리소스 | 무료 | 공유됨 | Basic | Standard | Premium(v3) | 격리 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Azure App Service 계획](../articles/app-service/overview-hosting-plans.md)당 [웹, 모바일 또는 API 앱 수](https://azure.microsoft.com/services/app-service/)<sup>1</sup> |10 |100 |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup>|
| [App Service 계획](../articles/app-service/overview-hosting-plans.md) |지역당 10개 |리소스 그룹 당 10 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100|
| 컴퓨팅 인스턴스 유형 |공유됨 |공유됨 |전용<sup>3</sup> |전용<sup>3</sup> |전용<sup>3</sup></p> |전용<sup>3</sup>|
| [규모 확장](../articles/app-service/manage-scale-up.md)(최대 인스턴스 수) |1개 공유됨 |1개 공유됨 |3개 전용됨<sup>3</sup> |10개 전용됨<sup>3</sup> |30개 전용<sup>3</sup>|100개 전용됨<sup> 4</sup>|
| 스토리지<sup>5</sup> |1GB<sup>5</sup> |1GB<sup>5</sup> |10GB<sup>5</sup> |50GB<sup>5</sup> |250GB<sup>5</sup> <br/><br/> 250GB를 초과하는 경우 지원 요청을 제출하세요. |1TB<sup>5</sup> <br/><br/> 사용 가능한 스토리지 할당량은 999GB입니다. |
| CPU 시간(5분)<sup>6</sup> |3분 |3분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a>|
| CPU 시간(일)<sup>6</sup> |60분 |240분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |
| 메모리(1시간) |App Service 계획당 1,024MB |앱당 1,024MB |해당 없음 |해당 없음 |해당 없음 |해당 없음 |
| 대역폭 |165 MB |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |
| 애플리케이션 아키텍처 |32비트 |32비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |
| 인스턴스당 웹 소켓 수<sup>7</sup> |5 |35 |350 |제한 없음 |제한 없음 |제한 없음 |
| IP 연결 수 | 600 | 600 | 인스턴스 크기에 따라 다름<sup>8</sup> | 인스턴스 크기에 따라 다름<sup>8</sup> | 인스턴스 크기에 따라 다름<sup>8</sup> | 16,000 |
| 애플리케이션당 동시 [디버거 연결](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| 구독당 App Service 인증서 수<sup>9</sup>| 지원 안 함 | 지원 안 함 |10 |10 |10 |10 |
| 앱당 사용자 지정 도메인</a> |0(azurewebsites.net 하위 도메인 전용)|500 |500 |500 |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/configure-ssl-certificate.md) |지원되지 않음, \*.azurewebsites.net에 대한 와일드카드 인증서는 기본적으로 사용 가능|지원되지 않음, \*.azurewebsites.net에 대한 와일드카드 인증서는 기본적으로 사용 가능|무제한 SNI SSL 연결 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함|
| 하이브리드 연결 | | | 플랜당 5개 | 플랜당 25개 | 앱당 200개 | 앱당 200개 |
| [가상 네트워크 통합](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| 통합 부하 분산 장치 | |X |X |X |X |X<sup>10</sup> |
| [액세스 제한](../articles/app-service/networking-features.md#access-restrictions) | 앱당 512개 규칙 | 앱당 512개 규칙 | 앱당 512개 규칙 | 앱당 512개 규칙 | 앱당 512개 규칙 | 앱당 512개 규칙 |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [예약된 백업 수](../articles/app-service/manage-backup.md) | | | | 2시간마다 예약된 백업 기준, 매일 최대 12개 백업(수동 + 예약) | 1시간마다 예약된 백업 기준, 매일 최대 50개 백업(수동 + 예약) | 1시간마다 예약된 백업 기준, 매일 최대 50개 백업(수동 + 예약) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [엔드포인트 모니터링](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| 앱당 [스테이징 슬롯 수](../articles/app-service/deploy-staging-slots.md)| | | |5 |20 |20 |
| [프로덕션에서 테스트](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [진단 로그](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [인증 및 권한 부여](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service 관리형 인증서(공개 미리 보기)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>별도로 명시되지 않는 한 App Service 계획당 앱 및 스토리지 할당량입니다.  
<sup>2</sup>이러한 컴퓨터에서 호스트할 수 있는 실제 앱 수는 앱의 활동, 컴퓨터 인스턴스 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>3</sup>전용 인스턴스의 크기는 다양할 수 있습니다. 자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.  
<sup>4</sup>요청 시 더 많이 사용할 수 있습니다.  
<sup>5</sup>스토리지 용량 한도는 동일한 App Service 계획에 있는 모든 앱의 전체 콘텐츠 크기입니다. 단일 리소스 그룹 및 지역의 모든 App Service 계획에 있는 모든 앱의 전체 콘텐츠 크기는 500GB를 초과할 수 없습니다.  
<sup>6</sup>이러한 리소스는 전용 인스턴스의 실제 리소스(인스턴스 크기 및 인스턴스 수)에 의해 제한됩니다.  
<sup>7</sup>두 인스턴스의 Basic 계층으로 앱을 조정하면 두 인스턴스 각각에 대해 동시에 350개가 연결됩니다. 표준 계층 이상에서는 웹 소켓에 대한 이론적 제한이 없지만 다른 요소에서 웹 소켓의 수를 제한할 수 있습니다. 예를 들어 허용되는 최대 동시 요청 수(`maxConcurrentRequestsPerCpu`로 정의됨)는 소규모 VM당 7,500개, 중간 규모 VM당 15,000개(7,500 x 2개 코어) 및 대규모 VM당 75,000개(18,750 x 4개 코어)입니다.  
<sup>8</sup>최대 IP 연결 수는 인스턴스당 기준이며, 인스턴스 크기에 따라 달라집니다. 즉, B1/S1/P1V3 인스턴스당 1,920개, B2/S2/P2V3 인스턴스당 3,968개, B3/S3/P3V3 인스턴스당 8,064개입니다.  
<sup>9</sup>구독당 App Service Certificate 할당량 제한은 지원 요청을 통해 200개(최대 제한)까지 늘릴 수 있습니다.  
<sup>10</sup>App Service 격리 SKU는 Azure Load Balancer를 통해 내부적으로 부하 분산(ILB)될 수 있으므로 인터넷으로부터의 퍼블릭 연결이 없습니다. 그 결과, ILB 격리 App Service의 일부 기능은 ILB 네트워크 엔드포인트와 직접 액세스되는 컴퓨터에서 사용해야 합니다.  
<sup>11</sup>사용자 지정 실행 파일 및/또는 스크립트는 App Service 인스턴스 내에서 요청 시 실행되거나, 일정에 따라 실행되거나, 백그라운드 작업에 따라 지속적으로 실행됩니다. Always On은 연속 Webjob 실행을 위해 필요합니다. App Service 인스턴스에서 실행할 수 있는 WebJobs 수에는 미리 정의된 제한이 없습니다. 애플리케이션 코드에서 수행하려고 하는 작업에 따라 실제적인 제한이 있습니다.

<sup>12</sup>Naked 도메인은 지원되지 않습니다. 표준 인증서만 발급합니다(와일드카드 인증서는 사용할 수 없음). 사용자 지정 도메인당 무료 인증서는 하나만 사용할 수 있습니다.
