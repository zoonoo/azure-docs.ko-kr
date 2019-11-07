---
title: Collation
description: Azure SQL Data Warehouse에서 지원 되는 데이터 정렬 형식입니다.
services: sql-data-warehouse
author: antvgski
manager: igorstan
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: f05e54a3dd4b69fff2bc7d122391d145b222b295
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692557"
---
# <a name="database-collation-support-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대 한 데이터베이스 데이터 정렬 지원

새 Azure SQL Data Warehouse 데이터베이스를 만들 때 Azure Portal에서 기본 데이터베이스 데이터 정렬을 변경할 수 있습니다. 이 기능을 사용 하면 SQL Data Warehouse에 대해 지원 되는 3800 데이터베이스 데이터 정렬 중 하나를 사용 하 여 새 데이터베이스를 훨씬 쉽게 만들 수 있습니다.
데이터 정렬은 문자 기반 데이터 형식에 대 한 로캘, 코드 페이지, 정렬 순서 및 문자 민감도 규칙을 제공 합니다. 데이터 정렬 정보를 요구 하는 모든 열과 식은 데이터베이스 설정에서 선택한 데이터 정렬을 상속 합니다. 기본 상속은 문자 기반 데이터 형식에 대해 다른 데이터 정렬을 명시적으로 명시 하 여 재정의할 수 있습니다.

## <a name="changing-collation"></a>데이터 정렬 변경
기본 데이터 정렬을 변경 하려면 프로 비전 환경의 데이터 정렬 필드를 간단 하 게 업데이트 합니다.

예를 들어, 기본 데이터 정렬을 대/소문자를 구분 하도록 변경 하려면 데이터 정렬의 이름을 SQL_Latin1_General_CP1_CI_AS에서 SQL_Latin1_General_CP1_CS_AS로 변경 하기만 하면 됩니다. 

## <a name="list-of-unsupported-collation-types"></a>지원 되지 않는 데이터 정렬 형식 목록
*   Japanese_Bushu_Kakusu_140_BIN
*   Japanese_Bushu_Kakusu_140_BIN2
*   Japanese_Bushu_Kakusu_140_CI_AI_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI
*   Japanese_Bushu_Kakusu_140_CI_AI_WS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AI
*   Japanese_Bushu_Kakusu_140_CS_AI_WS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*   Japanese_XJIS_140_BIN
*   Japanese_XJIS_140_BIN2
*   Japanese_XJIS_140_CI_AI_VSS
*   Japanese_XJIS_140_CI_AI_WS_VSS
*   Japanese_XJIS_140_CI_AI_KS_VSS
*   Japanese_XJIS_140_CI_AI_KS_WS_VSS
*   Japanese_XJIS_140_CI_AS_VSS
*   Japanese_XJIS_140_CI_AS_WS_VSS
*   Japanese_XJIS_140_CI_AS_KS_VSS
*   Japanese_XJIS_140_CI_AS_KS_WS_VSS
*   Japanese_XJIS_140_CS_AI_VSS
*   Japanese_XJIS_140_CS_AI_WS_VSS
*   Japanese_XJIS_140_CS_AI_KS_VSS
*   Japanese_XJIS_140_CS_AI_KS_WS_VSS
*   Japanese_XJIS_140_CS_AS_VSS
*   Japanese_XJIS_140_CS_AS_WS_VSS
*   Japanese_XJIS_140_CS_AS_KS_VSS
*   Japanese_XJIS_140_CS_AS_KS_WS_VSS
*   Japanese_XJIS_140_CI_AI
*   Japanese_XJIS_140_CI_AI_WS
*   Japanese_XJIS_140_CI_AI_KS
*   Japanese_XJIS_140_CI_AI_KS_WS
*   Japanese_XJIS_140_CI_AS
*   Japanese_XJIS_140_CI_AS_WS
*   Japanese_XJIS_140_CI_AS_KS
*   Japanese_XJIS_140_CI_AS_KS_WS
*   Japanese_XJIS_140_CS_AI
*   Japanese_XJIS_140_CS_AI_WS
*   Japanese_XJIS_140_CS_AI_KS
*   Japanese_XJIS_140_CS_AI_KS_WS
*   Japanese_XJIS_140_CS_AS
*   Japanese_XJIS_140_CS_AS_WS
*   Japanese_XJIS_140_CS_AS_KS
*   Japanese_XJIS_140_CS_AS_KS_WS
*   SQL_EBCDIC1141_CP1_CS_AS
*   SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>현재 데이터 정렬 확인
데이터베이스에 대 한 현재 데이터 정렬을 확인 하려면 다음 T-sql 코드 조각을 실행할 수 있습니다. SELECT DATABASEPROPERTYEX (DB_NAME (), ' Collation ')를 데이터 정렬로 ' Collation '을 속성 매개 변수로 전달 하는 경우 DatabasePropertyEx 함수는 지정 된 데이터베이스에 대 한 현재 데이터 정렬을 반환 합니다. 자세한 내용은 MSDN의 DatabasePropertyEx 함수를 확인할 수 있습니다.

