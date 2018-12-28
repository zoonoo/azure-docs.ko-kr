---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 515aeac3531a45080824df126ad674353f70cdb8
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279785"
---
| 리소스 | 무료 | 공유됨 | Basic | Standard | 프리미엄(v2) | 격리 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [App Service 계획](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)당 [웹, 모바일 또는 API 앱](https://azure.microsoft.com/services/app-service/)<sup>1</sup> |10 |100 |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup>|
| [App Service 계획](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |지역당 1개 |리소스 그룹 당 10 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100|
| Compute 인스턴스 유형 |공유됨 |공유됨 |전용<sup>3</sup> |전용<sup>3</sup> |전용<sup>3</sup></p> |전용<sup>3</sup>|
| [확장](../articles/app-service/web-sites-scale.md)(최대 인스턴스 수) |1개 공유됨 |1개 공유됨 |3개 전용됨<sup>3</sup> |10개 전용됨<sup>3</sup> |20개 전용됨<sup>3</sup>|100개 전용됨<sup> 4</sup>|
| 저장소<sup>5</sup> |1GB<sup>5</sup> |1GB<sup>5</sup> |10GB<sup>5</sup> |50GB<sup>5</sup> |250GB<sup>5</sup></p> |1TB<sup>5</sup>|
| CPU 시간(5분)<sup>6</sup> |3분 |3분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a>|
| CPU 시간(일)<sup>6</sup> |60분 |240분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |
| 메모리(1시간) |App Service 계획 당 1024MB |앱 당 1024MB |해당 없음 |해당 없음 |해당 없음 |해당 없음 |
| 대역폭 |165 MB |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |
| 응용 프로그램 아키텍처 |32비트 |32비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |
| 인스턴스당 웹 소켓<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |무제한 |
| 애플리케이션당 동시 [디버거 연결](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| 구독당 App Service 인증서 수<sup>10</sup>| 지원되지 않음 | 지원되지 않음 |10 |10 |10 |10 |
| 앱당 사용자 지정 도메인</a> |0(azurewebsites.net 하위 도메인 전용)|500 |500 |500 |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |지원되지 않습니다. 기본적으로 사용할 수 있는 *.azurewebsites.net의 와일드카드 인증서입니다.|지원되지 않습니다. 기본적으로 사용할 수 있는 *.azurewebsites.net의 와일드카드 인증서입니다.|무제한 SNI SSL 연결 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함|
| 통합 부하 분산 장치 | |X |X |X |X |X<sup>9</sup> |
| [무중단](../articles/app-service/web-sites-configure.md) | | |X |X |X |X |
| [예약된 Backup](../articles/app-service/web-sites-backup.md) | | | | 2시간마다 예약된 백업, 매일 최대 12개의 백업(수동 + 예약) | 1시간마다 예약된 백업, 매일 최대 50개의 백업(수동 + 예약) | 1시간마다 예약된 백업, 매일 최대 50개의 백업(수동 + 예약) |
| [자동 크기 조정](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) 지원 | |X |X |X |X |X |
| [엔드포인트 모니터링](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [스테이징 슬롯](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |20 |
| SLA | |  |99.9% |99.95%|99.95%|99.95%|  

<sup>1</sup> 별도로 명시하지 않는 한 App Service 계획당 앱 및 스토리지 할당량입니다.  
<sup>2</sup> 이러한 머신에서 호스팅할 수 있는 앱의 실제 수는 앱의 활동, 머신 인스턴스의 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>3</sup> 전용 인스턴스의 크기는 다를 수 있습니다. 자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하십시오.  
<sup>4</sup> 요청 시 더 많이 사용할 수 있습니다.  
<sup>5</sup> 스토리지 제한은 동일한 App Service 계획에 있는 모든 앱의 총 콘텐츠 크기입니다.  
<sup>6</sup> 이러한 리소스는 전용 인스턴스의 실제 리소스(인스턴스 크기 및 인스턴스 수)로 제한됩니다.  
<sup>7</sup> 기본 계층의 앱을 두 개의 인스턴스로 확장하면 두 인스턴스 각각에 350개의 동시 연결이 있습니다.  
<sup>8</sup> App Service 인스턴스 내에서 요청 시, 일정에 따라 또는 연속적인 백그라운드 작업으로 사용자 지정 실행 파일 및/또는 스크립트를 실행합니다. Always On은 연속 Webjob 실행을 위해 필요합니다. Azure Scheduler 무료 또는 표준은 예약된 WebJobs에 필요합니다. App Service 인스턴스를 실행할 수 있는 WebJob의 수에 미리 정의된 제한은 없지만 응용 프로그램 코드가 수행하려는 작업에 의존하는 실질적인 제한이 있습니다.  
<sup>9</sup> App Service 격리 SKU에는 Azure Load Balancer를 통해 내부적으로 부하가 분산되는 기능(ILB)이 있습니다. 즉 인터넷의 공용 연결성이 없습니다. 그 결과, ILB 격리 App Service의 일부 기능은 ILB 네트워크 엔드포인트와 직접 액세스되는 컴퓨터에서 사용해야 합니다.  
<sup>10</sup> 구독당 App Service Certificate 할당량 한도는 지원 요청을 통해 최대 200개까지 늘릴 수 있습니다.  