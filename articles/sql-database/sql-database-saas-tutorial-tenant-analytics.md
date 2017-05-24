---
title: "여러 테넌트에 대한 분석 쿼리 실행(Azure SQL Database를 사용하는 샘플 SaaS 응용 프로그램) | Microsoft Docs"
description: "여러 테넌트에 대한 분석 쿼리 실행"
keywords: "sql 데이터베이스 자습서"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b512e2f7833be1947ef7674d6e0266879789ac5a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="run-analytics-queries-against-multiple-tenants"></a>여러 테넌트에 대한 분석 쿼리 실행

이 자습서에서는 카탈로그의 각 테넌트에 대한 분석 쿼리를 실행합니다. 쿼리를 실행하는 탄력적 작업이 생성됩니다. 이 작업은 데이터를 검색한 후 이 데이터를 카탈로그 서버에서 생성된 별도의 분석 데이터베이스에 로드합니다. 이 데이터베이스를 쿼리하면 모든 테넌트의 일상적인 운영 데이터에 묻혀 있는 유용한 정보를 찾아낼 수 있습니다. 작업 결과로, 테이블이 테넌트 분석 데이터베이스 내부의 결과 반환 쿼리에서 만들어집니다.


이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 테넌트 분석 데이터베이스 만들기
> * 데이터를 검색하고 분석 데이터베이스를 채우는 예약된 작업 만들기

이 자습서를 수행하려면 다음 필수 조건이 충족되었는지 확인합니다.

* WTP 앱이 배포되었습니다. 5분 내에 배포하려면 [WTP SaaS 응용 프로그램 배포 및 탐색](sql-database-saas-tutorial.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.
* 최신 버전의 SSMS(SQL Server Management Studio)가 설치되어 있습니다. [SSMS 다운로드 및 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>테넌트 운영 분석 패턴

SaaS 응용 프로그램을 사용했을 때의 커다란 기회 중 하나는 클라우드에 저장되어 있는 풍부한 테넌트 데이터를 사용하는 것입니다. 이 데이터를 사용하여 응용 프로그램의 사용 및 작업, 그리고 테넌트에 대한 통찰력을 얻을 수 있습니다. 이 데이터는 기능 개발, 향상된 유용성, 앱 및 플랫폼의 기타 투자에 대한 지침을 제공할 수 있습니다. 단일 다중 테넌트 데이터베이스에 있을 때 이 데이터에 액세스하는 것은 쉽지만 잠재적인 수천 개의 데이터베이스 규모로 분산되는 경우는 그렇게 쉽지 않습니다. 이 데이터에 액세스하는 한 가지 방법은 작업 실행의 결과 반환 쿼리 결과가 출력 데이터베이스 및 테이블에 캡처되도록 할 수 있는 탄력적 작업을 사용하는 것입니다.

## <a name="get-the-wingtip-application-scripts"></a>Wingtip 응용 프로그램 스크립트 가져오기

Wingtip Tickets 스크립트 및 응용 프로그램 소스 코드는 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 리포지토리에서 사용할 수 있습니다. 스크립트 파일은 [Learning Modules 폴더](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)에 있습니다. **Learning Modules** 폴더의 구조를 유지하면서 이 폴더를 로컬 컴퓨터로 다운로드합니다.

## <a name="deploy-a-database-for-tenant-analytics-results"></a>테넌트 분석 결과에 대한 데이터베이스 배포

이 자습서를 사용하려면 결과 반환 쿼리가 포함된 스크립트의 작업 실행 결과를 캡처할 데이터베이스가 있어야 합니다. 이 용도로 사용할 tenantanalytics라는 데이터베이스를 만들어 보겠습니다.

1. *PowerShell ISE*에서 ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1*을 열고 다음 값을 설정합니다.
   * **$DemoScenario** = **2** *운영 분석 데이터베이스 배포*
1. **F5**를 눌러 테넌트 분석 데이터베이스를 만드는 데모 스크립트(*Deploy-TenantAnalyticsDB.ps1* 스크립트 호출)를 실행합니다.

## <a name="create-some-data-for-the-demo"></a>데모를 위한 몇 가지 데이터 만들기

1. *PowerShell ISE*에서 ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1*을 열고 다음 값을 설정합니다.
   * **$DemoScenario** = **1** *모든 장소에서 이벤트 티켓 구입*
1. **F5**를 눌러 스크립트를 실행하고 티켓 구입 기록을 만듭니다.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>티켓 구입에 대한 테넌트 분석을 검색하는 예약된 작업 만들기

이 스크립트는 모든 테넌트에서 티켓 구입 정보를 검색하는 작업을 만듭니다. 단일 테이블에 집계한 후, 테넌트 간에 티켓 구입 패턴에 대한 많은 유용한 메트릭을 얻을 수 있습니다.

1. SSMS를 열고 catalog-\<user\>.database.windows.net 서버에 연결합니다.
1. ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*TicketPurchasesfromAllTenants.sql*을 엽니다.
1. \<WtpUser\>를 수정합니다. **sp\_add\_target\_group\_member** 및 **sp\_add\_jobstep** 스크립트 맨 위에서 WTP 앱을 배포할 때 사용한 사용자 이름을 사용합니다.
1. 마우스 오른쪽 단추로 클릭하고 **연결**을 선택한 후 catalog-\<WtpUser\>.database.windows.net 서버에 연결합니다(아직 연결하지 않은 경우).
1. **jobaccount** 데이터베이스에 연결되어 있는지 확인하고 **F5**를 눌러 스크립트를 실행합니다.

* **sp\_add\_target\_group**은 대상 그룹 이름 *TenantGroup*을 만듭니다. 이제 대상 멤버를 추가해야 합니다.
* **sp\_add\_target\_group\_member**는 *server* 대상 멤버 유형을 추가하는데, 작업 실행 시 해당 서버(테넌트 데이터베이스를 포함하고 있는 customer1-&lt;WtpUser&gt; 서버) 내의 모든 데이터베이스가 작업에 포함되어야 합니다.
* **sp\_add\_job**은 "Ticket Purchases from all Tenants"라는 새로운 주별 예약 작업을 만듭니다.
* **sp\_add\_jobstep**은 모든 테넌트의 티켓 구입 정보를 검색하는 T-SQL 명령 텍스트를 포함하는 작업 단계를 만들고 *AllTicketsPurchasesfromAllTenants* 테이블에 반환 결과 집합을 복사합니다.
* 스크립트의 남은 보기에서 개체의 존재 여부를 표시하고 작업 실행을 모니터링합니다. **lifecycle** 열에서 상태 값을 검토하여 상태를 모니터링합니다. 성공하고 나면, 모든 테넌트 데이터베이스 및 참조 테이블을 포함하는 두 개의 추가 데이터베이스에서 작업을 성공적으로 끝마친 것입니다.

스크립트를 성공적으로 실행하면 다음과 비슷한 결과가 나옵니다.

![결과](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>모든 테넌트의 티켓 구입 요약 개수를 검색하는 작업 만들기

이 스크립트는 모든 테넌트의 모든 티켓 구입 합계를 검색하는 작업을 만듭니다.

1. SSMS를 열고 *catalog-&lt;User&gt;.database.windows.net* 서버에 연결합니다.
1. ...\\Learning Modules\\Provision and Catalog\\Operational Analytics\\Tenant Analytics\\*Results-TicketPurchasesfromAllTenants.sql* 파일을 엽니다.
1. &lt;WtpUser&gt;를 수정합니다. WTP 앱을 배포할 때 **sp\_add\_jobstep** 저장 프로시저에서 사용한 사용자 이름을 사용합니다.
1. 마우스 오른쪽 단추로 클릭하고 **연결**을 선택한 후 catalog-\<WtpUser\>.database.windows.net 서버에 연결합니다(아직 연결하지 않은 경우).
1. **tenantanalytics** 데이터베이스에 연결되어 있는지 확인하고 **F5**를 눌러 스크립트를 실행합니다.

스크립트를 성공적으로 실행하면 다음과 비슷한 결과가 나옵니다.

![결과](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job**은 "ResultsTicketsOrders"라는 새로운 주별 예약된 작업을 만듭니다.

* **sp\_add\_jobstep**은 모든 테넌트의 티켓 구입 정보를 검색하는 T-SQL 명령 텍스트를 포함하는 작업 단계를 만들고 CountofTicketOrders 테이블에 반환 결과 집합을 복사합니다.

* 스크립트의 남은 보기에서 개체의 존재 여부를 표시하고 작업 실행을 모니터링합니다. **lifecycle** 열에서 상태 값을 검토하여 상태를 모니터링합니다. 성공하고 나면, 모든 테넌트 데이터베이스 및 참조 테이블을 포함하는 두 개의 추가 데이터베이스에서 작업을 성공적으로 끝마친 것입니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]
> * 테넌트 분석 데이터베이스 배포
> * 테넌트 간에 분석 데이터를 검색하는 예약된 작업 만들기

축하합니다.

## <a name="additional-resources"></a>추가 리소스

* [초기 WTP(Wingtip Tickets Platform) 응용 프로그램 배포를 기반으로 하는 추가 자습서](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [탄력적 작업](sql-database-elastic-jobs-overview.md)
