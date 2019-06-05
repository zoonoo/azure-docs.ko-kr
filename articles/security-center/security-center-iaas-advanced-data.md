---
title: Azure Security Center에서 IaaS에 대 한 고급 데이터 보안 | Microsoft Docs
description: " Azure Security Center에서 IaaS에 대 한 고급 데이터 보안을 사용 하는 방법에 알아봅니다. "
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
ms.author: monhaber
ms.openlocfilehash: e7420adfe1608df39ef72124817f1d6dadf07db8
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400153"
---
# <a name="advanced-data-security-for-sql-servers-on-iaas"></a>IaaS의 SQL server에 대 한 고급 데이터 보안
Azure Virtual Machines에서 SQL Server에 대 한 고급 데이터 보안은 SQL의 고급 보안 기능에 대 한 통합된 패키지입니다. 현재 표시 잠재적인 데이터베이스 취약성을 완화 하 고 데이터베이스에 대 한 위협을 나타내는 비정상적인 활동을 탐지에 대 한 기능을 포함 합니다. 

Azure Vm SQL server에 대 한 제공이 보안은에 사용 된 동일한 기본 기술을 기반으로 합니다 [Azure SQL Database 고급 데이터 보안 패키지](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)합니다.


## <a name="overview"></a>개요

고급 데이터 보안 취약성 평가 및 Advanced Threat Protection 구성 된 고급 SQL 보안 기능을 제공 합니다.

* [취약성 평가](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대 한 가시성을 제공 하 고 보안 문제를 해결 하 고 사용자 데이터베이스 요새를 향상 하는 단계를 포함 합니다.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) 를 액세스 하거나 SQL server를 악용 하려는 비정상적이 고 잠재적으로 해로운 시도 나타내는 비정상적인 활동을 감지 합니다. 지속적으로 의심 스러운 활동에 대 한 데이터베이스를 모니터링 하 고 비정상적인 데이터베이스 액세스 패턴에 작업 지향 보안 경고를 제공 합니다. 이러한 경고는 의심 스러운 활동 세부 정보를 제공 하 고 조사 하 고 위협을 완화 하는 작업을 권장 합니다.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure Vm에서 SQL 용 고급 데이터 보안을 사용 하 여 시작

다음 단계를 Azure Vm에서 SQL에 대 한 고급 데이터 보안을 사용 하 여 시작할 수를 가져옵니다.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Azure Vm에서 SQL 용 고급 데이터 보안을 설정 합니다.

**시작 하기 전에**: 분석 되 고 보안 로그를 저장할 Log Analytics 작업 영역을 해야 합니다. 있지 않은, 경우에 설명 된 대로 쉽게 만들 수 있습니다 [Azure portal에서 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)합니다.

1. Log Analytics 작업 영역에 SQL server를 호스팅하는 VM을 연결 합니다. 자세한 내용은 [Azure Monitor에 연결 하는 Windows 컴퓨터](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)합니다.

1. Azure Marketplace로 이동 합니다 [SQL 고급 데이터 보안 솔루션](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity)합니다.
(찾을 수 있습니다 marketplace 검색 옵션을 사용 하 여 다음 이미지에서 참조 하세요.) 합니다 **SQL 고급 데이터 보안이** 페이지가 열립니다.

    ![IaaS에 대 한 고급 데이터 보안](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. **만들기**를 클릭합니다. 작업 영역 등에 표시 됩니다.

    ![고급 데이터 보안 만들기](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. 클릭 하 여 사용 하 여 작업 영역 선택 **만들기**합니다.

   ![작업 영역 선택](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. 다시 시작 합니다 [VM의 SQL server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017)합니다.


## <a name="explore-and-investigate-security-alerts"></a>탐색 하 고 보안 경고를 조사 합니다.

볼 수 있으며 현재 보안 경고를 관리할 수 있습니다.

1. 클릭 **Security Center** > **보안 경고**, 경고를 클릭 합니다.

    ![경고 찾기](./media/security-center-advanced-iaas-data/find-alert.png)

1. **공격 받은 리소스** 열, 공격을 받은 리소스를 클릭 합니다.

1. 아래로 스크롤하여 경고 세부 정보 및 현재 위협을 조사 하 고 향후 위협을 해결에 대 한 작업을 보려면 합니다 **일반 정보** 페이지 및 합니다 **재구성 단계** 섹션을 클릭 합니다  **조사 단계** 링크 합니다.

    ![수정 단계](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. 경고 트리거에와 연결 된 로그를 보려면로 이동 **Log analytics 작업 영역** 다음 단계를 수행 합니다.

     > [!NOTE]
     > 경우 **Log analytics 작업 영역** 왼쪽된 메뉴에 표시를 클릭 하지 **모든 서비스**를 검색 하 고 **Log analytics 작업 영역**합니다.

    1. 열 표시 해야 합니다 **가격 책정 계층** 하 고 **WorkspaceID** 열입니다. (**Log analytics 작업 영역** > **열을 편집할**에 추가 **가격 책정 계층** 하 고 **WorkspaceID**.)

     ![열 편집](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. 경고 로그 된 작업 영역을 클릭 합니다.

    1. 아래는 **일반적인** 메뉴에서 클릭 **로그**

    1. 눈 다음 클릭 **SQLAdvancedThreatProtection** 테이블입니다. 로그에 나열 되어 있습니다.

     ![로그 보기](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>ATP 경고에 대 한 전자 메일 알림 설정 

ASC 경고가 발생할 때 전자 메일 알림의 받을 받는 사람 목록을 설정할 수 있습니다. 전자 메일 링크를 모든 관련 세부 정보를 사용 하 여 Azure Security Center에서 경고를 포함합니다. 

1. 로 이동 **Security Center** > **보안 정책** 행 관련 구독을 클릭 하 고 **설정 편집 >** 합니다.

    ![구독 설정](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. **설정을** 메뉴 **전자 메일 알림**합니다. 
1. 에 **전자 메일 주소** 텍스트 상자에 알림을 수신 하도록 전자 메일 주소를 입력 합니다. 쉼표 (,)를 사용 하 여 전자 메일 주소를 구분 하 여 둘 이상의 전자 메일 주소를 입력할 수 있습니다.  예를 들어 admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![메일 설정](./media/security-center-advanced-iaas-data/email-settings.png)

1. 에 **전자 메일 알림** 설정을 다음 옵션을 설정 합니다.
  
    * **심각도 높은 경고에 대 한 전자 메일 알림을 보낼**: 모든 경고에 대 한 전자 메일을 전송 하는 대신 심각도 높은 경고에 대해서만 보냅니다.
    * **구독 소유자에 게 전자 메일 알림을 보낼 수도**:  너무 구독 소유자에 게 알림을 보냅니다.

1. 위쪽에는 **전자 메일 알림** 화면에서 **저장**합니다.

  > [!NOTE]
  > 클릭 해야 **저장할** 창 또는 새 닫기 전에 **전자 메일 알림** 설정이 저장 되지 것입니다.

## <a name="explore-vulnerability-assessment-reports"></a>취약성 평가 보고서를 탐색 합니다.

취약성 평가 대시보드에 모든 데이터베이스 간에 평가 결과 대 한 개요를 제공합니다. 데이터베이스 및 실패 한 위험의 분포에 따라 검사의 전체 요약을 전달 하는 비교는 실패의 요약 정보와 함께 SQL Server 버전에 따라 데이터베이스의 분포를 볼 수 있습니다.

취약성 평가 결과 및 Log Analytics에서 직접 보고서를 볼 수 있습니다.

1. 고급 데이터 보안 솔루션을 사용 하 여 Log Analytics 작업 영역으로 이동 합니다.
1. 이동할 **솔루션** 선택 합니다 **SQL Vulnerability Assessment** 솔루션입니다.
1. 에 **요약** 창 클릭 **요약 보기** 선택 하 고 프로그램 **SQL 취약성 평가 보고서**합니다.

    ![SQL 평가 보고서](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    보고서 대시보드를 로드합니다. 기간 설정 되어 있는지 확인 하려면 적어도 **지난 7 일 동안** 취약성 평가 검사는 7 일 마다 한 번의 고정 된 일정에 따라 데이터베이스에서 실행 되므로 합니다.

    ![지난 7 일 동안 설정](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. 에 대 한 자세한 드릴 다운 하려면 대시보드 요소 중 하나를 클릭 합니다. 예를 들면 다음과 같습니다.

   1. 취약점으로 인 한 확인을 클릭 합니다 **실패 확인 요약** 테이블을 보려면 Log Analytics이이 검사 결과가 포함 된 모든 데이터베이스에서 섹션. 결과 먼저 나열 됩니다.

   1. 취약성 설명 및 영향, 상태, 관련된 위험 및이 데이터베이스에서 실제 결과 포함 하 여 각 취약점에 대 한 세부 정보를 보려면 통해 차례로 클릭 합니다. 또한이 취약성을 해결 하는 것에 대 한이 검사 및 재구성 정보를 수행 하려면 실행 된 실제 쿼리를 볼 수 있습니다.

    ![작업 영역 선택](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![작업 영역 선택](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. 취약성 평가 결과 데이터를 조각화 및 필요에 따라 데이터를 분석에 Log Analytics 쿼리를 실행할 수 있습니다.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure Vm 경고에 SQL Server에 대 한 위협 보호를 고급
경고는 비정상적이 고 잠재적으로 해로운 시도를 액세스 하거나 SQL Server를 악용 하 여 생성 됩니다. 이러한 이벤트는 다음과 같은 경고를 트리거할 수 있습니다.

### <a name="anomalous-access-pattern-alerts"></a>비정상적인 액세스 패턴 경고

* **비정상적인 위치에서 액세스 합니다.** 비정상적인 지리적 위치에서 누군가가 SQL Server에 로그온한 위치에 SQL Server 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
     * 공격자 나 이전의 악의적인 사용 SQL Server에 액세스 합니다.
     * 합법적인 사용자가 새 위치에서 SQL Server에 액세스 합니다.
* **잠재적으로 위험한 응용 프로그램에서 액세스**: 잠재적으로 위험한 응용 프로그램을 데이터베이스에 액세스 하는 경우 자신의 경고가 트리거됩니다. 가능한 원인:
     * 공격자가 일반적인 공격 도구를 사용 하 여 SQL 위반 하려고 합니다.
     * 합법적인 작업의 침투 테스트 합니다.
* **알 수 없는 보안 주체에서 액세스**: 비정상적인 보안 주체(SQL 사용자)를 사용하여 누군가가 SQL Server에 로그온한 위치에 SQL Server 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
     * 공격자 나 이전의 악의적인 사용 SQL Server에 액세스 합니다. 
     * 합법적인 사용자가 새 보안 주체를 사용 하 여에서 SQL Server에 액세스 합니다.
* **무차별 암호 대입 SQL 자격 증명**: 자격 증명이 다른 로그인의 실패 횟수가 비정상적으로 많으면 이 경고가 트리거됩니다. 가능한 원인:
     * 공격자가 (brute force)를 사용 하 여 SQL 위반 하려고 합니다.
     * 합법적인 작업의 침투 테스트 합니다.

### <a name="potential-sql-injection-attacks-coming"></a>잠재적인 SQL 삽입 공격 (발생)

* **SQL 삽입에 대한 취약성**: 애플리케이션에서 데이터베이스에 잘못된 SQL 문을 생성하면 이 경고가 트리거됩니다. 이 경고는 SQL 삽입 공격에 대한 가능한 취약점을 나타낼 수 있습니다. 가능한 원인:
     * 잘못된 SQL문을 구성하는 애플리케이션 코드의 결함
     * 애플리케이션 코드 또는 저장 프로시저는 SQL 삽입에 악용될 수 있는 잘못된 SQL문을 생성할 때 사용자 입력을 삭제하지 않습니다.
* **잠재적 SQL 삽입**: SQL 삽입에 대해 식별된 애플리케이션 취약성으로 인해 활성 악용이 발생하면 이 경고가 트리거됩니다. 즉 공격자가 취약한 애플리케이션 코드 또는 저장 프로시저를 사용하여 악성 SQL문을 삽입하려고 하는 것을 의미합니다.
