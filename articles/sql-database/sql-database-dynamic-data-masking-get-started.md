---
title: SQL 데이터베이스 동적 데이터 마스킹 시작(Azure 포털)
description: Azure 포털에서 SQL 데이터베이스 동적 데이터 마스킹을 시작하는 방법
services: sql-database
documentationcenter: ''
author: ronitr
manager: jhubbard
editor: v-romcal

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronitr; ronmat; v-romcal; sstein

---
# SQL 데이터베이스 동적 데이터 마스킹 시작(Azure 포털)
> [!div class="op_single_selector"]
> * [동적 데이터 마스킹-Azure 클래식 포털](sql-database-dynamic-data-masking-get-started-portal.md)
> 
> 

## 개요
SQL 데이터베이스 동적 데이터 마스킹에서는 권한이 없는 사용자에 대해 중요한 데이터를 마스킹해 표시함으로써 데이터 노출을 제한합니다. Azure SQL 데이터베이스의 V12 버전에서는 동적 데이터 마스킹을 지원합니다.

동적 데이터 마스킹을 수행하면 고객이 응용 프로그램 레이어에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 동적 데이터 마스킹은 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다. 이때 데이터베이스의 데이터 자체는 변경되지 않습니다.

예를 들어 콜 센터 서비스 상담원은 주민 등록 번호나 신용 카드 번호 중 몇 자리로 발신자를 식별할 수 있지만 이러한 데이터 항목이 서비스 상담원에게 완전히 노출되어서는 안 됩니다. 모든 쿼리의 결과 집합에서 주민 등록 번호 또는 신용 카드 번호의 마지막 4자리를 제외한 모든 숫자를 마스킹하도록 마스킹 규칙을 정의할 수 있습니다. 또 다른 예로, 적합한 데이터 마스크를 정의하여 PII(개인 식별이 가능한 정보) 데이터를 보호하여 준수 규정을 위반하지 않고 문제 해결을 위해 프로덕션 환경을 쿼리할 수 있습니다.

## SQL 데이터베이스 동적 데이터 마스킹 관련 기본 사항
SQL 데이터베이스 구성 블레이드 또는 설정 블레이드에서 동적 데이터 마스킹 작업을 선택하여 Azure 포털에서 동적 데이터 마스킹 정책을 설정합니다.

### 동적 데이터 마스킹 사용 권한
Azure 데이터베이스 관리자, 서버 관리자 또는 보안 관리자 역할이 동적 데이터 마스킹을 구성할 수 있습니다.

### 동적 데이터 마스킹 정책
* **마스킹에서 제외되는 SQL 사용자** - SQL 쿼리 결과에서 마스킹되지 않은 데이터를 가져오는 SQL 사용자 또는 ADD ID 집합입니다. 관리자 권한이 있는 사용자는 항상 마스킹에서 제외되며 마스크 없이 원본 데이터를 보게 됩니다.
* **마스킹 규칙** - 마스킹하도록 지정되는 필드와 사용할 마스킹 기능을 정의하는 규칙 집합입니다. 데이터베이스 스키마 이름, 테이블 이름 및 열 이름을 사용하여 마스킹하도록 지정되는 필드를 정의할 수 있습니다.
* **마스킹 함수** - 각 시나리오에서 데이터 표시를 제어하는 메서드 집합입니다.

| 마스킹 기능 | 마스킹 논리 |
| --- | --- |
| **기본값** |**지정된 필드의 데이터 형식에 따른 전체 마스킹**<br/><br/>• 문자열 데이터 형식(nchar, ntext, nvarchar)의 필드 크기가 4자 미만이면 XXXX개 혹은 그 이하의 X를 사용합니다.<br/>• 숫자 데이터 형식(bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real)의 경우 0 값을 사용합니다.<br/>• 날짜/시간 데이터 형식(date, datetime2, datetime, datetimeoffset, smalldatetime, time)의 경우 01-01-1900을 사용합니다.<br/>• SQL 변형의 경우 현재 형식의 기본값이 사용됩니다.<br/>XML의 경우 <masked/> 문서가 사용됩니다.<br/>• 특수 데이터 형식(타임스탬프 테이블, hierarchyid, GUID, 이진, 이미지, varbinary 공간 형식)의 경우 빈 값을 사용합니다. |
| **신용 카드** |**지정된 필드의 마지막 4자리를 표시**하고 신용 카드 형식의 접두사로 상수 문자열을 추가하는 마스킹 메서드입니다.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **주민등록 번호** |**지정된 필드의 마지막 4자리를 표시**하고 주민 등록 번호 형식의 접두사로 상수 문자열을 추가하는 마스킹 메서드입니다.<br/><br/>XXX-XX-1234 |
| **Email** |메일 주소 형식의 상수 문자열 접두사를 사용하여 **첫 글자를 표시하고 도메인을 XXX.com으로 바꾸는 마스킹 메서드**입니다.<br/><br/>aXX@XXXX.com |
| **난수** |선택한 경계 및 실제 데이터 형식에 따라 **난수를 생성하는 마스킹 메서드**입니다. 지정된 경계가 같으면 마스킹 함수로 상수가 사용됩니다.<br/><br/>![탐색 창](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **사용자 지정 텍스트** |**첫 문자와 마지막 문자를 표시**하고 가운데에 사용자 지정 패딩 문자열을 추가하는 마스킹 메서드입니다. 원래 문자열이 노출된 접두사 및 접미사보다 짧은 경우 패딩 문자열만 사용됩니다.<br/>prefix[padding]suffix<br/><br/>![탐색 창](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### 마스크 권장 필드
DDM 권장 사항 엔진은 중요한 필드일 가능성이 있어 마스크 대상이 될 만한 데이터베이스의 특정 필드를 플래그합니다. 포털의 동적 데이터 마스킹 블레이드에는 데이터베이스에 대한 권장 열이 표시됩니다. 이 필드에 마스크를 적용하려면 하나 이상의 열에 대해 **마스크 추가**를 클릭한 다음 **저장**을 클릭하기만 하면 됩니다.

## Azure 포털을 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure 포털을 시작합니다.
2. 마스킹할 중요 데이터가 포함된 데이터베이스의 설정 블레이드로 이동합니다.
3. **동적 데이터 마스킹** 타일을 클릭하여 **동적 데이터 마스킹** 구성 블레이드를 시작합니다.
   
   * 아래쪽의 **작업** 섹션으로 스크롤한 다음 **동적 데이터 마스킹**을 클릭해도 됩니다.
     
     ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. **동적 데이터 마스킹** 구성 블레이드에 권장 엔진이 마스킹 대상으로 플래그한 몇 가지 데이터베이스 열이 나타날 수 있습니다. 권장을 적용하려면 하나 이상의 열에 대해 **마스크 추가**를 클릭합니다. 해당 열에 대해 기본 형식을 기초로 마스크가 생성됩니다. 마스킹 규칙을 클릭하고 마스킹 필드 형식을 원하는 다른 형식으로 편집하면 마스킹 기능을 변경할 수 있습니다. 반드시 **저장**을 클릭하여 설정을 저장합니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. 데이터베이스에서 열에 마스크를 추가하려면 **동적 데이터 마스킹** 구성 블레이드에서 **마스크 추가**를 클릭하여 **마스킹 규칙 추가** 구성 블레이드를 엽니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. **스키마**, **테이블** 및 **열**을 선택하여 마스킹하도록 지정되는 필드를 정의합니다.
7. 중요한 데이터 마스킹 범주 목록에서 **마스킹 필드 형식**을 선택합니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>
8. 데이터 마스킹 규칙 블레이드에서 **저장**을 클릭하여 동적 데이터 마스킹 정책의 마스킹 규칙 집합을 업데이트합니다.
9. 마스킹에서 제외되며 마스크되지 않은 중요 데이터에 액세스할 수 있는 SQL 사용자 또는 AAD ID를 입력합니다. 세미콜론으로 구분된 사용자 목록이어야 합니다. 관리자 권한이 있는 사용자는 항상 원래의 마스킹되지 않은 데이터에 액세스할 수 있습니다.
   
    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > 응용 프로그램 계층에서 권한이 있는 응용 프로그램 사용자에 대해 중요한 데이터를 표시할 수 있도록 지정하려면 응용 프로그램이 데이터베이스 쿼리에 사용할 SQL 사용자나 AAD ID를 추가합니다. 중요한 데이터의 노출을 최소화하려면 권한이 있는 사용자 수를 최소한으로 이 목록에 포함하는 것이 좋습니다.
   > 
   > 
10. 데이터 마스킹 구성 블레이드에서 **저장**을 클릭하여 신규 또는 업데이트된 마스킹 정책을 저장합니다.

## PowerShell cmdlet을 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정
[Azure SQL 데이터베이스 cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)을 참조하세요.

## REST API를 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정
[Azure SQL 데이터베이스에 대한 작업](https://msdn.microsoft.com/library/dn505719.aspx)을 참조하세요.

<!---HONumber=AcomDC_0713_2016-->