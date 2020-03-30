---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304935"
---
| 리소스 | 무료 | Shared | Basic | Standard | 프리미엄(v2) | 격리 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Azure 앱 서비스 계획](../articles/app-service/overview-hosting-plans.md)<sup>1당</sup> [웹, 모바일 또는 API 앱](https://azure.microsoft.com/services/app-service/) |10 |100 |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup>|
| [앱 서비스 계획](../articles/app-service/overview-hosting-plans.md) |지역당 10개 |리소스 그룹 당 10 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100|
| 컴퓨팅 인스턴스 유형 |Shared |Shared |전용<sup>3</sup> |전용<sup>3</sup> |전용<sup>3</sup></p> |전용<sup>3</sup>|
| [확장(최대](../articles/app-service/manage-scale-up.md) 인스턴스) |1개 공유됨 |1개 공유됨 |3개 전용됨<sup>3</sup> |10개 전용됨<sup>3</sup> |30 전용<sup>3</sup>|100개 전용됨<sup> 4</sup>|
| 스토리지<sup>5</sup> |1GB<sup>5</sup> |1GB<sup>5</sup> |10GB<sup>5</sup> |50GB<sup>5</sup> |250GB<sup>5</sup></p> |1TB<sup>5</sup>|
| CPU 시간 (5 분)<sup>6</sup> |3분 |3분 |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a> |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a> |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a> |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a>|
| CPU 시간(일)<sup>6</sup> |60분 |240분 |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a> |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a> |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a> |무제한, 표준 [요금으로](https://azure.microsoft.com/pricing/details/app-service/) 지불</a> |
| 메모리(1시간) |앱 서비스 계획당 1,024MB |앱당 1,024MB |해당 없음 |해당 없음 |해당 없음 |해당 없음 |
| 대역폭 |165 MB |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |
| 애플리케이션 아키텍처 |32비트 |32비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |
| 인스턴스당 웹 소켓<sup>7</sup> |5 |35 |350 |제한 없음 |제한 없음 |제한 없음 |
| IP 연결 | 600 | 600 | 인스턴스 크기<sup>8에</sup> 따라 다름 | 인스턴스 크기<sup>8에</sup> 따라 다름 | 인스턴스 크기<sup>8에</sup> 따라 다름 | 16,000 |
| 애플리케이션당 동시 [디버거 연결](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| 구독당 앱 서비스 인증서<sup>9</sup>| 지원되지 않음 | 지원되지 않음 |10 |10 |10 |10 |
| 앱당 사용자 지정 도메인</a> |0(azurewebsites.net 하위 도메인 전용)|500 |500 |500 |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/configure-ssl-certificate.md) |지원되지 않음* .azurewebsites.net 에 대한 와일드카드 인증서는 기본적으로 사용할 수 있습니다.|지원되지 않음* .azurewebsites.net 에 대한 와일드카드 인증서는 기본적으로 사용할 수 있습니다.|무제한 SNI SSL 연결 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함|
| 플랜당 하이브리드 연결 | | | 5 | 25 | 200 | 200 |
| 통합 부하 밸러블러 | |X |X |X |X |X<sup>10</sup> |
| [항상 켜기](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [예약된 백업](../articles/app-service/manage-backup.md) | | | | 예약된 백업 2시간마다, 하루에 최대 12회 백업(수동 + 예약) | 매시간 예약된 백업, 하루에 최대 50회 백업(수동 + 예약) | 매시간 예약된 백업, 하루에 최대 50회 백업(수동 + 예약) |
| [자동 크기 조정](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [웹 잡스](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [엔드포인트 모니터링](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| 앱당 [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md)| | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>별도로 명시되지 않는 한 App Service 계획당 앱 및 스토리지 할당량입니다.  
<sup>2</sup>이러한 컴퓨터에서 호스트할 수 있는 실제 앱 수는 앱의 활동, 컴퓨터 인스턴스 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>3</sup>전용 인스턴스의 크기는 다양할 수 있습니다. 자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.  
<sup>4개</sup> 요청 시 더 많은 것을 허용합니다.  
<sup>5개</sup> 저장소 제한은 동일한 앱 서비스 계획의 모든 앱의 총 콘텐츠 크기입니다. 단일 리소스 그룹 및 리전의 모든 앱 서비스 계획의 총 콘텐츠 크기는 500GB를 초과할 수 없습니다.  
<sup>6</sup>이러한 리소스는 전용 인스턴스의 실제 리소스(인스턴스 크기 및 인스턴스 수)에 의해 제한됩니다.  
<sup>7</sup>두 인스턴스의 Basic 계층으로 앱을 조정하면 두 인스턴스 각각에 대해 동시에 350개가 연결됩니다. 표준 계층 이상의 경우 웹 소켓에 대한 이론적 제한은 없지만 다른 요인으로 인해 웹 소켓 수가 제한될 수 있습니다. 예를 들어 허용되는 최대 동시 `maxConcurrentRequestsPerCpu`요청(정의됨)은 소형 VM당 7,500개, 중간 VM당 15,000개(7,500 x 2코어), 대형 VM당 75,000개(18,750 x 4코어)입니다.  
<sup>8개</sup> 최대 IP 연결은 인스턴스당 이며 인스턴스 크기에 따라 달라 집니다: B1/S1/P1V2 인스턴스당 1,920, B2/S2/P2V2 인스턴스당 3,968개, B3/S3/P3V2 인스턴스당 8,064개입니다.  
<sup>9개</sup> 지원 요청을 통해 구독당 앱 서비스 인증서 할당량 제한을 최대 200개로 늘릴 수 있습니다.  
<sup>10개</sup> 앱 서비스 격리된 SCO는 Azure 로드 밸런서를 통해 ILB(내부적으로 로드 균형 조정)할 수 있으므로 인터넷에서 공용 연결이 없습니다. 그 결과, ILB 격리 App Service의 일부 기능은 ILB 네트워크 엔드포인트와 직접 액세스되는 컴퓨터에서 사용해야 합니다.  
<sup>11세</sup> 주문형, 일정에 따라 또는 App Service 인스턴스 내의 백그라운드 작업으로 사용자 지정 실행 및/또는 스크립트를 계속 실행합니다. Always On은 연속 Webjob 실행을 위해 필요합니다. 앱 서비스 인스턴스에서 실행할 수 있는 WebJobs 수에는 미리 정의된 제한이 없습니다. 응용 프로그램 코드가 수행하려는 내용에 따라 실질적인 제한이 있습니다.  
