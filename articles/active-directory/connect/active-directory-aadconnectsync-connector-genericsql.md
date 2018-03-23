---
title: 일반 SQL 커넥터 | Microsoft Docs
description: 이 문서에서는 Microsoft의 일반 SQL 커넥터를 구성하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: bhu
editor: ''
ms.assetid: fd8ccef3-6605-47ba-9219-e0c74ffc0ec9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billmath
ms.openlocfilehash: 5ac8aff7e302a255efb86606416131ecf8088137
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2018
---
# <a name="generic-sql-connector-technical-reference"></a>일반 SQL 커넥터 기술 참조
이 문서에서는 일반 SQL 커넥터를 설명합니다. 이 문서는 다음 제품에 적용됩니다.

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * 핫픽스 4.1.3671.0 이상 [KB3092178](https://support.microsoft.com/kb/3092178)을 사용해야 합니다.

MIM2016와 FIM2010R2의 경우 커넥터는 [Microsoft 다운로드 센터](http://go.microsoft.com/fwlink/?LinkId=717495)에서 다운로드로 사용할 수 있습니다.

작업에서 이 커넥터를 보려면 [Generic SQL Connector step-by-step(일반 SQL 커넥터 단계별)](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) 문서를 참조하세요.

## <a name="overview-of-the-generic-sql-connector"></a>일반 SQL 커넥터의 개요
일반 SQL 커넥터를 사용하면 ODBC 연결을 제공하는 데이터베이스 시스템과 동기화 서비스를 통합할 수 있습니다.  

전체적인 관점에서 보면 커넥터의 현재 릴리스에서 다음과 같은 기능이 지원됩니다.

| 기능 | 지원 |
| --- | --- |
| 연결된 데이터 원본 |이 커넥터는 모든 64비트 ODBC 드라이버와 함께 지원됩니다. 다음으로 테스트되었습니다:  <li>Microsoft SQL Server 및 SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 및 11g</li><li>MySQL 5.x</li> |
| 시나리오 |<li>개체 수명 주기 관리</li><li>암호 관리</li> |
| 작업 |<li>전체 가져오기 및 델타 가져오기, 내보내기</li><li>내보내기: 추가, 삭제, 업데이트 및 바꾸기</li><li>암호 설정, 암호 변경</li> |
| 스키마 |<li>개체 및 특성의 동적 검색</li> |

### <a name="prerequisites"></a>필수 조건
커넥터를 사용하기 전에 동기화 서버에 다음 사항이 있는지 확인합니다.

* Microsoft.NET 4.5.2 Framework 이상
* 64비트 ODBC 클라이언트 드라이버

### <a name="permissions-in-connected-data-source"></a>연결된 데이터 원본의 사용 권한
일반 SQL 커넥터에 지원되는 작업을 만들거나 수행하려면 다음이 있어야 합니다.

* db_datareader
* db_datawriter

### <a name="ports-and-protocols"></a>포트 및 프로토콜
ODBC 드라이버가 작동하는 데 필요한 포트는 데이터베이스 공급 업체의 설명서를 참조하세요.

## <a name="create-a-new-connector"></a>새 커넥터 만들기
일반 SQL 커넥터를 만들려면 **동기화 서비스**에서 **관리 에이전트** 및 **만들기**를 선택합니다. **일반 SQL(Microsoft)** 커넥터를 선택합니다.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>연결
커넥터는 연결에 ODBC DSN 파일을 사용합니다. **관리 도구**의 시작 메뉴에 위치한 **ODBC 데이터 원본**을 사용하여 DSN 파일을 만듭니다. 관리 도구에서 커넥터에 제공할 수 있도록 **파일 DSN**을 만듭니다.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

연결 화면은 새 일반 SQL 커넥터를 만들 때의 첫 번째 화면입니다. 우선 다음 정보를 제공해야 합니다.

* DSN 파일 경로
* 인증
  * 사용자 이름
  * 암호

데이터베이스는 다음과 같은 인증 방법 중 하나를 지원해야 합니다.

* **Windows 인증**: 인증하는 데이터베이스는 Windows 자격 증명을 사용하여 사용자를 확인합니다. 지정된 사용자 이름/암호가 데이터베이스 인증에 사용됩니다. 이 계정에는 데이터베이스에 대한 권한이 필요합니다.
* **SQL 인증**: 인증하는 데이터베이스는 사용자 이름/암호가 정의된 연결 화면을 사용하여 데이터베이스에 연결합니다. DSN 파일에서 사용자 이름/암호를 저장하는 경우 연결 화면에 제공된 자격 증명은 우선 순위를 가집니다.
* **Azure SQL Database 인증**: 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL Database에 연결](../../sql-database/sql-database-aad-authentication.md)을 참조하세요.

**DN이 앵커**: 이 옵션을 선택하면 DN이 앵커 특성으로 사용됩니다. 간단한 구현을 위해 사용할 수 있지만 다음과 같은 제한이 있습니다.

* 커넥터는 하나의 개체 유형만 지원합니다. 따라서 참조 특성은 동일한 개체 형식을 참조할 수 있습니다.

**내보내기 형식: 개체 대체**: 내보내는 동안 일부 특성이 변경된 경우 모든 특성을 가진 전체 개체는 내보내지고 기존 개체를 대체합니다.

### <a name="schema-1-detect-object-types"></a>스키마1(개체 형식 검색)
이 페이지에서는 커넥터가 데이터베이스의 다른 개체 형식을 검색하는 방법을 구성하려고 합니다.

모든 개체 형식은 파티션으로 표시되며 **파티션 및 계층 구성**에서 자세히 구성됩니다.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**개체 형식 검색 방법**: 커넥터가 이러한 개체 형식 검색 방법을 지원합니다.

* **고정 값**: 쉼표로 구분된 목록을 사용하여 개체 유형 목록을 제공합니다. 예: `User,Group,Department`.  
  ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
* **테이블/뷰/저장 프로시저**: 테이블/뷰/저장 프로시저의 이름 및 개체 형식 목록을 제공하는 열 이름을 제공합니다. 저장 프로시저를 사용하면 또한 **[Name]:[Direction]:[Value]** 형식에서 매개 변수를 제공합니다. 별도 줄에 각 매개 변수를 제공합니다.(Ctrl + Enter를 사용하여 새 줄 가져옴)  
  ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
* **SQL 쿼리**: 이 옵션을 사용하면 개체 형식을 가진 단일 열을 반환하는 SQL 쿼리를 제공할 수 있습니다.예: `SELECT [Column Name] FROM TABLENAME`. 반환된 열은 형식 문자열(varchar)이어야 합니다.

### <a name="schema-2-detect-attribute-types"></a>스키마2(특성 형식 검색)
이 페이지에서 특성 이름 및 형식이 검색될 방법을 구성하려고 합니다. 구성 옵션은 이전 페이지에서 검색된 모든 개체 형식에 대해 나열됩니다.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**특성 형식 검색 방법**: 커넥터는 스키마1 화면에서 검색된 개체를 사용하여 이러한 특성 형식 검색 방법을 지원합니다.

* **테이블/뷰/저장 프로시저**: 특성 이름을 사용해야 하는 테이블/뷰/저장 프로시저의 이름을 제공합니다. 저장 프로시저를 사용하면 또한 **[Name]:[Direction]:[Value]** 형식에서 매개 변수를 제공합니다. 별도 줄에 각 매개 변수를 제공합니다.(Ctrl + Enter를 사용하여 새 줄 가져옴) 다중값 특성의 특성 이름을 검색하려면 테이블 또는 뷰의 쉼표로 구분된 목록을 제공합니다. 부모 및 자식 테이블에 동일한 열 이름이 있는 경우 다중값 시나리오가 지원되지 않습니다.
* **SQL 쿼리**: 이 옵션을 사용하면 특성 이름을 가진 단일 열을 반환하는 SQL 쿼리를 제공할 수 있습니다.예: `SELECT [Column Name] FROM TABLENAME`. 반환된 열은 형식 문자열(varchar)이어야 합니다.

### <a name="schema-3-define-anchor-and-dn"></a>스키마3(앵커 및 DN 정의)
이 페이지에서는 각 검색된 개체 형식에 대해 앵커 및 DN 특성을 구성할 수 있습니다. 앵커를 고유하게 만드는 여러 특성을 선택할 수 있습니다.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

* 다중값 및 부울 특성은 나열되지 않습니다.
* **DN이 앵커** 가 연결 페이지에서 선택되지 않는 한 동일한 특성은 앵커 및 DN 특성에 사용될 수 없습니다.
* **DN이 앵커** 가 연결 페이지에서 선택되면 이 페이지는 DN 특성에만 필요합니다. 이 특성은 앵커 특성으로도 사용됩니다.

  ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>스키마4(특성 형식, 참조 및 방향 정의)
이 페이지를 사용하면 정수, 이진 또는 부울 값과 같은 특성 형식 및 각 특성에 대한 방향을 구성할 수 있습니다. **스키마2** 페이지에서 모든 특성은 다중값 특성을 포함하여 나열됩니다.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

* **DataType**: 동기화 엔진에 의해 알려진 해당 형식에 특성 형식을 매핑하는 데 사용됩니다. 기본값은 SQL 스키마에서 검색하는 대로 동일한 형식을 사용하지만 날짜/시간 및 참조는 쉽게 감지할 수 없습니다. 이 경우에 **DateTime** 또는 **참조**를 지정해야 합니다.
* **방향**: 방향 특성을 가져오기, 내보내기 또는 ImportExport로 설정할 수 있습니다. ImportExport는 기본값입니다.

![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

참고:

* 특성 형식을 커넥터에서 감지할 수 없는 경우 문자열 데이터 형식을 사용합니다.
* **중첩 테이블** 은 열이 하나인 데이터베이스 테이블을 고려할 수 있습니다. Oracle은 특정 순서 없이 중첩 테이블의 행을 저장합니다. 그러나 PL/SQL 변수로 중첩 테이블을 검색하는 경우 행은 1부터 시작하는 지정된 연속 첨자입니다. 개별 행에 배열형 액세스를 제공합니다.
* **VARRYS** 는 커넥터에서 지원되지 않습니다.

### <a name="schema-5-define-partition-for-reference-attributes"></a>스키마5(참조 특성에 대한 파티션 정의)
이 페이지에서는 모든 참조 특성에 대해 특성이 참조하는 파티션(개체 형식)을 구성합니다.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

**DN이 앵커**를 사용하는 경우 참조하는 것과 동일한 개체 형식을 사용해야 합니다. 다른 개체 형식을 참조할 수 없습니다.

>[!NOTE]
2017년 3월 업데이트부터 "*"에 대한 옵션이 추가됩니다. 이 옵션을 선택하면 모든 가능한 멤버 유형을 가져옵니다.

![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/any-option.png)

>[!IMPORTANT]
 2017년 5월을 기준으로 “\*”, 즉 **옵션**이 흐름 내보내기 및 가져오기를 지원하도록 변경되었습니다. 이 옵션을 사용하려는 경우 다중 값 테이블/뷰에 개체 형식을 포함하는 특성이 있어야 합니다.

![](./media/active-directory-aadconnectsync-connector-genericsql/any-02.png)

 </br> "*"를 선택하면 해당 개체 형식의 열 이름도 지정해야 합니다.</br> ![](./media/active-directory-aadconnectsync-connector-genericsql/any-03.png)

가져온 후 아래 이미지와 유사한 화면이 표시됩니다.

  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/after-import.png)



### <a name="global-parameters"></a>글로벌 매개 변수
글로벌 매개 변수 페이지는 델타 가져오기, 날짜/시간 형식 및 암호 메서드를 구성하는 데 사용됩니다.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)



일반 SQL 커넥터는 델타 가져오기에 대한 다음 메서드를 지원합니다.

* **트리거**: [트리거를 사용하여 델타 뷰 생성](https://technet.microsoft.com/library/cc708665.aspx)을 참조하세요.
* **워터 마크**: 모든 데이터베이스에 사용할 수 있는 일반적인 방법입니다. 워터 마크 쿼리는 데이터베이스 공급 업체에 따라 미리 채워집니다. 워터 마크 열은 사용되는 모든 테이블/보기에 있어야 합니다. 이 열은 테이블 및 종속(다중값 또는 하위) 테이블에 대한 삽입 및 업데이트를 추적해야 합니다. 동기화 서비스와 데이터베이스 서버 간에 시계는 동기화되어야 합니다. 그렇지 않은 경우 델타 가져오기의 일부 항목은 생략될 수 있습니다.  
  제한 사항:
  * 워터 마크 전략은 삭제된 개체를 지원하지 않습니다.
* **스냅숏**: (Microsoft SQL Server로만 작동) [스냅숏을 사용하여 델타 뷰 생성](https://technet.microsoft.com/library/cc720640.aspx)
* **변경 내용 추적**: (Microsoft SQL Server로만 작동) [About 변경 내용 추적](https://msdn.microsoft.com/library/bb933875.aspx)  
  제한 사항:
  * 앵커 및 DN 특성은 테이블에서 선택한 개체에 대한 기본 키의 일부여야 합니다.
  * 변경 내용 추적을 사용하여 가져오기 및 내보내기를 수행하는 동안 SQL 쿼리가 지원되지 않습니다.

**추가 매개 변수**: 데이터베이스 서버가 있는 위치를 나타내는 데이터베이스 서버 표준 시간대를 지정합니다. 이 값은 다양한 형식의 날짜 및 시간 특성을 지원하는 데 사용됩니다.

커넥터는 항상 날짜 및 날짜-시간을 UTC 형식으로 저장합니다. 날짜 및 시간을 올바르게 변환할 수 있으려면 데이터베이스 서버의 표준 시간대 및 형식을 지정해야 합니다. 형식은 .Net 형식으로 표현되어야 합니다.

내보내는 동안 모든 날짜 시간 특성은 커넥터에 UTC 시간 형식으로 제공되어야 합니다.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**암호 구성**: 커넥터는 암호 동기화 기능을 제공하고 암호 설정 및 변경을 지원합니다.

커넥터는 암호 동기화를 지원하는 다음과 같은 두 가지 방법을 제공합니다.

* **저장 프로시저**: 이 메서드는 암호 집합 및 변경을 지원하기 위해 두 개의 저장 프로시저가 필요합니다. 아래 예제처럼 **암호 SP 설정** 및 **암호 SP 변경** 매개 변수에서 각각 암호 작업을 추가하고 변경하려는 모든 매개 변수를 입력합니다.
  ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
* **암호 확장**: 이 방법은 암호 확장 DLL이 필요합니다([IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) 인터페이스를 구현하는 확장 DLL 이름을 제공해야 합니다.). 암호 확장 어셈블리는 확장 폴더에 배치되어야 하므로 커넥터는 런타임 시 DLL을 로드할 수 있도록 합니다.
  ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

또한 **구성 확장** 페이지에서 암호 관리를 사용하도록 설정해야 합니다.
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>파티션 및 계층 구성
파티션 및 계층 구조 페이지에서 모든 개체 형식을 선택합니다. 각 개체 형식은 고유한 파티션입니다.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

또한 **연결** 또는 **글로벌 매개 변수** 페이지에 정의된 값을 재정의할 수도 있습니다.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>앵커 구성
앵커가 이미 정의되어 있으므로 이 페이지는 읽기 전용입니다. 선택된 앵커 특성은 항상 개체 형식을 사용하여 추가되어 개체 형식에 걸쳐 고유하게 유지되도록 합니다.

![anchors](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>실행 단계 매개 변수 구성
이 단계는 커넥터의 실행 프로필에서 구성됩니다. 이러한 구성은 데이터 가져오기 및 내보내기의 실제 작업을 수행합니다.

### <a name="full-and-delta-import"></a>전체 및 델타 가져오기
일반 SQL 커넥터는 다음의 메서드를 사용하여 전체 및 델타 가져오기를 지원합니다.

* 테이블
* 보기
* 저장 프로시저
* SQL 쿼리

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**테이블/뷰**  
개체에 대한 다중값 특성을 가져오려면 **다중값 테이블/뷰의 이름**에서 테이블/뷰 이름 및 상위 테이블을 통해 **조인 조건**에서 해당하는 조인 조건을 제공해야 합니다. 데이터 원본에 둘 이상의 다중값 테이블이 있는 경우 단일 보기에 union을 사용할 수 있습니다.

>[!IMPORTANT]
일반 SQL 관리 에이전트는 하나의 다중값 테이블과만 작동할 수 있습니다. 다중값 테이블/뷰의 이름에 둘 이상의 테이블 이름을 넣지 마십시오. 일반 SQL의 제한입니다.


예제: Employee 개체 및 해당하는 모든 다중값 특성을 가져오려고 합니다. 직원(주 테이블) 및 부서(다중값)라는 두 개의 테이블이 있습니다.
다음을 수행합니다.

* **테이블/뷰/SP**에서 **직원**을 입력합니다.
* **다중값 테이블/뷰 이름**에서 부서를 입력합니다.
* **조인 조건**의 직원과 부서 간에 조인 조건을 입력합니다. 예: `Employee.DEPTID=Department.DepartmentID`.
  ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**저장 프로시저**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

* 많은 양의 데이터가 있는 경우 저장 프로시저를 사용하여 페이지 매김을 구현하는 것이 좋습니다.
* 저장 프로시저의 경우 페이지 매김을 지원하려면 시작 인덱스 및 끝 인덱스를 제공해야 합니다. 참고: [많은 양의 데이터를 효율적으로 페이징](https://msdn.microsoft.com/library/bb445504.aspx).
* @StartIndex 및 @EndIndex은(는) 실행 시 **구성 단계** 페이지에 구성된 해당 페이지 크기 값으로 바꿉니다. 예를 들어, 커넥터가 첫 번째 페이지를 검색하고 페이지 크기가 500으로 설정된 경우 이러한 상황에서 @StartIndex은(는) 1 및 @EndIndex 500입니다. 커넥터가 후속 페이지를 검색하고 @StartIndex 및 @EndIndex 값을 변경하는 경우 이러한 값은 증가합니다.
* 매개 변수가 있는 저장 프로시저를 실행하려면 매개 변수를 `[Name]:[Direction]:[Value]` 형식으로 제공합니다. 별도 줄에 각 매개 변수를 입력합니다.(Ctrl + Enter를 사용하여 새 줄 가져옴)
* 또한 일반 SQL 커넥터는 Microsoft SQL Server의 연결된 서버에서 가져오기 작업을 지원합니다. 정보가 연결된 서버의 테이블에서 검색되어야 하는 경우 테이블은 다음 형식으로 제공되어야 합니다. `[ServerName].[Database].[Schema].[TableName]`
* 일반 SQL 커넥터는 실행 단계 정보와 스키마 탐색 간에 구조가 비슷한(모두 별칭 이름 및 데이터 형식) 개체만 지원합니다. 실행된 단계에서 스키마 및 제공된 정보로 선택한 개체가 다른 경우 SQL 커넥터는 이러한 형식의 시나리오를 지원할 수 없습니다.

**SQL 쿼리**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

* 여러 결과 집합 쿼리는 지원되지 않습니다.
* SQL 쿼리는 페이지 매김을 지원하고 시작 인덱스 및 끝 인덱스 변수를 제공하여 페이지 매김을 지원합니다.

### <a name="delta-import"></a>델타 가져오기
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

델타 가져오기 구성은 전체 가져오기에 비해 몇 가지 추가 구성이 필요합니다.

* 트리거 또는 스냅숏 접근 방식을 선택하여 델타 변경 내용을 추적하는 경우 **기록 테이블 또는 스냅숏 데이터베이스 이름** 상자에서 기록 테이블 또는 스냅숏 데이터베이스를 제공합니다.
* 또한 기록 테이블과 상위 테이블 간의 조인 조건을 제공해야 합니다. 예:`Employee.ID=History.EmployeeID`
* 기록 테이블에서 상위 테이블의 트랜잭션을 추적하려면 작업 정보(추가/업데이트/삭제)를 포함하는 열 이름을 제공해야 합니다.
* 워터 마크를 선택하여 델타 변경 내용을 추적하는 경우 **워터 마크 열 이름**에서 작업 정보를 포함하는 열 이름을 제공합니다.
* **형식 특성 변경** 열은 형식 변경에 필요합니다. 이 열은 기본 테이블 또는 다중값 테이블에서 발생하는 변경을 델타 뷰의 변경 형식에 매핑합니다. 이 열은 특성 수준 변경에 대해 Modify_Attribute 변경 유형을 포함할 수 있거나 개체 수준 변경 형식에 대한 변경 형식을 추가, 수정 또는 삭제합니다. 기본값 추가, 수정, 삭제 이외의 경우 사용자는 이 옵션을 사용하여 해당 값을 정의할 수 있습니다.

### <a name="export"></a>내보내기
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

일반 SQL 커넥터는 다음과 같이 네 가지 지원된 방법을 사용하여 내보내기를 지원합니다.

* 테이블
* 보기
* 저장 프로시저
* SQL 쿼리

**테이블/뷰**  
테이블/뷰 옵션을 선택한 경우 커넥터는 해당 쿼리를 생성하여 내보내기 작업을 수행합니다.

**저장 프로시저**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

저장 프로시저 옵션을 선택한 경우 내보내기에는 삽입/업데이트/삭제 작업을 수행할 세 가지 다른 저장 프로시저가 필요합니다.

* **SP 이름 추가**: 개체가 해당 테이블에서 삽입하려는 커넥터에 제공되는 경우 이 SP가 실행됩니다.
* **SP 이름 업데이트**: 개체가 해당 테이블에서 업데이트하려는 커넥터에 제공되는 경우 이 SP가 실행됩니다.
* **SP 이름 삭제**: 개체가 해당 테이블에서 삭제하려는 커넥터에 제공되는 경우 이 SP가 실행됩니다.
* 저장 프로시저에 매개 변수 값으로 사용되는 스키마에서 선택된 특성입니다. 예를 들어, `@EmployeeName: INPUT: EmployeeName` (EmployeeName을 커넥터 스키마에서 선택하고 내보내기를 수행하는 동안 커넥터가 해당 값을 바꿉니다.)
* 매개 변수가 있는 저장 프로시저를 실행하려면 매개 변수를 `[Name]:[Direction]:[Value]` 형식으로 제공합니다. 별도 줄에 각 매개 변수를 입력합니다.(Ctrl + Enter를 사용하여 새 줄 가져옴)

**SQL query**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

SQL 쿼리 옵션을 선택한 경우 내보내기에는 삽입/업데이트/삭제 작업을 수행할 세 가지 다른 쿼리가 필요합니다.

* **쿼리 삽입**: 개체가 해당 테이블에서 삽입하려는 커넥터에 제공되는 경우 이 쿼리가 실행됩니다.
* **쿼리 업데이트**: 개체가 해당 테이블에서 업데이트하려는 커넥터에 제공되는 경우 이 쿼리가 실행됩니다.
* **쿼리 삭제**: 개체가 해당 테이블에서 삭제하려는 커넥터에 제공되는 경우 이 쿼리가 실행됩니다.
* 쿼리에 매개 변수 값으로 사용되는 스키마에서 선택된 특성입니다. 예: `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>문제 해결
* 커넥터의 문제를 해결하기 위해 로깅을 사용하는 방법에 대한 자세한 내용은 [커넥터에 ETW 추적을 사용하는 방법](http://go.microsoft.com/fwlink/?LinkId=335731)참조하세요.
