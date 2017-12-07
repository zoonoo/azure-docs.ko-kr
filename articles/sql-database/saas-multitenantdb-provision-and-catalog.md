---
title: "다중 테넌트 Azure SQL Database SQL 데이터베이스를 사용하여 SaaS 응용 프로그램에 새 테넌트를 프로비전하고 카탈로그로 만들기 | Microsoft Docs"
description: "Azure SQL Database 다중 테넌트 SaaS 앱에서 새 테넌트를 프로비전하고 카탈로그로 만드는 방법 알아보기"
keywords: "SQL Database 자습서"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: e7de7bb545e0ce04dc1b3dd398cc920213d09bae
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>공유 다중 테넌트 SQL 데이터베이스를 사용하여 SaaS 응용 프로그램에 새 테넌트를 프로비전하고 카탈로그로 만들기

이 자습서에서는 공유 다중 테넌트 데이터베이스 모델을 사용하는 경우 테넌트를 프로비전하고 카탈로그로 만드는 패턴에 대해 설명합니다. 

테넌트 데이터를 호스팅하는 테이블의 기본 키에 테넌트 id를 포함하고 있는 다중 테넌트 스키마를 사용하면 단일 데이터베이스에 여러 테넌트를 저장할 수 있습니다. 많은 수의 테넌트를 지원하기 위해 테넌트 데이터가 여러 분할된 데이터베이스 또는 데이터베이스에 분산됩니다. 한 테넌트의 데이터는 항상 단일 데이터베이스에 완전히 포함됩니다.  카탈로그는 데이터베이스에 대한 테넌트의 매핑을 보관하는 데 사용됩니다.   

또한 일부 데이터베이스를 단일 테넌트로만 채우도록 선택할 수 있습니다. 여러 테넌트를 포함하는 데이터베이스는 테넌트 격리 수준을 희생한 대가로 테넌트당 비용이 저렴합니다.  단일 테넌트만 포함하는 데이터베이스는 높은 격리 수준을 제공하지만 비용이 저렴하지 않습니다.  다중 테넌트를 사용하는 데이터베이스와 단일 테넌트를 사용하는 데이터베이스를 동일한 SaaS 응용 프로그램에 혼합하여 각 테넌트의 비용 또는 격리 수준을 최적화할 수 있습니다. 테넌트를 프로비전할 때 고유의 데이터베이스를 지정할 수도 있고 나중에 고유의 데이터베이스로 이동할 수도 있습니다.

   ![테넌트 카탈로그가 있는 공유 다중 테넌트 데이터베이스 앱](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>테넌트 카탈로그 패턴

테넌트 데이터가 여러 데이터베이스에 분산되는 경우 각 테넌트의 데이터가 저장되는 위치를 파악해야 합니다. 카탈로그 데이터베이스는 테넌트 그리고 그 테넌트의 데이터를 저장하는 데이터베이스 사이의 매핑을 보관합니다.

각 테넌트는 카탈로그의 키를 통해 식별됩니다. Wingtip Tickets SaaS 앱에서 테넌트 키는 테넌트 이름의 해시에서 형성됩니다. 따라서 응용 프로그램이 웹 페이지 URL에서 테넌트 이름을 추출하여 적절한 데이터베이스에 연결하는 데 사용할 수 있습니다. 다른 테넌트 키 스키마를 사용할 수도 있습니다.

카탈로그를 사용하면 프로비전 후 응용 프로그램을 방해하지 않고 테넌트 데이터베이스의 이름 또는 위치를 변경할 수 있습니다.  다중 테넌트 데이터베이스 모델에서는 데이터베이스 간에 테넌트를 이동할 수 있습니다.  유지 관리 또는 기타 작업을 위해 테넌트가 오프라인 상태인지 여부를 응용 프로그램에 알려주는 데에도 카탈로그를 사용할 수 있습니다.

또한 카탈로그는 데이터베이스의 계층 또는 버전, 데이터베이스 스키마 버전, 테넌트 이름 및 서비스 계획 또는 SLA, 응용 프로그램 관리/고객 지원/devops 프로세스를 가능하게 하는 기타 정보와 같은 추가 테넌트 또는 데이터베이스 메타데이터를 저장할 수 있습니다.  

카탈로그를 사용하여 테넌트 간 보고, 스키마 관리, 분석용 데이터 추출을 지원할 수도 있습니다. 

### <a name="elastic-database-client-library"></a>Elastic Database 클라이언트 라이브러리 
Wingtip Tickets SaaS 앱에서 카탈로그는 [EDCL(Elastic Database 클라이언트 라이브러리)](sql-database-elastic-database-client-library.md)의 분할 관리 기능을 사용하여 *tenantcatalog* 데이터베이스에 구현됩니다. 이 라이브러리는 응용 프로그램에서 데이터베이스 기반 '분할 맵'을 생성, 관리 및 사용할 수 있게 해줍니다. 분할 맵은 분할된 데이터베이스(데이터베이스)의 목록 및 키(테넌트 ID)와 분할된 데이터베이스 간의 매핑을 포함합니다.  분할 맵에서 항목을 만들고 나중에 올바른 데이터베이스에 연결하도록 테넌트를 프로비전하는 동안 응용 프로그램 또는 PowerShell 스크립트에서 EDCL 함수를 사용할 수 있습니다. 이 라이브러리는 연결 정보를 캐시하여 카탈로그 데이터베이스에 대한 트래픽을 최소화하고 연결 속도를 높입니다. 

> [!IMPORTANT]
> 매핑 데이터는 카탈로그 데이터베이스에서 액세스할 수 있지만 *편집하지는 마세요!* 매핑 데이터는 Elastic Database 클라이언트 라이브러리 API를 사용해서만 편집하세요. 매핑 데이터의 직접 조작은 카탈로그가 손상될 위험이 있으며 지원되지 않습니다.


## <a name="tenant-provisioning-pattern"></a>테넌트 프로비전 패턴

분할 다중 테넌트 데이터베이스 모델에서 새 테넌트를 프로비전하는 경우 테넌트를 공유 데이터베이스에 프로비전할 것인지 아니면 자체 데이터베이스를 지정할 것인지를 먼저 결정해야 합니다. 공유 데이터베이스에 프로비전하는 경우 기존 데이터베이스에 공간이 있는지 아니면 새 데이터베이스가 필요한지 확인해야 합니다. 새 데이터베이스가 필요한 경우 새 데이터베이스를 적절한 위치 및 서비스 계층에 프로비전하고, 적절한 스키마 및 참조 데이터를 사용하여 초기화한 다음 카탈로그에 등록해야 합니다. 마지막으로, 적절한 분할된 데이터베이스를 참조하도록 테넌트 매핑을 추가할 수 있습니다.

SQL 스크립트를 실행하거나 bacpac을 배포하거나 템플릿 데이터베이스를 복사하여 데이터베이스를 프로비전합니다. Wingtip Tickets SaaS 응용 프로그램은 템플릿 데이터베이스를 복사하여 새 테넌트 데이터베이스를 만듭니다.

데이터베이스 프로비전 방법이 전체 스키마 관리 전략에서 포괄적이어야 하며, 새 데이터베이스에 최신 스키마가 프로비전되도록 보장해야 합니다.  자세한 내용은 [스키마 관리 자습서](saas-tenancy-schema-management.md)에서 다룹니다.  

이 자습서의 테넌트 프로비전 스크립트는 다른 테넌트와 공유하는 기존 데이터베이스에 테넌트를 프로비전할 뿐 아니라 테넌트를 자체 데이터베이스에 프로비전합니다. 그런 다음 테넌트 데이터가 초기화되고 카탈로그 분할 맵에 등록됩니다. 샘플 앱에서, 여러 테넌트를 포함하고 있는 데이터베이스에는 *tenants1*, *tenants2* 등의 일반적인 이름이 부여되고, 단일 테넌트를 포함하고 있는 데이터베이스에는 테넌트 이름이 부여됩니다. 샘플에 사용 되는 특정 명명 규칙은 패턴에서 중요한 부분이 아닙니다. 카탈로그를 사용하면 데이터베이스에 아무 이름이나 할당할 수 있기 때문입니다.  

## <a name="provision-and-catalog-tutorial"></a>프로비전 및 카탈로그 자습서

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 다중 테넌트 데이터베이스에 테넌트 프로비전
> * 단일 테넌트 데이터베이스에 테넌트 프로비전
> * 다중 테넌트 및 단일 테넌트 데이터베이스에 테넌트 일괄 처리 프로 비전
> * 카탈로그에 데이터베이스 및 테넌트 매핑 등록

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip Tickets SaaS 다중 테넌트 데이터베이스 앱이 배포되어 있어야 합니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS 다중 테넌트 데이터베이스 응용 프로그램 배포 및 탐색](saas-multitenantdb-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip Tickets SaaS 다중 테넌트 데이터베이스 응용 프로그램 소스 코드 및 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 응용 프로그램 소스 코드는 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 리포지토리에서 확인할 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인하세요. 

## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>다른 테넌트와 공유하는 데이터베이스에 테넌트 프로비전

Wingtip Tickets 응용 프로그램이 공유 데이터베이스에 새 테넌트를 프로비전하는 방법을 이해하려면 중단점을 추가하여 워크플로를 단계별로 실행합니다.

1. _PowerShell ISE_에서 ...\\학습 모듈\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_을 열고 다음 매개 변수를 설정합니다.
   * **$TenantName** = **Bushwillow 블루스**: 새 장소의 이름.
   * **$VenueType** = **블루스**: 미리 정의된 장소 유형 중 하나: 블루스, 클래식 음악, 댄스, 재즈, 유도, 자동차 경주, 다목적, 오페라, 록 음악, 축구(공백 없이 소문자로).
   * **$DemoScenario** = **1**: *다른 테넌트와 공유하는 데이터베이스에 테넌트 프로비전*.

1. 커서를 38행(*New-Tenant `*)의 아무 위치에 두고 중단점을 추가한 다음 **F9**를 누릅니다.

   ![중단점](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. 스크립트를 실행하려면 **F5** 키를 누릅니다.

1. 스크립트 실행이 중단점에서 중지된 후 **F11**을 눌러 한 단계씩 코드를 실행합니다.

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

**디버그** 메뉴 옵션(**F10** 및 **F11** 키)을 사용하여 스크립트 실행을 추적하면서 피호출 함수로 이동합니다. PowerShell 스크립트를 디버깅하는 방법에 대한 자세한 내용은 [PowerShell 스크립트 사용 및 디버깅 관련 팁](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)을 참조하세요.


다음은 단계별로 실행하는 프로비전 워크플로의 주요 요소입니다.

* **새 테넌트 키를 계산**합니다. 테넌트 이름에서 테넌트 키를 만드는 데에는 해시 함수가 사용됩니다.
* **테넌트 키가 이미 존재하는지 여부를 확인**합니다. 카탈로그를 확인하여 키가 아직 등록되지 않았는지 확인합니다.
* **기본 테넌트 데이터베이스에서 테넌트를 초기화**합니다. 새 테넌트 정보를 추가하도록 테넌트 데이터베이스가 업데이트됩니다.  
* **카탈로그에 테넌트 등록** 새 테넌트 키와 기존 tenants1 데이터베이스 간의 매핑이 카탈로그에 추가됩니다. 
* **테넌트 이름을 카탈로그 확장 테이블에 추가**합니다. 장소 이름이 카탈로그의 테넌트 테이블에 추가됩니다.  다음은 추가 응용 프로그램 관련 데이터를 지원하도록 카탈로그 데이터베이스를 확장하는 방법입니다.
* **새 테넌트의 이벤트 페이지를 엽니다**. 브라우저에 *Bushwillow Blues* 이벤트 페이지가 열립니다.

   ![events](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>테넌트를 자체 데이터베이스에 프로비전

이제 자체 데이터베이스에 테넌트를 만드는 프로세스를 단계별로 알아보겠습니다.

1. ...\\학습 모듈\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_을 열고 다음 매개 변수를 설정합니다.
   * **$TenantName** = **Sequoia Soccer**: 새 장소의 이름.
   * **$VenueType** = **축구**: 미리 정의된 장소 유형 중 하나: 블루스, 클래식 음악, 댄스, 재즈, 유도, 자동차 경주, 다목적, 오페라, 록 음악, 축구(공백 없이 소문자로).
   * **$DemoScenario** = **2**: *자체 데이터베이스에 테넌트 프로비전*.

1. 커서를 57행(*&&nbsp;$PSScriptRoot\New-TenantAndDatabase `*)의 아무 위치에 두고 새로운 중단점을 추가한 다음 **F9**를 누릅니다.

   ![중단점](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. **F5** 키를 눌러 스크립트를 실행합니다.

1. 스크립트 실행이 중단점에서 중지된 후 **F11**을 눌러 한 단계씩 코드를 실행합니다.  **F10** 및 **F11** 키를 사용하여 함수를 건너뛰거나 시작하면서 실행을 추적합니다.

다음은 스크립트를 추적하는 동안 단계별로 실행하는 워크플로의 주요 요소입니다.

* **새 테넌트 키를 계산**합니다. 테넌트 이름에서 테넌트 키를 만드는 데에는 해시 함수가 사용됩니다.
* **테넌트 키가 이미 존재하는지 여부를 확인**합니다. 카탈로그를 확인하여 키가 아직 등록되지 않았는지 확인합니다.
* **새 테넌트 데이터베이스를 만듭니다**. Resource Manager 템플릿을 사용해 *basetenantdb*를 복사하여 데이터베이스를 만듭니다.  새 데이터베이스 이름은 테넌트 이름을 기반으로 합니다.
* **카탈로그에 데이터베이스를 추가**합니다. 새 테넌트 데이터베이스가 카탈로그에 하나의 분할된 데이터베이스로 등록됩니다.
* **기본 테넌트 데이터베이스에서 테넌트를 초기화**합니다. 새 테넌트 정보를 추가하도록 테넌트 데이터베이스가 업데이트됩니다.  
* **카탈로그에 테넌트 등록** 새 테넌트 키와 *sequoiasoccer* 데이터베이스 간의 매핑이 카탈로그에 추가됩니다.
* **테넌트 이름이 카탈로그에 추가**됩니다. 장소 이름이 카탈로그의 테넌트 확장 테이블에 추가됩니다.
* **새 테넌트의 이벤트 페이지를 엽니다**. 브라우저에 *Sequoia 축구* 이벤트 페이지가 열립니다.

   ![events](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>테넌트의 배치 프로비전

이 연습에서는 17개의 테넌트의 배치를 프로비전합니다. 데이터베이스가 여러 개 있도록 다른 Wingtip Tickets 자습서를 시작하기 전에 이 테넌트의 일괄 처리를 프로비전하는 것이 좋습니다.


1. *PowerShell ISE*에서 ...\\학습 모듈\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*을 열고 *$DemoScenario* 매개 변수를 4로 변경합니다.
   * **$DemoScenario** = **4**: *공유 데이터베이스에 테넌트 일괄 처리 프로비전*.
1. **F5**를 누르고 스크립트를 실행합니다.


### <a name="verify-the-deployed-set-of-tenants"></a>배포된 테넌트 집합 확인 
이 단계에서는 공유 데이터베이스에 혼합 테넌트가 배포되었고, 테넌트가 자체 데이터베이스에 배포되었습니다. Azure Portal을 사용하여 생성된 데이터베이스를 검사할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 SQL 서버 목록으로 이동하여 **tenants1-mt-\<USER\>** 서버를 엽니다.  **SQL 데이터베이스** 목록에 공유 데이터베이스 **tenants1** 및 자체 데이터베이스에 있는 테넌트의 데이터베이스가 표시될 것입니다.

   ![데이터베이스 목록](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Azure Portal은 테넌트 데이터베이스를 표시하지만 공유 데이터베이스 *내부*의 테넌트는 볼 수 없습니다. 테넌트 전체 목록은 Wingtip 티켓 이벤트 허브 페이지에서 카탈로그를 탐색하여 확인할 수 있습니다.   

**Wingtip 티켓 이벤트 허브 페이지 사용** <br>
브라우저에서 이벤트 허브 페이지 열기(http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

**카탈로그 데이터베이스 사용** <br>
테넌트 및 해당 데이터베이스의 전체 목록은 카탈로그에서 사용할 수 있습니다. Tenants 테이블에 저장된 테넌트 이름을 Shard Management 테이블의 데이터베이스 이름에 조인하는 tenantcatalog 데이터베이스에 SQL 보기가 제공됩니다. 이 보기는 카탈로그에 저장된 메타데이터를 확장하면 어떤 가치가 있는지 아주 잘 보여 줍니다.

1. *SSMS(SQL Server Management Studio)*에서 로그인: **developer**, 암호: **P@ssword1**를 사용하여 **catalog-mt.\<USER\>.database.windows.net**에서 테넌트 서버에 연결합니다.

    ![SSMS 연결 대화 상자](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

1. *개체 탐색기*에서 *tenantcatalog* 데이터베이스의 보기를 찾습니다.
1. *TenantsExtended* 보기를 마우스 오른쪽 단추로 클릭하고 **상위 1000개 행 선택**을 선택합니다. 테넌트 이름과 다른 테넌트의 데이터베이스 간 매핑을 확인합니다.

    ![SSMS의 ExtendedTenants 보기](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>기타 프로비전 패턴

그 외에도 다음과 같은 흥미로운 프로비전 패턴이 있습니다.

**탄력적 풀에 데이터베이스를 미리 프로비전.** 미리 프로비전은 탄력적 풀을 사용하는 경우 데이터베이스가 아닌 풀을 대상으로 요금이 청구된다는 것을 활용하는 패턴입니다. 따라서 필요할 때 추가 비용 없이 데이터베이스를 탄력적 풀에 추가할 수 있습니다. 풀에 데이터베이스를 미리 프로비전하고 필요할 때 할당하면 데이터베이스에 테넌트를 프로비전하는 데 걸리는 시간을 크게 단축할 수 있습니다. 버퍼를 예상 프로비전 속도에 적합하게 유지하기 위해 필요한 경우 미리 프로비전한 데이터베이스 수를 조정할 수 있습니다.

**자동 프로비전.** 자동 프로비전 패턴에서는 전용 프로비전 서비스를 사용하여 서버, 풀 및 필요한 경우 데이터베이스를 자동으로 프로비전하며, 탄력적 풀에 데이터베이스 미리 프로비전을 포함합니다. 또한 데이터베이스가 디커미션되고 삭제된 경우 원한다면 이로 인해 발생하는 탄력적 풀의 빈 공간을 프로비전 서비스로 채울 수 있습니다. 그러한 서비스는 단순할 수도 있고 복잡할 수도 있으며(예를 들어 여러 지리적 영역에 걸쳐 프로비전을 처리) 재해 복구에 대해 지역에서 복제를 설정할 수 있습니다. 자동 프로비전 패턴에서 클라이언트 응용 프로그램 또는 스크립트는 프로비전 서비스에 의해 처리할 프로비전 요청을 큐에 제출한 다음 폴링하여 완료를 결정할 수 있습니다. 미리 프로비전을 사용하는 경우 요청이 신속하게 처리되고, 다른 서비스가 백그라운드에서 대체 데이터베이스 프로비전을 관리합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]

> * 새로운 단일 테넌트를 공유 다중 테넌트 데이터베이스 및 자체 데이터베이스에 프로비전
> * 추가 테넌트의 배치 프로비전
> * 테넌트를 프로비전하고 카탈로그에 테넌트를 등록하는 구체적인 방법을 단계별로 알아봅니다.

[성능 모니터링 자습서](saas-multitenantdb-performance-monitoring.md)를 시도해 봅니다.

## <a name="additional-resources"></a>추가 리소스

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)
* [Windows PowerShell ISE에서 스크립트를 디버그하는 방법](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
