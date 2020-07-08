---
title: 데이터 정렬
description: Azure Synapse SQL에서 지원 되는 데이터 정렬 유형
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0e9d8048c88a5ef37df2fde1ab282a834b07228a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206345"
---
# <a name="database-collation-support-for-synapse-sql"></a>Synapse SQL에 대 한 데이터베이스 데이터 정렬 지원

데이터 정렬은 문자 기반 데이터 형식에 대 한 로캘, 코드 페이지, 정렬 순서 및 문자 민감도 규칙을 제공 합니다. 데이터 정렬 정보를 요구 하는 모든 열과 식은 데이터베이스 설정에서 선택한 데이터 정렬을 상속 합니다. 기본 상속은 문자 기반 데이터 형식에 대해 다른 데이터 정렬을 명시적으로 명시 하 여 재정의할 수 있습니다.

새 SQL 풀 데이터베이스를 만들 때 Azure Portal에서 기본 데이터베이스 데이터 정렬을 변경할 수 있습니다. 이 기능을 사용 하면 3800 지원 데이터베이스 데이터 정렬 중 하나를 사용 하 여 새 데이터베이스를 훨씬 쉽게 만들 수 있습니다.

CREATE DATABASE 문을 사용 하 여 만들 때 기본 Synapse SQL 주문형 데이터베이스 데이터 정렬을 지정할 수 있습니다.

## <a name="changing-collation"></a>데이터 정렬 변경
SQL 풀 데이터베이스의 기본 데이터 정렬을 변경 하려면 프로 비전 환경의 데이터 정렬 필드를 간단 하 게 업데이트 해야 합니다. 예를 들어, 기본 데이터 정렬을 대/소문자를 구분 하도록 변경 하려면 데이터 정렬의 이름을 SQL_Latin1_General_CP1_CI_AS에서 SQL_Latin1_General_CP1_CS_AS으로 변경 하면 됩니다. 

SQL 주문형 데이터베이스의 기본 데이터 정렬을 변경 하려면 ALTER DATABASE 문을 사용 하면 됩니다.

## <a name="list-of-unsupported-collation-types"></a>지원 되지 않는 데이터 정렬 형식 목록
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

또한 SQL 풀은 다음 데이터 정렬 유형을 지원 하지 않습니다.

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="checking-the-current-collation"></a>현재 데이터 정렬 확인
데이터베이스의 현재 데이터 정렬을 확인 하려면 다음 T-sql 코드 조각을 실행할 수 있습니다.
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
' Collation '을 속성 매개 변수로 전달 하는 경우 DatabasePropertyEx 함수는 지정 된 데이터베이스에 대 한 현재 데이터 정렬을 반환 합니다. 자세한 내용은 MSDN의 DatabasePropertyEx 함수를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

SQL 풀 및 주문형 SQL의 모범 사례에 대한 추가 정보는 다음 문서를 참조하세요.

- [SQL 풀에 대한 모범 사례](best-practices-sql-pool.md)
- [주문형 SQL에 대한 모범 사례](best-practices-sql-on-demand.md)


