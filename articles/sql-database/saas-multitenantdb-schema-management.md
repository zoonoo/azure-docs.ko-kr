---
title: "다중 테넌트 앱에서 Azure SQL Database 스키마 관리 | Microsoft Docs"
description: "Azure SQL Database를 사용하는 다중 테넌트 응용 프로그램에서 여러 테넌트에 대한 스키마 관리"
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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: 346177be29ec196464f4f441858222ac5d5eb8c3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database를 사용하는 다중 테넌트 응용 프로그램에서 여러 테넌트에 대한 스키마 관리

[첫 번째 Wingtip Tickets SaaS 다중 테넌트 데이터베이스 자습서](saas-multitenantdb-get-started-deploy.md)는 앱이 공유 다중 테넌트 데이터베이스를 프로비전하고 카탈로그에 등록하는 방법을 보여 줍니다. 다른 응용 프로그램과 마찬가지로 Wingtip Tickets SaaS 앱도 시간이 지나면서 개선될 것이며 때때로 데이터베이스를 변경해야 할 것입니다. 변경 내용에는 최적의 앱 성능을 보장하기 위해 새로운 스키마나 변경된 스키마, 새로운 참조 데이터나 변경된 참조 데이터, 일상적인 데이터베이스 유지 관리 작업이 포함될 수 있습니다. SaaS 응용 프로그램에서 이러한 변경 내용은 테넌트 데이터베이스의 잠재적인 대규모 fleet에 통합된 방식으로 배포되어야 합니다. 이러한 변경 내용을 이후 테넌트 데이터베이스에 포함하려면 프로비전 프로세스에 해당 변경 내용을 통합해야 합니다.

이 자습서에서는 모든 테넌트에 대한 참조 데이터 업데이트를 배포하고 참조 데이터가 포함된 테이블에서 인덱스를 재조정하는 두 시나리오에 대해 알아봅니다. [탄력적 작업](sql-database-elastic-jobs-overview.md) 기능은 테넌트 데이터베이스 및 새 데이터베이스에 대한 템플릿으로 사용되는 *golden* 테넌트 데이터베이스 간에 이러한 작업을 실행하는 데 사용됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 작업 계정 만들기
> * 여러 테넌트 쿼리
> * 모든 테넌트 데이터베이스의 데이터 업데이트
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스 만들기


이 자습서를 수행하려면 다음 필수 조건이 충족되었는지 확인합니다.

* Wingtip Tickets SaaS 다중 테넌트 데이터베이스 응용 프로그램이 배포되어야 합니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS 다중 테넌트 데이터베이스 응용 프로그램 배포 및 탐색](saas-multitenantdb-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.
* 최신 버전의 SSMS(SQL Server Management Studio)가 설치되어 있습니다. [SSMS 다운로드 및 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> *이 자습서에서는 제한된 미리 보기(Elastic Database 작업)에 있는 SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행하려는 경우 subject=탄력적인 작업 미리 보기를 사용하여 구독 ID를 SaaSFeedback@microsoft.com에 제공하세요. 구독이 활성화되었다는 확인을 받은 후 [최신 시험판 작업 cmdlet을 다운로드하여 설치하세요](https://github.com/jaredmoo/azure-powershell/releases). 이 미리 보기는 제한적으로만 제공되므로 관련 질문이 있거나 지원이 필요한 경우 SaaSFeedback@microsoft.com에 문의해야 합니다.*


## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 스키마 관리 패턴 소개

이 샘플에 사용된 공유 다중 테넌트 데이터베이스 모델을 사용하면 테넌트 데이터베이스에 원하는 수의 테넌트를 포함할 수 있습니다. 이 샘플은 다중 테넌트 및 단일 테넌트 데이터베이스의 혼합을 사용하여 '하이브리드' 테넌트 관리 모델을 사용할 수 있는지 확인합니다. 이러한 데이터베이스의 유지 관리는 복잡합니다. [탄력적 작업](sql-database-elastic-jobs-overview.md)은 SQL 데이터 계층의 관리를 용이하게 합니다. 이 작업을 통해 데이터베이스 그룹에 대한 작업(T-SQL 스크립트)을 사용자 상호 작용 또는 입력과 관계 없이 안전하고 안정적으로 실행할 수 있습니다. 이 메서드는 응용 프로그램의 모든 테넌트에서 스키마 및 일반 참조 데이터 변경 내용을 배포하는 데 사용할 수 있습니다. 탄력적 작업은 항상 최신 스키마 및 참조 데이터가 포함되도록 데이터베이스의 *golden* 복사본을 유지 관리하는 데 사용할 수도 있습니다.

![화면](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>탄력적 작업의 제한된 미리 보기

현재 Azure SQL Database(추가 서비스 또는 구성 요소 불필요)의 통합 기능인 새로운 탄력적 작업 버전이 있습니다. 이 새로운 탄력적 작업 버전은 현재 제한된 미리 보기 상태입니다. 이 제한된 미리 보기에서는 현재 작업 계정을 만드는 PowerShell 및 작업을 만들고 관리하는 T-SQL을 지원합니다.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-scripts"></a>Wingtip Tickets SaaS 다중 테넌트 데이터베이스 응용 프로그램 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 응용 프로그램 소스 코드는 [WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 리포지토리에서 사용할 수 있습니다. <!-- [Steps to download the Wingtip Tickets SaaS Multi-tenant Database scripts](saas-multitenantdb-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts)-->

## <a name="create-a-job-account-database-and-new-job-account"></a>작업 계정 데이터베이스 및 새 작업 계정 만들기

이 자습서를 사용하려면 PowerShell을 사용하여 작업 계정 데이터베이스 및 작업 계정을 만들어야 합니다. MSDB 및 SQL 에이전트와 같이, 탄력적 작업에서는 Azure SQL Database를 사용하여 작업 정의, 작업 상태 및 기록을 저장합니다. 작업 계정이 만들어지면 작업을 만들어 즉시 모니터링할 수 있습니다.

1. **PowerShell ISE**에서 *...\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1*을 엽니다.
1. **F5** 키를 눌러 스크립트를 실행합니다.

*Demo-SchemaManagement.ps1* 스크립트는 *Deploy-SchemaManagement.ps1*을 호출하여 카탈로그 서버에서 **jobaccount**라는 이름의 *S2* 데이터베이스를 만듭니다. 그런 다음, 작업 계정을 만들어 jobaccount 데이터베이스를 매개 변수로 작업 계정 생성 호출로 전달합니다.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>모든 테넌트에 새 참조 데이터를 배포하는 작업 만들기

각 테넌트 데이터베이스에는 **VenueTypes** 테이블에 한 장소에서 호스트되는 이벤트 종류를 정의하는 일련의 장소 유형이 포함되어 있습니다. 이 연습에서는 두 개의 추가 장소 유형인 *Motorcycle Racing* 및 *Swimming Club*을 추가하기 위해 모든 데이터베이스에 업데이트를 배포합니다. 이러한 장소 유형은 테넌트 이벤트 앱에 표시된 배경 이미지에 해당합니다.

장소 유형 드롭다운 메뉴를 클릭하고 10개 장소 유형 옵션만 사용할 수 있고 특히 'Motorcycle Racing' 및 'Swimming Club'이 목록에 포함되어 있지 않은지 확인합니다.

이제 모든 테넌트 데이터베이스의 **VenueTypes** 테이블을 업데이트하고 새 장소 유형을 추가하는 작업을 만들어 보겠습니다.

새 작업을 만들려면 작업 계정을 만들 때 jobaccount 데이터베이스에서 생성한 작업 시스템 저장 프로시저 집합을 사용합니다.

1. SSMS에서 테넌트 서버: tenants1-\<user\>.database.windows.net에 연결합니다.
2. *tenants1-mt-\<user\>.database.windows.net* 서버에서 *tenants1* 데이터베이스로 이동한 후 *VenueTypes* 테이블을 쿼리하여 *Motorcycle Racing* 및 *Swimming Club*이 결과 목록에 **없는지** 확인합니다.
3. 카탈로그 서버 catalog-mt-\<user\>.database.windows.net에 연결합니다.
4. 카탈로그 서버의 jobaccount 데이터베이스에 연결합니다.
5. SSMS에서 ...\\Learning Modules\\Schema Management\\DeployReferenceData.sql 파일을 엽니다.
6. 문을 set @User = &lt;user&gt;로 수정하고 Wingtip Ticket SaaS 다중 테넌트 데이터베이스 응용 프로그램을 배포할 때 사용된 사용자 값을 바꿉니다.
7. **F5** 키를 눌러 스크립트를 실행합니다.

    * **sp\_add\_target\_group**은 대상 그룹 이름 DemoServerGroup을 만듭니다. 이제 대상 멤버를 그룹에 추가합니다.
    * **sp\_add\_target\_group\_member**는 *server* 대상 멤버 유형을 추가하는데, 작업 실행 시 해당 서버(테넌트 데이터베이스를 포함하고 있는 tenants1-&lt;user&gt; 서버) 내의 모든 데이터베이스가 작업에 포함되어야 합니다. *database* 대상 멤버 유형이 추가됩니다. 이 유형은 catalog-mt-&lt;user&gt; 서버에 있는 'golden' 데이터베이스(basetenantdb)입니다. 그리고 마지막으로 자습서 뒷부분에서 사용되는 adhocreporting 데이터베이스를 포함하는 *database* 대상 그룹 멤버 유형이 추가됩니다.
    * **sp\_add\_job**은 "참조 데이터 배포"라는 작업을 만듭니다.
    * **sp\_add\_jobstep**은 VenueTypes 참조 테이블을 업데이트하기 위한 T-SQL 명령 텍스트가 포함된 작업 단계를 만듭니다.
    * 스크립트의 남은 보기에서 개체의 존재 여부를 표시하고 작업 실행을 모니터링합니다. 이러한 쿼리를 사용하여 **lifecycle** 열의 상태 값을 검토해 테넌트 데이터베이스와 참조 테이블이 포함된 추가 데이터베이스 2개에서 작업이 정상적으로 완료된 시기를 확인합니다.

1. SSMS에서 *tenants1-mt-&lt;user&gt;* 서버의 테넌트 데이터베이스로 이동한 후 *VenueTypes* 테이블을 쿼리하여 이제 *Motorcycle Racing* 및 *Swimming Club*이 테이블에 **추가*되었는지 확인합니다.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>참조 테이블 인덱스를 관리하는 작업 만들기

이전 연습과 비슷하게, 이 연습에서는 참조 테이블 기본 키의 인덱스를 다시 작성하는 작업을 만듭니다. 이 작업은 테이블에 대량의 데이터를 로드한 후 관리자가 수행할 수 있는 일반적인 데이터베이스 관리 작업입니다.


1. SSMS에서 catalog-mt-&lt;User&gt;.database.windows.net 서버의 jobaccount 데이터베이스에 연결합니다.
2. SSMS에서 ...\\Learning Modules\\Schema Management\\OnlineReindex.sql 파일을 엽니다.
3. **F5** 키를 눌러 스크립트를 실행합니다.

    * **sp\_add\_job**은 "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"라는 새 작업을 만듭니다.
    * **sp\_add\_jobstep**은 인덱스를 업데이트할 T-SQL 명령 텍스트를 포함하는 작업 단계를 만듭니다.
    * 스크립트의 나머지 뷰는 작업 실행을 모니터링합니다. 이러한 쿼리를 사용하여 **lifecycle** 열의 상태 값을 검토해 작업이 모든 대상 그룹 구성원에서 성공적으로 완료된 시기를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]

> * 여러 테넌트에서 쿼리하는 작업 계정 만들기
> * 모든 테넌트 데이터베이스의 데이터 업데이트
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스 만들기

[임시 분석 자습서](saas-multitenantdb-adhoc-reporting.md)


## <a name="additional-resources"></a>추가 리소스

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [규모가 확장된 클라우드 데이터베이스 관리](sql-database-elastic-jobs-overview.md)
* [규모가 확장된 클라우드 데이터베이스 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)
