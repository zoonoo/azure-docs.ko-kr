---
title: Azure Security Center의 IaaS에 대 한 고급 데이터 보안 Microsoft Docs
description: " Azure Security Center에서 IaaS에 대 한 고급 데이터 보안을 사용 하도록 설정 하는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: 068fb9f61b7dcb3948e4f03c284ddfa680522c85
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907016"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines의 SQL server에 대 한 고급 데이터 보안 (미리 보기)
Azure Virtual Machines의 SQL Server에 대 한 고급 데이터 보안은 고급 SQL 보안 기능을 위한 통합 패키지입니다. 이 미리 보기 기능에는 잠재적 데이터베이스 취약성을 식별 하 고 완화 하 고 데이터베이스에 대 한 위협을 나타낼 수 있는 비정상적인 활동을 검색 하기 위한 기능이 포함 되어 있습니다. 

Azure Vm SQL server에 대 한이 보안 제품은 [Azure SQL Database 고급 데이터 보안 패키지](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)에서 사용 되는 것과 동일한 기본 기술을 기반으로 합니다.


## <a name="overview"></a>개요

Advanced data security는 취약성 평가 및 고급 위협 방지로 구성 된 고급 SQL 보안 기능 집합을 제공 합니다.

* [취약성 평가](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대 한 가시성을 제공 하 고, 보안 문제를 해결 하 고 데이터베이스 fortifications을 개선 하는 단계를 포함 합니다.
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) 은 비정상적인 활동을 검색 하 여 SQL server에 액세스 하거나 악용 하려는 비정상적인 시도를 검색 합니다. 데이터베이스에서 의심 스러운 활동을 지속적으로 모니터링 하 고 비정상적인 데이터베이스 액세스 패턴에 대해 액션 지향 보안 경고를 제공 합니다. 이러한 경고는 의심 스러운 활동 세부 정보 및 위협을 조사 하 고 완화 하기 위한 권장 조치를 제공 합니다.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure Vm에서 SQL에 대 한 고급 데이터 보안 시작

다음 단계를 사용 하 여 Azure Vm의 SQL에 대 한 고급 데이터 보안 공개 미리 보기를 시작할 수 있습니다.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Azure Vm에서 SQL에 대 한 고급 데이터 보안 설정

구독/작업 영역 수준에서 Virtual Machines의 SQL Server에 대 한 고급 데이터 보안을 사용 하도록 설정 합니다.
 
1. Security Center의 사이드바에서 **가격 책정 & 설정** 페이지를 엽니다.

1. Azure Vm에서 SQL에 대 한 고급 데이터 보안을 사용 하도록 설정할 구독 또는 작업 영역을 선택 합니다.

1. **VM의 SQL server (미리 보기)** 에 대 한 옵션을 사용으로 설정/해제 합니다. 

    (스크린샷을 확장 하려면 클릭)

    [Windows 관리 센터에 표시 된 것 처럼 Security Center 권장 사항 및 경고를 ![합니다.](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    선택한 작업 영역 또는 선택한 구독의 기본 작업 영역에 연결 된 모든 SQL server에서 SQL Server에 대 한 고급 데이터 보안을 사용할 수 있습니다.

    >[!NOTE]
    > SQL Server를 처음 다시 시작한 후 솔루션이 완전히 활성화 됩니다. 

새 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)의 지침을 따르세요.

SQL Server의 호스트를 작업 영역에 연결 하려면 [Azure Monitor에 Windows 컴퓨터 연결](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)의 지침을 따르세요.


## <a name="set-up-email-notification-for-atp-alerts"></a>ATP 경고에 대 한 전자 메일 알림 설정 

Security Center 경고가 생성 될 때 전자 메일 알림을 받을 받는 사람 목록을 설정할 수 있습니다. 전자 메일에는 관련 된 모든 세부 정보와 함께 Azure Security Center의 경고에 대 한 직접 링크가 포함 됩니다. 

1. **Security Center** > **가격 책정 & 설정** 으로 이동 하 여 관련 구독을 클릭 합니다.

    ![구독 설정](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 설정 메뉴에서 **전자 메일 알림**을 클릭 합니다. 
1. **전자 메일 주소** 텍스트 상자에 알림을 받을 전자 메일 주소를 입력 합니다. 전자 메일 주소를 쉼표 (,)로 구분 하 여 전자 메일 주소를 두 개 이상 입력할 수 있습니다.  예 admin1@mycompany.com들어admin2@mycompany.com,admin3@mycompany.com

    ![메일 설정](./media/security-center-advanced-iaas-data/email-settings.png)

1. **전자 메일 알림** 설정에서 다음 옵션을 설정 합니다.
  
    * **심각도가 높은 경고에 대 한 전자 메일 알림 보내기**: 모든 경고에 대 한 전자 메일을 보내는 대신 심각도가 높은 경고에 대해서만 보냅니다.
    * **또한 구독 소유자에 게 전자 메일 알림 보내기**: 구독 소유자에 게 알림을 보냅니다.

1. **전자 메일 알림** 화면 위쪽에서 **저장**을 클릭 합니다.

  > [!NOTE]
  > 창을 닫기 전에 **저장** 을 클릭 해야 합니다. 그렇지 않으면 새 **전자 메일 알림** 설정이 저장 되지 않습니다.

## <a name="explore-vulnerability-assessment-reports"></a>취약성 평가 보고서 살펴보기

취약성 평가 대시보드는 모든 데이터베이스에서 평가 결과의 개요를 제공 합니다. SQL Server 버전에 따라 데이터베이스 배포를 확인 하 고, 실패 대비 데이터베이스 전달 및 위험 분포에 따른 실패 한 검사의 전체 요약을 확인할 수 있습니다.

Security Center에서 직접 취약성 평가 결과를 볼 수 있습니다.

1. Security Center의 사이드바에 있는 리소스 보안 예방에서 **데이터 & 저장소**를 선택 합니다.

1. **Vm의 SQL database에 대 한 권장 구성 취약성 (미리 보기)** 을 선택 합니다. 자세한 내용은 [Security Center 권장 사항](security-center-recommendations.md)을 참조 하세요. 

    [Vm의 SQL database에 대 한 ![* * 취약성을 재구성 해야 함 (미리 보기) * * 권장 사항](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    이 권장 사항에 대 한 상세 뷰가 나타납니다.

    [Vm의 SQL database에 대 한 * * 취약성에 대 한 자세한 내용은 재구성 해야 함 (미리 보기) * * 권장 사항 ![](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 자세한 내용을 보려면 드릴 다운 합니다.

    * 스캔 한 리소스 (데이터베이스)의 개요와 테스트 된 보안 검사 목록을 보려면 관심 있는 서버를 클릭 합니다.
    [SQL server 별로 그룹화 된 ![취약점](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * 특정 SQL 데이터베이스 별로 그룹화 된 취약점의 개요를 보려면 원하는 데이터베이스를 클릭 합니다.
    [SQL server 별로 그룹화 된 ![취약점](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    각 보기에서 보안 검사는 **심각도**를 기준으로 정렬 됩니다. 특정 보안 검사를 클릭 하 여 세부 정보 창에 **설명** **,이를 수정 하는** 방법 및 **영향** 또는 **벤치 마크**와 같은 기타 관련 정보를 표시 합니다.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure Vm의 SQL server에 대 한 Advanced threat protection 경고
경고는 SQL Server에 대 한 액세스 또는 악용을 위한 비정상이 고 잠재적으로 유해한 시도로 생성 됩니다. 이러한 이벤트는 다음과 같은 경고를 트리거할 수 있습니다.

### <a name="anomalous-access-pattern-alerts-preview"></a>비정상적인 액세스 패턴 경고 (미리 보기)

* **비정상적인 위치에서 액세스:** 이 경고는 다른 사람이 비정상적인 지리적 위치에서 SQL server에 로그온 한 SQL server에 대 한 액세스 패턴이 변경 된 경우에 트리거됩니다. 가능한 원인:
    * 공격자 또는 이전의 악의적인 사용자가 SQL Server에 액세스 했습니다.
    * 합법적인 사용자가 새 위치에서 SQL Server에 액세스 했습니다.
* **잠재적으로 위험한 애플리케이션에서 액세스**: 잠재적으로 위험한 애플리케이션이 데이터베이스에 액세스하는 데 사용되는 경우 이 경고가 트리거됩니다. 가능한 원인:
    * 공격자가 일반적인 공격 도구를 사용 하 여 SQL을 위반 하려고 합니다.
    * 정상적인 침투 테스트를 수행 합니다.
* **잘 모르는 보안 주체에서 액세스**: 비정상적인 보안 주체(SQL 사용자)를 사용하여 누군가가 SQL 서버에 로그온한 위치에 SQL 서버에 대한 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
    * 공격자 또는 이전의 악의적인 사용자가 SQL Server에 액세스 했습니다. 
    * 합법적인 사용자가 새 보안 주체를 사용 하 여 SQL Server에 액세스 했습니다.
* **무차별 암호 대입 SQL 자격 증명**: 서로 다른 자격 증명을 사용하는 비정상적인 많은 수의 실패한 로그인이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
    * 공격자가 무차별 암호 대입을 사용 하 여 SQL을 위반 하려고 합니다.
    * 정상적인 침투 테스트를 수행 합니다.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>잠재적 SQL 삽입 공격 (SQL Server 2019에서 지원)

* **SQL 삽입에 대 한 취약점**:이 경고는 응용 프로그램이 데이터베이스에서 잘못 된 SQL 문을 생성할 때 트리거됩니다. 이 경고는 SQL 삽입 공격에 대한 가능한 취약점을 나타낼 수 있습니다. 가능한 원인:
    * 잘못된 SQL문을 구성하는 애플리케이션 코드의 결함
    * 애플리케이션 코드 또는 저장 프로시저는 SQL 삽입에 악용될 수 있는 잘못된 SQL문을 생성할 때 사용자 입력을 삭제하지 않습니다.
* **잠재적인 SQL 삽입**: 활성 악용이 SQL 삽입에 대한 식별된 애플리케이션 취약성에 대해 발생할 때 이 경고가 트리거됩니다. 즉 공격자가 취약한 애플리케이션 코드 또는 저장 프로시저를 사용하여 악성 SQL문을 삽입하려고 하는 것을 의미합니다.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Unsafe 명령 (SQL Server 2019에서 지원)

* **잠재적으로 안전 하지 않은 작업**:이 경고는 높은 권한 및 안전 하지 않은 명령이 실행 될 때 트리거됩니다. 가능한 원인:
    * 더 나은 보안 상태를 위해 사용 하지 않도록 설정 하는 것이 좋습니다.
    * 공격자가 SQL 액세스를 악용 하거나 권한을 에스컬레이션 하려고 합니다.   


## <a name="explore-and-investigate-security-alerts"></a>보안 경고 탐색 및 조사

데이터 보안 경고는 Security Center의 경고 페이지, 리소스의 보안 탭 또는 경고 전자 메일의 직접 링크를 통해 사용할 수 있습니다.

1. 경고를 보려면 다음을 수행 합니다.

    * Security Center에서 사이드바의 **보안 경고** 를 클릭 하 고 경고를 선택 합니다.
    * 리소스 범위-관련 리소스 페이지를 열고 사이드바에서 **보안**을 클릭 합니다. 

1. 경고는 각각에 대 한 자세한 수정 단계와 조사 정보를 포함 하 여 자체 포함 되도록 설계 되었습니다. 더 광범위 한 보기에 다른 Azure Security Center 및 Azure 센티널 기능을 사용 하 여 추가로 조사할 수 있습니다.

    * 추가 조사를 위해 SQL Server의 감사 기능을 사용 하도록 설정 합니다. Azure 센티널 사용자 인 경우 Windows 보안 로그 이벤트에서 센티널로 SQL 감사 로그를 업로드 하 고 풍부한 조사 환경을 즐길 수 있습니다.
    * 보안 상태를 향상 시키려면 각 경고에 표시 된 호스트 컴퓨터에 대 한 Security Center 권장 사항을 사용 합니다. 이렇게 하면 향후 공격의 위험을 줄일 수 있습니다. 


## <a name="next-steps"></a>다음 단계

관련 자료는 다음 문서를 참조 하세요.

- [권장 사항을 수정 하는 방법](security-center-remediate-recommendations.md)