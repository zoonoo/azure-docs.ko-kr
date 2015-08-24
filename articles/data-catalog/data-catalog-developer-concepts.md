<properties
   pageTitle="Azure 데이터 카탈로그 개발자 개념"
   description="Azure 데이터 카탈로그에 대한 개발자 개념: 카탈로그, 사용자, 자산, 크라우드소싱"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags 
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Azure 데이터 카탈로그 개발자 개념

Microsoft**Azure 데이터 카탈로그**는 데이터 원본 검색 및 크라우드소싱 데이터 원본 메타데이터에 대한 기능을 제공하는 완전히 관리되는 클라우드 서비스입니다. 개발자는 REST API를 통해 서비스를 사용할 수 있습니다. 서비스에서 구현되는 개념을 이해 하는 것은 개발자가 **Azure 데이터 카탈로그**를 성공적으로 통합하는 데 중요합니다.

## 주요 개념

**Azure 데이터 카탈로그**개념적 모델은 **카탈로그**, **사용자**, **자산** 및 **주석**의 4개의 주요 개념에 기반합니다.

![개념][1]

*그림 1-Azure 데이터 카탈로그 단순 개념적 모델*

### 카탈로그

**카탈로그**는 조직이 저장되는 모든 메타데이터에 대한 최상위 컨테이너입니다. Azure 계정당 하나의**카탈로그**가 허용 합니다. 카탈로그는 Azure 구독에 연결되어 있지만, 계정에 여러 구독이 있을 수 있더라도 지정된 Azure 계정에 대해 **카탈로그**를 한 개만 만들 수 있습니다.

카탈로그에는**사용자** 및 **자산**이 포함됩니다.

### 사용자

사용자는 카탈로그에서 작업 (카탈로그 검색, 항목 추가, 편집 또는 제거 등..)을 수행할 권한이 있는 보안 주체입니다.

사용자가 할 수 있는 여러 역할이 있습니다. 역할에 대한 자세한 내용은 역할 및 권한 부여 섹션을 참조하세요.

개별 사용자(보안 그룹 제외)만 추가할 수 있습니다.

Azure 데이터 카탈로그는 관리를 식별하고 액세스하기 위해 Azure Active Directory를 사용합니다. 각 카탈로그 사용자는 계정에 대해 Active Directory의 구성원이어야 합니다.

### 자산

**카탈로그**에는 데이터 자산이 포함됩니다. **자산**은 카탈로그에서 관리하는 항목의 단위입니다.

자산의 세분성은 데이터 소스에 따라 다릅니다. SQL Server 또는 Oracle 데이터베이스에서 자산은 테이블 또는 보기가 될 수 있습니다. SQL Server Analysis Services에서 자산은 측정값, 차원, 또는 KPI(주요 성능 표시기)가 될 수 있습니다. SQL Server Reporting Services에서 자산은 보고서 될 수 있습니다.

**자산**은 카탈로그에서 추가 하거나 제거할 항목입니다. 자산은 **검색**에서 얻게 되는 결과의 단위입니다.

**자산**은 핵심 정보, 해당 위치 및 해당 형식뿐만 아니라 이를 보다 자세히 설명 하기 주석으로 구성됩니다.

### 주석

주석은 자산에 대한 메타데이터를 나타내는 항목입니다.

주석의 예에는 설명, 태그, 스키마, 설명서 등...이 있습니다. 자산 형식 및 주석 형식의 전체 목록은 자산 개체 모델 섹션에 있습니다.

## 크라우드소싱 주석 및 사용자 관점 (의견의 복합성)

Azure 데이터 카탈로그의 주요 측면은 시스템에서 메타데이터의 크라우드소싱을 지원하는 방법입니다. 하나의 의견만 있고 마지막 기록자가 우선하는 wiki 접근 방식과 대비하여, Azure 데이터 카탈로그 모델은 시스템에 여러 의견이 나란히 있도록 허용합니다.

이 방법은 여러 사용자가 지정된 자산에 대해 다른 관점을 가질 수 있는 기업 데이터의 실제 세계를 반영합니다.

-	데이터베이스 관리자는 대량 ETL 작업에 대한 서비스 수준 계약 또는 사용 가능한 처리 창에 대한 정보를 제공할 수 있습니다.
-	데이터 담당자는 자산이 적용되는 비즈니스 프로세스 또는 비즈니스가 적용되는 분류에 대한 정보를 제공할 수 있습니다.
-	재무 분석가는 보고 작업 종료의 기간 동안 데이터를 사용하는 방법에 대한 정보를 제공할 수 있습니다.

이 예시를 지원하기 위해 각 사용자(DBA, 데이터 담당자 및 분석가)는 카탈로그에 등록된 단일 테이블에 대한 설명을 추가할 수 있습니다. 모든 설명은 시스템 및 모든 설명이 표시되는 Azure 데이터 카탈로그에서 유지 관리됩니다.

이 패턴은 개체 모델의 대부분의 항목에 적용 됩니다. 이 때문에 대체로 JSON 페이로드의 개체 형식이 Singleton을 예상할 수 있는 속성에 대해 배열됩니다.

예를 들어, 자산에서 루트는 설명 개체의 배열입니다. 배열 속성을 "설명" 이라고 합니다. 설명 개체에는 설명, 태그 및 friendlyName의 3 개의 속성이 있습니다. 이러한 속성 중 하나의 형식인 각 사용자가 사용자에 의해 제공된 값에 대해 생성된 설명 개체를 가져오는 것이 패턴입니다.

이 때 UX는 조합을 표시하는 방법을 선택할 수 있습니다. 디스플레이 대한 세 가지 다른 패턴이 있습니다.

-	가장 간단한 패턴은 "모두 표시"입니다. 이 패턴에서 모든 개체는 일종의 목록 보기에 표시됩니다. 이는 Azure 데이터 카탈로그 포털 UX가 설명하는 것입니다.
-	다른 패턴은 "병합"입니다. 이 패턴에서 다른 사용자의 모든 값 중복을 제거하여 함께 병합됩니다. Azure 데이터 카탈로그 포털 UX에서 이 패턴의 예는 태그 및 전문가 속성입니다.
-	세번째 패턴은 "마지막 기록자 우선"입니다. 이 패턴에서는 입력한 가장 최근의 값만 표시 됩니다. friendlyName은 이 패턴의 예입니다. 일부 형식에는 여러 인스턴스가 허용되지 않습니다.

다음은 여러 인스턴스가 허용되거나 허용되지 않는 형식의 목록입니다.

다음의 형식에는 여러 인스턴스가 허용됩니다.

- 설명
- 전문가
- 스키마 설명

다음 형식에는 여러 인스턴스가 허용되지 **않습니다**.

- 루트 형식
- 스키마
- 미리 보기

## 자산 개체 모델

주요 개념 섹션에 소개된 바와 같이**Azure 데이터 카탈로그**개체 모델에는 자산 또는 주석일 수 있는 항목이 포함됩니다. 항목에는 선택 또는 필수가 될 수 있는 속성이 있습니다. 일부 속성은 모든 항목에 적용 됩니다. 일부 속성은 모든 자산에 적용 됩니다. 일부 속성은 특정 자산 형식에만 적용 됩니다.

### 공용 속성

이러한 속성은 모든 루트 자산 형식 및 모든 주석 형식에 적용 됩니다.

> [AZURE.NOTE]이중 밑줄로 시작하는 속성에는 시스템 형식이 있습니다.

<table><tr><td><b>속성 이름</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr><tr><td>modifiedTime</td><td>DateTime</td><td>마지막으로 루트가 수정된 시간. 이 시간은 클라이언트에 의해 설정됩니다. (서버에 이 값이 유지되지 않습니다).</td></tr><tr><td>__id</td><td>Guid</td><td>항목의 id입니다 (읽기 전용). 이 id는 자산에 대해 고유성을 보장합니다. 따라서 항목에 중요한 것은 __Id, 루트의__id 입니다. 이 튜플은 디렉터리 내에서 고유성을 보장합니다.</td></tr><tr><td>__typeId</td><td>Guid</td><td>자산 유형(읽기 전용) </td></tr><tr><td>__creatorId</td><td>문자열</td><td>자산을 고유하게 식별하는 자산의 작성자에 의해 사용되는 문자열입니다. </td></tr></table>

### 공용 루트 속성

이러한 속성은 모든 루트 자산 형식에 적용 됩니다.

<table><tr><td><b>속성 이름</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr><tr><td>name</td><td>문자열</td><td>데이터 원본 위치 정보에서 파생된 이름</td></tr><tr><td>dsl</td><td>데이터 원본 위치</td><td>고유하게 데이터 원본을 설명하고 자산에 대한 식별자 중 하나입니다. (이중 ID 섹션 참조). dsl의 구조는 원본 유형에 따라 달라집니다.</td></tr><tr><td>DataSource</td><td>DataSourceInfo</td><td>자산 형식에 대한 세부 정보.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>이 자산을 가장 최근에 등록한 사용자를 설명합니다. 사용 (upn)뿐만 아니라 표시 이름 (lastName과 firstName)에 대한 고유 id를 포함합니다.</td></tr><tr><td>lastRegisteredTime</td><td>datetime</td><td>이 자산이 카탈로그에 등록된 마지막 시간.</td></tr></table>

### 루트 자산 형식

루트 자산 형식은 카탈로그에 등록 될 수 있는 다양한 유형의 데이터 자산을 나타내는 자산 형식입니다.

<table><tr><td><b>자산 형식</b></td><td><b>추가 속성</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr><tr><td>테이블</td><td></td><td></td><td>테이블은 모든 테이블 형식 데이터를 나타냅니다. 여기에는 SQL 테이블, SQL 보기, Analysis Services 테이블 형식 테이블, Analysis Services 다차원 차원, Oracle 등...이 포함됩니다    </td></tr><tr><td>측정값</td><td></td><td></td><td>이 유형은 Analysis Services 측정값을 나타냅니다.</td></tr><tr><td></td><td>측정값</td><td>열</td><td>측정값을 설명하는 메타데이터</td></tr><tr><td></td><td>isCalculated </td><td>Boolean</td><td>측정값이 계산할지 여부를 지정합니다.</td></tr><tr><td></td><td>측정값 그룹</td><td>문자열</td><td>측정값에 대한 물리적 컨테이너</td></tr><tr><td>KPI</td><td></td><td></td><td>이 형식은 Analysis Services 핵심 성과 지표를 나타냅니다.</td></tr><tr><td></td><td>goalExpression</td><td>문자열</td><td>KPI의 목표값을 반환하는 MDX 숫자 식 또는 계산.</td></tr><tr><td></td><td>valueExpression</td><td>문자열</td><td>KPI의 실제 값을 반환하는 MDX 숫자 식.</td></tr><tr><td></td><td>statusExpression</td><td>문자열</td><td>특정 시점에 지정한 KPI의 상태를 나타내는 MDX 식.</td></tr><tr><td></td><td>trendExpression</td><td>문자열</td><td>시간이 지남에 따라 KPI 값을 계산하는 MDX 식.  특정 비즈니스 컨텍스트에서 유용한 시간 기반 조건이 추세가 될 수 있습니다.</td></tr><tr><td></td><td>측정값 그룹</td><td>문자열</td><td>측정값에 대한 물리적 컨테이너</td></tr><tr><td>보고서</td><td></td><td></td><td>이 형식은 SQL Server Reporting Services 보고서를 나타냅니다. </td></tr><tr><td></td><td>만든 사람</td><td>문자열</td><td></td></tr><tr><td></td><td>CreatedDate</td><td>문자열</td><td></td></tr></table>

### 주석 형식

주석 형식은 카탈로그 내에서 다른 형식으로 할당할 수 있는 메타데이터의 형식을 나타냅니다.

<table><tr><td><b>주석 형식</b></td><td><b>추가 속성</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr><tr><td>설명</td><td></td><td></td><td>시스템의 각 사용자는 자신의 설명 및 태그를 추가할 수 있습니다. 해당 사용자만 설명 개체를 편집할 수 있습니다. (Admins 및 자산 소유자는 설명 개체를 삭제할 수 있지만 편집할 수는 없습니다). 시스템은 이 개체를 별도로 유지합니다. 따라서 각 자산에 대한 설명의 배열이 있습니다.(아마도 데이터 소스로부터 도출 된 정보를 포함한 사람 이외에, 자산에 대한 지식에 기여한 각 사용자에 대한 배열)</td></tr><tr><td></td><td>friendlyName</td><td>string</td><td>데이터 소스에서 파생된 이름 대신 사용할 수 있는 친숙한 이름입니다. 표시 및 검색에 유용합니다.</td></tr><tr><td></td><td>tags</td><td>string[]</td><td>자산에 대한 태그의 배열</td></tr><tr><td></td><td>description</td><td>string</td><td>자산에 대해 간단한 설명 (2-3 줄)</td></tr><tr><td>스키마</td><td></td><td></td><td>스키마는 데이터의 구조를 설명합니다. 스키마는 특성 (즉, 열, 특성, 필드, 등...), 이름, 형식 뿐 아니라 다른 메타데이터도 나열합니다. 이 정보는 모든 데이터 소스에서 파생됩니다. 일반적으로 자산에 대해 하나의 스키마 항목이 있습니다.</td></tr><tr><td></td><td>열</td><td>Column[]</td><td>열 개체의 배열입니다. 열을 데이터 소스에서 파생된 정보와 함께 설명합니다.</td></tr><tr><td>SchemaDescription</td><td></td><td></td><td>여기에는 스키마에 정의된 각 특성에 대 한 설명 및 태그 집합이 포함됩니다. 시스템의 각 사용자는 자신의 설명 및 태그를 추가할 수 있습니다. 해당 사용자만 설명 개체를 편집할 수 있습니다. (Admins 및 자산 소유자는 SchemaDescription 개체를 삭제할 수 있지만 편집할 수는 없습니다) 시스템은 이 개체를 별도로 유지합니다. 따라서 각 자산에 대한 SchemaDescriptio의 배열이 있습니다.(아마도 데이터 소스로부터 도출 된 정보를 포함한 사람 이외에, 속성에 대한 지식에 기여한 각 사용자에 대한 배열) SchemaAttributes는 스키마에 느슨하게 바인딩되어 있어서 비동기화 될 수 있습니다. 즉, SchemaDescription는 더이상 스키마에 존재하지 않거나 최근에 추가된 새 열을 참조하는 데 실패하는 열을 설명할 수 없습니다. 동기화에 이 상태를 유지하는 것은 작성자에 따라 다릅니다. 데이터 소스에는 설명 정보도 있을 수 있습니다. 이 정보는 도구를 실행할 때 발생할 수 있는 추가 schemaDescription 개체가 될 것입니다.</td></tr><tr><td></td><td>columnDescriptions</td><td>ColumnDescription[]</td><td>스키마의 열을 설명하는 ColumnDescriptions의 배열입니다. </td></tr><tr><td>전문가</td><td></td><td></td><td>여기에는 데이터 집합에서 전문가로 간주되는 사용자의 목록이 포함되어 있습니다. 전문가 의견(즉, 설명)은 설명 목록을 만들 때 UX 위에 풍선으로 표시됩니다. 각 사용자는 전문가의 고유한 목록을 지정할 수 있습니다. 해당 사용자만 전문가 개체를 편집할 수 있습니다. (Admins 및 자산 소유자는 삭제할 수 있지만 편집할 수는 없습니다).</td></tr><tr><td></td><td>전문가</td><td>string[]</td><td>전자 메일 주소의 배열입니다.</td></tr><tr><td>미리 보기</td><td></td><td></td><td>미리 보기는 자산에 대한 데이터의 상위 20 행의 스냅숏을 포함합니다. 미리 보기는 일부 형식의 자산에만 적합합니다. (예: 테이블에 대해서는 적합하지만  측정값에 대해서는 적합하지 않음).</td></tr><tr><td></td><td>미리 보기</td><td>object[]</td><td>열을 나타내는 개체의 배열입니다. 각 개체에는 행에 대해 해당 열에 대한 값을 가진 열에 매핑되는 속성이 있습니다.</td></tr>
<tr><td>AccessInstruction</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>콘텐츠의 mime 형식입니다.</td></tr>
<tr><td></td><td>콘텐츠</td><td>string</td><td>이 데이터 자산에 액세스하는 방법에 대한 지침입니다. URL, 메일 주소 또는 명령 집합일 수 있습니다.</td></tr>

</table>

### 일반 형식

일반 형식 속성에 대 한 형식으로 사용할 수 있지만 항목은 없습니다.

<table><tr><td><b>일반 형식</b></td><td><b>속성</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr><tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr><tr><td></td><td>sourceType</td><td>string</td><td>SQL Server, Oracle 데이터베이스 등 데이터 소스의 형식을 설명합니다.   </td></tr><tr><td></td><td>ObjectType</td><td>string</td><td>테이블, SQL Server에 대한 보기 등 데이터 소스에 대한 개체의 형식을 설명합니다.</td></tr><tr><td></td><td>formatType</td><td>string</td><td>데이터의 구조를 설명합니다. 현재 값은 구조적 또는 비구조적입니다.</td></tr><tr><td>SecurityPrincipal</td><td></td><td></td><td></td></tr><tr><td></td><td>upn</td><td>string</td><td>사용자의 고유한 전자 메일 주소입니다.</td></tr><tr><td></td><td>firstname</td><td>string</td><td>사용자의 이름(표시 용).</td></tr><tr><td></td><td>Lastname</td><td>string</td><td>사용자 성(표시 용).</td></tr><tr><td>열</td><td></td><td></td><td></td></tr><tr><td></td><td>name</td><td>string</td><td>열 또는 특성의 이름입니다.</td></tr><tr><td></td><td>type</td><td>string</td><td>열 또는 특성의 데이터 형식입니다. 허용 되는 형식은 자산의 데이터 소스 형식에 따라 달라집니다. 형식의 하위 집합만 지원됩니다.</td></tr><tr><td></td><td>maxLength</td><td>int</td><td>열 또는 특성에 허용되는 최대 길이입니다. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr><tr><td></td><td>자릿수</td><td>바이트</td><td>열 또는 특성에 대한 자릿수입니다. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr><tr><td></td><td>isNullable</td><td>Boolean</td><td>열에 null 값의 허용 여부. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr><tr><td></td><td>식</td><td>string</td><td>값에 계산된 열이 있는 경우 이 필드에는 값을 표현하는 식이 포함됩니다. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr><tr><td></td><td>defaultValue</td><td>object</td><td>개체에 대한 insert 문에 지정하지 않으면 기본 값이 삽입됩니다. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr><tr><td>ColumnDescription</td><td></td><td></td><td></td></tr><tr><td></td><td>tags</td><td>string[]</td><td>열을 설명하는 태그의 배열입니다.</td></tr><tr><td></td><td>description</td><td>string</td><td>열을 설명하는 설명입니다.</td></tr><tr><td></td><td>columnName</td><td>string</td><td>이 정보가 참조하는 열의 이름입니다.</td></tr>
</table>

## 역할 및 권한 부여

Microsoft Azure 데이터 카탈로그는 자산 및 주석에 대한 CRUD 작업에 대해 권한 부여 기능을 제공합니다.

## 주요 개념

Azure 데이터 카탈로그는 두 가지 권한 부여 메커니즘을 사용합니다.

- 역할 기반 권한 부여
- 사용 권한 기반의 권한 부여

### 역할

3가지 역할:**관리자**, **소유자** 및 **참여자**. 각 역할에는 다음 표에 요약된 범위와 권한이 있습니다.

<table><tr><td><b>역할</b></td><td><b>범위</b></td><td><b>권한</b></td></tr><tr><td>관리자</td><td>카탈로그 (즉, 카탈로그의 모든 자산/주석)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>소유자</td><td>각 자산 (즉,일명 루트 항목)</td><td>Read Delete ViewRoles ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>참여자</td><td>각 개별 자산 및 주석</td><td>읽기/업데이트/삭제/권한보기/참고(Read Update Delete ViewRoles Note): 항목에서 읽기(Read) 권한을 참여자가 해지하는 경우 모든 권한이 취소 됩니다.</td></tr></table>

> [AZURE.NOTE]**읽기****업데이트****삭제****역할 보기**권한은 모든 항목 (자산 또는 주석)에 적용할 수 있으며, 반면에**소유권 획득 ****소유권 변경****가시성 변경****권한 보기**는 루트 자산에만 적용됩니다.
>
>**삭제** 권한은 항목 뿐만 그 아래에 있는의 모든 하위 항목 단일 항목에 적용 됩니다. 예를들어, 자산을 삭제하면 해당 자산에 대한 모든 주석도 삭제됩니다.

### 권한

권한은 액세스 제어 항목의 목록입니다. 각 액세스 제어 항목은 보안 주체에 대한 권한의 집합을 할당합니다. 권한은 자산(예: 루트 항목)에만 지정할 수 있으며 자산 및 모든 하위 항목에 적용됩니다.

**Azure 데이터 카탈로그**를 미리 보는 동안 자산의 표시를 제한하는 시나리오를 사용하려면 **읽기** 권한만 권한 목록에서 지원됩니다.

기본적으로 모든 인증된 사용자는 권한의 주체 집합에 표시를 제한하지 않는 한 카탈로그에 있는 모든 항목에 대한 **읽기**권한이 있습니다.

## REST API

**PUT** 및 **POST** 보기 항목 요청은 역할 및 권한을 제어하는 데 사용할 수 있습니다. 항목 페이로드 외에도 두 시스템 속성인 **\_\_roles** 및 **\_\_permissions**를 지정할 수 있습니다.

> [AZURE.NOTE]
>
> **__**\_\_permissions**는 루트 항목에만 적용됩니다.
>
> **소유자** 역할은 루트 항목에만 적용됩니다.
>
> 기본적으로 카탈로그에서 항목을 만들면 **참여자**는 현재 인증된 사용자로 설정됩니다. 항목을 누구나 업데이트할 수 있어야 한다면 항목을 처음 게시할 때 **\_\_roles** 속성에서 **참여자**를 <Everyone> 특수 보안 주체로 설정해야 합니다. 아래 예를 참조하세요. **참여자**는 변경할 수 없으며 항목의 수명 동안 동일하게 유지됩니다. 예를 들어 **관리자**나 **소유자**도 **참여자**를 변경할 수 있는 권한은 없습니다. **참여자**의 명시적 설정에 지원되는 유일한 값은 <Everyone>입니다. 예를 들어 **참여자**는 항목을 만든 사용자나 <Everyone>으로만 설정할 수 있습니다.

###예
**항목을 게시할 때 참여자를 <Everyone>으로 설정합니다.** 특수 보안 주체에는 <Everyone>의 objectId는 "00000000-0000-0000-0000-000000000201"입니다. **POST** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/?api-version=2015-07.1.0-Preview **Body**

	{
	    "__roles": [
	        {
	            "role": "Contributor",
	            "members": [
	                {
	                    "objectId": "00000000-0000-0000-0000-000000000201"
	                }
	            ]
	        }
	    ],
	    … other table properties
	}

**소유자를 할당하고 기존 루트 항목에 대한 표시를 제한** **PUT** https://123154bb...6aad6370ee14.datacatalog.azure.com/default/views/tables/042297b0...1be45ecd462a?api-version=2015-07.1.0-Preview

	{
	    "__roles": [
	        {
	            "role": "Owner",
	            "members": [
	                {
	                    "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
	                    "upn": "user1@contoso.com"
	                },
	                {
	                    "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
	                    "upn": "user2@contoso.com"
	                }
	            ]
	        }
	    ],
	    "__permissions": [
	        {
	            "principal": {
	                "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
	                "upn": "user3@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        },
	        {
	            "principal": {
	                "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
	                "upn": "user4@contoso.com"
	            },
	            "rights": [
	                {
	                    "right": "Read"
	                }
	            ]
	        }
	    ]
	}

> [AZURE.NOTE]PUT에서 본문에는 항목 페이로드 지정은 필수 사항이 아닙니다. PUT은 역할 및/또는 사용 권한을 업데이트 하는 데에만 사용할 수 있습니다.

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept.png

<!---HONumber=August15_HO7-->