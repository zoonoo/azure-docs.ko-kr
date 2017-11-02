---
title: "다중 테넌트 SaaS 자습서 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database를 사용하는 SaaS 패턴을 보여 주는 Wingtip SaaS 다중 테넌트 응용 프로그램을 배포하고 탐색합니다."
keywords: "SQL Database 자습서"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: sstein
ms.openlocfilehash: f624485d32901abb5f0d01c86fc30ddaaae261a3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-and-explore-a-multi-tenant-application-that-uses-azure-sql-database---wingtip-saas"></a>Azure SQL Database를 사용하는 다중 테넌트 응용 프로그램 배포 및 탐색 - Wingtip SaaS

이 자습서에서는 Wingtip SaaS 응용 프로그램을 배포하고 탐색합니다. 응용 프로그램은 테넌트별 데이터베이스(SaaS 응용 프로그램 패턴)를 사용하여 여러 테넌트에 서비스를 제공합니다. 응용 프로그램은 SaaS 시나리오를 간소화하는 Azure SQL Database의 기능을 보여 주도록 설계되었습니다.

아래 *Azure에 배포* 단추를 클릭한 후 5분 내에 클라우드에서 가동 및 실행되며 SQL Database를 사용하는 다중 테넌트 SaaS 응용 프로그램을 갖게 됩니다. 이 응용 프로그램은 세 가지 샘플 테넌트를 사용하여 배포되는데, 각각 고유한 데이터베이스가 있으며 모두 SQL 탄력적 풀에 배포됩니다. 앱은 Azure 구독에 배포되므로 개별 응용 프로그램 구성 요소를 탐색하고 사용할 수 있는 전체 액세스 권한을 제공합니다. 응용 프로그램 소스 코드와 관리 스크립트는 WingtipSaaS GitHub 리포지토리에서 사용할 수 있습니다.


이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]

> * Wingtip SaaS 응용 프로그램을 배포하는 방법
> * 응용 프로그램 소스 코드 및 관리 스크립트를 가져올 위치
> * 서버, 풀 및 앱을 구성하는 데이터베이스 정보
> * *카탈로그*를 통해 테넌트가 데이터에 매핑되는 방법
> * 새 테넌트를 프로비전하는 방법
> * 앱에서 테넌트 활동을 모니터링하는 방법

다양한 SaaS 디자인 및 관리 패턴을 탐색하려면 이 초기 배포 시 생성되는 [일련의 관련 자습서](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)를 이용할 수 있습니다. 이 자습서를 진행하는 동안 제공된 스크립트를 자세히 살펴보고 다양한 SaaS 패턴 구현 방법을 검토해 보세요. 각 자습서의 스크립트를 통해 SaaS 응용 프로그램 개발을 간소화하는 많은 SQL Database 기능을 구현하는 방법을 자세히 알아보세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="deploy-the-wingtip-saas-application"></a>Wingtip SaaS 응용 프로그램 배포

Wingtip SaaS 앱을 배포합니다.

1. **Azure에 배포** 단추를 클릭하면 Azure Portal이 Wingtip SaaS 배포 템플릿으로 열립니다. 템플릿에는 새 리소스 그룹의 이름 및 Wingtip SaaS 앱의 다른 배포와 이 배포를 구분하는 사용자 이름이 매개 변수 값으로 필요합니다. 다음 단계에서는 이러한 값을 설정하는 방법을 자세히 설명합니다.

   나중에 구성 파일에 입력해야 하므로 사용할 정확한 값을 기록해 두어야 합니다.

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 배포에 필요한 매개 변수 값을 입력합니다.

    > [!IMPORTANT]
    > 일부 인증 및 서버 방화벽은 데모 목적으로 의도적으로 보호되지 않습니다. **새 리소스 그룹을 만들고**, 기존 리소스 그룹, 서버 또는 풀을 사용하지 마세요. 이 응용 프로그램이나 여기에서 만든 리소스를 프로덕션에 사용하지 마세요. 관련된 결제를 중지하려면 응용 프로그램을 완료할 때 이 리소스 그룹을 삭제합니다.

    * **리소스 그룹** - **새로 만들기**를 선택하고 리소스 그룹의 **이름**을 입력합니다. 드롭다운 목록에서 **위치**를 선택합니다.
    * **사용자** - 일부 리소스에는 전역적으로 고유한 이름이 필요합니다. 고유성을 보장하기 위해 응용 프로그램을 배포할 때마다 직접 만든 리소스와 다른 Wingtip 응용 프로그램 배포 시 생성된 리소스를 구별할 수 있는 값을 제공합니다. 이니셜 및 숫자(예: *bg1*)와 같은 짧은 **User** 이름을 사용한 다음, 리소스 그룹 이름(예: *wingtip-bg1*)에서 그 이름을 사용하는 것이 좋습니다. **User** 매개 변수에는 문자, 숫자 및 하이픈만 포함할 수 있습니다(공백 없음). 첫 번째와 마지막 문자는 문자 또는 숫자여야 합니다(모두 소문자 권장).


1. **응용 프로그램을 배포**합니다.

    * 사용 약관에 동의하려면 클릭합니다.
    * **구매**를 클릭합니다.

1. **알림**(검색 상자 오른쪽의 벨 아이콘)을 클릭하여 배포 상태를 모니터링합니다. Wingtip SaaS 앱을 배포하는 데는 5분 정도 걸립니다.

   ![배포 성공](media/sql-database-saas-tutorial/succeeded.png)

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Wingtip SaaS 스크립트 다운로드 및 차단 해제

응용 프로그램이 배포되는 동안 소스 코드 및 관리 스크립트를 다운로드합니다.

> [!IMPORTANT]
> zip 파일이 외부 원본에서 다운로드되고 추출될 때 Windows에서 실행 가능한 콘텐츠(스크립트, dll)를 차단할 수 있습니다. Zip 파일에서 스크립트를 추출할 경우 아래 단계에 따라 추출하기 전에 .zip 파일을 차단 해제하세요. 이렇게 하면 스크립트를 실행할 수 있습니다.

1. [Wingtip SaaS GitHub 리포지토리](https://github.com/Microsoft/WingtipSaaS)로 이동합니다.
1. **복제 또는 다운로드**를 클릭합니다.
1. **ZIP 다운로드**를 클릭하고 파일을 저장합니다.
1. **WingtipSaaS-master.zip** 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
1. **일반** 탭에서 **차단 해제**를 선택하고 **적용**을 클릭합니다.
1. **확인**을 클릭합니다.
1. 파일의 압축을 풉니다.

스크립트는 *..\\WingtipSaaS-master\\Learning Modules* 폴더에 있습니다.

## <a name="update-the-configuration-file-for-this-deployment"></a>이 배포에 대한 구성 파일 업데이트

스크립트를 실행하기 전에 **UserConfig.psm1**에서 *리소스 그룹* 및 *사용자* 값을 설정합니다. 이러한 변수를 배포 중에 설정한 값으로 설정합니다.

1. *PowerShell ISE*에서 ...\\Learning Modules\\*UserConfig.psm1*을 엽니다.
1. 배포에 대한 특정 값(줄 10 및 11에서만)으로 *ResourceGroupName* 및 *Name*을 업데이트합니다.
1. 변경 내용을 저장합니다.

여기서 이렇게 설정하면 모든 스크립트에서 이러한 배포별 값을 업데이트할 필요가 없습니다.

## <a name="run-the-application"></a>응용 프로그램 실행

이 앱에서는 이벤트를 개최하는 콘서트 홀, 재즈 클럽, 스포츠 클럽 등의 장소를 보여 줍니다. 장소는 간단한 방법으로 이벤트를 나열하고 티켓을 판매할 수 있도록 Wingtip 플랫폼의 고객(또는 테넌트)으로 등록됩니다. 각 장소에는 다른 테넌트와 격리되어 독립적으로 해당 이벤트를 관리 및 나열하고 티켓을 판매하는 맞춤형 웹앱이 있습니다. 내부적으로 각 테넌트는 SQL 탄력적 풀에 배포된 SQL 데이터베이스를 가져옵니다.

중앙 **이벤트 허브**는 배포에 특정한 테넌트 URL 목록을 제공합니다.

1. 웹 브라우저에서 _이벤트 허브_ http://events.wtp.&lt;USER&gt;.trafficmanager.net을 엽니다. 여기서 USER는 배포의 사용자 이름으로 바꿉니다.

    ![Events Hub](media/sql-database-saas-tutorial/events-hub.png)

1. *이벤트 허브*에서 **Fabrikam Jazz Club**을 클릭합니다.

   ![이벤트](./media/sql-database-saas-tutorial/fabrikam.png)


들어오는 요청의 배포를 제어하기 위해 앱에서는 [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)를 사용합니다. 테넌트와 관련된 이벤트 페이지는 테넌트 이름이 URL에 포함되어야 합니다. 모든 테넌트 URL에는 특정 *User* 값이 http://events.wtp.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*과 같은 형식으로 포함됩니다. 이벤트 앱이 URL에서 테넌트 이름을 구문 분석하고 이를 사용하여 [*분할된 데이터베이스 맵 관리*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management)를 사용하는 카탈로그에 액세스하기 위한 키를 만듭니다. 카탈로그는 테넌트의 데이터베이스 위치에 키를 매핑합니다. **이벤트 허브**는 카탈로그의 확장 메타데이터를 통해 각 데이터베이스에 연결된 테넌트의 이름을 검색하여 URL 목록을 제공합니다.

프로덕션 환경에서는 일반적으로 CNAME DNS 레코드를 만들어 [*회사 인터넷 도메인*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain)이 트래픽 관리자 프로필을 가리키도록 합니다.

## <a name="start-generating-load-on-the-tenant-databases"></a>테넌트 데이터베이스에서 부하 생성 시작

이제 앱이 배포되었으므로 작동해 보겠습니다. *Demo-LoadGenerator* PowerShell 스크립트는 모든 테넌트 데이터베이스에서 실행되는 워크로드를 시작합니다. 많은 SaaS 앱상의 실제 부하는 일반적으로 간헐적이고 예측할 수 없습니다. 이 유형의 부하를 시뮬레이션하기 위해 생성기에서는 임의 간격으로 발생하는 각 테넌트에 대한 임의 버스트를 통해 모든 테넌트에 분산된 부하를 생성합니다. 이로 인해 부하 패턴이 나타나는 데는 몇 분 정도 걸리므로 부하를 모니터링하기 전에 생성기를 최소한 3~4분 실행하는 것이 가장 좋습니다.

1. *PowerShell ISE*에서 ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* 스크립트를 엽니다.
1. **F5**를 눌러 스크립트를 실행하고 부하 생성기를 시작합니다(지금은 기본 매개 변수 값을 그대로 사용).

> [!IMPORTANT]
> 다른 스크립트를 실행하려면 새 PowerShell ISE 창을 엽니다. 부하 생성기는 로컬 PowerShell 세션에서 일련의 작업으로 실행 중입니다. *Demo-LoadGenerator.ps1* 스크립트는 실제 부하 생성기 스크립트를 포그라운드 작업 및 일련의 백그라운드 부하 생성 작업으로 시작합니다. 카탈로그에 등록된 각 데이터베이스에 대해 부하 생성기 작업이 호출됩니다. 작업은 로컬 PowerShell 세션에서 실행되므로 PowerShell 세션을 닫으면 모든 작업이 중지합니다. 컴퓨터를 일시 중단하면 부하 생성이 일시 중지되고 컴퓨터의 절전 모드를 해제하면 다시 시작됩니다.

부하 생성기가 각 테넌트에 대한 부하 생성 작업을 호출한 후 포그라운드 작업은 작업 호출 상태로 유지됩니다. 이 상태에서는 부하 생성기가 이후에 프로비전되는 새 테넌트에 대한 추가 백그라운드 작업을 시작합니다. *Ctrl-C*를 사용하거나 *중지* 단추를 눌러 포그라운드 작업을 중지할 수 있지만, 기존 백그라운드 작업은 각 데이터베이스에 대한 부하를 계속 생성합니다. 백그라운드 작업을 모니터링 및 제어해야 하는 경우 *Get-Job*, *Receive-Job* 및 *Stop-Job*을 사용합니다. 포그라운드 작업이 실행되는 동안 다른 스크립트를 실행하는 데는 동일한 PowerShell 세션을 사용할 수 없습니다. 다른 스크립트를 실행하려면 새 PowerShell ISE 창을 엽니다.

예를 들어 다른 매개 변수를 사용하여 부하 생성기 세션을 다시 시작하려면 포그라운드 작업을 중지하고 나서 *Demo-LoadGenerator.ps1* 스크립트를 다시 실행하면 됩니다. *Demo-LoadGenerator.ps1*을 다시 실행하면 먼저 현재 실행 중인 작업이 중지되고 나서 생성기가 다시 시작되고 이 생성기가 현재 매개 변수를 사용하여 새 작업 집합을 시작합니다.

지금은 부하 생성기를 작업 호출 상태로 계속 실행합니다.


## <a name="provision-a-new-tenant"></a>새 테넌트 프로비전

초기 배포 시에는 세 개의 샘플 테넌트가 생성되지만, 또 다른 테넌트를 만들어 이것이 배포된 응용 프로그램에 어떻게 영향을 미치는지 확인해 보겠습니다. Wingtip SaaS 프로비전 테넌트 워크플로는 [프로비전 및 카탈로그 자습서](sql-database-saas-tutorial-provision-and-catalog.md)에서 자세히 설명합니다. 이 단계에서는 신속하게 새 테넌트를 만들 수 있습니다.

1. *PowerShell ISE*에서 ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*을 엽니다.
1. **F5** 키를 눌러 스크립트를 실행합니다(지금은 기본값을 그대로 사용).

   > [!NOTE]
   > 대부분의 Wingtip SaaS 스크립트에서는 *$PSScriptRoot*를 사용하여 다른 스크립트에 있는 함수를 호출하기 위한 폴더 탐색을 허용합니다. 이 변수는 **F5** 키를 눌러 스크립트를 실행할 경우에만 평가됩니다.  선택 내용을 강조 표시하고 실행(**F8**)하면 오류가 발생할 수 있으므로 스크립트를 실행할 때는 **F5** 키를 누르세요.

새 테넌트 데이터베이스는 SQL 탄력적 풀에 생성되고 카탈로그에서 초기화 및 등록됩니다. 성공적으로 프로비전한 후, 새 테넌트의 티켓 판매 *이벤트* 사이트가 브라우저에 나타납니다.

![새 테넌트](./media/sql-database-saas-tutorial/red-maple-racing.png)

*이벤트 허브*를 새로 고치면 이제 목록에 새 테넌트가 나타납니다.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>서버, 풀 및 테넌트 데이터베이스 탐색

테넌트 컬렉션에 대해 부하 실행을 시작했으므로 배포된 리소스 중 일부를 살펴보겠습니다.

1. [Azure Portal](http://portal.azure.com)에서 SQL 서버 목록으로 이동하여 **catalog-&lt;USER&gt;** 서버를 엽니다. 카탈로그 서버에는 두 개의 데이터베이스가 포함되어 있습니다. **tenantcatalog** 및 **basetenantdb**(새 테넌트를 만들기 위해 복사한 빈 *golden* 또는 템플릿 데이터베이스)입니다.

   ![데이터베이스](./media/sql-database-saas-tutorial/databases.png)

1. SQL 서버 목록으로 돌아와서 테넌트 데이터베이스가 있는 **tenants1-&lt;USER&gt;** 서버를 엽니다. 각 테넌트 데이터베이스는 50eDTU 표준 풀의 _탄력적 표준_ 데이터베이스입니다. 또한 이전에 프로비전한 테넌트 데이터베이스인 _Red Maple Racing_ 데이터베이스가 있습니다.

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>풀 모니터링

몇 분 동안 부하 생성기를 실행한 경우 풀 및 데이터베이스에 기본 제공되는 모니터링 기능 중 몇 가지를 시작할 정도의 충분한 데이터를 이용할 수 있습니다.

1. **tenants1-&lt;USER&gt;** 서버로 이동한 후 **Pool1**을 클릭하여 풀의 리소스 사용률을 확인합니다(다음 차트에서는 부하 생성기가 1시간 동안 실행됨).

   ![풀 모니터링](./media/sql-database-saas-tutorial/monitor-pool.png)

이 두 차트는 SaaS 응용 프로그램 워크로드에 대해 탄력적 풀 및 SQL Database가 얼마나 적합한지 보기 좋게 설명하고 있습니다. 40eDTU까지 폭발적으로 증가하고 있는 4개의 데이터베이스가 50eDTU 풀에서 손쉽게 지원됩니다. 데이터베이스가 독립 실행형 데이터베이스로 프로비전된 경우, 데이터베이스는 각각 버스트를 지원하는 S2(50 DTU)가 되어야 합니다. 4개의 독립 실행형 S2 데이터베이스의 비용은 거의 풀 가격의 3배가 되고 풀에는 아직 많은 데이터베이스에 충분한 여유가 있습니다. 실제 상황에서 SQL Database 고객은 현재 200eDTU 풀에서 최대 500개의 데이터베이스까지 실행 중입니다. 자세한 내용은 [성능 모니터링 자습서](sql-database-saas-tutorial-performance-monitoring.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]

> * Wingtip SaaS 응용 프로그램을 배포하는 방법
> * 서버, 풀 및 앱을 구성하는 데이터베이스 정보
> * *카탈로그*를 사용하여 데이터에 매핑된 테넌트
> * 새 테넌트를 프로비전하는 방법
> * 풀 사용률을 검토하여 테넌트 작업을 모니터링하는 방법
> * 샘플 리소스를 삭제하여 관련 결제를 중지하는 방법

이제 [프로비전 및 카탈로그 자습서](sql-database-saas-tutorial-provision-and-catalog.md)를 사용해 보세요.



## <a name="additional-resources"></a>추가 리소스

* [Wingtip SaaS 응용 프로그램을 기반으로 작성된 추가 자습서](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* 탄력적 풀에 대한 자세한 내용은 [*Azure SQL 탄력적 풀이란?*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)을 참조하세요.
* 탄력적 작업에 대해 알아보려면 [*규모가 확장된 클라우드 데이터베이스 관리*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)를 참조하세요.
* 다중 테넌트 SaaS 응용 프로그램에 대해 알아보려면 [*다중 테넌트 SaaS 응용 프로그램을 위한 디자인 패턴*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)을 참조하세요.
