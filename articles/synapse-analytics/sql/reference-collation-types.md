---
title: 데이터 정렬
description: Azure 시냅스 SQL에서 지원되는 데이터 정렬 유형
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4270677f8f5f77e1ada0b1d9385163dad762bbda
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431541"
---
# <a name="database-collation-support-for-synapse-sql"></a>시냅스 SQL에 대한 데이터베이스 데이터 정렬 지원

데이터 정렬은 문자 기반 데이터 형식에 대한 로캘, 코드 페이지, 정렬 순서 및 문자 민감도 규칙을 제공합니다. 선택한 모든 열과 데이터 정렬 정보가 필요한 식은 데이터베이스 설정에서 선택한 데이터 정렬을 상속합니다. 문자 기반 데이터 형식에 대해 다른 데이터 정렬을 명시적으로 명시하여 기본 상속을 재정의할 수 있습니다.

새 SQL 풀 데이터베이스를 만들 때 Azure 포털에서 기본 데이터베이스 데이터 정렬을 변경할 수 있습니다. 이 기능을 사용하면 지원되는 3800개의 데이터베이스 데이터 데이터 중 하나를 사용하여 새 데이터베이스를 더 쉽게 만들 수 있습니다.

CREATE DATABASE 문을 사용하여 생성 시 기본 Synapse SQL 주문형 데이터베이스 데이터 정렬을 지정할 수 있습니다.

## <a name="changing-collation"></a>데이터 정렬 변경
SQL 풀 데이터베이스의 기본 데이터 정렬을 변경하려면 프로비저닝 환경에서 데이터 정렬 필드를 간단하게 업데이트합니다. 예를 들어 기본 데이터 정렬을 대/소문자 구분으로 변경하려는 경우 Collation의 이름을 SQL_Latin1_General_CP1_CI_AS SQL_Latin1_General_CP1_CS_AS 바꿉니다. 

SQL 온디맨드 데이터베이스의 기본 데이터 정렬을 변경하려면 ALTER DATABASE 문을 사용할 수 있습니다.

## <a name="list-of-unsupported-collation-types"></a>지원되지 않는 데이터 정렬 유형 목록
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

또한 SQL 풀은 다음과 같은 데이터 정렬 형식을 지원하지 않습니다.

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>현재 데이터 정렬 확인
데이터베이스의 현재 데이터 정렬을 확인하려면 다음 T-SQL 스니펫을 실행할 수 있습니다.
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
속성 매개 변수로 '데이터 정렬'을 전달하면 DatabasePropertyEx 함수는 지정된 데이터베이스에 대한 현재 데이터 정렬을 반환합니다. MSDN의 DatabasePropertyEx 함수에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

SQL 풀 및 주문형 SQL에 대한 모범 사례에 대한 추가 정보는 다음 문서에서 찾을 수 있습니다.

- [SQL 풀에 대한 모범 사례](best-practices-sql-pool.md)
- [SQL 온디맨드 모범 사례](best-practices-sql-on-demand.md)


