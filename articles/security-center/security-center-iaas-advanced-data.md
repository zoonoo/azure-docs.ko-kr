---
title: Azure 보안 센터의 IaaS를 위한 고급 데이터 보안 | 마이크로 소프트 문서
description: " Azure 보안 센터에서 IaaS에 대한 고급 데이터 보안을 사용하도록 설정하는 방법을 알아봅니다. "
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
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282732"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 가상 머신의 SQL 서버에 대한 고급 데이터 보안(미리 보기)
Azure 가상 머신의 SQL 서버에 대한 고급 데이터 보안은 고급 SQL 보안 기능을 위한 통합 패키지입니다. 이 미리 보기 기능에는 잠재적인 데이터베이스 취약점을 식별 및 완화하고 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 검색하는 기능이 포함되어 있습니다. 

Azure VM SQL 서버에 대한 이 보안 제품은 [Azure SQL Database 고급 데이터 보안 패키지에](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)사용되는 것과 동일한 기본 기술을 기반으로 합니다.


## <a name="overview"></a>개요

고급 데이터 보안은 취약점 평가 및 고급 위협 보호로 구성된 고급 SQL 보안 기능 집합을 제공합니다.

* [취약성 평가는](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) 잠재적인 데이터베이스 취약점을 검색, 추적 및 해결하는 데 도움이 되는 서비스를 쉽게 구성할 수 있습니다. 보안 상태에 대한 가시성을 제공하며 보안 문제를 해결하고 데이터베이스 강화를 강화하는 단계를 포함합니다.
* [고급 위협 보호는](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) SQL 서버에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지합니다. 데이터베이스를 지속적으로 모니터링하여 의심스러운 활동을 모니터링하고 비정상적인 데이터베이스 액세스 패턴에 대한 작업 지향 보안 경고를 제공합니다. 이러한 경고는 의심스러운 활동 세부 정보와 위협을 조사하고 완화하기 위한 권장 작업을 제공합니다.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM에서 SQL에 대한 고급 데이터 보안 시작

다음 단계는 Azure VM 공개 미리 보기에서 SQL에 대한 고급 데이터 보안을 시작합니다.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM에서 SQL에 대한 고급 데이터 보안 설정

구독/작업 영역 수준에서 가상 컴퓨터에서 SQL 서버에 대한 고급 데이터 보안 을 사용하도록 설정합니다.
 
1. 보안 센터의 사이드바에서 가격 **책정 & 설정** 페이지를 엽니다.

1. Azure VM에서 SQL에 대한 고급 데이터 보안을 사용하도록 설정하려는 구독 또는 작업 영역을 선택합니다.

1. **VM(미리 보기)의 SQL 서버에** 대한 옵션을 사용하도록 전환합니다. 

    (스크린샷을 클릭하여 확장)

    [![Windows 관리 센터에서 볼 수 있는 보안 센터 권장 사항 및 경고](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    선택한 작업 영역 또는 선택한 구독의 기본 작업 영역에 연결된 모든 SQL Server에서 SQL Server에 대한 고급 데이터 보안이 활성화됩니다.

    >[!NOTE]
    > 이 솔루션은 SQL Server를 처음 다시 시작한 후 완전히 활성화됩니다. 

새 작업 영역을 만들려면 [로그 분석 작업 영역 만들기의](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)지침을 따릅니다.

SQL Server의 호스트를 작업 영역에 연결하려면 Windows [컴퓨터 연결의](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)지침을 Azure 모니터에 따릅니다.


## <a name="set-up-email-notification-for-security-alerts"></a>보안 경고에 대한 이메일 알림 설정 

보안 센터 경고가 생성될 때 수신자 목록을 설정하여 이메일 알림을 받을 수 있습니다. 전자 메일에는 모든 관련 세부 정보가 포함된 Azure 보안 센터의 경고에 대한 직접 링크가 포함되어 있습니다. 

1. 보안 **센터** > **가격 & 설정으로** 이동하여 관련 구독을 클릭합니다.

    ![구독 설정](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 설정 메뉴에서 **이메일 알림을**클릭합니다. 
1. 이메일 **주소** 텍스트 상자에 전자 메일 주소를 입력하여 알림을 받습니다. 이메일 주소를 쉼표(,)로 구분하여 두 개 이상의 이메일 주소를 입력할 수 있습니다.  예를 admin1@mycompany.com들어admin2@mycompany.com,admin3@mycompany.com

    ![전자 메일 설정](./media/security-center-advanced-iaas-data/email-settings.png)

1. 전자 **메일 알림** 설정에서 다음 옵션을 설정합니다.
  
    * **심각도가 높은 경고에 대한 이메일 알림 보내기:** 모든 경고에 대한 전자 메일을 보내는 대신 심각도가 높은 경고에 대해서만 보냅니다.
    * **또한 구독 소유자에게 이메일 알림을 보냅니다**: 구독 소유자에게도 알림을 보냅니다.

1. **이메일 알림** 화면 상단에서 **저장을**클릭합니다.

  > [!NOTE]
  > 창을 닫기 전에 **저장을** 클릭해야 하거나 새 **전자 메일 알림** 설정이 저장되지 않습니다.

## <a name="explore-vulnerability-assessment-reports"></a>취약성 평가 보고서 살펴보기

취약성 평가 대시보드는 모든 데이터베이스에서 평가 결과에 대한 개요를 제공합니다. SQL Server 버전에 따라 데이터베이스 의 분포를 볼 수 있으며, 실패와 전달 되는 데이터베이스에 대한 요약 및 위험 분포에 따른 실패한 검사에 대한 전반적인 요약을 볼 수 있습니다.

보안 센터에서 직접 취약점 평가 결과를 볼 수 있습니다.

1. 리소스 보안 위생 하에 보안 센터의 사이드바에서 **데이터 & 저장소를**선택합니다.

1. **VM에서 SQL 데이터베이스에서 권장 취약점을 수정해야 하는 권장 취약점을 선택합니다(미리 보기).** 자세한 내용은 [보안 센터 권장 사항을](security-center-recommendations.md)참조하십시오. 

    [![**VM의 SQL 데이터베이스의 취약점을 수정해야 합니다(미리 보기)** 권장 사항](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    이 권장 사항에 대한 자세한 보기가 나타납니다.

    [![VM의 SQL 데이터베이스의 **취약성에 대한 자세한 보기를 수정해야 합니다(미리 보기)** 권장 사항](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 자세한 내용을 보려면 드릴다운하려면 다음을 수행하십시오.

    * 검색된 리소스(데이터베이스)와 테스트된 보안 검사 목록에 대한 개요를 보려면 관심 있는 서버를 클릭합니다.
    [![SQL 서버별로 그룹화된 취약점](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * 특정 SQL 데이터베이스로 그룹화된 취약점에 대한 개요를 보려면 관심 있는 데이터베이스를 클릭합니다.
    [![SQL 서버별로 그룹화된 취약점](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    각 보기에서 보안 검사는 **심각도**로 정렬됩니다. 특정 보안 검사를 클릭하여 **설명이**있는 세부 정보 창, 이를 **수정하는** 방법 및 **영향** 또는 **벤치마크와**같은 기타 관련 정보를 볼 수 있습니다.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure VM 경고에서 SQL 서버에 대한 고급 위협 보호
경고는 SQL Server에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도에 의해 생성됩니다. 이러한 이벤트는 다음과 같은 경고를 트리거할 수 있습니다.

### <a name="anomalous-access-pattern-alerts-preview"></a>비정상적인 액세스 패턴 경고(미리 보기)

* **비정상적인 위치에서 액세스 :** 이 경고는 SQL 서버에 대한 액세스 패턴이 변경될 때 트리거되며, 여기서 누군가가 비정상적인 지리적 위치에서 SQL 서버에 로그온했습니다. 가능한 원인:
    * 공격자 또는 이전 악의적인 사용자가 SQL Server에 액세스했습니다.
    * 합법적인 사용자가 새 위치에서 SQL Server에 액세스했습니다.
* **잠재적으로 위험한 애플리케이션에서 액세스**: 잠재적으로 위험한 애플리케이션이 데이터베이스에 액세스하는 데 사용되는 경우 이 경고가 트리거됩니다. 가능한 원인:
    * 일반적인 공격 도구를 사용하여 SQL을 침해하려는 공격자입니다.
    * 합법적인 침투 테스트.
* **잘 모르는 보안 주체에서 액세스**: 비정상적인 보안 주체(SQL 사용자)를 사용하여 누군가가 SQL 서버에 로그온한 위치에 SQL 서버에 대한 액세스 패턴에 변경이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
    * 공격자 또는 이전 악의적인 사용자가 SQL Server에 액세스했습니다. 
    * 합법적인 사용자가 새 보안 주체를 통해 SQL Server에 액세스했습니다.
* **무차별 암호 대입 SQL 자격 증명**: 서로 다른 자격 증명을 사용하는 비정상적인 많은 수의 실패한 로그인이 있는 경우 이 경고가 트리거됩니다. 가능한 원인:
    * 무차별 대입으로 SQL을 위반하려는 공격자입니다.
    * 합법적인 침투 테스트.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>잠재적인 SQL 주입 공격(SQL Server 2019에서 지원)

* **SQL 주입에 대한 취약성**: 응용 프로그램이 데이터베이스에서 잘못된 SQL 문을 생성할 때 이 경고가 트리거됩니다. 이 경고는 SQL 삽입 공격에 대한 가능한 취약점을 나타낼 수 있습니다. 가능한 원인:
    * 잘못된 SQL문을 구성하는 애플리케이션 코드의 결함
    * 애플리케이션 코드 또는 저장 프로시저는 SQL 삽입에 악용될 수 있는 잘못된 SQL문을 생성할 때 사용자 입력을 삭제하지 않습니다.
* **잠재적인 SQL 삽입**: 활성 악용이 SQL 삽입에 대한 식별된 애플리케이션 취약성에 대해 발생할 때 이 경고가 트리거됩니다. 즉 공격자가 취약한 애플리케이션 코드 또는 저장 프로시저를 사용하여 악성 SQL문을 삽입하려고 하는 것을 의미합니다.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>안전하지 않은 명령(SQL Server 2019에서 지원)

* **잠재적으로 안전하지 않은 작업:** 이 경고는 권한이 높고 잠재적으로 안전하지 않은 명령이 실행될 때 트리거됩니다. 가능한 원인:
    * 더 나은 보안 자세를 위해 비활성화하는 것을 권장하는 명령이 활성화되어 있습니다.
    * SQL 액세스를 악용하거나 권한을 에스컬레이션하려는 공격자입니다.   


## <a name="explore-and-investigate-security-alerts"></a>보안 경고 탐색 및 조사

데이터 보안 경고는 보안 센터의 경고 페이지, 리소스의 보안 탭 또는 경고 이메일의 직접 링크를 통해 사용할 수 있습니다.

1. 경고를 보려면

    * 보안 센터에서 - 사이드바에서 **보안 경고를** 클릭하고 경고를 선택합니다.
    * 리소스 범위에서 - 관련 리소스 페이지를 열고 사이드바에서 **보안**을 클릭합니다. 

1. 경고는 각 경고에 대한 자세한 수정 단계 및 조사 정보가 포함된 자체 포함되도록 설계되었습니다. 더 넓은 보기를 위해 다른 Azure 보안 센터 및 Azure Sentinel 기능을 사용하여 추가로 조사할 수 있습니다.

    * 추가 조사를 위해 SQL Server의 감사 기능을 활성화합니다. Azure Sentinel 사용자인 경우 Windows 보안 로그 이벤트의 SQL 감사 로그를 Sentinel에 업로드하고 풍부한 조사 환경을 즐길 수 있습니다.
    * 보안 상태를 개선하려면 각 경고에 표시된 호스트 컴퓨터에 대한 보안 센터의 권장 사항을 사용합니다. 이렇게 하면 향후 공격의 위험이 줄어듭니다. 


## <a name="next-steps"></a>다음 단계

관련 자료는 다음 문서를 참조하십시오.

- [권장 사항을 수정하는 방법](security-center-remediate-recommendations.md)