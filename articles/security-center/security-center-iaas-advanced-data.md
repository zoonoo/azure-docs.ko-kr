---
title: Azure Security Center의 IaaS에 대 한 고급 데이터 보안 Microsoft Docs
description: " Azure Security Center에서 IaaS에 대 한 고급 데이터 보안을 사용 하도록 설정 하는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0b83575baa2221f0b502abbf919654492c7ab6cf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295749"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Azure Virtual Machines의 SQL server에 대 한 고급 데이터 보안 (공개 미리 보기)
Azure Virtual Machines의 SQL Server에 대 한 고급 데이터 보안은 고급 SQL 보안 기능을 위한 통합 패키지입니다. 현재 (공개 미리 보기)에는 잠재적인 데이터베이스 취약성을 표시 하 고 완화 하 고 데이터베이스에 위협을 나타낼 수 있는 비정상적인 활동을 검색 하기 위한 기능이 포함 되어 있습니다. 

Azure Vm SQL server에 대 한이 보안 제품은 [Azure SQL Database 고급 데이터 보안 패키지](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)에서 사용 되는 것과 동일한 기본 기술을 기반으로 합니다.


## <a name="overview"></a>개요

Advanced data security는 취약성 평가 및 고급 위협 방지로 구성 된 고급 SQL 보안 기능 집합을 제공 합니다.

* [취약성 평가](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대 한 가시성을 제공 하 고, 보안 문제를 해결 하 고 데이터베이스 fortifications을 개선 하는 단계를 포함 합니다.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) 은 비정상적인 활동을 검색 하 여 SQL server에 액세스 하거나 악용 하려는 비정상적인 시도를 검색 합니다. 데이터베이스에서 의심 스러운 활동을 지속적으로 모니터링 하 고 비정상적인 데이터베이스 액세스 패턴에 대해 액션 지향 보안 경고를 제공 합니다. 이러한 경고는 의심 스러운 활동 세부 정보 및 위협을 조사 하 고 완화 하기 위한 권장 조치를 제공 합니다.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure Vm에서 SQL에 대 한 고급 데이터 보안 시작

다음 단계를 사용 하 여 Azure Vm의 SQL에 대 한 고급 데이터 보안 공개 미리 보기를 시작할 수 있습니다.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Azure Vm에서 SQL에 대 한 고급 데이터 보안 설정

**시작 하기 전에**: 분석 되는 보안 로그를 저장 하려면 Log Analytics 작업 영역이 필요 합니다. 없는 경우 [Azure Portal에서 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)에 설명 된 대로 쉽게 만들 수 있습니다.

1. SQL server를 호스트 하는 VM을 Log Analytics 작업 영역에 연결 합니다. 자세한 내용은 [Azure Monitor에 Windows 컴퓨터 연결](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)을 참조 하세요.

1. Azure Marketplace에서 [SQL 고급 데이터 보안 솔루션](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity)으로 이동 합니다.
(다음 이미지에 나와 있는 것 처럼 마켓플레이스 검색 옵션을 사용 하 여 찾을 수 있습니다.) **SQL 고급 데이터 보안** 페이지가 열립니다.

    ![IaaS에 대 한 고급 데이터 보안](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. **만들기**를 클릭합니다. 작업 영역 등에이 표시 됩니다.

    ![고급 데이터 보안 만들기](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. 사용할 작업 영역을 선택 하 고 **만들기**를 클릭 합니다.

   ![작업 영역 선택](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. [VM의 SQL server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017)를 다시 시작 합니다.


## <a name="explore-and-investigate-security-alerts"></a>보안 경고 탐색 및 조사

현재 보안 경고를 보고 관리할 수 있습니다.

1. **Security Center** > **보안 경고**를 클릭 하 고 경고를 클릭 합니다.

    ![경고 찾기](./media/security-center-advanced-iaas-data/find-alert.png)

1. 공격을 받은 **리소스** 열에서 공격 받은 리소스를 클릭 합니다.

1. 현재 위협을 조사 하 고 향후 위협을 해결 하기 위한 경고 세부 정보와 작업을 보려면 **일반 정보** 페이지로 스크롤하고 **수정 단계** 섹션에서 **조사 단계** 링크를 클릭 합니다.

    ![수정 단계](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. 경고와 연결 된 로그를 보려면 **Log analytics 작업 영역** 으로 이동 하 고 다음 단계를 수행 합니다.

     > [!NOTE]
     > **Log analytics 작업 영역이** 왼쪽 메뉴에 표시 되지 않으면 **모든 서비스**를 클릭 하 고 **log analytics 작업 영역**을 검색 합니다.

    1. 열이 **가격 책정 계층** 및 **WorkspaceID** 열을 표시 해야 합니다. (**Log analytics 작업 영역** > **열 편집**, **가격 책정 계층** 및 **WorkspaceID**추가)

     ![열 편집](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. 경고 로그가 있는 작업 영역을 클릭 합니다.

    1. **일반** 메뉴에서 **로그** 를 클릭 합니다.

    1. **SQLAdvancedThreatProtection** 표 옆의 눈동자를 클릭 합니다. 로그가 나열 됩니다.

     ![로그 보기](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>ATP 경고에 대 한 전자 메일 알림 설정 

ASC 경고가 생성 될 때 전자 메일 알림을 받을 받는 사람 목록을 설정할 수 있습니다. 전자 메일에는 관련 된 모든 세부 정보와 함께 Azure Security Center의 경고에 대 한 직접 링크가 포함 됩니다. 

1. **Security Center** > **가격 & 설정** 으로 이동 하 여 관련 구독을 클릭 합니다.

    ![구독 설정](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. **설정** 메뉴에서 **전자 메일 알림**을 클릭 합니다. 
1. **전자 메일 주소** 텍스트 상자에 알림을 받을 전자 메일 주소를 입력 합니다. 전자 메일 주소를 쉼표 (,)로 구분 하 여 전자 메일 주소를 두 개 이상 입력할 수 있습니다.  예: admin1@mycompany.com,admin2@mycompany.comadmin3@mycompany.com

      ![메일 설정](./media/security-center-advanced-iaas-data/email-settings.png)

1. **전자 메일 알림** 설정에서 다음 옵션을 설정 합니다.
  
    * **심각도가 높은 경고에 대 한 전자 메일 알림 보내기**: 모든 경고에 대 한 전자 메일을 보내는 대신 심각도가 높은 경고에 대해서만 보냅니다.
    * **또한 구독 소유자에 게 전자 메일 알림을 보냅니다**.  구독 소유자에 게 알림을 보냅니다.

1. **전자 메일 알림** 화면 위쪽에서 **저장**을 클릭 합니다.

  > [!NOTE]
  > 창을 닫기 전에 **저장** 을 클릭 해야 합니다. 그렇지 않으면 새 **전자 메일 알림** 설정이 저장 되지 않습니다.

## <a name="explore-vulnerability-assessment-reports"></a>취약성 평가 보고서 살펴보기

취약성 평가 대시보드는 모든 데이터베이스에서 평가 결과의 개요를 제공 합니다. SQL Server 버전에 따라 데이터베이스 배포를 확인 하 고, 실패 대비 데이터베이스 전달 및 위험 분포에 따른 실패 한 검사의 전체 요약을 확인할 수 있습니다.

Log Analytics에서 직접 취약점 평가 결과 및 보고서를 볼 수 있습니다.

1. 고급 데이터 보안 솔루션을 사용 하 여 Log Analytics 작업 영역으로 이동 합니다.
1. **솔루션** 으로 이동 하 여 **SQL 취약성 평가** 솔루션을 선택 합니다.
1. **요약** 창에서 **요약 보기** 를 클릭 하 고 **SQL 취약성 평가 보고서**를 선택 합니다.

    ![SQL 평가 보고서](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    보고서 대시보드가 로드 됩니다. 시간 기간이 7 일에 한 번씩 고정 된 일정에  따라 데이터베이스에서 실행 되 고 있는지 확인 합니다.

    ![최근 7 일 설정](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. 세부 정보를 드릴 다운 하려면 대시보드 요소 중 하나를 클릭 합니다. 예:

   1. **실패 한 검사 요약** 섹션에서 취약성 확인을 클릭 하 여 모든 데이터베이스에서이 검사 결과를 포함 하는 Log Analytics 테이블을 표시 합니다. 결과가 있는 항목이 먼저 나열 됩니다.

   1. 그런 다음이 데이터베이스의 취약점 설명 및 영향, 상태, 관련 된 위험 및 실제 결과를 포함 하 여 각 취약점에 대 한 세부 정보를 보려면 클릭 합니다. 이 확인을 수행 하기 위해 실행 된 실제 쿼리와이 취약성을 해결 하기 위한 재구성 정보를 확인할 수도 있습니다.

    ![작업 영역 선택](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![작업 영역 선택](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. 취약성 평가 결과 데이터에 대 한 모든 Log Analytics 쿼리를 실행 하 여 필요에 따라 데이터를 조각화 및 분석 할 수 있습니다.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure Vm의 SQL Server에 대 한 Advanced Threat Protection 경고
경고는 SQL Server에 대 한 액세스 또는 악용을 위한 비정상이 고 잠재적으로 유해한 시도로 생성 됩니다. 이러한 이벤트는 다음과 같은 경고를 트리거할 수 있습니다.

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>비정상적인 액세스 패턴 경고 (공개 미리 보기에서 지원 됨)

* **비정상적인 위치에서 액세스:** 비정상적인 지리적 위치에서 누군가가 SQL Server에 로그온한 위치에 SQL Server 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
     * 공격자 또는 이전의 악의적인 사용자가 SQL Server에 액세스 했습니다.
     * 합법적인 사용자가 새 위치에서 SQL Server에 액세스 했습니다.
* **잠재적으로 위험한 응용 프로그램에서 액세스**: 잠재적으로 위험한 응용 프로그램이 데이터베이스에 액세스 하는 데 사용 되는 경우 경고가 트리거됩니다. 가능한 원인:
     * 공격자가 일반적인 공격 도구를 사용 하 여 SQL을 위반 하려고 합니다.
     * 정상적인 침투 테스트를 수행 합니다.
* **알 수 없는 보안 주체에서 액세스**: 비정상적인 보안 주체(SQL 사용자)를 사용하여 누군가가 SQL Server에 로그온한 위치에 SQL Server 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
     * 공격자 또는 이전의 악의적인 사용자가 SQL Server에 액세스 했습니다. 
     * 합법적인 사용자가 새 보안 주체를 사용 하 여 SQL Server에 액세스 했습니다.
* **무차별 암호 대입 SQL 자격 증명**: 자격 증명이 다른 로그인의 실패 횟수가 비정상적으로 많으면 이 경고가 트리거됩니다. 가능한 원인:
     * 공격자가 무차별 암호 대입을 사용 하 여 SQL을 위반 하려고 합니다.
     * 정상적인 침투 테스트를 수행 합니다.

### <a name="potential-sql-injection-attacks-coming"></a>잠재적 SQL 삽입 공격 (출시)

* **SQL 삽입에 대한 취약성**: 애플리케이션에서 데이터베이스에 잘못된 SQL 문을 생성하면 이 경고가 트리거됩니다. 이 경고는 SQL 삽입 공격에 대한 가능한 취약점을 나타낼 수 있습니다. 가능한 원인:
     * 잘못된 SQL문을 구성하는 애플리케이션 코드의 결함
     * 애플리케이션 코드 또는 저장 프로시저는 SQL 삽입에 악용될 수 있는 잘못된 SQL문을 생성할 때 사용자 입력을 삭제하지 않습니다.
* **잠재적 SQL 삽입**: SQL 삽입에 대해 식별된 애플리케이션 취약성으로 인해 활성 악용이 발생하면 이 경고가 트리거됩니다. 즉 공격자가 취약한 애플리케이션 코드 또는 저장 프로시저를 사용하여 악성 SQL문을 삽입하려고 하는 것을 의미합니다.
