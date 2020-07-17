---
title: 고급 데이터 보안
description: 중요 한 데이터를 검색 및 분류 하 고, 데이터베이스 취약성을 관리 하 고, Azure SQL Database, Azure SQL Managed Instance 또는 Azure Synapse에서 데이터베이스에 대 한 위협을 나타낼 수 있는 비정상적인 활동을 검색 하는 기능에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 53765ee97f0f253db4df4ecca3c1c90d6068fb07
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983997"
---
# <a name="advanced-data-security"></a>고급 데이터 보안
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


광고 (advanced Data Security)는 고급 SQL 보안 기능을 위한 통합 패키지입니다. 광고는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse 분석에 사용할 수 있습니다. 여기에는 중요한 데이터 검색 및 분류, 잠재적인 데이터베이스 취약성 표시 및 완화, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다. 이러한 기능을 설정하고 관리하는 단일 준비 위치를 제공합니다.

## <a name="overview"></a>개요

광고는 데이터 검색 & 분류, SQL 취약점 평가 및 고급 위협 방지를 비롯 한 고급 SQL 보안 기능 집합을 제공 합니다.
- [데이터 검색 & 분류](data-discovery-and-classification-overview.md) 는 데이터베이스에서 중요 한 데이터를 검색, 분류, 레이블 지정 및 보고 하기 위해 Azure SQL Database, azure SQL Managed Instance 및 azure Synapse에 기본 제공 되는 기능을 제공 합니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고, 데이터베이스 내 및 해당 경계 외부의 중요한 데이터에 대한 액세스를 추적하는 데 사용할 수 있습니다.
- [취약성 평가](sql-vulnerability-assessment.md) 는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 간편한 구성 서비스입니다. 보안 상태에 대 한 가시성을 제공 하 고, 보안 문제를 해결 하 고 데이터베이스 fortifications을 개선 하기 위한 실행 가능한 단계를 포함 합니다.
- [Advanced Threat Protection](threat-detection-overview.md)은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 탐지합니다. 데이터베이스에서 의심 스러운 활동을 지속적으로 모니터링 하 고 잠재적인 취약점, Azure SQL 삽입 공격 및 비정상적인 데이터베이스 액세스 패턴에 대 한 즉각적인 보안 경고를 제공 합니다. Advanced Threat Protection 경고는 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

이러한 포함 된 모든 기능을 사용 하려면 고급 데이터 보안을 한 번 사용 하도록 설정 합니다. 한 번의 클릭으로 Azure 또는 SQL Managed Instance의 [서버](logical-servers.md) 에 있는 모든 데이터베이스에 대 한 광고를 사용 하도록 설정할 수 있습니다. ADS 설정을 사용 하거나 관리 하려면 [SQL security manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) 역할 또는 데이터베이스 또는 서버 관리자 역할 중 하나에 속해 있어야 합니다.

광고 가격은 각 보호 된 서버 또는 관리 되는 인스턴스가 하나의 노드로 계산 되는 Azure Security Center 표준 계층에 부합 합니다. 새로 보호되는 리소스는 Security Center 표준 계층의 평가판 자격을 획득합니다. 자세한 내용은 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="getting-started-with-ads"></a>광고 시작

다음 단계에서는 ADS를 시작합니다.

## <a name="1-enable-ads"></a>1. 광고 사용

서버 또는 관리 되는 인스턴스의 **보안** 제목에서 **고급 데이터 보안** 으로 이동 하 여 광고를 사용 하도록 설정 합니다.

> [!NOTE]
> 저장소 계정이 자동으로 만들어지고 **취약성 평가** 검사 결과를 저장 하도록 구성 됩니다. 동일한 리소스 그룹 및 지역에 있는 다른 서버에 대 한 광고를 이미 사용 하도록 설정한 경우 기존 저장소 계정이 사용 됩니다.

![ADS 사용](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> 광고 비용은 노드당 표준 계층 가격 책정에 따라 결정 Azure Security Center 됩니다. 여기서 노드는 전체 서버 또는 관리 되는 인스턴스입니다. 따라서 광고를 사용 하 여 서버 또는 관리 되는 인스턴스의 모든 데이터베이스를 보호 하기 위해 한 번만 지불 하면 됩니다. ADS를 처음에 평가판으로 사용해볼 수 있습니다.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. 데이터 분류, 취약성 추적 및 위협 경고 조사를 시작 합니다.

**데이터 검색 및 분류** 카드를 클릭하여 분류하도록 추천받은 중요한 열을 확인하고 영구 민감도 레이블을 포함한 데이터를 분류합니다. **취약성 평가** 카드를 클릭하여 취약성 검색 및 보고서를 보고 관리하고, 보안 상태를 추적합니다. 보안 경고가 수신 되 면 **Advanced Threat Protection** 카드를 클릭 하 여 경고 세부 정보를 확인 하 고 Azure Security Center 보안 경고 페이지를 통해 Azure 구독의 모든 경고에 대 한 통합 보고서를 확인 합니다.

## <a name="3-manage-ads-settings"></a>3. 광고 설정 관리

광고 설정을 보고 관리 하려면 서버 또는 관리 되는 인스턴스의 **보안** 제목 아래에 있는 **고급 데이터 보안** 으로 이동 합니다. 이 페이지에서는 광고를 사용 하거나 사용 하지 않도록 설정 하 고 전체 서버 또는 관리 되는 인스턴스에 대해 취약성 평가 및 고급 위협 방지 설정을 수정할 수 있습니다.

![서버 설정](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-database"></a>4. 데이터베이스에 대 한 ADS 설정 관리

특정 데이터베이스의 ADS 설정을 재정의하려면 **데이터베이스 수준에서 Advanced Data Security 사용** 확인란을 선택합니다. 이 옵션은 서버 또는 관리 되는 인스턴스의 모든 데이터베이스에 대해 발생 하는 경고 및 결과와 더불어 개별 데이터베이스에 대 한 별도의 Advanced Threat Protection 경고 또는 취약점 평가 결과를 수신 해야 하는 특정 요구 사항이 있는 경우에만 사용 합니다.

이 확인란을 선택 하면이 데이터베이스에 대 한 관련 설정을 구성할 수 있습니다.

![데이터베이스 및 고급 위협 방지 설정](./media/advanced-data-security/database_threat_detection_settings.png)

서버 또는 관리 되는 인스턴스의 고급 데이터 보안 설정에도 ADS 데이터베이스 창에서 연결할 수 있습니다. 기본 ADS 창에서 **설정**을 클릭한 다음, **Advanced Data Security 서버 설정 보기**를 클릭합니다.

![데이터베이스 설정](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>다음 단계

- [데이터 검색 & 분류](data-discovery-and-classification-overview.md) 에 대 한 자세한 정보
- [취약성 평가](sql-vulnerability-assessment.md) 에 대 한 자세한 정보
- [Advanced Threat Protection](threat-detection-configure.md) 에 대 한 자세한 정보
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
