---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: a05229f22e1682d73b627e3b4644ba64e653335f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414602"
---
| 리소스 | 체험판 | 공유 | Basic | Standard | 프리미엄(v2) | 격리 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web, mobile, or API apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup> |무제한<sup>2</sup>|
| [App Service 계획](../articles/app-service/overview-hosting-plans.md) |지역당 10개 |리소스 그룹 당 10 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100 |리소스 그룹당 100|
| 컴퓨팅 인스턴스 유형 |공유 |공유 |전용<sup>3</sup> |전용<sup>3</sup> |전용<sup>3</sup></p> |전용<sup>3</sup>|
| [Scale out](../articles/app-service/manage-scale-up.md) (maximum instances) |1개 공유됨 |1개 공유됨 |3개 전용됨<sup>3</sup> |10개 전용됨<sup>3</sup> |30 dedicated<sup>3</sup>|100개 전용됨<sup> 4</sup>|
| 스토리지<sup>5</sup> |1GB<sup>5</sup> |1GB<sup>5</sup> |10GB<sup>5</sup> |50GB<sup>5</sup> |250GB<sup>5</sup></p> |1TB<sup>5</sup>|
| CPU time (5 minutes)<sup>6</sup> |3분 |3분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a>|
| CPU 시간(일)<sup>6</sup> |60분 |240분 |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a> |
| 메모리(1시간) |1,024 MB per App Service plan |1,024 MB per app |N/A |N/A |N/A |N/A |
| Bandwidth |165 MB |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/) 이 적용 |
| 애플리케이션 아키텍처 |32비트 |32비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |32비트/64비트 |
| Web sockets per instance<sup>7</sup> |5 |35 |350 |제한 없음 |제한 없음 |제한 없음 |
| IP connections | 600 | 600 | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | Depends on instance size<sup>8</sup> | 64,000 |
| 애플리케이션당 동시 [디버거 연결](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| App Service Certificates per subscription<sup>9</sup>| 지원하지 않음 | 지원하지 않음 |10 |10 |10 |10 |
| 앱당 사용자 지정 도메인</a> |0(azurewebsites.net 하위 도메인 전용)|500 |500 |500 |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/configure-ssl-certificate.md) |Not supported, wildcard certificate for *.azurewebsites.net available by default|Not supported, wildcard certificate for *.azurewebsites.net available by default|무제한 SNI SSL 연결 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 |무제한 SNI SSL 및 1개의 IP SSL 연결 포함 | 무제한 SNI SSL 및 1개의 IP SSL 연결 포함|
| Hybrid connections per plan | | | 5 | 25 | 200 | 200 |
| Integrated load balancer | |X |X |X |X |X<sup>10</sup> |
| [무중단](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Scheduled backups](../articles/app-service/manage-backup.md) | | | | Scheduled backups every 2 hours, a maximum of 12 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) | Scheduled backups every hour, a maximum of 50 backups per day (manual + scheduled) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) 지원 | |X |X |X |X |X |
| [엔드포인트 모니터링](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Staging slots](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>별도로 명시되지 않는 한 App Service 계획당 앱 및 스토리지 할당량입니다.  
<sup>2</sup>이러한 컴퓨터에서 호스트할 수 있는 실제 앱 수는 앱의 활동, 컴퓨터 인스턴스 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>3</sup>전용 인스턴스의 크기는 다양할 수 있습니다. 자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.  
<sup>4</sup>More are allowed upon request.  
<sup>5</sup>The storage limit is the total content size across all apps in the same App service plan. The total content size of all apps across all App service plans in a single resource group and region cannot exceed 500GB.  
<sup>6</sup>이러한 리소스는 전용 인스턴스의 실제 리소스(인스턴스 크기 및 인스턴스 수)에 의해 제한됩니다.  
<sup>7</sup>두 인스턴스의 Basic 계층으로 앱을 조정하면 두 인스턴스 각각에 대해 동시에 350개가 연결됩니다. For Standard tier and above, there are no theoretical limits to web sockets, but other factors can limit the number of web sockets. For example, maximum concurrent requests allowed (defined by `maxConcurrentRequestsPerCpu`) are: 7,500 per small VM, 15,000 per medium VM (7,500 x 2 cores), and 75,000 per large VM (18,750 x 4 cores).  
<sup>8</sup>The maximum IP connections are per instance and depend on the instance size: 1,920 per B1/S1/P1V2 instance, 3,968 per B2/S2/P2V2 instance, 8,064 per B3/S3/P3V2 instance.  
<sup>9</sup>The App Service Certificate quota limit per subscription can be increased via a support request to a maximum limit of 200.  
<sup>10</sup>App Service Isolated SKUs can be internally load balanced (ILB) with Azure Load Balancer, so there's no public connectivity from the internet. 그 결과, ILB 격리 App Service의 일부 기능은 ILB 네트워크 엔드포인트과 직접 액세스되는 컴퓨터에서 사용해야 합니다.  
<sup>11</sup>Run custom executables and/or scripts on demand, on a schedule, or continuously as a background task within your App Service instance. 연속적인 WebJobs 실행을 위해서는 무중단이 필요합니다. 예약된 WebJobs에는 Azure Scheduler 무료 또는 표준이 필요합니다. There's no predefined limit on the number of WebJobs that can run in an App Service instance. There are practical limits that depend on what the application code is trying to do.  
