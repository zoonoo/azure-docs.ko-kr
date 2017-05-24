---
title: "다중 테넌트 앱에서 Azure SQL Database 스키마 관리 | Microsoft Docs"
description: "Azure SQL Database를 사용하는 다중 테넌트 응용 프로그램에서 여러 테넌트에 대한 스키마 관리"
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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 19d02229781186053a0063af1c7e1a3280179f46
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017


---
# <a name="manage-schema-for-multiple-tenants-in-the-wtp-saas-application"></a>WTP SaaS 응용 프로그램에서 여러 테넌트에 대한 스키마 관리

WTP 응용 프로그램 자습서 소개에서는 WTP 앱이 초기 스키마를 사용하여 테넌트 데이터베이스를 프로비전하고 카탈로그에 등록하는 방법을 보여 줍니다. 다른 응용 프로그램과 마찬가지로 WTP 앱도 시간이 지나면서 개선되며 때때로 데이터베이스를 변경해야 합니다. 변경 내용에는 최적의 앱 성능을 보장하기 위해 새로운 스키마나 변경된 스키마, 새로운 참조 데이터나 변경된 참조 데이터, 일상적인 데이터베이스 유지 관리 작업이 포함될 수 있습니다. SaaS 응용 프로그램에서 이러한 변경 내용은 테넌트 데이터베이스의 잠재적인 대규모 fleet에 통합된 방식으로 배포되어야 합니다. 또한 변경 내용은 이후 테넌트 데이터베이스 프로비전 프로세스에 통합되어야 합니다.

이 자습서에서는 모든 테넌트에 대한 참조 데이터 업데이트를 배포하고 참조 데이터가 포함된 테이블에서 인덱스를 재조정하는 두 시나리오에 대해 알아봅니다. [탄력적 작업](sql-database-elastic-jobs-overview.md) 기능은 모든 테넌트 및 새 데이터베이스에 대한 템플릿으로 사용되는 *golden* 테넌트 데이터베이스 간에 이러한 작업을 실행하는 데 사용됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]

> * 여러 테넌트에서 쿼리하는 작업 계정 만들기
> * 모든 테넌트 데이터베이스의 데이터 업데이트
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스 만들기


이 자습서를 수행하려면 다음 필수 조건이 충족되었는지 확인합니다.

* WTP 앱이 배포되었습니다. 5분 내에 배포하려면 [WTP SaaS 응용 프로그램 배포 및 탐색](sql-database-saas-tutorial.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.
* 최신 버전의 SSMS(SQL Server Management Studio)가 설치되어 있습니다. [SSMS 다운로드 및 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*이 자습서에서는 제한된 미리 보기(Elastic Database 작업)에 있는 SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행하려는 경우 subject=탄력적인 작업 미리 보기를 사용하여 구독 ID를 SaaSFeedback@microsoft.com에 제공하세요. 구독이 활성화되었다는 확인을 받은 후 [최신 시험판 작업 cmdlet을 다운로드하여 설치하세요](https://github.com/jaredmoo/azure-powershell/releases). 제한된 미리 보기이므로 관련 질문 사항이 있거나 지원이 필요한 경우 SaaSFeedback@microsoft.com에 문의해야 합니다.*


## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 스키마 관리 패턴 소개

데이터베이스 SaaS 패턴당 단일 테넌트는 그 결과 데이터 격리로부터 여러 면에서 장점이 있지만, 동시에 많은 데이터베이스의 유지 관리 및 관리에서 복잡성이 더 커집니다. [탄력적 작업](sql-database-elastic-jobs-overview.md)은 SQL 데이터 계층의 관리를 용이하게 합니다. 이 작업을 통해 데이터베이스 그룹에 대한 작업(T-SQL 스크립트)을 사용자 상호 작용 또는 입력과 관계 없이 안전하고 안정적으로 실행할 수 있습니다. 이 메서드는 응용 프로그램의 모든 테넌트에서 스키마 및 일반 참조 데이터 변경 내용을 배포하는 데 사용할 수 있습니다. 탄력적 작업은 항상 최신 스키마 및 참조 데이터가 포함되도록 데이터베이스의 *golden* 복사본을 유지 관리하는 데 사용할 수도 있습니다.

![화면](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>탄력적 작업의 제한된 미리 보기

현재 Azure SQL Database(추가 서비스 또는 구성 요소 불필요)의 통합 기능인 새로운 탄력적 작업 버전이 있습니다. 이 새로운 탄력적 작업 버전은 현재 제한된 미리 보기 상태입니다. 이 제한된 미리 보기에서는 현재 작업 계정을 만드는 PowerShell 및 작업을 만들고 관리하는 T-SQL을 지원합니다.

> [!NOTE]
> *이 자습서에서는 제한된 미리 보기(Elastic Database 작업)에 있는 SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행하려는 경우 subject=탄력적인 작업 미리 보기를 사용하여 구독 ID를 SaaSFeedback@microsoft.com에 제공하세요. 구독이 활성화되었다는 확인을 받은 후 [최신 시험판 작업 cmdlet을 다운로드하여 설치하세요](https://github.com/jaredmoo/azure-powershell/releases). 제한된 미리 보기이므로 관련 질문 사항이 있거나 지원이 필요한 경우 SaaSFeedback@microsoft.com에 문의해야 합니다.*

## <a name="get-the-wingtip-application-scripts"></a>Wingtip 응용 프로그램 스크립트 가져오기

Wingtip Tickets 스크립트 및 응용 프로그램 소스 코드는 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 리포지토리에서 사용할 수 있습니다. 스크립트 파일은 [Learning Modules 폴더](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)에 있습니다. **Learning Modules** 폴더의 구조를 유지하면서 이 폴더를 로컬 컴퓨터로 다운로드합니다.

## <a name="create-a-job-account-database-and-new-job-account"></a>작업 계정 데이터베이스 및 새 작업 계정 만들기

이 자습서를 사용하려면 PowerShell을 사용하여 작업 계정 데이터베이스 및 작업 계정을 만들어야 합니다. MSDB 및 SQL 에이전트와 같이, 탄력적 작업에서는 Azure SQL Database를 사용하여 작업 정의, 작업 상태 및 기록을 저장합니다. 작업 계정이 만들어지면 작업을 만들어 즉시 모니터링할 수 있습니다.

1. **PowerShell ISE**에서 ...\\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1*을 엽니다.
1. **F5** 키를 눌러 스크립트를 실행합니다.

*Demo-SchemaManagement.ps1* 스크립트는 *Deploy-SchemaManagement.ps1*을 호출하여 카탈로그 서버에서 **jobaccount**라는 이름의 *S2* 데이터베이스를 만듭니다. 그런 다음, 작업 계정을 만들어 jobaccount 데이터베이스를 매개 변수로 작업 계정 생성 호출로 전달합니다.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>모든 테넌트에 새 참조 데이터를 배포하는 작업 만들기

각 테넌트 데이터베이스에는 한 장소에서 호스팅되는 이벤트 종류를 정의하는 일련의 장소 유형이 포함되어 있습니다. 이 연습에서는 두 개의 추가 장소 유형인 *Motorcycle Racing* 및 *Swimming Club*을 추가하기 위해 모든 테넌트 데이터베이스에 업데이트를 배포합니다. 이러한 장소 유형은 테넌트 이벤트 앱에 표시된 배경 이미지에 해당합니다.

장소 유형 드롭다운 메뉴를 클릭하고 10개 장소 유형 옵션만 사용할 수 있고 특히 'Motorcycle Racing' 및 'Swimming Club'이 목록에 포함되어 있지 않은지 확인합니다.

이제 모든 테넌트 데이터베이스의 *VenueTypes* 테이블을 업데이트하고 새 장소 유형을 추가하는 작업을 만들어 보겠습니다.

새 작업을 만들려면 작업 계정을 만들 때 jobaccount 데이터베이스에서 생성한 작업 시스템 저장 프로시저 집합을 사용합니다.

1. SSMS를 열고 카탈로그 서버에 연결: catalog-\<사용자\>.database.windows.net 서버
1. 테넌트 서버에 연결: tenants1-\<사용자\>.database.windows.net
1. *tenants1* 서버에서 *contosoconcerthall* 데이터베이스로 이동한 후 *VenueTypes* 테이블을 쿼리하여 *Motorcycle Racing* 및 *Swimming Club*이 결과 목록에 **없는지** 확인합니다.
1. ...\\Learning Modules\\Schema Management\\DeployReferenceData.sql 파일을 엽니다.
1. \<사용자\>를 수정하되, WTP 앱을 배포할 때 스크립트의 3개 위치에서 모두 사용된 사용자 이름을 사용합니다.
1. jobaccount 데이터베이스에 연결되어 있는지 확인하고 **F5**를 눌러 스크립트를 실행합니다.

* **sp\_add\_target\_group**은 대상 그룹 이름 DemoServerGroup를 만듭니다. 이제 대상 멤버를 추가해야 합니다.
* **sp\_add\_target\_group\_member**는 *server* 대상 멤버 유형을 추가하는데, 작업 실행 시 해당 서버(테넌트 데이터베이스를 포함하고 있는 customer1-&lt;WtpUser&gt; 서버) 내의 모든 데이터베이스가 작업에 포함되어야 합니다. 두 번째는 *database* 대상 멤버 유형, 특히 'golden' 데이터베이스, catalog-&lt;WtpUser&gt; 서버에 상주하는 baseTenantDB를 추가하고, 마지막으로 나중에 자습서에서 사용되는 임시 분석 데이터베이스가 포함된 다른 *database* 대상 그룹 멤버 유형을 추가합니다.
* **sp\_add\_job**은 "참조 데이터 배포"라는 작업을 만듭니다.
* **sp\_add\_jobstep**은 VenueTypes 참조 테이블에 업데이트할 T-SQL 명령 텍스트가 포함된 작업 단계를 만듭니다.
* 스크립트의 남은 보기에서 개체의 존재 여부를 표시하고 작업 실행을 모니터링합니다. **lifecycle** 열에서 상태 값을 검토합니다. 모든 테넌트 데이터베이스 및 참조 테이블을 포함하는 두 개의 추가 데이터베이스에서 작업을 성공적으로 마쳤습니다.

1. SSMS에서 *tenants1* 서버의 *contosoconcerthall* 데이터베이스로 이동한 후 *VenueTypes* 테이블을 쿼리하여 이제 *Motorcycle Racing* 및 *Swimming Club***이** 결과 목록에 있는지 확인합니다.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>참조 테이블 인덱스를 관리하는 작업 만들기

이전 연습과 비슷하게, 이 연습에서는 참조 테이블 기본 키의 인덱스를 다시 작성하는 작업을 만듭니다. 이 작업은 테이블에 대량의 데이터를 로드한 후 관리자가 수행할 수 있는 일반적인 데이터베이스 관리 작업입니다.

동일한 작업 '시스템' 저장 프로시저를 사용하여 작업을 만듭니다.

1. SSMS를 열고 catalog-&lt;WtpUser&gt;.database.windows.net 서버에 연결합니다.
1. ...\\Learning Modules\\Schema Management\\OnlineReindex.sql 파일을 엽니다.
1. 마우스 오른쪽 단추로 클릭하고 연결을 선택한 후 catalog-&lt;WtpUser&gt;.database.windows.net 서버에 연결합니다(아직 연결하지 않은 경우).
1. jobaccount 데이터베이스에 연결되어 있는지 확인하고 F5를 눌러 스크립트를 실행합니다.

* sp\_add\_job은 "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"라는 새 작업을 만듭니다.
* sp\_add\_jobstep은 인덱스를 업데이트할 T-SQL 명령 텍스트를 포함하는 작업 단계를 만듭니다.




## <a name="next-steps"></a>다음 단계

이 자습서에서 학습한 방법은 다음과 같습니다.

> [!div class="checklist"]

> * 여러 테넌트에서 쿼리하는 작업 계정 만들기
> * 모든 테넌트 데이터베이스의 데이터 업데이트
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스 만들기

[임시 분석 자습서](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>추가 리소스

* [초기 WTP(Wingtip Tickets Platform) 응용 프로그램 배포를 기반으로 하는 추가 자습서](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [규모가 확장된 클라우드 데이터베이스 관리](sql-database-elastic-jobs-overview.md)
* [규모가 확장된 클라우드 데이터베이스 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)
