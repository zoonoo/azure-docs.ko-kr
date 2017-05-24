---
title: "SaaS 알아보기(Azure SQL Database를 사용하는 샘플 SaaS 응용 프로그램) | Microsoft Docs"
description: "SQL Database를 사용한 SaaS 응용 프로그램 빌드"
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
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>WTP(Wingtip 티켓 플랫폼) 샘플 SaaS 응용 프로그램 소개

WTP(Wingtip 티켓 플랫폼) SaaS 응용 프로그램은 SQL Database의 고유 장점을 보여주는 샘플 다중 테넌트 앱입니다. 앱은 테넌트별 데이터베이스(SaaS 응용 프로그램 패턴)를 사용하여 여러 테넌트에 서비스를 제공합니다. WTP 앱은 SaaS 디자인 및 관리 패턴을 포함한 SaaS 시나리오를 사용할 수 있게 해주는 Azure SQL Database의 기능을 보여주기 위해 설계되었습니다. 신속하게 가동 및 실행하기 위해 [WTP 앱에서 5분 내에 배포합니다](sql-database-saas-tutorial.md).

WTP 앱을 배포한 후 초기 배포 시 작성된 [자습서 컬렉션](#sql-database-saas-tutorials)을 탐색합니다. 각 자습서는 SaaS 응용 프로그램에서 구현되는 일반적인 작업에 중점을 두고 있습니다. 작업은 SQL Database의 기본 제공 기능을 활용하는 SaaS 패턴을 따라 구현됩니다. 설명된 패턴에는 새로운 테넌트 프로비전, 테넌트 데이터베이스 복원, 모든 테넌트에서 분산 쿼리 실행, 모든 테넌트 데이터베이스에서 스키마 변경 내용 롤아웃이 포함됩니다. 각 자습서에는 재사용 가능한 스크립트와 함께 응용 프로그램에서 동일한 SaaS 관리 패턴에 대한 이해 및 구현을 간소화해주는 상세한 설명이 포함됩니다.

WTP 응용 프로그램이 어느 정도 완료되고 샘플 응용 프로그램으로 유용하면 데이터 계층과 관련된 핵심 SaaS 패턴에 중점을 두는 것이 중요합니다. 즉, 데이터 계층에 집중하고 앱 자체를 과도하게 분석하지 마세요. 이러한 핵심 SaaS 패턴의 구현 방식을 이해하는 것은 특정 비즈니스 요구 사항에 필요한 모든 수정을 고려하면서 응용 프로그램에서 이러한 패턴을 구현하기 위한 열쇠입니다.



## <a name="application-architecture"></a>응용 프로그램 아키텍처

WTP 앱은 테넌트별 데이터베이스 모델을 사용하며 효율을 극대화하기 위해 SQL 탄력적 풀을 사용합니다.
관리 및 연결 프로비전을 위한 테넌트 카탈로그 사용.
통합된 앱, 풀, 데이터베이스 모니터링 및 경고(OMS).
테넌트 간 스키마 및 참조 데이터 관리(Elastic Database 작업).
테넌트 간 쿼리, 운영 분석(탄력적 쿼리).
지리적으로 분산된 데이터를 사용하여 도달률 확대.
스스로 야기된 문제로부터 복구하기 위해 규모(지역 복원, 지역에서 복제, 자동 DR)에 관계 없이 테넌트 셀프 서비스 관리(관리 API를 통해) PITR에서 비즈니스 연속성 단일 테넌트 복구(PITR) DR.

세 가지 샘플 테넌트 및 카탈로그 데이터베이스와 함께 풀을 사용하는 핵심 Wingtip 응용 프로그램.

![WTP 아키텍처](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>SQL Database WTP SaaS 자습서

다음 자습서는 [Wingtip 티켓 플랫폼 SaaS 응용 프로그램 샘플](sql-database-saas-tutorial.md)의 초기 배포 시 생성됩니다.

| 영역 | 설명 | 스크립트 위치 |
|:--|:--|:--|
|[프로비전 및 카탈로그 테넌트 자습서](sql-database-saas-tutorial-provision-and-catalog.md)| 새 테넌트를 프로비전하고 카탈로그에 등록 | [github의 스크립트](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[성능 모니터링 및 관리 자습서](sql-database-saas-tutorial-performance-monitoring.md)| 데이터베이스와 풀의 성능 모니터링 및 관리 | [github의 스크립트](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[단일 테넌트 복원 자습서](sql-database-saas-tutorial-restore-single-tenant.md)| 테넌트 데이터베이스 복원 | [github의 스크립트](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[테넌트 스키마 관리 자습서](sql-database-saas-tutorial-schema-management.md)| 모든 테넌트에서 쿼리 실행  | [github의 스크립트](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[임시 분석 실행 자습서](sql-database-saas-tutorial-adhoc-analytics.md) | 임시 분석 데이터베이스를 만들고 모든 테넌트에서 쿼리 실행  | [github의 스크립트](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Log Analytics(OMS)를 사용한 관리 자습서](sql-database-saas-tutorial-log-analytics.md) | Log Analytics 구성 및 탐색 | [github의 스크립트](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[테넌트 분석 실행 자습서](sql-database-saas-tutorial-tenant-analytics.md) | 테넌트 분석 쿼리 설정 및 실행 | [github의 스크립트](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Wingtip 응용 프로그램 스크립트 가져오기

Wingtip Tickets 스크립트 및 응용 프로그램 소스 코드는 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 리포지토리에서 사용할 수 있습니다. 스크립트 파일은 [Learning Modules 폴더](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)에 있습니다. **Learning Modules** 폴더의 구조를 유지하면서 이 폴더를 로컬 컴퓨터로 다운로드합니다.

## <a name="working-with-the-wtp-powershell-scripts"></a>WTP PowerShell 스크립트 작업

WTP 응용 프로그램 작업의 이점은 제공된 스크립트를 활용하고 서로 다른 SaaS 패턴의 구현 방법을 검사하는 데 있습니다.

제공된 스크립트 및 모듈을 확인하고 단계별 코드 실행을 통해 보다 쉽게 이해할 수 있도록 [Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)를 사용하세요. *Demo-*로 시작하는 대부분의 스크립트에는 실행 전에 수정할 수 있는 변수가 포함되어 있으므로 PowerShell ISE를 사용하면 이러한 스크립트 작업을 간소화할 수 있습니다.

각 WTP 앱 배포의 경우 배포 중에 정의된 리소스 그룹 및 사용자 이름 값을 설정하는 데 사용하는 두 개의 매개 변수가 포함된 **UserConfig.psm1** 파일이 있습니다. 배포가 완료된 후 _ResourceGroupName_ 및 _Name_을 설정하는 **UserConfig.psm1** 모듈을 편집하세요. 이러한 값은 다른 스크립트에서 사용하여 성공적으로 실행되므로 배포가 완료되면 설정하는 것이 좋습니다.



### <a name="execute-scripts-by-pressing-f5"></a>F5 키를 눌러 스크립트 실행

여러 스크립트에서 *$PSScriptRoot*를 사용하여 폴더를 탐색할 수 있으며 이 변수는 **F5** 키를 눌러 스크립트를 실행할 때만 평가됩니다.  선택 내용을 강조 표시하고 실행(**F8**)하면 오류가 발생할 수 있으므로 WTP 스크립트를 실행할 때는 **F5**를 누르세요.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>스크립트를 단계별로 실행하여 구현 검사

스크립트 탐색 시 실제 값은 수행할 작업을 단계별로 실행하여 가져옵니다. 각 작업을 수행하는 데 필요한 단계를 보여 주는 높은 수준의 워크플로를 읽기 쉽게 제공하는 첫 번째 수준 _Demo-_ 스크립트를 체크아웃합니다. 개별 호출을 자세히 살펴보고 다른 SaaS 패턴에 대한 구현 세부 정보를 확인합니다.

[PowerShell 스크립트 디버깅](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise) 및 작업 팁:

* PowerShell ISE에서 demo- 스크립트를 열고 구성합니다.
* **F5**를 눌러 실행하거나 계속합니다. 스크립트의 선택 항목을 실행할 때 *$PSScriptRoot*가 평가되지 않으므로 **F8**은 사용하지 않는 것이 좋습니다.
* 행을 클릭하거나 선택하고 **F9** 키를 눌러 중단점을 배치합니다.
* **F10**을 사용하여 함수 또는 스크립트 호출을 실행합니다.
* **F11**을 사용하여 함수 또는 스크립트 호출 코드를 한 단계씩 실행합니다.
* **Shift + F11**을 사용하여 현재 함수 또는 스크립트 호출을 종료합니다.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>데이터베이스 스키마를 탐색하고 SSMS를 사용하여 SQL 쿼리를 실행합니다.

[SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 WTP 서버 및 데이터베이스에 연결하고 탐색합니다.

WTP 샘플 앱에는 연결할 두 SQL Database 서버가 있는데 *tenants1* 서버 및 *카탈로그* 서버입니다.


1. *SSMS*를 열고 *tenants1-&lt;User&gt;.database.windows.net* 서버에 연결합니다.
2. **연결** > **데이터베이스 엔진...**을 클릭합니다.

   ![카탈로그 서버](media/sql-database-wtp-overview/connect.png)

1. 데모 자격 증명: 로그인 = *developer*, 암호 =*P@ssword1*

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. 2-3단계를 반복하고 *catalog-&lt;User&gt;.database.windows.net* 서버에 연결합니다.

성공적으로 연결된 후 두 서버가 표시됩니다. 프로비전한 테넌트 수에 따라 데이터베이스가 더 많이 표시되거나 적게 표시될 수 있습니다.

![개체 탐색기](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>다음 단계

[Wingtip 티켓 SaaS 응용 프로그램 샘플 배포](sql-database-saas-tutorial.md)
