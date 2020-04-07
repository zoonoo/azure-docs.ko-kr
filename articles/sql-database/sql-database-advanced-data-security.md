---
title: 고급 데이터 보안
description: 중요한 데이터 검색 및 분류, 데이터베이스 취약성 관리 및 Azure SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677461"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Azure SQL Database에 대한 고급 데이터 보안

고급 데이터 보안은 고급 SQL 보안 기능의 통합 패키지입니다. 여기에는 중요한 데이터 검색 및 분류, 잠재적인 데이터베이스 취약성 표시 및 완화, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다. 이러한 기능을 설정하고 관리하는 단일 준비 위치를 제공합니다.

## <a name="overview"></a>개요

ADS(고급 데이터 보안)는 데이터 검색 & 분류, 취약성 평가 및 고급 위협 보호를 비롯한 고급 SQL 보안 기능을 제공합니다.

- [데이터 검색 & 분류는](sql-database-data-discovery-and-classification.md) 데이터베이스의 중요한 데이터를 보고하는 & 검색, 분류, 레이블 지정을 위해 Azure SQL Database에 기본 제공된 기능을 제공합니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고, 데이터베이스 내 및 해당 경계 외부의 중요한 데이터에 대한 액세스를 추적하는 데 사용할 수 있습니다.
- [취약성 평가](sql-vulnerability-assessment.md)는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 서비스를 간편하게 구성합니다. 보안 상태에 대한 가시성을 제공하고, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위한 실행 가능한 단계를 포함합니다.
- [Advanced Threat Protection](sql-database-threat-detection-overview.md)은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 탐지합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. Advanced Threat Protection 경고는 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

한 번 SQL ADS를 활성화하면 포함된 기능을 모두 사용할 수 있습니다. 클릭 한 번으로 SQL Database 서버 또는 관리되는 인스턴스에서 모든 데이터베이스에 대해 ADS를 사용하도록 설정할 수 있습니다. ADS 설정을 사용하거나 관리하려면 [SQL 보안 관리자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) 역할, SQL Database 관리자 역할 또는 SQL Server 관리자 역할에 속해야 합니다. 

ADS 가격 책정은 Azure Security Center 표준 계층에 부합합니다. 여기서 보호되는 각 SQL Database 서버 또는 관리되는 인스턴스는 하나의 노드로 계산됩니다. 새로 보호되는 리소스는 Security Center 표준 계층의 평가판 자격을 획득합니다. 자세한 내용은 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="getting-started-with-ads"></a>ADS 시작

다음 단계에서는 ADS를 시작합니다.

## <a name="1-enable-ads"></a>1. 광고 사용

SQL Database 서버 또는 관리되는 인스턴스의 **보안** 제목 에서 **고급 데이터 보안으로** 이동하여 ADS를 사용하도록 설정합니다. 데이터베이스 서버 또는 관리되는 인스턴스에서 모든 데이터베이스에 ADS를 사용하도록 설정하려면 **서버에서 Advanced Data Security 사용**을 클릭합니다.

> [!NOTE]
> 저장소 계정이 자동으로 만들어지고 **취약성 평가** 검색 결과를 저장하도록 구성됩니다. 동일한 리소스 그룹 및 지역의 다른 서버에 대해 ADS를 이미 사용하도록 설정한 경우 기존 저장소 계정이 사용됩니다.

![ADS 사용](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> ADS의 비용은 노드당 Azure Security Center 표준 계층 가격 책정에 부합합니다. 여기서 노드는 전체 SQL Database 서버 또는 관리되는 인스턴스입니다. 따라서 ADS를 사용하여 데이터베이스 서버 또는 관리되는 인스턴스의 모든 데이터베이스를 보호하기 위해 한 번만 결제합니다. ADS를 처음에 평가판으로 사용해볼 수 있습니다.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. 데이터 분류, 취약성 추적 및 위협 경고 조사 시작

**데이터 검색 및 분류** 카드를 클릭하여 분류하도록 추천받은 중요한 열을 확인하고 영구 민감도 레이블을 포함한 데이터를 분류합니다. **취약성 평가** 카드를 클릭하여 취약성 검색 및 보고서를 보고 관리하고, 보안 상태를 추적합니다. 보안 경고가 수신된 경우 **고급 위협 보호** 카드를 클릭하여 경고의 세부 정보를 보고 Azure 보안 센터 보안 경고 페이지를 통해 Azure 구독의 모든 경고에 대한 통합 보고서를 볼 수 있습니다.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. SQL 데이터베이스 서버 또는 관리 인스턴스에서 ADS 설정 관리

ADS 설정을 보고 관리하려면 SQL Database 서버 또는 관리되는 인스턴스의 **보안** 제목에서 **Advanced Data Security**로 이동합니다. 이 페이지에서 ADS를 사용 또는 비활성화하고 전체 SQL Database 서버 또는 관리되는 인스턴스에 대한 취약성 평가 및 고급 위협 보호 설정을 수정할 수 있습니다.

![서버 설정](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. SQL 데이터베이스에 대한 ADS 설정 관리

특정 데이터베이스의 ADS 설정을 재정의하려면 **데이터베이스 수준에서 Advanced Data Security 사용** 확인란을 선택합니다. 데이터베이스 서버 또는 관리되는 인스턴스의 모든 데이터베이스에 대해 수신된 경고 및 결과 대신 또는 추가하여 개별 데이터베이스에 대해 별도의 고급 위협 보호 경고 또는 취약점 평가 결과를 수신해야 하는 특정 요구 사항이 있는 경우에만 이 옵션을 사용합니다.

확인란을 선택하면 이 데이터베이스에 대한 관련 설정을 구성할 수 있습니다.
 
![데이터베이스 및 고급 위협 보호 설정](./media/sql-advanced-protection/database_threat_detection_settings.png) 

데이터베이스 서버 또는 관리되는 인스턴스의 고급 데이터 보안 설정은 ADS 데이터베이스 창에서도 액세스할 수 있습니다. 기본 ADS 창에서 **설정**을 클릭한 다음, **Advanced Data Security 서버 설정 보기**를 클릭합니다. 

![데이터베이스 설정](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>다음 단계 

- [데이터 검색 & 분류에](sql-database-data-discovery-and-classification.md) 대해 자세히 알아보기 
- [취약성 평가에](sql-vulnerability-assessment.md) 대해 자세히 알아보기 
- [고급 위협 보호에](sql-database-threat-detection.md) 대해 자세히 알아보기
- [Azure 보안 센터에](https://docs.microsoft.com/azure/security-center/security-center-intro) 대해 자세히 알아보기
