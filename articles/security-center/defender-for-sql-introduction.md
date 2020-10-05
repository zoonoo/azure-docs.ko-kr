---
title: Azure Defender for SQL - 이점 및 특징
description: Azure Defender for SQL의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 328a565dbb22a13c71a7001d43941e7be062dff9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449057"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Azure Defender for SQL 소개

SQL용 Azure Defender에는 Azure Security Center의 [데이터 보안 패키지](../azure-sql/database/advanced-data-security.md)를 확장하여 데이터베이스와 해당 데이터를 어디서나 안전하게 보호하는 두 개의 Azure Defender 요금제가 포함되어 있습니다. 

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|**Azure SQL 데이터베이스 서버용 Azure Defender** - 일반 공급(GA)<br>**머신의 SQL 서버용 Azure Defender** - 미리 보기|
|가격 책정:|**Azure Defender for SQL**을 구성하는 두 요금제는 [가격 책정 페이지](security-center-pricing.md)에 표시된 대로 청구됩니다.|
|보호되는 SQL 버전:|Azure SQL Database <br>Azure SQL Managed Instance<br>Azure Synapse Analytics(이전의 SQL DW)<br>SQL Server(지원되는 모든 버전)|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) US Gov<br>![아니요](./media/icons/no-icon.png) 중국 정부, 기타 정부|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Azure Defender for SQL은 무엇을 보호하나요?

**Azure Defender for SQL**은 두 개의 개별 Azure Defender 요금제로 구성됩니다.

- **Azure SQL 데이터베이스 서버용 Azure Defender**는 다음을 보호합니다.
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **머신의 SQL 서버용 Azure Defender(미리 보기)** 는 하이브리드 환경을 완벽하게 지원하고 Azure, 기타 클라우드 환경 및 온-프레미스 머신에서 호스팅되는 SQL 서버(지원되는 모든 버전)를 보호하도록 Azure 기반 SQL Server에 대한 보호를 확장합니다.


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL의 이점은?

이러한 두 요금제에는 잠재적인 데이터베이스 취약성 식별 및 완화, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다.

- [취약성 평가](../azure-sql/database/sql-vulnerability-assessment.md) - 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정하는 검사 서비스입니다. 평가 검사는 SQL 머신의 보안 상태에 대한 개요와 보안 결과에 대한 세부 정보를 제공합니다.

- [지능형 위협 방지](../azure-sql/database/threat-detection-overview.md) - SQL 서버에서 SQL 삽입, 무차별 암호 대입 공격 및 권한 남용 같은 위협을 지속적으로 모니터링하는 검색 서비스입니다. 이 서비스는 Azure Security Center에서 의심스러운 활동에 대한 세부 정보, 위협을 완화하는 방법에 대한 지침, Azure Sentinel에서 조사를 계속하는 옵션이 포함된 작업 지향 보안 경고를 제공합니다.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Azure Defender for SQL은 어떤 종류의 경고를 제공하나요?

다음과 같은 경우에 보안 경고가 트리거됩니다.

- **잠재적 SQL 삽입 공격** - 애플리케이션이 데이터베이스에서 잘못된 SQL 문을 생성할 때 검색되는 취약성 포함
- **비정상적인 데이터베이스 액세스 및 쿼리 패턴** - 예: 여러 자격 증명을 사용한 로그인 시도 실패 횟수가 비정상적으로 많은 경우(무차별 암호 대입 시도)
- **의심스러운 데이터베이스 작업** - 예: SQL 가져오기 및 내보내기 작업을 위한 내보내기 스토리지 대상 변경

경고에는 경고를 트리거한 인시던트의 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 사항이 포함됩니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for SQL에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- [머신의 SQL 서버용 Azure Defender를 사용하도록 설정하는 방법](defender-for-sql-usage.md)
- [SQL 데이터베이스 서버용 Azure Defender를 사용하도록 설정하는 방법](../azure-sql/database/advanced-data-security.md)
- [SQL용 Azure Defender 경고 목록](alerts-reference.md#alerts-sql-db-and-warehouse)