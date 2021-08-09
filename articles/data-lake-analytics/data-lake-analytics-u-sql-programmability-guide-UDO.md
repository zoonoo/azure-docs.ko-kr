---
title: Azure Data Lake에 대한 U-SQL UDO 프로그래밍 기능 가이드
description: 적절한 USQL 스크립트를 만들 수 있도록 하는 U-SQL UDO 프로그래밍 기능 Azure Data Lake Analytics에 대해 알아봅니다.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512652"
---
# <a name="u-sql-user-defined-objects-overview"></a>U-SQL 사용자 정의 개체 개요


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: 사용자 정의 개체: UDO
U-SQL을 사용하면 UDO(사용자 정의 개체)라는 사용자 지정 프로그래밍 기능 개체를 정의할 수 있습니다.

U-SQL의 UDO 목록은 다음과 같습니다.

* 사용자 정의 추출기
    * 행 단위 추출
    * 사용자 지정 구조화된 파일에서 데이터 추출을 구현하는 데 사용됨

* 사용자 정의 출력자
    * 행 단위 출력
    * 사용자 지정 데이터 형식 또는 사용자 지정 파일 형식을 출력하는 데 사용됨

* 사용자 정의 처리기
    * 한 행씩 가져와 한 행씩 생성
    * 열 수를 줄이거나 기존 열 집합에서 파생된 값으로 새 열을 생성하는 데 사용됨

* 사용자 정의 적용자
    * 한 행씩 가져와 0-n 개 행 생성
    * OUTER/CROSS APPLY 사용

* 사용자 정의 결합자
    * 행 집합 결합--사용자 정의 JOIN

* 사용자 정의 리듀서
    * n개 행을 가져와 한 행씩 생성
    * 행 수를 줄이는 데 사용됨

UDO는 일반적으로 다음 U-SQL 문의 일부로 U-SQL 스크립트에서 명시적으로 호출됩니다.

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDO는 0.5Gb 메모리를 사용하도록 제한됩니다.  이 메모리 제한은 로컬 실행에 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계
* [U-SQL 프로그래밍 기능 가이드 - 개요](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 프로그래밍 기능 가이드 - UDT 및 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)