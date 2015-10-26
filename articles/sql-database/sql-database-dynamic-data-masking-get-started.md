<properties 
   pageTitle="SQL 데이터베이스 동적 데이터 마스킹 시작(Azure Preview 포털)" 
   description="Azure Preview 포털에서 SQL 데이터베이스 동적 데이터 마스킹을 시작하는 방법" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman"
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# SQL 데이터베이스 동적 데이터 마스킹 시작(Azure Preview 포털)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## 개요

SQL 데이터베이스 동적 데이터 마스킹에서는 권한이 없는 사용자에 대해 중요한 데이터를 마스킹해 표시함으로써 데이터 노출을 제한합니다. 동적 데이터 마스킹은 V12 버전 Azure SQL 데이터베이스의 기본, 표준 및 프리미엄 서비스 계층에서 미리 보기로 제공됩니다.

동적 데이터 마스킹을 수행하면 고객이 응용 프로그램 레이어에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 동적 데이터 마스킹은 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다. 이때 데이터베이스의 데이터 자체는 변경되지 않습니다.

예를 들어 콜 센터 지원 담당자는 주민 등록 번호나 신용 카드 번호 중 몇 자리로 발신자를 식별할 수 있지만 이러한 데이터 항목이 지원 담당자에게 완전히 노출되어서는 안 됩니다. 이러한 경우 개발자는 결과 집합에서 주민 등록 번호 또는 신용 카드 번호의 마지막 4자리를 제외한 모든 숫자를 마스킹하도록 각 쿼리 결과에 적용할 마스킹 규칙을 정의할 수 있습니다. 또 다른 예로, 개발자는 적합한 데이터 마스크를 사용하여 PII(개인 식별이 가능한 정보) 데이터를 보호하여 준수 규정을 위반하지 않고 문제 해결을 위해 프로덕션 환경을 쿼리할 수 있습니다.

## SQL 데이터베이스 동적 데이터 마스킹 관련 기본 사항

SQL 데이터베이스 구성 블레이드에서 동적 데이터 마스킹 작업을 선택하여 Azure Preview 포털에서 동적 데이터 마스킹 정책을 설정합니다. 동적 데이터 마스킹을 설정하기 전에 ["하위 클라이언트"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)를 사용 중인지 확인합니다.


### 동적 데이터 마스킹 사용 권한

Azure 데이터베이스 관리자, 서버 관리자 또는 보안 관리자 역할이 동적 데이터 마스킹을 구성할 수 있습니다.

### 동적 데이터 마스킹 정책

* **권한 있는 로그인** - SQL 쿼리 결과에서 마스킹되지 않은 데이터를 가져올 수 있는 로그인 집합입니다.
  
* **마스킹 규칙** - 마스킹하도록 지정되는 필드와 사용할 마스킹 기능을 정의하는 규칙 집합입니다. 데이터베이스 테이블 이름과 열 이름을 사용하여 마스킹하도록 지정되는 필드를 정의할 수 있습니다.

* **마스킹 함수** - 각 시나리오에서 데이터 표시를 제어하는 메서드 집합입니다.

| 마스킹 기능 | 마스킹 논리 |
|----------|---------------|
| **기본값** |**지정된 필드의 데이터 형식에 따른 전체 마스킹**<br/><br/>• 문자열 데이터 형식(nchar, ntext, nvarchar)의 필드 크기가 8자 미만이면 XXXXXXXX개 혹은 그 이하의 X를 사용합니다.<br/>• 숫자 데이터 형식(bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real)의 경우 0 값을 사용합니다.<br/>• 날짜/시간 데이터 형식(date, datetime2, datetime, datetimeoffset, smalldatetime, time)의 경우 01-01-1900을 사용합니다.<br/>• SQL 변형의 경우 현재 형식의 기본값이 사용됩니다.<br/>XML의 경우 <masked/> 문서가 사용됩니다.<br/>• 특수 데이터 형식(타임스탬프 테이블, hierarchyid, GUID, 이진, 이미지, varbinary 공간 형식)의 경우 빈 값을 사용합니다.
| **신용 카드** |**지정된 필드의 마지막 4자리를 표시**하고 신용 카드 형식의 접두사로 상수 문자열을 추가하는 마스킹 메서드입니다.<br/><br/>XXXX-XXXX-XXXX-1234|
| **주민등록 번호** |**지정된 필드의 마지막 2자리를 표시**하고 주민 등록 번호 형식의 접두사로 상수 문자열을 추가하는 마스킹 메서드입니다.<br/><br/>XXX-XX-XX12 |
| **Email** | 메일 주소 형식의 상수 문자열 접두사를 사용하여 **첫 글자를 표시하고 도메인을 XXX.com으로 바꾸는 마스킹 메서드**입니다.<br/><br/>aXX@XXXX.com |
| **난수** | 선택한 경계 및 실제 데이터 형식에 따라 **난수를 생성하는 마스킹 메서드**입니다. 지정된 경계가 같으면 마스킹 함수로 상수가 사용됩니다.<br/><br/>![탐색 창](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **사용자 지정 텍스트** | **첫 문자와 마지막 문자를 표시**하고 가운데에 사용자 지정 패딩 문자열을 추가하는 마스킹 메서드입니다.<br/>접두사[패딩]접미사<br/><br/>![탐색 창](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### 보안 지원 연결 문자열

["하위 클라이언트"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)를 사용하는 경우 수정된 연결 문자열 형식을 사용하도록 기존 클라이언트(예: 응용 프로그램)를 업데이트해야 합니다. 자세한 내용을 보려면 [여기](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)를 클릭하세요.

## Azure Preview 포털을 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Preview 포털을 시작합니다.
	 
2. 마스킹할 중요한 데이터가 포함된 데이터베이스의 구성 블레이드로 이동합니다.
	
3. **동적 데이터 마스킹** 타일을 클릭하여 **동적 데이터 마스킹** 구성 블레이드를 시작합니다.

	* 아래쪽의 **작업** 섹션으로 스크롤한 다음 **동적 데이터 마스킹**을 클릭해도 됩니다.
	 
	![탐색 창](./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png)<br/><br/>

4. **동적 데이터 마스킹** 구성 블레이드에서 **마스크 추가**를 클릭하여 **마스킹 규칙 추가** 구성 블레이드를 엽니다.

	![탐색 창](./media/sql-database-dynamic-data-masking-get-started/5_ddm_policy_tile.png)<br/><br/>

5. **테이블** 및 **열**을 선택하여 마스킹하도록 지정되는 필드를 정의합니다.

6. 중요한 데이터 마스킹 범주 목록에서 **마스킹 필드 형식**을 선택합니다.

	![탐색 창](./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png)<br/><br/>

7. 데이터 마스킹 규칙 블레이드에서 **저장**을 클릭하여 동적 데이터 마스킹 정책의 마스킹 규칙 집합을 업데이트합니다.

8. 마스킹되지 않은 중요한 데이터에 액세스할 수 있어야 하는 권한이 있는 로그인을 입력합니다.
 
	![탐색 창](./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png)

	>[AZURE.TIP]응용 프로그램 레이어에서 권한이 있는 응용 프로그램 사용자에 대해 중요한 데이터를 표시할 수 있도록 지정하려면 데이터베이스 쿼리에 사용되는 응용 프로그램의 SQL 로그인을 추가합니다. 중요한 데이터의 노출을 최소화하려면 이 목록에 최소한의 로그인만 포함하는 것이 좋습니다.

9. 데이터 마스킹 구성 블레이드에서 **저장**을 클릭하여 신규 또는 업데이트된 마스킹 정책을 저장합니다.

10. ["하위 클라이언트"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)를 사용하는 경우 수정된 연결 문자열 형식을 사용하도록 기존 클라이언트(예: 응용 프로그램)를 업데이트해야 합니다. 자세한 내용은 [하위 클라이언트](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)를 참조하세요.

## PowerShell cmdlet을 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정

[Azure SQL 데이터베이스 cmdlet](https://msdn.microsoft.com/library/azure/mt163521.aspx)을 참조하세요.


## REST API를 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정

[Azure SQL 데이터베이스에 대한 작업](https://msdn.microsoft.com/library/dn505719.aspx)을 참조하세요.

<!---HONumber=Oct15_HO3-->