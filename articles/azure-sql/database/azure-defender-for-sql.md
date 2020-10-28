---
title: SQL용 Azure Defender
description: 데이터베이스 취약성을 관리 하 고 Azure SQL Database, Azure SQL Managed Instance 또는 Azure Synapse의 데이터베이스에 대 한 위협을 나타낼 수 있는 비정상적인 활동을 검색 하는 기능에 대해 알아봅니다.
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
ms.date: 09/21/2020
ms.openlocfilehash: d147303df43c4f86843df518c71316e6a97b6671
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678072"
---
# <a name="azure-defender-for-sql"></a>SQL용 Azure Defender
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender for SQL은 고급 SQL 보안 기능의 통합 패키지입니다. Azure Defender는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse 분석에 사용할 수 있습니다. 여기에는 중요한 데이터 검색 및 분류, 잠재적인 데이터베이스 취약성 표시 및 완화, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다. 이러한 기능을 설정하고 관리하는 단일 준비 위치를 제공합니다.

## <a name="overview"></a>개요

Azure Defender는 SQL 취약성 평가 및 고급 위협 방지를 비롯 한 고급 SQL 보안 기능 집합을 제공 합니다.
- [취약성 평가](sql-vulnerability-assessment.md) 는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정할 수 있는 간편한 구성 서비스입니다. 보안 상태에 대 한 가시성을 제공 하 고, 보안 문제를 해결 하 고 데이터베이스 fortifications을 개선 하기 위한 실행 가능한 단계를 포함 합니다.
- [Advanced Threat Protection](threat-detection-overview.md)은 비정상적이며 잠재적으로 유해할 수 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 탐지합니다. 데이터베이스에서 의심 스러운 활동을 지속적으로 모니터링 하 고 잠재적인 취약점, Azure SQL 삽입 공격 및 비정상적인 데이터베이스 액세스 패턴에 대 한 즉각적인 보안 경고를 제공 합니다. Advanced Threat Protection 경고는 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

포함된 모든 기능을 사용하도록 설정하려면 SQL용 Azure Defender를 한 번 사용하도록 설정합니다. 한 번의 클릭으로 Azure 또는 SQL Managed Instance의 [서버](logical-servers.md) 에 있는 모든 데이터베이스에 대해 azure Defender를 사용 하도록 설정할 수 있습니다. Azure Defender 설정을 사용 하도록 설정 하거나 관리 하려면 [SQL security manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할 또는 데이터베이스 또는 서버 관리자 역할 중 하나에 속해 있어야 합니다.

SQL 가격에 대 한 Azure Defender에 대 한 자세한 내용은 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조 하세요.

## <a name="getting-started-with-azure-defender"></a>Azure Defender 시작

다음 단계를 수행 하 여 Azure Defender를 시작 합니다.

## <a name="enable-azure-defender"></a>Azure Defender 사용

Azure Defender는 [Azure Portal](https://portal.azure.com)를 통해 액세스할 수 있습니다. 서버 또는 관리 되는 인스턴스의 **보안** 제목 아래에 있는 **security center** 로 이동 하 여 Azure Defender를 사용 하도록 설정 합니다.

> [!NOTE]
> 저장소 계정이 자동으로 만들어지고 **취약성 평가** 검사 결과를 저장 하도록 구성 됩니다. 동일한 리소스 그룹 및 지역에 있는 다른 서버에 대해 Azure Defender를 이미 사용 하도록 설정한 경우 기존 저장소 계정이 사용 됩니다.
>
> Azure Defender 비용은 노드당 표준 계층 가격 책정에 따라 결정 Azure Security Center 됩니다. 여기서 노드는 전체 서버 또는 관리 되는 인스턴스입니다. 따라서 Azure Defender를 사용 하 여 서버 또는 관리 되는 인스턴스의 모든 데이터베이스를 보호 하기 위해 한 번만 지불 하면 됩니다. 처음에는 무료 평가판을 사용 하 여 Azure Defender를 사용해 볼 수 있습니다.

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>취약점 추적 및 위협 경고 조사 시작

**취약성 평가** 카드를 클릭하여 취약성 검색 및 보고서를 보고 관리하고, 보안 상태를 추적합니다. 보안 경고가 수신 되 면 **Advanced Threat Protection** 카드를 클릭 하 여 경고 세부 정보를 확인 하 고 Azure Security Center 보안 경고 페이지를 통해 Azure 구독의 모든 경고에 대 한 통합 보고서를 확인 합니다.

## <a name="manage-azure-defender-settings"></a>Azure Defender 설정 관리

Azure Defender 설정을 보고 관리 하려면 서버 또는 관리 되는 인스턴스의 **보안** 제목 아래에 있는 **security center** 로 이동 합니다. 이 페이지에서 Azure Defender를 사용 하거나 사용 하지 않도록 설정 하 고 전체 서버 또는 관리 되는 인스턴스에 대 한 취약성 평가 및 고급 위협 방지 설정을 수정할 수 있습니다.

## <a name="manage-azure-defender-settings-for-a-database"></a>데이터베이스에 대 한 Azure Defender 설정 관리

특정 데이터베이스에 대 한 Azure Defender 설정을 재정의 하려면 **데이터베이스 수준에서 azure defender FOR SQL 사용** 확인란을 선택 합니다. 이 옵션은 서버 또는 관리 되는 인스턴스의 모든 데이터베이스에 대해 발생 하는 경고 및 결과와 더불어 개별 데이터베이스에 대 한 별도의 Advanced Threat Protection 경고 또는 취약점 평가 결과를 수신 해야 하는 특정 요구 사항이 있는 경우에만 사용 합니다.

이 확인란을 선택 하면이 데이터베이스에 대 한 관련 설정을 구성할 수 있습니다.

서버 또는 관리 되는 인스턴스에 대 한 azure Defender for SQL 설정은 Azure Defender 데이터베이스 창에서 연결할 수도 있습니다. 기본 Azure Defender 창에서 **설정** 을 클릭 한 다음 **SQL Server 용 Azure defender 설정 보기** 를 클릭 합니다.

## <a name="next-steps"></a>다음 단계

- [취약성 평가](sql-vulnerability-assessment.md) 에 대 한 자세한 정보
- [Advanced Threat Protection](threat-detection-configure.md) 에 대 한 자세한 정보
- [Azure Security Center](../../security-center/security-center-introduction.md)에 대한 자세한 정보