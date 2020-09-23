---
title: SQL 용 Azure Defender-혜택 및 기능
description: Azure Defender for SQL의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: e76fd33ccca73394c4ee48b3737db8b93a990659
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940301"
---
# <a name="introduction-to-azure-defender-for-sql"></a>SQL 용 Azure Defender 소개

SQL 용 azure Defender에는 데이터베이스와 해당 데이터의 위치에 관계 없이 데이터를 보호 하기 위해 Azure Security Center의 [데이터 보안 패키지](../azure-sql/database/advanced-data-security.md) 를 확장 하는 두 개의 azure defender 요금제가 포함 되어 있습니다. 

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|**AZURE SQL database 서버용 Azure Defender** -일반적으로 사용 가능 (GA)<br>**컴퓨터의 SQL server에 대 한 Azure Defender** -미리 보기|
|결정|**Azure Defender FOR SQL** 을 구성 하는 두 요금제는 [가격 책정 페이지](security-center-pricing.md) 에 표시 된 대로 청구 됩니다.|
|보호 된 SQL 버전:|Azure SQL Database <br>Azure SQL Managed Instance<br>Azure Synapse Analytics(이전의 SQL DW)<br>SQL Server (지원 되는 모든 버전)|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) US Gov<br>![아니요](./media/icons/no-icon.png) 중국 .Gov, 기타 .Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Azure Defender for SQL은 무엇을 보호 하나요?

**SQL 용 Azure defender는** 두 개의 개별 azure defender 계획으로 구성 됩니다.

- Azure **SQL database 서버용 Azure Defender는 다음을 보호 합니다** .
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **컴퓨터의 sql server에 대 한 Azure Defender (미리 보기)** 는 하이브리드 환경을 완벽 하 게 지원 하 고 azure, 다른 클라우드 환경 및 온-프레미스 컴퓨터에서 호스트 되는 SQL server (지원 되는 모든 버전)를 보호 하기 위해 AZURE 기본 sql server에 대 한 보호를 확장 합니다.


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Azure Defender for SQL의 이점은 무엇 인가요?

이러한 두 계획에는 잠재적인 데이터베이스 취약성을 식별 하 고 완화 하 고 데이터베이스에 대 한 위협을 나타낼 수 있는 비정상적인 활동을 검색 하는 기능이 포함 됩니다.

- [취약성 평가](../azure-sql/database/sql-vulnerability-assessment.md) -잠재적인 데이터베이스 취약성을 검색, 추적 및 해결 하는 데 도움이 되는 검색 서비스입니다. 평가 검색은 SQL 컴퓨터의 보안 상태에 대 한 개요와 보안 결과에 대 한 세부 정보를 제공 합니다.

- [Advanced threat protection](../azure-sql/database/threat-detection-overview.md) -sql 삽입, 무차별 암호 대입 공격, 권한 남용 등의 위협에 대해 sql server를 지속적으로 모니터링 하는 검색 서비스입니다. 이 서비스는 의심 스러운 활동에 대 한 세부 정보, 위협에 대 한 완화 방법에 대 한 지침, Azure 센티널에서 조사를 계속 하는 옵션을 사용 하 여 Azure Security Center의 작업 기반 보안 경고를 제공 합니다.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Azure Defender for SQL에서 제공 하는 경고 종류는 무엇 인가요?

보안 경고는 다음과 같은 경우에 트리거됩니다.

- **잠재적 sql 삽입 공격** -응용 프로그램이 데이터베이스에서 잘못 된 sql 문을 생성할 때 검색 되는 취약성을 포함 합니다.
- **비정상적인 데이터베이스 액세스 및 쿼리 패턴** -예를 들어, 서로 다른 자격 증명을 사용한 비정상적으로 많은 수의 실패 한 로그인 시도 (무차별 암호 대입 시도)
- **의심 스러운 데이터베이스 활동** -예를 들어 SQL 가져오기 및 내보내기 작업에 대 한 내보내기 저장소 대상의 변경

경고에는 위협을 조사 하 고 수정 하는 방법에 대 한 권장 사항 뿐만 아니라 트리거를 트리거한 인시던트의 세부 정보가 포함 됩니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 SQL 용 Azure Defender에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- [컴퓨터에서 SQL server에 대해 Azure Defender를 사용 하도록 설정 하는 방법](defender-for-sql-usage.md)
- [SQL database 서버에 Azure Defender를 사용 하도록 설정 하는 방법](../azure-sql/database/advanced-data-security.md)
- [SQL에 대 한 Azure Defender 경고 목록](alerts-reference.md#alerts-sql-db-and-warehouse)