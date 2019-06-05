---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ce64047fd7490106790ea8bb1ad7963d82a87c24
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238451"
---
| 리소스 | 무료 | 공유됨 | Basic | Standard | 프리미엄(v2) | 격리 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [웹, 모바일 또는 API 앱](https://azure.microsoft.com/services/app-service/) 당 [Azure App Service 계획](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup>|
| [App Service 계획](../articles/app-service/overview-hosting-plans.md) |지역당 10개 |리소스 그룹 당 10 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100|
| 컴퓨팅 인스턴스 유형 |공유됨 |공유됨 |전용<sup>3</sup> |전용<sup>3</sup> |전용<sup>3</sup></p> |전용<sup>3</sup>|
| [규모 확장](../articles/app-service/web-sites-scale.md) (최대 인스턴스) |1개 공유됨 |1개 공유됨 |3개 전용됨<sup>3</sup> |10개 전용됨<sup>3</sup> |20개 전용됨<sup>3</sup>|100개 전용됨<sup> 4</sup>|
| 저장소<sup>5</sup> |1GB<sup>5</sup> |1GB<sup>5</sup> |10GB<sup>5</sup> |50GB<sup>5</sup> |250GB<sup>5</sup></p> |1TB<sup>5</sup>|
| CPU 시간 (5 분)<sup>6</sup> |3분 |3분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a>|
| CPU 시간(일)<sup>6</sup> |60분 |240분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |
| 메모리(1시간) |App Service 계획 당 1,024MB |앱 당 1,024MB |N/A |해당 사항 없음 |해당 사항 없음 |N/A |
| 대역폭 |165 MB |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |
| 애플리케이션 아키텍처 |32비트 |32비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |
| 웹 소켓 인스턴스당<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |무제한 |
| 애플리케이션당 동시 [디버거 연결](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| App Service 구독 당 인증서<sup>10</sup>| 지원되지 않음 | 지원되지 않음 |10 |10 |10 |10 |
| 앱당 사용자 지정 도메인</a> |0(azurewebsites.net 하위 도메인 전용)|500 |500 |500 |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |지원 되지 않음, 와일드 카드 인증서에 대 한 *. azurewebsites.net 기본적으로 사용 가능|지원 되지 않음, 와일드 카드 인증서에 대 한 *. azurewebsites.net 기본적으로 사용 가능|무제한 SNI SSL 연결 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함|
| 통합 된 부하 분산 장치 | |X |X |X |X |X<sup>9</sup> |
| [무중단](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [예약 된 백업](../articles/app-service/manage-backup.md) | | | | 예약 된 백업 2 시간 마다 최대 12 개의 백업 (수동 + 예약) 하루 | 예약 된 백업 시간에는 최대 50 개의 백업 (수동 + 예약) 하루 | 예약 된 백업 시간에는 최대 50 개의 백업 (수동 + 예약) 하루 |
| [Autoscale](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) 지원 | |X |X |X |X |X |
| [엔드포인트 모니터링](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.9% |99.95%|99.95%|99.95%|  

<sup>1</sup>별도로 명시되지 않는 한 App Service 계획당 앱 및 저장소 할당량입니다.  
<sup>2</sup>이러한 컴퓨터에서 호스트할 수 있는 실제 앱 수는 앱의 활동, 컴퓨터 인스턴스 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>3</sup>전용 인스턴스의 크기는 다양할 수 있습니다. 자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.  
<sup>4</sup>요청 시 더 허용 됩니다.  
<sup>5</sup>저장소 제한은 동일한 App Service 계획에서 모든 앱에 걸쳐 총 콘텐츠 크기입니다.  
<sup>6</sup>이러한 리소스는 전용 인스턴스의 실제 리소스(인스턴스 크기 및 인스턴스 수)에 의해 제한됩니다.  
<sup>7</sup>두 인스턴스의 Basic 계층으로 앱을 조정하면 두 인스턴스 각각에 대해 동시에 350개가 연결됩니다.  
<sup>8</sup>사용자 지정 실행 파일 및/또는 스크립트를 주문형, 예약형 또는 지속형으로 App Service 인스턴스 내에서 백그라운드 작업으로 실행합니다. Always On은 연속 Webjob 실행을 위해 필요합니다. Azure Scheduler 무료 또는 표준은 예약된 WebJobs에 필요합니다. App Service 인스턴스에서 실행할 수 있는 Webjob의 수에 미리 정의 된 제한은 없습니다. 응용 프로그램 코드는 수행 하려는 작업에 의존 하는 실질적인 제한이 있습니다.  
<sup>9</sup>app Service 격리 Sku 수 내부적으로 부하 분산된 (ILB) Azure Load Balancer를 사용 하 여 인터넷에서 공용 연결성이 없습니다 이므로 수 있습니다. 그 결과, ILB 격리 App Service의 일부 기능은 ILB 네트워크 엔드포인트와 직접 액세스되는 컴퓨터에서 사용해야 합니다.  
<sup>10</sup>200의 최대 제한 지원 요청을 통해 구독 당 the App Service Certificate 할당량 한도 늘릴 수 있습니다.  