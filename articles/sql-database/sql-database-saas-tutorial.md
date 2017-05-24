---
title: "WTP(Wingtip 티켓 플랫폼) 응용 프로그램 배포 및 탐색(Azure SQL Database를 사용하는 샘플 SaaS 응용 프로그램) | Microsoft Docs"
description: "Azure SQL Database를 사용하는 샘플 SaaS 응용 프로그램 배포 및 탐색"
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
ms.openlocfilehash: ff4dc19bb6e24ea9ceeed9721cfb3a85b4d10965
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Azure SQL Database를 사용하는 다중 테넌트 SaaS 응용 프로그램 배포 및 탐색

이 자습서에서는 WTP(Wingtip 티켓 플랫폼) SaaS 응용 프로그램을 배포하고 탐색합니다. 응용 프로그램은 테넌트별 데이터베이스(SaaS 응용 프로그램 패턴)를 사용하여 여러 테넌트에 서비스를 제공합니다. 응용 프로그램은 SaaS 시나리오, SaaS 디자인 및 관리 패턴을 사용할 수 있는 Azure SQL Database의 기능을 보여주기 위해 설계되었습니다.

아래 *Azure에 배포* 단추를 클릭한 후 5분 내에 클라우드에서 가동 및 실행되며 SQL Database를 사용하는 다중 테넌트 SaaS 응용 프로그램을 갖게 됩니다. 이 응용 프로그램은 세 가지 샘플 테넌트를 사용하여 배포되는데, 각각 고유한 데이터베이스가 있으며 모두 SQL 탄력적 풀에 배포됩니다. 앱은 Azure 구독에 배포되므로 개별 응용 프로그램 구성 요소를 검사하고 사용할 수 있는 전체 액세스 권한을 제공합니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]

> * WTP 응용 프로그램을 배포하는 방법
> * 서버, 풀 및 앱을 구성하는 데이터베이스 정보
> * *카탈로그*를 사용하여 데이터에 매핑된 테넌트
> * 새 테넌트를 프로비전하는 방법
> * 풀 사용률을 검토하여 테넌트 작업을 모니터링하는 방법
> * 샘플 리소스를 삭제하여 관련 결제를 중지하는 방법

다양한 SaaS 디자인 및 관리 패턴을 탐색하려면 이 초기 배포 시 생성되는 [일련의 관련 자습서](sql-database-wtp-overview.md)를 이용할 수 있습니다. 이 자습서를 진행하는 동안 제공된 스크립트를 자세히 살펴보고 다양한 SaaS 패턴 구현 방법을 검토해 보세요. 각 자습서의 스크립트를 통해 SaaS 응용 프로그램 개발을 간소화하는 많은 SQL Database 기능을 구현하는 방법을 자세히 알아보세요.

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>WTP(Wingtip 티켓) SaaS 응용 프로그램 배포

5분 내에 Wingtip 티켓 플랫폼을 배포할 수 있습니다.

1. 클릭하여 배포합니다.

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 배포에 필요한 매개 변수 값을 입력합니다.

    > [!IMPORTANT]
    > 일부 인증 및 서버 방화벽은 데모 목적으로 의도적으로 보호되지 않습니다. **새 리소스 그룹을 만들고** 기존 리소스 그룹, 서버 또는 풀은 사용하지 않도록 합니다. 또한 프로덕션에 이 응용 프로그램이나 이 잉용 프로그램으로 만드는 리소스를 사용하지 않도록 합니다. 관련된 결제를 중지하려면 응용 프로그램을 완료할 때 이 리소스 그룹을 삭제합니다.

    * **리소스 그룹** - **새로 만들기**를 선택하고 **이름** 및 **위치**를 제공합니다.
    * **사용자** -일부 리소스는 모든 Azure 구독에서 고유 이름이 필요합니다. 고유성을 보장하기 위해 Wingtip 응용 프로그램을 배포하는 다른 사용자가 만든 리소스와 사용자가 만드는 리소스를 구별할 수 있는 값을 제공합니다. 이니셜 및 숫자(예: *bg1*)와 같은 짧은 **User** 이름을 사용한 다음, 리소스 그룹 이름(예: *wingtip-bg1*)에서 그 이름을 사용하는 것이 좋습니다. **User** 매개 변수에는 문자, 숫자 및 하이픈만 포함할 수 있습니다. 첫 번째와 마지막 문자는 문자 또는 숫자여야 합니다(모두 소문자 권장).

     ![template](./media/sql-database-saas-tutorial/template.png)

1. **응용 프로그램을 배포**합니다.

    * 사용 약관에 동의하는 경우 클릭합니다.
    * **대시보드에 고정**을 선택합니다.
    * **구매**를 클릭합니다.

1. **알림**(검색 상자 오른쪽의 벨 아이콘)을 클릭하여 배포 상태를 모니터링합니다. WTP 앱을 배포하는 데는 4분 정도 걸립니다.

   ![배포 성공](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>응용 프로그램 살펴보기

이 앱에서는 이벤트를 개최하는 콘서트 홀, 재즈 클럽, 스포츠 클럽 등의 장소를 보여 줍니다. 장소는 간단한 방법으로 이벤트를 나열하고 티켓을 판매할 수 있도록 WTP(Wingtip 티켓 플랫폼)의 고객(또는 테넌트)으로 등록됩니다. 각 장소에는 다른 테넌트와 격리되어 독립적으로 해당 이벤트를 관리 및 나열하고 티켓을 판매하는 맞춤형 웹앱이 있습니다. 내부적으로 각 테넌트는 SQL 탄력적 풀에 배포된 SQL 데이터베이스를 가져옵니다.

중앙 **이벤트 허브**는 배포에 특정한 테넌트 URL 목록을 제공합니다. 모든 테넌트 URL에는 특정 *User* 값이 http://events.wtp.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*과 같은 형식으로 포함됩니다. 

1. _이벤트 허브_ 열기: http://events.wtp.&lt;USER&gt;.trafficmanager.net(사용자 이름으로 대체):

    ![이벤트 허브](media/sql-database-saas-tutorial/events-hub.png)

1. *이벤트 허브*에서 **Fabrikam Jazz Club**을 클릭합니다.

   ![이벤트](./media/sql-database-saas-tutorial/fabrikam.png)

1. **티켓**을 클릭하고 이벤트에 대해 구매 중인 티켓을 탐색합니다.

WTP 응용 프로그램에서는 [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)를 사용하여 들어오는 트래픽의 분산을 제어할 수 있습니다. 테넌트와 관련된 이벤트 페이지는 테넌트 이름이 URL에 포함되어야 합니다. 이벤트 앱이 URL에서 테넌트 이름을 구문 분석하고 이를 사용하여 [*분할된 데이터베이스 맵 관리*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management)를 사용하는 카탈로그에 액세스하기 위한 키를 만듭니다. 카탈로그는 테넌트의 데이터베이스 위치에 키를 매핑합니다. **이벤트 허브**는 카탈로그의 확장 메타데이터를 사용하여 각 데이터베이스에 연결된 테넌트의 이름을 검색합니다.

프로덕션 환경에서는 일반적으로 CNAME DNS 레코드를 만들어 [*회사 인터넷 도메인*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain)이 트래픽 관리자 프로필을 가리키도록 합니다.

## <a name="get-the-wingtip-application-scripts"></a>Wingtip 응용 프로그램 스크립트 가져오기

Wingtip Tickets 스크립트 및 응용 프로그램 소스 코드는 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 리포지토리에서 사용할 수 있습니다. 스크립트 파일은 [Learning Modules 폴더](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)에 있습니다. **Learning Modules** 폴더의 구조를 유지하면서 이 폴더를 로컬 컴퓨터로 다운로드합니다.

## <a name="provision-a-new-tenant"></a>새 테넌트 프로비전

초기 배포에서는 세 가지 샘플 테넌트를 만들지만 최상의 자습서 환경을 위해 더 많은 테넌트를 만들어야 합니다. 이 단계에서는 신속하게 새 테넌트를 만들 수 있습니다. 나중에 응용 프로그램에 등록 구성 요소를 구현하고 고객이 등록할 때 테넌트를 자동으로 프로비전하는 간단한 방법을 확인할 수 있는 [프로비전 및 카탈로그 자습서](sql-database-saas-tutorial-provision-and-catalog.md)에서 새 테넌트 프로비전에 대한 세부 정보를 살펴보아야 합니다.

### <a name="initialize-the-user-config-file-for-your-deployment"></a>배포에 대한 사용자 구성 파일 초기화

초기 WTP 응용 프로그램 배포 후 실행하는 첫 번째 스크립트이므로 사용자 구성 파일을 업데이트해야 합니다. 배포의 특정 값으로 변수를 업데이트합니다.

   1. *PowerShell ISE*에서 ...\\Learning Modules\\*UserConfig.psm1*을 엽니다.
   1. 앱을 배포할 때 설정한 _리소스 그룹_으로 _$userConfig.ResourceGroupName_을 수정합니다.
   1. 앱을 배포할 때 설정한 _사용자_ 이름으로 _$userConfig.Name_을 수정합니다.

### <a name="provision-the-new-tenant"></a>새 테넌트 프로비전

1. *PowerShell ISE*에서 ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*을 엽니다.
1. **F5**를 눌러 스크립트를 실행합니다(지금은 스크립트의 기본값을 그대로 사용).

새 테넌트가 카탈로그에 등록됩니다. 데이터베이스는 생성되어 SQL 탄력적 풀에 추가됩니다. 성공적으로 프로비전한 후, 새 테넌트의 티켓 판매 사이트가 브라우저에 나타납니다.

![새 테넌트](./media/sql-database-saas-tutorial/red-maple-racing.png)

이벤트 허브를 새로 고치고 목록에 새 테넌트가 있는지 확인합니다.

## <a name="start-generating-load-on-the-tenant-databases"></a>테넌트 데이터베이스에서 부하 생성 시작

이제 여러 테넌트 데이터베이스가 있으므로 활용해 보도록 하겠습니다. PowerShell 스크립트는 모든 테넌트 데이터베이스에 대해 실행되는 워크로드를 시뮬레이션하는 경우 제공됩니다. 부하는 기본적으로 60분 동안 실행됩니다. Wingtip 티켓은 SaaS 앱이며 SaaS 앱상의 실제 부하는 일반적으로 간헐적이고 예측할 수 없습니다. 이를 시뮬레이션하기 위해 부하 생성기에서 모든 테넌트에 대해 분산된 임의의 부하를 생성합니다. 패턴이 나타나기 위해서는 몇 분 정도 걸리므로, 다음 섹션의 부하 모니터링을 수행하기 전에 약 5-10분 동안 부하 생성기를 실행할 수 있습니다.

1. **PowerShell ISE**에서 ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1*을 엽니다.
1. **F5**를 눌러 스크립트를 실행하고 부하 생성기를 시작합니다(지금은 기본 매개 변수 값을 그대로 사용).

> [!IMPORTANT]
> 부하 생성기는 로컬 PowerShell 세션에서 일련의 작업으로 실행되므로 PowerShell 세션을 열어 둡니다. PowerShell을 닫거나 부하 생성기를 시작한 탭을 닫거나 컴퓨터를 일시 중단하면 작업이 중지됩니다.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>서버, 풀 및 테넌트 데이터베이스 탐색

응용 프로그램을 탐색하고 새 테넌트를 프로비전하고 테넌트 컬렉션에 대해 부하 실행을 시작했으므로 배포된 리소스 중 일부를 살펴보겠습니다.

1. [Azure Portal](http://portal.azure.com)에서 **catalog-&lt;USER&gt;** 서버를 엽니다. 카탈로그 서버에는 두 개의 데이터베이스가 포함되어 있습니다. **tenantcatalog** 및 **basetenantdb**(새 테넌트를 만들기 위해 복사한 빈 *golden* db)입니다.

   ![데이터베이스](./media/sql-database-saas-tutorial/databases.png)

1. 테넌트 데이터베이스가 있는 **tenants1-&lt;USER&gt;** 서버를 엽니다. 각 테넌트 데이터베이스는 50eDTU 표준 풀의 _탄력적 표준_ 데이터베이스입니다. 또한 이전에 프로비전한 테넌트인 _Red Maple Racing_ 데이터베이스가 있습니다.

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>풀 모니터링

몇 분 동안 부하 생성기를 실행한 경우 풀 및 데이터베이스에 기본 제공되는 모니터링 기능 중 몇 가지를 시작할 정도의 충분한 데이터를 이용할 수 있습니다.

1. *tenants1-&lt;USER&gt;* 서버로 이동한 후 **Pool1**을 클릭하여 풀의 리소스 사용률을 확인합니다.

   ![풀 모니터링](./media/sql-database-saas-tutorial/monitor-pool.png)

이 두 차트는 SaaS 응용 프로그램 워크로드에 대해 탄력적 풀 및 SQL Database가 얼마나 적합한지 보기 좋게 설명하고 있습니다. 40eDTU까지 폭발적으로 증가하고 있는 4개의 데이터베이스가 50eDTU 풀에서 손쉽게 지원됩니다. 각각이 독립 실행형 데이터베이스로 프로비전된 경우, 각각은 갑작스런 증가를 지원하는 S2(50 DTU)가 되어야 하지만 4개의 독립 실행형 S2 데이터베이스의 비용은 거의 풀 가격의 3배가 됩니다. 또한 풀에는 아직 많은 데이터베이스에 충분한 여유가 있습니다. 실제 상황에서 고객은 현재 200eDTU 풀에서 최대 500개의 데이터베이스까지 실행 중입니다. 자세한 내용은 [성능 모니터링 자습서](sql-database-saas-tutorial-performance-monitoring.md)를 참조하세요.


## <a name="deleting-the-resources-created-with-this-tutorial"></a>이 자습서를 사용하여 만든 리소스 삭제

WTP 앱 탐색 및 작업을 완료했으면 포털에서 응용 프로그램의 리소스 그룹으로 이동한 후 이를 삭제하여 이 배포와 관련된 모든 청구를 중지합니다. 함께 제공되는 자습서 중 하나를 사용한 경우, 만든 모든 리소스가 해당 응용 프로그램과 함께 삭제됩니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]

> * WTP 응용 프로그램을 배포하는 방법
> * 서버, 풀 및 앱을 구성하는 데이터베이스 정보
> * *카탈로그*를 사용하여 데이터에 매핑된 테넌트
> * 새 테넌트를 프로비전하는 방법
> * 풀 사용률을 검토하여 테넌트 작업을 모니터링하는 방법
> * 샘플 리소스를 삭제하여 관련 결제를 중지하는 방법

이제 [프로비전 및 카탈로그 자습서](sql-database-saas-tutorial-provision-and-catalog.md)를 사용해 보세요.



## <a name="additional-resources"></a>추가 리소스

* [초기 WTP(Wingtip Tickets Platform) 응용 프로그램 배포를 기반으로 하는 추가 자습서](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* 탄력적 풀에 대한 자세한 내용은 [*Azure SQL 탄력적 풀이란?*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)을 참조하세요.
* 탄력적 작업에 대해 알아보려면 [*규모가 확장된 클라우드 데이터베이스 관리*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)를 참조하세요.
* 다중 테넌트 SaaS 응용 프로그램에 대해 알아보려면 [*다중 테넌트 SaaS 응용 프로그램을 위한 디자인 패턴*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)을 참조하세요.

