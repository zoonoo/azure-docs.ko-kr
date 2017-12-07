---
title: "다중 테넌트 SaaS 앱에서 Azure SQL Database 복원 | Microsoft Docs"
description: "실수로 데이터를 삭제한 후 단일 테넌트 SQL Database를 복원하는 방법에 대해 알아봅니다."
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
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: ee2bc6d8b75b92243c0550db0044895e41c9474b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>다중 테넌트 SaaS 앱에서 단일 테넌트의 Azure SQL Database 복원

Wingtip Tickets SaaS 앱은 각 테넌트에 자체 데이터베이스가 있는 테넌트당 데이터베이스 모델을 사용하여 빌드됩니다. 이 모델의 이점 중 하나는 다른 테넌트에 영향을 주지 않으면서 격리된 단일 테넌트의 데이터를 쉽게 복원할 수 있다는 것입니다.

이 자습서에서는 다음 두 가지 데이터 복구 패턴에 대해 알아봅니다.

> [!div class="checklist"]

> * 병렬 데이터베이스에 데이터베이스 복원(병렬)
> * 원래 위치에 데이터베이스 복원


|||
|:--|:--|
| **병렬 데이터베이스에 이전 시점의 테넌트 복원** | 이 패턴은 검토, 감사, 준수 등을 위해 테넌트에서 사용할 수 있습니다. 원본 데이터베이스는 온라인 상태에서 변경되지 않은 채 유지됩니다. |
| **원래 위치에 테넌트 복원** | 이 패턴은 일반적으로 테넌트에서 실수로 데이터를 삭제한 후 이전 시점으로 테넌트를 복구하는 데 사용합니다. 원본 데이터베이스는 오프라인 상태가 되고 복원된 데이터베이스로 대체됩니다. |
|||

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip SaaS 앱이 배포되었습니다. 5분 내에 배포하려면 [Wingtip SaaS 응용 프로그램 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>SaaS 테넌트 복원 패턴 소개

테넌트 복원 패턴의 경우 개별 테넌트의 데이터를 복원하기 위한 두 가지 간단한 패턴이 있습니다. 테넌트 데이터베이스가 서로 격리되어 있기 때문에 하나의 테넌트를 복원해도 다른 테넌트의 데이터에는 영향을 주지 않습니다.

첫 번째 패턴에서는 데이터가 새 데이터베이스로 복원됩니다. 그런 다음 프로덕션 데이터와 함께 이 데이터베이스에 대한 액세스 권한이 테넌트에 부여됩니다. 이 패턴을 사용하면 테넌트 관리자가 복원된 데이터를 검토하고 현재 데이터 값을 선택적으로 덮어쓸 수 있습니다. 데이터 복구 옵션이 얼마나 정교해야 하는지 결정하는 것은 SaaS 앱 디자이너에게 달려 있습니다. 일부 시나리오에서는 특정 시점의 데이터를 검토할 수 있는 것만 필요할 수도 있습니다. 데이터베이스에서 [지역에서 복제](sql-database-geo-replication-overview.md)를 사용하는 경우 필요한 데이터를 복원된 복사본에서 원본 데이터베이스로 복사하는 것이 좋습니다. 원본 데이터베이스를 복원된 데이터베이스로 바꾸는 경우 활성 지역 복제를 다시 구성하고 다시 동기화해야 합니다.

테넌트에서 데이터가 손실되거나 손상되었다고 가정하는 두 번째 패턴에서는 테넌트의 프로덕션 데이터베이스가 이전 시점으로 복원됩니다. 원래 위치에 복원 패턴에서는 데이터베이스를 복원하고 다시 온라인 상태로 전환하는 동안 테넌트가 잠시 동안 오프라인 상태가 됩니다. 원본 데이터베이스는 삭제되지만 훨씬 이전 시점으로 되돌아가야 하는 경우에도 여전히 복원할 수 있습니다. 이 패턴의 변형은 데이터베이스를 삭제하는 대신 데이터베이스 이름을 바꿀 수 있지만, 데이터베이스 이름을 바꾸는 경우 데이터 보안 측면에서 추가적인 이점이 없습니다.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant 응용 프로그램 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 응용 프로그램 소스 코드는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 확인할 수 있습니다. [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)에서 Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계를 확인하세요.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>실수로 데이터를 삭제하는 테넌트 시뮬레이션

이러한 복구 시나리오를 실제로 보여 주려면 테넌트 데이터베이스 중 하나에서 일부 데이터를 *실수로* 삭제해야 합니다. 레코드를 삭제할 수는 있지만 다음 단계에서 참조 무결성 위반으로 차단되지 않도록 데모를 설정해야 합니다. 또한 나중에 *Wingtip SaaS 분석 자습서*에서 사용할 수 있는 몇 가지 티켓 구매 데이터를 추가합니다.

티켓 생성기 스크립트를 실행하고 추가 데이터를 만듭니다. 티켓 생성기는 의도적으로 각 테넌트의 마지막 이벤트에 대한 티켓을 구매하지 않습니다.

1. *PowerShell ISE*에서 \\Learning Modules\\Utilities\\*Demo-TicketGenerator.ps1*을 엽니다.
1. **F5** 키를 눌러 스크립트를 실행하고 고객 및 티켓 판매 데이터를 생성합니다.


### <a name="open-the-events-app-to-review-the-current-events"></a>Events 앱을 열어 현재 이벤트 검토

1. *Events Hub*(http://events.wtp.&lt;user&gt;.trafficmanager.net)를 열고 **Contoso Concert Hall**을 클릭합니다.

   ![Events Hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. 이벤트 목록을 스크롤하고 목록의 마지막 이벤트를 적어 둡니다.

   ![마지막 이벤트](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>데모 시나리오를 실행하여 실수로 마지막 이벤트 삭제

1. *PowerShell ISE*에서 \\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1*을 열고 다음 값을 설정합니다.
   * **$DemoScenario** = **1** - **1**로 설정하면 티켓 판매가 없는 이벤트를 삭제합니다.
1. **F5** 키를 눌러 스크립트를 실행하고 마지막 이벤트를 삭제합니다. 다음과 비슷한 확인 메시지가 표시됩니다.

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Contoso 이벤트 페이지가 열립니다. 아래로 스크롤하여 해당 이벤트가 삭제되었는지 확인합니다. 해당 이벤트가 여전히 목록에 있으면 새로 고침을 클릭하고 삭제되었는지 확인합니다.

   ![마지막 이벤트](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>프로덕션 데이터베이스와 병렬로 테넌트 데이터베이스 복원

이 연습에서는 이벤트를 삭제하기 전 특정 시점의 Contoso Concert Hall 데이터베이스를 복원합니다. 이전 단계에서 이벤트를 삭제한 후에 삭제된 데이터를 복구하고 확인하려고 합니다. 삭제된 레코드가 포함된 프로덕션 데이터베이스를 복원할 필요는 없지만, 다른 비즈니스상의 이유로 이전 데이터에 액세스하려면 이전 데이터베이스를 복구해야 합니다.

 *Restore-TenantInParallel.ps1* 스크립트는 병렬 테넌트 데이터베이스와 병렬 카탈로그 항목을 모두 *ContosoConcertHall\_old*라는 이름으로 만듭니다. 이 복원 패턴은 사소한 데이터 손실로부터의 복구 또는 복구 시나리오에 대한 규정 준수 및 감사에 가장 적합합니다. 또한 [지역에서 복제](sql-database-geo-replication-overview.md)를 사용할 때 권장되는 방법입니다.

1. [사용자가 실수로 데이터를 삭제하는 시뮬레이션](#simulate-a-tenant-accidentally-deleting-data) 섹션을 완료합니다.
1. *PowerShell ISE*에서 \\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_을 엽니다.
1. **$DemoScenario** = **2**를 설정합니다. **2**로 설정하면 *테넌트를 병렬로 복원합니다*.
1. **F5** 키를 눌러 스크립트를 실행합니다.

스크립트는 이전 섹션에서 이벤트를 삭제하기 전 특정 시점의 테넌트 데이터베이스를 병렬 데이터베이스로 복원합니다. 두 번째 데이터베이스를 만들고, 이 데이터베이스에 있는 기존 카탈로그 메타데이터를 제거하며, *ContosoConcertHall\_old* 항목 아래의 카탈로그에 데이터베이스를 추가합니다.

데모 스크립트는 브라우저에서 이벤트 페이지를 엽니다. 참고: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` URL에서 복원된 데이터베이스(*_old*가 이름에 추가되어 있음)의 데이터를 보여 주고 있습니다.

브라우저에 나열된 이벤트를 스크롤하여 이전 섹션에서 삭제한 이벤트가 복원되었는지 확인합니다.

복원된 테넌트를 추가 테넌트로 노출하고, 자체 Events 앱으로 티켓을 검색하면 복원된 데이터에 대한 액세스를 테넌트에 제공하는 방법은 아니지만 복원 패턴을 쉽게 보여 줄 수 있습니다.

실제로 정의된 기간 동안 복원된 이 데이터베이스만 유지합니다. *Remove-RestoredTenant.ps1* 스크립트를 호출하여 완료되면 복원된 테넌트 항목을 삭제할 수 있습니다.

1. **$DemoScenario**를 **4**로 설정하여 *복원된 테넌트 제거* 시나리오를 선택합니다.
1. **F5** **키를 사용하여** **실행합니다**.
1. 이제 카탈로그에서 *ContosoConcertHall\_old* 항목이 삭제됩니다. 계속해서 브라우저에서 이 테넌트의 이벤트 페이지를 닫습니다.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>원래 위치에 테넌트 복원 및 기존 테넌트 데이터베이스 대체

이 연습에서는 이벤트를 삭제하기 전 특정 시점의 Contoso Concert Hall 테넌트를 복원합니다. *Restore-TenantInPlace* 스크립트는 현재 테넌트 데이터베이스를 이전 시점을 가리키는 새 데이터베이스로 복원하고, 원본 데이터베이스를 삭제합니다. 이 복원 패턴은 테넌트에서 수용해야 하는 상당한 데이터 손실이 있을 수 있기 때문에 중요한 데이터 손상으로부터 복구하는 데 가장 적합합니다.

1. PowerShell ISE에서 **Demo-RestoreTenant.ps1** 파일을 엽니다.
1. **$DemoScenario**를 **5**로 설정하여 *원래 위치에 테넌트 복원 시나리오*를 선택합니다.
1. **F5** 키를 사용하여 실행합니다.

스크립트는 이벤트를 삭제하기 5분 전의 테넌트 데이터베이스를 복원합니다. 이렇게 하려면 먼저 Contoso Concert Hall 테넌트를 오프라인으로 전환하여 더 이상 데이터를 업데이트하지 않아야 합니다. 다음으로, 복원 지점에서 복원하여 병렬 데이터베이스를 만들고, 타임스탬프가 포함된 이름을 지정하여 데이터베이스 이름이 기존 테넌트 데이터베이스 이름과 충돌하지 않도록 합니다. 다음으로, 이전 테넌트 데이터베이스를 삭제하고, 복원된 데이터베이스 이름을 원본 데이터베이스 이름으로 바꿉니다. 마지막으로, Contoso Concert Hall이 온라인 상태가 되어 응용 프로그램에서 복원된 데이터베이스에 액세스할 수 있게 합니다.

이벤트를 삭제하기 전 특정 시점의 데이터베이스를 성공적으로 복원했습니다. Events 페이지가 열리므로 마지막 이벤트가 복원되었는지 확인합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]

> * 병렬 데이터베이스에 데이터베이스 복원(병렬)
> * 원래 위치에 데이터베이스 복원

[테넌트 데이터베이스 스키마 관리](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>추가 리소스

* [Wingtip SaaS 응용 프로그램을 빌드하는 또 다른 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
* [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)
* [SQL Database 백업에 대한 자세한 정보](sql-database-automated-backups.md)
