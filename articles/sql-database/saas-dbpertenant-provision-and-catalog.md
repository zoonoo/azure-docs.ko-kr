---
title: "Azure SQL Database를 사용하는 다중 테넌트 앱에서 새 테넌트 프로비전 | Microsoft Docs"
description: "Azure SQL Database 다중 테넌트 SaaS 앱에서 새 테넌트를 프로비전하고 카탈로그로 만드는 방법 알아보기"
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
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 64e70013c267a472a57b2cf172fa50d12a170515
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>새 테넌트를 프로비전하고 카탈로그에 등록하는 방법 알아보기

이 자습서에서는 프로비전 및 카탈로그 SaaS 패턴 및 Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램에서 구현되는 방법에 대해 알아봅니다. 새 테넌트 데이터베이스를 만들고 초기화하고 응용 프로그램의 테넌트 카탈로그에 등록합니다. 카탈로그는 SaaS 응용 프로그램 간에 많은 테넌트와 해당 데이터를 유지 관리하는 데이터베이스입니다. 카탈로그는 응용 프로그램 및 관리 요청을 올바른 데이터베이스에 디렉션하는 중요한 역할을 합니다.  

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 구현되는 방법을 단계별로 포함하는 단일 새 테넌트 프로비전
> * 추가 테넌트의 배치 프로비전


이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip Tickets SaaS 테넌트별 데이터베이스 앱이 배포됩니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="introduction-to-the-saas-catalog-pattern"></a>SaaS 카탈로그 패턴 소개

데이터베이스 지원 다중 테넌트 SaaS 응용 프로그램에서 각 테넌트 정보가 저장되어 있는 위치를 아는 것이 중요합니다. SaaS 카탈로그 패턴에서 카탈로그 데이터베이스를 사용하여 각 테넌트와 해당 데이터가 저장되는 데이터베이스 간의 매핑을 유지합니다. Wingtip Tickets SaaS 테넌트별 데이터베이스 앱은 데이터베이스별 단일 테넌트 모델을 사용하지만 테넌트 대 데이터베이스 매핑을 카탈로그에 적용하는 기본적인 패턴은 사용하는 데이터베이스가 다중 테넌트인지 단일 테넌트인지 여부에 상관없이 테넌트 데이터가 여러 데이터베이스에 걸쳐 분산될 때마다 적용됩니다.

각 테넌트는 카탈로그에서 이를 식별하는 키에 할당되며 적절한 데이터베이스의 위치에 매핑됩니다. Wingtip Tickets SaaS 앱에서 키는 테넌트 이름의 해시에서 형성됩니다. 이를 통해 응용 프로그램 URL의 테넌트 이름 부분을 사용하여 키를 생성할 수 있습니다. 다른 테넌트 키 스키마를 사용할 수 있습니다.  

카탈로그는 응용 프로그램에 미치는 영향을 최소화하면서 데이터베이스의 이름 또는 위치의 변경을 허용합니다.  다중 테넌트 데이터베이스 모델에서 데이터베이스 간에 테넌트 ‘이동’을 적용합니다.  카탈로그는 테넌트 또는 데이터베이스가 유지 관리 또는 기타 작업에 대해 오프라인 상태인지 여부를 나타나내는 데 사용될 수도 있습니다. 이는 [단일 테넌트 복원 자습서](saas-dbpertenant-restore-single-tenant.md)에서 살펴봅니다.

또한 카탈로그, 사실상 SaaS 응용 프로그램에 대한 관리 데이터베이스는 데이터베이스의 계층 또는 버전, 스키마 버전, 서비스 계획 또는 테넌트에 제공되는 SLA 및 응용 프로그램 관리, 고객 지원 또는 devops 프로세스를 활성화하는 기타 정보와 같은 추가 테넌트 또는 데이터베이스 메타데이터를 저장할 수 있습니다.  

SaaS 응용 프로그램 이외에도 카탈로그는 데이터베이스 도구를 활성화할 수 있습니다.  Wingtip Tickets SaaS 테넌트별 데이터베이스 샘플에서 카탈로그는 [임시 분석 자습서](saas-tenancy-adhoc-analytics.md)에서 설명된 테넌트 간 쿼리를 활성화하는 데 사용됩니다. 데이터베이스 간 작업 관리는 [스키마 관리](saas-tenancy-schema-management.md) 및 [테넌트 분석](saas-tenancy-tenant-analytics.md) 자습서에서 살펴봅니다. 

Wingtip Tickets SaaS 샘플에서 카탈로그는 [EDCL(Elastic Database 클라이언트 라이브러리)](sql-database-elastic-database-client-library.md)의 분할 관리 기능을 사용하여 구현됩니다. EDCL은 Java 및 .Net Framework에서 사용할 수 있습니다. EDCL을 통해 응용 프로그램은 데이터베이스 기반 분할 맵을 만들고, 관리하고 사용할 수 있습니다. 분할 맵은 분할된 데이터베이스(데이터베이스)의 목록 및 키(테넌트)와 분할된 데이터베이스 간의 매핑을 포함합니다.  분할 맵에서 항목을 만들고 응용 프로그램에서 올바른 데이터베이스로 효율적으로 연결하기 위해 테넌트를 프로비전하는 동안 응용 프로그램 또는 PowerShell 스크립트에서 EDCL 함수를 사용할 수 있습니다. EDCL은 연결 정보를 캐시하여 카탈로그 데이터베이스에 대한 트래픽을 최소화하고 응용 프로그램 속도를 높입니다.  

> [!IMPORTANT]
> 매핑 데이터는 카탈로그 데이터베이스에서 액세스할 수 있지만 *편집하지는 마세요*! 매핑 데이터는 Elastic Database 클라이언트 라이브러리 API를 사용해서만 편집하세요. 매핑 데이터의 직접 조작은 카탈로그가 손상될 위험이 있으며 지원되지 않습니다.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>SaaS 프로비전 패턴 소개

단일 테넌트 데이터베이스 모델을 사용하는 SaaS 응용 프로그램에서 새 테넌트를 온보딩하는 경우 새 테넌트 데이터베이스를 프로비전해야 합니다.  적절한 위치 및 서비스 계층에 만들어지고 적절한 스키마 및 참조 데이터를 사용하여 초기화한 다음 적절한 테넌트 키에서 카탈로그에 등록되어야 합니다.  

SQL 스크립트 실행, bacpac 배포 또는 템플릿 데이터베이스 복사를 포함할 수 있는 데이터베이스 프로비전에 서로 다른 방법을 사용할 수 있습니다.  

사용하는 프로비전 방법은 전체 스키마 관리 전략에서 포괄적이어야 하며 새 데이터베이스가 최신 스키마로 프로비전되는지 확인해야 합니다.  이는 [스키마 관리 자습서](saas-tenancy-schema-management.md)에서 살펴봅니다.  

Wingtip Tickets SaaS 테넌트별 데이터베이스 앱은 카탈로그 서버에 배포된 _basetenantdb_라는 템플릿 데이터베이스를 복사하여 새 테넌트를 프로비전합니다.  프로비전은 등록 환경의 일부로 응용 프로그램 및/또는 스크립트를 사용하여 지원되는 오프라인으로 통합될 수 있습니다. 이 자습서는 PowerShell을 사용하는 프로비전을 살펴봅니다. 프로비전 스크립트는 basetenantdb 데이터베이스를 복사하여 탄력적 풀에 새 테넌트 데이터베이스를 만든 다음 테넌트 관련 정보를 사용하여 초기화하고 카탈로그 분할 맵에 등록합니다.  Wingtip Tickets SaaS 테넌트별 데이터베이스 앱에서 데이터베이스는 테넌트 이름을 기반으로 이름이 지정되지만 이는 패턴의 중요한 부분이 아닙니다. 카탈로그를 사용하면 테넌트 데이터베이스에 모든 이름을 할당할 수 있습니다.+ 


## <a name="get-the-wingtip-application-scripts"></a>Wingtip 응용 프로그램 스크립트 가져오기

Wingtip SaaS 스크립트 및 응용 프로그램 소스 코드는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) 리포지토리에서 사용할 수 있습니다. [Wingtip Tickets SaaS 스크립트를 다운로드하는 단계](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).


## <a name="provision-and-catalog-detailed-walkthrough"></a>자세한 연습 프로비전 및 카탈로그

Wingtip 응용 프로그램에서 새 테넌트 프로비전을 구현하는 방법을 이해하려면 테넌트를 프로비전하는 동안 워크플로를 통해 중단점 및 단계를 추가합니다.

1. _PowerShell ISE_에서 ...\\학습 모듈\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_을 열고 다음 매개 변수를 설정합니다.
   * **$TenantName** = 새 장소의 이름(예: *Bushwillow 블루스*).
   * **$VenueType** = 미리 정의된 장소 유형 중 하나: *블루스*, 클래식 음악, 댄스, 재즈, 유도, 자동차 경주, 다목적, 오페라, 록 음악, 축구.
   * **$DemoScenario** = **1**, *단일 테넌트를 프로비전합니다*.

1. 커서를 다음 48행의 아무 위치에 두고 중단점을 추가합니다. *새로 만들기-테넌트 `*를 클릭하고 **F9**를 누릅니다.

   ![중단점](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. 스크립트를 실행하려면 **F5** 키를 누릅니다.

1. 스크립트 실행이 중단점에서 중지된 후 **F11**을 눌러 한 단계씩 코드를 실행합니다.

   ![디버그](media/saas-dbpertenant-provision-and-catalog/debug.png)



**디버그** 메뉴 옵션(**F10** 및 **F11** 키)을 사용하여 스크립트 실행을 추적하면서 피호출 함수로 이동합니다. PowerShell 스크립트를 디버깅하는 방법에 대한 자세한 내용은 [PowerShell 스크립트 사용 및 디버깅 관련 팁](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)을 참조하세요.


다음은 명시적으로 팔로우하는 단계는 아니지만 스크립트를 디버깅하는 동안 단계별로 실행하는 워크플로의 설명입니다.

1. Azure에 로그인하여 작업 중인 Azure 구독을 선택하는 함수를 포함하고 있는 **SubscriptionManagement.psm1 모듈을 가져옵니다**.
1. [Shard Management](sql-database-elastic-scale-shard-map-management.md) 함수에 대한 카탈로그 및 테넌트 수준 추상화를 제공하는 **CatalogAndDatabaseManagement.psm1 모듈을 가져옵니다**. 이는 카탈로그 패턴의 많은 부분을 캡슐화하고 탐색할 가치가 있는 중요한 모듈입니다.
1. **구성 세부 정보를 가져옵니다**. 구성 가져오기(F11을 사용하여)까지 단계별로 실행하고 앱 구성이 어떻게 지정되었는지 확인합니다. 리소스 이름 및 기타 앱별 값이 여기서 정의되지만 스크립트를 숙지할 때까지 이러한 값을 변경하지 마세요.
1. **카탈로그 개체를 가져옵니다**. 더 높은 수준의 스크립트에 사용되는 카탈로그 개체를 작성하고 반환하는 카탈로그 가져오기까지 단계별로 실행합니다.  이 함수는 **AzureShardManagement.psm1**에서 가져온 분할 관리 기능을 사용합니다. 카탈로그 개체는 다음과 같은 개체로 구성됩니다.
   * $catalogServerFullyQualifiedName은 표준 stem과 사용자 이름을 사용하여 생성됩니다. _catalog-\<user\>.database.windows.net_.
   * $catalogDatabaseName은 구성에서 검색됩니다. *tenantcatalog*.
   * $shardMapManager 개체는 카탈로그 데이터베이스에서 초기화됩니다.
   * $shardMap 개체는 카탈로그 데이터베이스의 *tenantcatalog* 분할 맵에서 초기화됩니다.
   카탈로그 개체가 구성되고 반환되며 더 높은 수준의 스크립트에서 사용됩니다.
1. **새 테넌트 키를 계산**합니다. 테넌트 이름에서 테넌트 키를 만드는 데에는 해시 함수가 사용됩니다.
1. **테넌트 키가 이미 존재하는지 여부를 확인**합니다. 카탈로그에 대해 키를 사용할 수 있는지 확인합니다.
1. **New-TenantDatabase를 사용하여 테넌트 데이터베이스가 프로비전됩니다.** **F11**을 사용하여 단계를 실행하고 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-manager-template-walkthrough.md)을 사용하여 데이터베이스가 프로비전되는 방법을 확인합니다.

    어느 분할이 어느 테넌트에 속하는지 밝히기 위해 테넌트 이름에서 데이터베이스 이름이 구성됩니다. (데이터베이스 이름 지정에 대한 다른 전략을 사용해도 좋습니다.) 카탈로그 서버에서 템플릿 데이터베이스(baseTenantDB)를 복사하여 테넌트 데이터베이스를 만드는 데에는 Resource Manager 템플릿이 사용됩니다. 대안 방법은 빈 데이터베이스를 만든 다음 bacpac을 가져와서 초기화하거나 잘 알려진 위치에서 초기화 스크립트를 실행하는 것입니다.  

    Resource Manager 템플릿은 \Learning Modules\Common\ 폴더: *tenantdatabasecopytemplate.json*에 있습니다.

1. 테넌트 데이터베이스가 만들어진 후 **장소(테넌트) 이름과 장소 유형으로 추가로 초기화**됩니다. 여기서 다른 초기화가 수행될 수도 있습니다.

1. 테넌트 데이터베이스는 테넌트 키를 사용하여 *Add-TenantDatabaseToCatalog*를 통해 **카탈로그에 등록됩니다**. **F11**을 사용하여 세부 정보까지 단계별로 실행합니다.

    * 카탈로그 데이터베이스가 분할 맵(알려진 데이터베이스의 목록)에 추가됩니다.
    * 키 값을 분할에 연결하는 매핑이 만들어집니다.
    * 테넌트에 관한 추가 메타데이터(장소의 이름)가 카탈로그의 테넌트 테이블에 추가됩니다.  테넌트 테이블은 ShardManagement 스키마의 일부가 아니며 EDCL에 의해 설치되지 않습니다.  이 테이블은 카탈로그 데이터베이스가 추가 응용 프로그램 관련 데이터를 지원하기 위해 확장하는 방법을 보여 줍니다.   


프로비전이 완료된 후 실행은 *Demo-ProvisionAndCatalog* 스크립트로 반환되며 브라우저에 새 테넌트에 대한 **이벤트** 페이지가 열립니다.

   ![events](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>테넌트의 배치 프로비전

이 연습에서는 17개의 테넌트의 배치를 프로비전합니다. 다른 Wingtip Tickets SaaS 테넌트별 데이터베이스 자습서를 시작하기 전에 이 테넌트의 배치를 프로비전하므로 작업할 데이터베이스가 몇 개 있는 것이 좋습니다.

1. *PowerShell ISE*에서 ...\\학습 모듈\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*을 열고 *$DemoScenario* 매개 변수를 3으로 변경합니다.
   * **$DemoScenario** = **3**, *테넌트 일괄 처리를 프로비전합니다*.
1. **F5** 키를 눌러 스크립트를 실행합니다.

이 스크립트는 추가 테넌트의 배치를 배포합니다. [Azure Resource Manager 템플릿](../azure-resource-manager/resource-manager-template-walkthrough.md)을 사용하여 배치를 제어한 다음 각 데이터베이스의 프로비전을 연결된 템플릿에 위임합니다. 템플릿을 이 방법으로 사용하면 Azure Resource Manager가 스크립트에 대한 프로비전 프로세스를 중개할 수 있습니다. 템플릿은 데이터베이스를 가능하면 병렬로 프로비전하고 필요한 경우 재시도를 처리하여 전체 프로세스를 최적화합니다. 이 스크립트는 idempotent이므로 어떤 이유로 실패하거나 중지되는 경우 다시 실행합니다.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>테넌트의 배치가 성공적으로 배포되었는지 확인

* [Azure Portal](https://portal.azure.com)에서 서버 목록으로 이동하여 *tenants1* 서버를 엽니다. **SQL 데이터베이스**를 클릭하고 17개의 추가 데이터베이스 일괄 처리가 목록에 표시되는지 확인합니다.

   ![데이터베이스 목록](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>기타 프로비전 패턴

이 자습서에 포함되지 않은 기타 프로비전 패턴은 다음을 포함합니다.

**데이터베이스를 미리 프로비전.** 사전 프로비전 패턴은 탄력적 풀의 데이터베이스가 추가 비용을 추가하지 않는다는 사실을 악용합니다. 요금은 데이터베이스가 아닌 탄력적 풀에 대해 청구되며 유휴 데이터베이스는 리소스를 소비하지 않습니다. 풀에 있는 데이터베이스를 미리 프로비전하고 필요한 경우 할당하면 테넌트 온보딩 시간이 크게 감소할 수 있습니다. 버퍼를 예상 프로비전 속도에 적합하게 유지하기 위해 필요한 경우 미리 프로비전한 데이터베이스 수를 조정할 수 있습니다.

**자동 프로비전.** 자동 프로비전 패턴에서는 프로비저닝 서비스가 필요에 따라 서버, 풀 및 데이터베이스를 자동으로 프로비전하며, 원하는 경우 탄력적 풀에 데이터베이스 미리 프로비전을 포함합니다. 또한 데이터베이스가 디커미션되고 삭제된 경우 프로비전 서비스에 의해 탄력적 풀의 빈 공간을 채울 수 있습니다. 그러한 서비스는 단순할 수도 있고 복잡할 수도 있으며(예를 들어 여러 지리적 영역에 걸쳐 프로비전을 처리) 재해 복구에 대해 지역에서 복제를 설정할 수 있습니다. 자동 프로비전 패턴에서 클라이언트 응용 프로그램 또는 스크립트는 프로비전 서비스에 의해 처리할 프로비전 요청을 큐에 제출한 다음 서비스를 폴링하여 완료를 결정할 수 있습니다. 미리 프로비전을 사용하는 경우 서비스가 백그라운드에서 대체 데이터베이스를 프로비전하므로 요청이 신속하게 처리됩니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]

> * 단일 새 테넌트 프로비전
> * 추가 테넌트의 배치 프로비전
> * 테넌트 프로비전 및 카탈로그에 테넌트 등록의 세부 정보를 한 단계씩 알아봅니다.

[성능 모니터링 자습서](saas-dbpertenant-performance-monitoring.md)를 시도해 봅니다.

## <a name="additional-resources"></a>추가 리소스

* [Wingtip Tickets SaaS 테넌트당 데이터베이스 응용 프로그램을 기반으로 빌드되는 추가 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)
* [Windows PowerShell ISE에서 스크립트를 디버그하는 방법](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
