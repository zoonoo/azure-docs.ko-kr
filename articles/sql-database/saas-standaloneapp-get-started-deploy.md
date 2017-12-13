---
title: "다중 테넌트 SaaS 자습서 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database를 사용하는 독립형 단일 테넌트 SaaS 응용 프로그램 배포 및 탐색"
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
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: d38cd108821bce05824732bbdbdd322ae8563bde
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database를 사용하는 독립형 단일 테넌트 응용 프로그램을 배포하고 탐색합니다.

이 자습서에서는 Wingtip 티켓 SaaS 독립 실행형 응용 프로그램을 배포하고 탐색합니다. 응용 프로그램은 SaaS 시나리오를 간소화하는 Azure SQL Database의 기능을 보여 주도록 설계되었습니다.

독립형 응용 프로그램 패턴은 단일 테넌트 응용 프로그램이 포함된 Azure 리소스 그룹과 각 테넌트에 대한 단일 테넌트 데이터베이스를 배포합니다.  다중 테넌트 솔루션을 제공하기 위해 응용 프로그램의 여러 인스턴스를 프로비전할 수 있습니다.

이 자습서에서는 Azure 구독에 여러 테넌트에 대한 리소스 그룹을 배포합니다.  이 패턴에서는 리소스 그룹을 테넌트의 Azure 구독에 배포할 수 있습니다. Azure에는 테넌트를 대표하여 서비스 공급자가 이러한 리소스 그룹을 관리하도록 허용하는 파트너 프로그램이 있습니다. 서비스 공급자는 테넌트 구독의 관리자입니다.

이후 배포 섹션에서는 3가지 파란색 **Azure에 배포** 단추가 표시됩니다. 각 단추는 응용 프로그램의 다른 인스턴스를 배포합니다. 각 인스턴스는 특정 테넌트에 맞게 사용자 지정됩니다. 각각의 버튼을 누르면 해당 응용 프로그램이 5분 내에 완전히 배포됩니다.  앱은 Azure 구독에 배포됩니다.  개별 응용 프로그램 구성 요소를 탐색하고 작업할 수 있는 전체 액세스 권한이 있습니다.

응용 프로그램 소스 코드 및 관리 스크립트는 [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub 리포지토리에서 사용할 수 있습니다.


이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Wingtip 티켓 SaaS 독립형 응용 프로그램 배포 방법
> * 응용 프로그램 소스 코드 및 관리 스크립트를 가져올 위치.
> * 앱을 구성하는 서버 및 데이터베이스 정보.

추가 자습서가 제공될 예정입니다. 이러한 자습서를 통해 이 응용 프로그램 패턴을 기준으로 다양한 관리 시나리오를 탐색할 수 있습니다.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wingtip 티켓 SaaS 독립형 응용 프로그램 배포

제공된 3개 테넌트에 대한 앱을 배포합니다.

1. [Azure Portal](https://portal.azure.com)에서 배포 템플릿을 열려면 파란색의 각 **Azure에 배포** 단추를 클릭합니다. 각 템플릿에는 새 리소스 그룹의 이름 및 앱의 다른 배포와 이 배포를 구분하는 사용자 이름이 매개 변수 값으로 필요합니다. 다음 단계에서는 이러한 값을 설정하는 방법을 자세히 설명합니다.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. 각각의 배포에 필요한 매개 변수 값을 입력합니다.

    > [!IMPORTANT]
    > 일부 인증 및 서버 방화벽은 데모 목적으로 의도적으로 보호되지 않습니다. 각각의 응용 프로그램 배포에 대한 **새 리소스 그룹을 만듭니다**.  기존 리소스 그룹을 사용하지 마세요. 이 응용 프로그램이나 여기에서 만든 리소스를 프로덕션에 사용하지 마세요. 관련된 결제를 중지하려면 응용 프로그램을 완료할 때 모든 리소스 그룹을 삭제합니다.

    리소스 이름에는 소문자, 숫자 및 하이픈만 사용하는 것이 가장 좋습니다.
    * **리소스 그룹** - **새로 만들기**를 선택한 후 리소스 그룹의 **이름**을 소문자로 입력합니다.
        * 대시를 추가하고 이니셜을 더한 다음 숫자를 추가하는 것이 좋습니다(예: *wingtip-sa-af1*).
        * 드롭다운 목록에서 **위치**를 선택합니다.

    * **사용자** - 이니셜에 숫자를 더한 짧은 사용자 값을 선택하는 것이 좋습니다(예: *af1*).


3. **응용 프로그램을 배포**합니다.

    * 사용 약관에 동의하려면 클릭합니다.
    * **구매**를 클릭합니다.

4. **알림**(검색 상자 오른쪽의 벨 아이콘)을 클릭하여 세 가지 전체 배포의 배포 상태를 모니터링합니다. 앱 배포는 5분이 소요됩니다.


## <a name="run-the-application"></a>응용 프로그램 실행

앱은 이벤트를 호스트하는 장소를 표시합니다. 장소 유형에는 콘서트 홀, 재즈 클럽 및 스포츠 클럽 등이 포함됩니다. 장소는 Wingtip Tickets 앱 고객에 해당합니다. Wingtip Tickets에서 장소는 *테넌트*로 등록됩니다. 테넌트가 되면 장소에 이벤트를 나열하고 고객에게 티켓을 판매하는 편리한 방법을 제공할 수 있습니다. 각 장소는 해당 이벤트를 나열하고 티켓을 판매하는 개인 설정된 웹 사이트를 얻게 됩니다. 각 테넌트는 다른 테넌트와 격리되며 서로 독립됩니다. 백그라운드에서 각 테넌트는 별도의 응용 프로그램 인스턴스와 고유한 독립형 SQL 데이터베이스를 가져옵니다.

1. 별도의 브라우저 탭에서 세 개의 각 테넌트에 대한 이벤트 페이지를 엽니다.

    - http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    각 URL에서 &lt;USER&gt;를 배포의 사용자 값으로 바꿉니다.

   ![이벤트](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

들어오는 요청의 배포를 제어하기 위해 앱에서는 [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md)를 사용합니다. 각 테넌트 관련 앱 인스턴스는 URL에 도메인 이름의 일부로 테넌트 이름을 포함합니다. 모든 테넌트 URL에는 특정 **사용자** 값이 포함됩니다. URL은 다음 형식을 따릅니다.
- http://events.&lt;venuename&gt;.&lt;USER&gt;.trafficmanager.net

각 테넌트의 데이터베이스 **위치**는 배포된 해당 앱의 앱 설정에 포함됩니다.

프로덕션 환경에서는 일반적으로 CNAME DNS 레코드를 만들어 [*회사 인터넷 도메인*](../traffic-manager/traffic-manager-point-internet-domain.md)이 트래픽 관리자 프로필의 URL을 가리키도록 합니다.


## <a name="explore-the-servers-and-tenant-databases"></a>서버 및 테넌트 데이터베이스 탐색

배포된 리소스 중 일부를 살펴보겠습니다.

1. [Azure Portal](http://portal.azure.com)에서 리소스 그룹 목록으로 이동합니다.
2. **wingtip-sa-catalog-&lt;USER&gt;** 리소스 그룹을 참조하세요.
    - 이 리소스 그룹에는 **catalog-sa-&lt;USER&gt;** 서버가 배포됩니다. 서버에는 **tenantcatalog** 데이터베이스가 포함됩니다.
    - 또한 세 개의 테넌트 리소스 그룹도 표시됩니다.
3. Fabrikam Jazz Source 배포의 리소스를 포함하는 **wingtip-sa-fabrikam-&lt;USER&gt;** 리소스 그룹을 엽니다.  **fabrikamjazzclub-&lt;USER&gt;** 서버에 **fabrikamjazzclub** 데이터베이스가 포함되어 있습니다.

각 테넌트 데이터베이스는 50개의 DTU *독립형* 데이터베이스입니다.

## <a name="additional-resources"></a>추가 리소스

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- 다중 테넌트 SaaS 응용 프로그램에 대해 알아보려면 [다중 테넌트 SaaS 응용 프로그램을 위한 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * Wingtip 티켓 SaaS 독립형 응용 프로그램 배포 방법
> * 앱을 구성하는 서버 및 데이터베이스 정보.
> * 샘플 리소스를 삭제하여 관련 결제를 중지하는 방법

