---
title: 고급 데이터 보안 - Azure SQL Database | Microsoft Docs
description: 중요한 데이터 검색 및 분류, 데이터베이스 취약성 관리 및 Azure SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 388a1a2ff70f4d00e66511811bffdb76806ed713
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735216"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Azure SQL Database에 대한 고급 데이터 보안

고급 데이터 보안은 고급 SQL 보안 기능의 통합 패키지입니다. 여기에는 중요한 데이터 검색 및 분류, 잠재적인 데이터베이스 취약성 표시 및 완화, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다. 이러한 기능을 설정하고 관리하는 단일 준비 위치를 제공합니다.

## <a name="overview"></a>개요

SQL ADS(Advanced Data Security)는 데이터 검색 및 분류, 취약성 평가 및 위협 탐지를 포함하여 고급 SQL 보안 기능 세트를 제공합니다.

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)(현재 미리 보기)는 데이터베이스에 있는 중요한 데이터의 검색, 분류, 레이블 지정 및 보호를 위해 Azure SQL Database에 기본 제공된 기능을 제공합니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고, 데이터베이스 내 및 해당 경계 외부의 중요한 데이터에 대한 액세스를 추적하는 데 사용할 수 있습니다.
- [취약성 평가](sql-vulnerability-assessment.md)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대한 가시성을 제공하고, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위한 실행 가능한 단계를 포함합니다.
- [위협 탐지](sql-database-threat-detection-overview.md)는 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. 위협 탐지 경고는 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

한 번 SQL ADS를 활성화하면 포함된 기능을 모두 사용할 수 있습니다. 클릭 한 번으로 SQL Database 서버 또는 관리되는 인스턴스에서 모든 데이터베이스에 대해 ADS를 사용하도록 설정할 수 있습니다. ADS 설정을 사용하거나 관리하려면 [SQL 보안 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) 역할, SQL Database 관리자 역할 또는 SQL Server 관리자 역할에 속해야 합니다. 

ADS 가격 책정은 Azure Security Center 표준 계층에 부합합니다. 여기서 보호되는 각 SQL Database 서버 또는 관리되는 인스턴스는 하나의 노드로 계산됩니다. 새로 보호되는 리소스는 Security Center 표준 계층의 평가판 자격을 획득합니다. 자세한 내용은 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="getting-started-with-ads"></a>ADS 시작

다음 단계에서는 ADS를 시작합니다.

## <a name="1-enable-ads"></a>1. ADS 사용

SQL Database 서버 또는 관리되는 인스턴스의 **보안** 제목에서 **Advanced Data Security**로 이동하여 ADS를 사용하도록 설정합니다. 데이터베이스 서버 또는 관리되는 인스턴스에서 모든 데이터베이스에 ADS를 사용하도록 설정하려면 **서버에서 Advanced Data Security 사용**을 클릭합니다.

![ADS 사용](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> ADS의 비용은 노드당 Azure Security Center 표준 계층 가격 책정에 부합합니다. 여기서 노드는 전체 SQL Database 서버 또는 관리되는 인스턴스입니다. 따라서 ADS를 사용하여 데이터베이스 서버 또는 관리되는 인스턴스의 모든 데이터베이스를 보호하기 위해 한 번만 결제합니다. ADS를 처음에 평가판으로 사용해볼 수 있습니다.

## <a name="2-configure-vulnerability-assessment"></a>2. 취약성 평가 구성

취약성 평가를 사용하기 시작하려면 검사 결과를 저장하는 스토리지 계정을 구성해야 합니다. 이렇게 하려면 취약성 평가 카드를 클릭합니다.

![VA 구성](./media/sql-advanced-protection/configure_va.png) 

검사 결과를 저장할 저장소 계정을 선택하거나 만듭니다. 또한 일 주일에 한 번씩 자동 검사를 실행하는 취약성 평가를 구성하도록 정기 되풀이 검사를 설정할 수 있습니다. 사용자가 제공한 이메일 주소로 검색 결과 요약이 전송됩니다.

![VA 설정](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. 데이터를 분류하고, 취약점을 추적하고, 위협 경고를 조사하기 시작

**데이터 검색 및 분류** 카드를 클릭하여 분류하도록 추천받은 중요한 열을 확인하고 영구 민감도 레이블을 포함한 데이터를 분류합니다. **취약성 평가** 카드를 클릭하여 취약성 검색 및 보고서를 보고 관리하고, 보안 상태를 추적합니다. 보안 경고를 받은 경우 **위협 검색** 카드를 클릭하여 Azure Security Center 보안 경고 페이지를 통해 경고의 세부 정보를 보고 Azure 구독의 모든 경고에 대한 통합 보고서를 확인합니다.

## <a name="4-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>4. SQL Database 서버 또는 관리되는 인스턴스에서 ADS 설정 관리

ADS 설정을 보고 관리하려면 SQL Database 서버 또는 관리되는 인스턴스의 **보안** 제목에서 **Advanced Data Security**로 이동합니다. 이 페이지에서 ADS를 사용하거나 사용하지 않도록 설정하고, 전체 SQL Database 서버 또는 관리되는 인스턴스의 위협 탐지 설정을 수정할 수 있습니다.

![서버 설정](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-ads-settings-for-a-sql-database"></a>5. SQL 데이터베이스의 ADS 설정 관리

특정 데이터베이스의 ADS 설정을 재정의하려면 **데이터베이스 수준에서 Advanced Data Security 사용** 확인란을 선택합니다. 데이터베이스 서버 또는 관리되는 인스턴스의 모든 데이터베이스에 수신된 경고 대신 또는 이외에도 개별 데이터베이스에 대한 별도 위협 탐지 경고를 받기 위한 특정 요구 사항이 있는 경우에만 이 옵션을 사용합니다. 

이 확인란을 선택하면 **이 데이터베이스에 대한 위협 검색 설정**을 클릭한 다음, 이 데이터베이스에 대한 관련 설정을 구성합니다.

![데이터베이스 및 위협 검색 설정](./media/sql-advanced-protection/database_threat_detection_settings.png) 

데이터베이스 서버 또는 관리되는 인스턴스의 고급 데이터 보안 설정은 ADS 데이터베이스 창에서도 액세스할 수 있습니다. 기본 ADS 창에서 **설정**을 클릭한 다음, **Advanced Data Security 서버 설정 보기**를 클릭합니다. 

![데이터베이스 설정](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>다음 단계 

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md)에 대해 자세히 알아보기 
- [취약성 평가](sql-vulnerability-assessment.md)에 대해 자세히 알아보기 
- [위협 탐지](sql-database-threat-detection.md)에 대해 자세히 알아보기
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대해 자세히 알아보기
