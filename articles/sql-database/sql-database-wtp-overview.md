---
title: "Azure SQL Database 다중 테넌트 앱 예제 - Wingtip SaaS | Microsoft Docs"
description: "Azure SQL Database를 사용하는 샘플 다중 테넌트 응용 프로그램인 Wingtip SaaS 예제 사용에 대해 알아봅니다."
keywords: "SQL Database 자습서"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 46c9a3eadc2c23959b4d08649c6c0215d44b493e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>SQL Database 다중 테넌트 SaaS 앱 예제 소개

*Wingtip SaaS* 응용 프로그램은 SQL Database의 고유 장점을 보여주는 샘플 다중 테넌트 앱입니다. 앱은 테넌트별 데이터베이스(SaaS 응용 프로그램 패턴)를 사용하여 여러 테넌트에 서비스를 제공합니다. 이 앱은 여러 SaaS 디자인 및 관리 패턴을 포함한 SaaS 시나리오를 사용할 수 있게 해주는 Azure SQL Database의 기능을 보여주기 위해 설계되었습니다. 신속하게 가동 및 실행하기 위해 Wingtip SaaS 앱에서 5분 내에 배포합니다.

응용 프로그램 소스 코드는 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 리포지토리에서 사용할 수 있습니다. 스크립트를 실행하려면 로컬 컴퓨터에 [Learning Modules 폴더를 다운로드](#download-and-unblock-the-wingtip-saas-scripts)합니다.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS 자습서

앱을 배포한 후 초기 배포 시 작성된 다음 자습서를 탐색합니다. 이러한 자습서에서는 SQL Database, SQL Data Warehouse 및 기타 Azure 서비스의 기본 제공 기능을 활용하는 일반적인 SaaS 패턴을 살펴봅니다. 자습서에는 PowerShell 스크립트와 함께 응용 프로그램에서 동일한 SaaS 관리 패턴에 대한 이해 및 구현을 간소화해주는 상세한 설명이 포함됩니다.


| 자습서 | 설명 |
|:--|:--|
|[Wingtip SaaS 응용 프로그램 배포 및 탐색](sql-database-saas-tutorial.md)| **여기에서 시작하세요.** Azure 구독에 Wingtip SaaS 응용 프로그램을 배포하고 탐색합니다. |
|[프로비전 및 카탈로그 테넌트](sql-database-saas-tutorial-provision-and-catalog.md)| 카탈로그 데이터베이스를 사용하여 응용 프로그램을 테넌트에 연결하는 방법 및 카탈로그가 테넌트를 해당 데이터에 매핑하는 방법을 알아봅니다. |
|[성능 모니터링 및 관리](sql-database-saas-tutorial-performance-monitoring.md)| SQL Database의 모니터링 기능을 사용하는 방법 및 성능 임계값이 초과된 경우 경고를 설정하는 방법을 알아봅니다. |
|[Log Analytics(OMS)로 모니터링](sql-database-saas-tutorial-log-analytics.md) | [Log Analytics](../log-analytics/log-analytics-overview.md)를 사용하여 여러 풀에 걸쳐 있는 많은 양의 리소스를 모니터링하는 방법을 알아봅니다. |
|[단일 테넌트 복원](sql-database-saas-tutorial-restore-single-tenant.md)| 테넌트 데이터베이스를 이전 시점으로 복원하는 방법을 알아봅니다. 기존 테넌트 데이터베이스를 온라인으로 유지한 상태에서 병렬 데이터베이스를 복원하는 단계도 포함됩니다. |
|[테넌트 스키마 관리](sql-database-saas-tutorial-schema-management.md)| 모든 Wingtip SaaS 테넌트에서 스키마를 업데이트하고 참조 데이터를 업데이트하는 방법을 알아봅니다. |
|[임시 분석 실행](sql-database-saas-tutorial-adhoc-analytics.md) | 임시 분석 데이터베이스를 만들고 모든 테넌트에서 실시간 분산 쿼리를 실행합니다.  |
|[테넌트 분석 실행](sql-database-saas-tutorial-tenant-analytics.md) | 오프라인 분석 쿼리를 실행하기 위해 테넌트 데이터를 분석 데이터베이스 또는 데이터 웨어하우스에 추출합니다. |



## <a name="application-architecture"></a>응용 프로그램 아키텍처

Wingtip SaaS 앱은 테넌트별 데이터베이스 모델을 사용하며 효율을 극대화하기 위해 SQL 탄력적 풀을 사용합니다. 테넌트를 해당 데이터에 매핑하고 프로비전하는 데 카탈로그 데이터베이스가 사용됩니다. 핵심 Wingtip SaaS 응용 프로그램은 세 가지 샘플 테넌트가 있는 풀 및 카탈로그 데이터베이스를 사용합니다. 다양한 Wingtip SaaS 자습서를 완료하면 초기 배포에 분석 데이터베이스, 데이터베이스 간 스키마 관리 등의 추가 기능을 이해할 수 있습니다.


![Wingtip SaaS 아키텍처](media/sql-database-wtp-overview/app-architecture.png)


자습서를 진행하고 앱을 사용하는 동안 데이터 계층과 관련된 SaaS 패턴에 중점을 두어야 합니다. 즉, 데이터 계층에 집중하고 앱 자체를 과도하게 분석하지 마세요. 이러한 SaaS 패턴의 구현 방식을 이해하는 것은 특정 비즈니스 요구 사항에 필요한 모든 수정을 고려하면서 응용 프로그램에서 이러한 패턴을 구현하기 위한 열쇠입니다.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Wingtip SaaS 스크립트 다운로드 및 차단 해제

zip 파일이 외부 원본에서 다운로드되고 추출될 때 Windows에서 실행 가능한 콘텐츠(스크립트, dll)를 차단할 수 있습니다. Zip 파일에서 스크립트를 추출할 경우 ***아래 단계에 따라 추출하기 전에 .zip 파일을 차단 해제***하세요. 이렇게 하면 스크립트를 실행할 수 있습니다.

1. [Wingtip SaaS GitHub 리포지토리](https://github.com/Microsoft/WingtipSaaS)로 이동합니다.
1. **복제 또는 다운로드**를 클릭합니다.
1. **ZIP 다운로드**를 클릭하고 파일을 저장합니다.
1. **WingtipSaaS-master.zip** 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
1. **일반** 탭에서 **차단 해제**를 선택합니다.
1. **확인**을 클릭합니다.
1. 파일의 압축을 풉니다.

스크립트는 *..\\WingtipSaaS-master\\Learning Modules* 폴더에 있습니다.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Wingtip SaaS PowerShell 스크립트 작업

샘플을 최대한 활용하려면 제공된 스크립트를 자세히 살펴보아야 합니다. 중단점을 사용하고 스크립트를 단계별로 실행하면서 다양한 SaaS 패턴이 구현된 방식을 자세히 살펴보세요. 가장 잘 이해할 수 있도록 제공된 스크립트 및 모듈을 손쉽게 단계별로 실행하려면 [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)를 사용하는 것이 좋습니다.

### <a name="update-the-configuration-file-for-your-deployment"></a>배포에 대한 구성 파일 업데이트

배포 중에 설정한 사용자 값 및 리소스 그룹을 사용하여 **UserConfig.psm1** 파일을 편집합니다.

1. *PowerShell ISE*를 열고 ...\\Learning Modules\\*UserConfig.psm1*을 로드합니다. 
1. 배포에 대한 특정 값(줄 10 및 11에서만)으로 *ResourceGroupName* 및 *Name*을 업데이트합니다.
1. 변경 내용을 저장합니다.

여기서 이러한 값을 설정하면 모든 스크립트에서 이러한 배포별 값을 업데이트할 필요가 없습니다.

### <a name="execute-scripts-by-pressing-f5"></a>F5 키를 눌러 스크립트 실행

여러 스크립트에서 *$PSScriptRoot*를 사용하여 폴더를 탐색할 수 있으며 *$PSScriptRoot*는 **F5** 키를 눌러 스크립트를 실행할 때만 평가됩니다.  선택 내용을 강조 표시하고 실행(**F8**)하면 오류가 발생할 수 있으므로 스크립트를 실행할 때는 **F5** 키를 누르세요.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>스크립트를 단계별로 실행하여 구현 검사

스크립트를 이해하는 가장 좋은 방법은 단계별로 실행하여 수행되는 작업을 확인하는 것이 좋습니다. 개략적인 워크플로를 따르는 포함된 **Demo-** 스크립트를 확인합니다. **Demo-** 스크립트는 각 작업을 완료하는 데 필요한 단계를 보여 주므로 중단점을 설정하고 개별 호출로 드릴다운하여 여러 SaaS 패턴에 대한 구현 정보를 확인합니다.

PowerShell 스크립트 탐색 및 단계별 실행에 대한 팁

* PowerShell ISE에서 **Demo-** 스크립트를 엽니다.
* **F5** 키를 사용하여 실행하거나 계속합니다. 스크립트의 선택 항목을 실행할 때 *$PSScriptRoot*가 평가되지 않으므로 **F8** 키는 사용하지 않는 것이 좋습니다.
* 행을 클릭하거나 선택하고 **F9** 키를 눌러 중단점을 배치합니다.
* **F10**을 사용하여 함수 또는 스크립트 호출을 실행합니다.
* **F11**을 사용하여 함수 또는 스크립트 호출 코드를 한 단계씩 실행합니다.
* **Shift + F11**을 사용하여 현재 함수 또는 스크립트 호출을 종료합니다.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>데이터베이스 스키마를 탐색하고 SSMS를 사용하여 SQL 쿼리를 실행합니다.

[SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 응용 프로그램 서버 및 데이터베이스에 연결하고 탐색합니다.

초기에 배포에는 연결할 두 SQL Database 서버가 있는데 *tenants1-&lt;User&gt;* 서버 및 *catalog-&lt;User&gt;* 서버입니다. 성공적인 데모 연결을 위해 두 서버 모두에 모든 IP의 통과를 허용하는 [방화벽 규칙](sql-database-firewall-configure.md)이 있습니다.


1. *SSMS*를 열고 *tenants1-&lt;User&gt;.database.windows.net* 서버에 연결합니다.
1. **연결** > **데이터베이스 엔진...**을 클릭합니다.

   ![카탈로그 서버](media/sql-database-wtp-overview/connect.png)

1. 데모 자격 증명: 로그인 = *developer*, 암호 =*P@ssword1*

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. 2-3단계를 반복하고 *catalog-&lt;User&gt;.database.windows.net* 서버에 연결합니다.

성공적으로 연결된 후 두 서버가 표시됩니다. 프로비전한 테넌트에 따라 데이터베이스 목록이 다를 수 있습니다.

![개체 탐색기](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>다음 단계

[Wingtip SaaS 응용 프로그램 배포](sql-database-saas-tutorial.md)
