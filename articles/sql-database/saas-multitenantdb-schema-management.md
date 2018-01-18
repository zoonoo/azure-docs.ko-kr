---
title: "다중 테넌트 앱에서 Azure SQL Database 스키마 관리 | Microsoft Docs"
description: "Azure SQL Database를 사용하는 다중 테넌트 응용 프로그램에서 여러 테넌트에 대한 스키마 관리"
keywords: "SQL Database 자습서"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database를 사용하는 다중 테넌트 응용 프로그램에서 여러 테넌트에 대한 스키마 관리

이 자습서에서는 클라우드의 SaaS(Software as a Service) 응용 프로그램에서 방대해질 수 있는 대규모 데이터베이스를 유지 관리하는 데 따른 문제를 검토합니다. 앱의 수명 주기 동안 개발 및 구현되는 스키마 고급 기능을 관리하기 위한 솔루션도 제시됩니다.

응용 프로그램이 발전함에 따라, 해당 테이블 열 또는 기타 스키마, 해당 참조 스키마 또는 성능 관련 항목이 변경될 수 있습니다. SaaS 앱을 사용할 경우 이러한 변경 내용이 기존의 다양한 테넌트 데이터베이스에서 조정된 방식으로 배포되어야 합니다. 또한 이러한 변경 내용이 앱에 추가되는 향후 테넌트 데이터베이스에 포함되어야 합니다. 따라서 변경 내용 또한 새 데이터베이스를 프로비전하는 프로세스에 통합되어야 합니다.

#### <a name="two-scenarios"></a>두 가지 시나리오

이 자습서에서는 다음 두 가지 시나리오를 살펴봅니다.
- 모든 테넌트에 대한 참조 데이터 업데이트를 배포합니다.
- 참조 데이터가 포함된 테이블의 인덱스를 재조정합니다.

Azure SQL Database의 [탄력적 작업](sql-database-elastic-jobs-overview.md) 기능은 테넌트 데이터베이스에서 이러한 작업을 실행하는 데 사용됩니다. 또한 작업은 골든 템플릿 테넌트 데이터베이스에서도 작동합니다. 이 템플릿은 새 데이터베이스가 프로비전될 때 사용됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 작업 계정을 만듭니다.
> * 여러 테넌트에서 쿼리합니다.
> * 모든 테넌트 데이터베이스에서 데이터를 업데이트합니다.
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스를 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- Wingtip Tickets 앱이 이미 배포되어 있어야 합니다.
    - 자세한 내용은 *Wingtip Tickets* SaaS 다중 테넌트 데이터베이스 앱을 소개하는 첫 번째 자습서를 참조하세요.<br />[Azure SQL Database를 사용하는 분할된 다중 테넌트 응용 프로그램 배포 및 탐색](saas-multitenantdb-get-started-deploy.md)합니다.
        - 배포 프로세스는 5분 이내에 실행됩니다.
    - *분할된 다중 테넌트* 버전의 Wingtip이 설치되어 있어야 합니다. *독립 실행형* 및 *테넌트별 데이터베이스*용 버전은 제공된 자습서를 지원하지 않습니다.

- 최신 버전의 SSMS(SQL Server Management Studio)가 설치되어 있어야 합니다. [SSMS를 다운로드 및 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)합니다.

- Azure PowerShell이 설치되어 있어야 합니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

> [!NOTE]
> 이 자습서에서는 제한된 미리 보기([Elastic Database 작업](sql-database-elastic-database-client-library.md))에 있는 Azure SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행하려는 경우 subject=탄력적인 작업 미리 보기인 *SaaSFeedback@microsoft.com*에 구독 ID를 제공하세요. 구독이 활성화되었다는 확인을 받은 후 [최신 시험판 작업 cmdlet을 다운로드하여 설치하세요](https://github.com/jaredmoo/azure-powershell/releases). 이 미리 보기는 제한적으로만 제공되므로 관련 질문이 있거나 지원이 필요한 경우 *SaaSFeedback@microsoft.com*에 문의해야 합니다.

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 스키마 관리 패턴 소개

이 샘플에 사용된 공유 다중 테넌트 데이터베이스 모델을 사용하면 테넌트 데이터베이스에 하나 이상의 테넌트를 포함할 수 있습니다. 이 샘플은 다중 테넌트 및 단일 테넌트 데이터베이스의 혼합을 사용하여 *하이브리드* 테넌트 관리 모델을 사용할 수 있는지 확인합니다. 이러한 데이터베이스의 관리는 복잡합니다. [탄력적 작업](sql-database-elastic-jobs-overview.md)은 SQL 데이터 계층의 관리를 용이하게 합니다. 작업을 사용하면 테 넌 트 데이터베이스 그룹에 대해 태스크와 같은 Transact-SQL 스크립트를 안전하고 안정적으로 실행할 수 있습니다. 태스크는 사용자 상호 작용 또는 입력과 별개입니다. 이 메서드는 응용 프로그램의 모든 테넌트에서 스키마 및 일반 참조 데이터에 변경 내용을 배포하는 데 사용할 수 있습니다. 데이터베이스의 골든 템플릿 복사본을 유지하기 위해 탄력적 작업을 사용할 수도 있습니다. 템플릿은 새 테넌트를 만드는 데 사용되어, 항상 최신 스키마 및 참조 데이터가 사용되도록 합니다.

![화면](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>탄력적 작업의 제한된 미리 보기

현재 Azure SQL Database의 통합 기능인 새로운 탄력적 작업 버전이 있습니다. 통합되므로 추가 서비스 또는 구성 요소가 필요하지 않게 됩니다. 이 새로운 탄력적 작업 버전은 현재 제한된 미리 보기 상태입니다. 제한된 미리 보기에서는 현재 작업 계정을 만드는 PowerShell 및 작업을 만들고 관리하는 T-SQL을 지원합니다.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip Tickets SaaS 다중 테넌트 데이터베이스 응용 프로그램 소스 코드 및 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 응용 프로그램 소스 코드는 GitHub의 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) 리포지토리에서 사용할 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 참조하세요. 

## <a name="create-a-job-account-database-and-new-job-account"></a>작업 계정 데이터베이스 및 새 작업 계정 만들기

이 자습서를 사용하려면 PowerShell을 사용하여 작업 계정 데이터베이스 및 작업 계정을 만들어야 합니다. SQL 에이전트에서 사용되는 MSDB 데이터베이스와 마찬가지로, 탄력적 작업에서는 Azure SQL Database를 사용하여 작업 정의, 작업 상태 및 기록을 저장합니다. 작업 계정이 만들어지면 작업을 만들어 즉시 모니터링할 수 있습니다.

1. **PowerShell ISE**에서 *...\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1*을 엽니다.
2. **F5** 키를 눌러 스크립트를 실행합니다.

*Demo-SchemaManagement.ps1* 스크립트는 *Deploy-SchemaManagement.ps1*을 호출하여 카탈로그 서버에서 **jobaccount**라는 이름의 계층 *S2* 데이터베이스를 만듭니다. 그런 다음, 이 스크립트는 작업 계정을 만들어 jobaccount 데이터베이스를 매개 변수로 작업 계정 생성 호출로 전달합니다.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>모든 테넌트에 새 참조 데이터를 배포하는 작업 만들기

#### <a name="prepare"></a>준비

각 테넌트 데이터베이스의 **VenueTypes** 테이블에는 장소 유형 집합이 포함되어 있습니다. 장소 유형은 한 장소에 호스트되는 이벤트의 종류를 정의합니다. 이 연습에서는 두 개의 추가 장소 유형인 *Motorcycle Racing* 및 *Swimming Club*을 추가하기 위해 모든 데이터베이스에 업데이트를 배포합니다. 이러한 장소 유형은 테넌트 이벤트 앱에 표시된 배경 이미지에 해당합니다.

새 참조 데이터를 배포하기 전에 이미 존재하는 장소 유형의 수를 적어둡니다(10일 수 있음). Wingtip 웹 UI에 대한 다음 링크를 클릭한 후 **장소 유형** 드롭다운 메뉴를 클릭하여 이 데이터를 적어둡니다.
- http://events.wingtip-mt.<USER>.trafficmanager.net

이제 원래 장소 유형의 수를 계산할 수 있습니다. 특히 *Motorcycle Racing* 나 *Swimming Club*은 아직 존재하지 않습니다.

#### <a name="steps"></a>단계

이제 2개의 새 장소 유형을 추가하여 각 테넌트 데이터베이스의 **VenueTypes** 테이블을 업데이트하는 작업을 만들어 보겠습니다.

새 작업을 만들려면 *jobaccount* 데이터베이스에서 생성된 작업 시스템 저장 프로시저 집합을 사용합니다. 이 프로시저는 작업 계정을 만들 때 생성된 것입니다.

1. SSMS에서 테넌트 서버: tenants1-\<user\>.database.windows.net에 연결합니다.

2. *tenants1-mt-\<user\>.database.windows.net* 서버의 *tenants1* 데이터베이스로 이동합니다.

3. *VenueTypes* 테이블을 쿼리하여 *Motorcycle Racing* 및 *Swimming Club*이 결과 목록에 아직 없는지 확인합니다.

4. 카탈로그 서버 *catalog-mt-\<user\>.database.windows.net*에 연결합니다.

5. 카탈로그 서버의 *jobaccount* 데이터베이스에 연결합니다.

6. SSMS에서 *...\\Learning Modules\\Schema Management\\DeployReferenceData.sql* 파일을 엽니다.

7. 문을 set @User = &lt;user&gt;로 수정하고 Wingtip Ticket SaaS 다중 테넌트 데이터베이스 응용 프로그램을 배포할 때 사용된 사용자 값을 바꿉니다.

8. **F5** 키를 눌러 스크립트를 실행합니다.

#### <a name="observe"></a>관찰

*DeployReferenceData.sql* 스크립트에서 다음 항목을 살펴봅니다.

- **sp\_add\_target\_group**은 대상 그룹 이름 *DemoServerGroup*을 만든 후 대상 구성원을 그룹에 추가합니다.

- **sp\_add\_target\_group\_member**는 다음 항목을 추가합니다.
    - *server* 대상 구성원 유형.
        - 테넌트 데이터베이스를 포함하는 *tenants1-mt-&lt;user&gt;* 서버입니다.
        - 따라서 작업이 실행될 때 서버의 모든 데이터베이스가 작업에 포함됩니다.
    - *catalog-mt-&lt;user&gt;* 서버에 있는 골든 데이터베이스(*basetenantdb*)에 대한 *database* 대상 구성원 유형
    - 이후 자습서에서 사용되는 *adhocreporting* 데이터베이스를 포함할 *database* 대상 구성원 유형.

- **sp\_add\_job**은 *참조 데이터 배포*라는 작업을 만듭니다.

- **sp\_add\_jobstep**은 VenueTypes 참조 테이블을 업데이트하기 위한 T-SQL 명령 텍스트가 포함된 작업 단계를 만듭니다.

- 스크립트의 남은 보기에서 개체의 존재 여부를 표시하고 작업 실행을 모니터링합니다. 이러한 쿼리를 사용하여 **lifecycle** 열의 상태 값을 검토해 작업이 성공적으로 완료된 시기를 확인합니다. 작업은 테넌트 데이터베이스를 업데이트하고, 참조 테이블을 포함하는 두 개의 추가 데이터베이스를 업데이트합니다.

SSMS에서 *tenants1-mt-&lt;user&gt;* 서버에 있는 테넌트 데이터베이스로 이동합니다. 이제 *VenueTypes* 테이블을 쿼리하여 *Motorcycle Racing* 및 *Swimming Club*이 테이블에 추가되었는지 확인합니다. 총 장소 유형 수는 2씩 증가했어야 하는데 그렇지 않았습니다.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>참조 테이블 인덱스를 관리하는 작업 만들기

이 연습은 이전 연습과 비슷합니다. 이 연습에서는 참조 테이블 기본 키에 인덱스를 다시 작성하는 작업을 만듭니다. 인덱스 다시 작성은 일반적으로 대량의 데이터가 테이블에 로드된 후에 성능 향상을 위해 관리자가 실행할 수 있는 일반적인 데이터베이스 관리 작업입니다.

1. SSMS에서 *catalog-mt-&lt;User&gt;.database.windows.net* 서버의 *jobaccount* 데이터베이스에 연결합니다.

2. SSMS에서 *...\\Learning Modules\\Schema Management\\OnlineReindex.sql* 파일을 엽니다.

3. **F5** 키를 눌러 스크립트를 실행합니다.

#### <a name="observe"></a>관찰

*OnlineReindex.sql* 스크립트에서 다음 항목을 살펴봅니다.

* **sp\_add\_job**은 *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*라는 새 작업을 만듭니다.

* **sp\_add\_jobstep**은 인덱스를 업데이트할 T-SQL 명령 텍스트를 포함하는 작업 단계를 만듭니다.

* 스크립트의 나머지 뷰는 작업 실행을 모니터링합니다. 이러한 쿼리를 사용하여 **lifecycle** 열의 상태 값을 검토해 작업이 모든 대상 그룹 구성원에서 성공적으로 완료된 시기를 확인합니다.

## <a name="additional-resources"></a>추가 리소스

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [규모가 확장된 클라우드 데이터베이스 관리](sql-database-elastic-jobs-overview.md)
* [규모가 확장된 클라우드 데이터베이스 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> - 여러 테넌트에서 쿼리하는 작업 계정 만들기
> - 모든 테넌트 데이터베이스에서 데이터를 업데이트합니다.
> - 모든 테넌트 데이터베이스의 테이블에서 인덱스를 만듭니다.

다음으로, [임시 보고 자습서](saas-multitenantdb-adhoc-reporting.md)를 시도해 보세요.

