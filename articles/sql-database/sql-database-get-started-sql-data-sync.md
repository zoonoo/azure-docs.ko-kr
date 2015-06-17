<properties 
	pageTitle="SQL 데이터베이스 데이터 동기화 시작" 
	description="이 자습서는 Azure SQL 데이터 동기화를 시작하도록 도와줍니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="3/2/2015" 
	ms.author="spelluru"/>


# Azure SQL 데이터 동기화 시작(미리 보기)
이 자습서에서는 Azure(미리 보기) 포털을 사용하여 Azure SQL 데이터 동기화의 기본 사항에 대해 알아봅니다. 

이 자습서에서는 SQL Server 및 Azure SQL 데이터베이스를 사용한 경험이 매우 적다고 가정합니다. 이 자습서에서는 완전하게 구성되고 설정된 일정에 동기화되는 하이브리드(SQL Server 및 SQL 데이터베이스 인스턴스) 동기화 그룹을 만듭니다.


## 1단계: Azure SQL 데이터베이스에 연결

1. [관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. 왼쪽 창에서 **SQL 데이터베이스**를 클릭합니다.

3. 페이지 아래쪽에서 **동기화**를 클릭합니다. 동기화를 클릭하면 추가할 수 있는 항목인 **새 동기화 그룹** 및 **새 동기화 에이전트**가 표시된 목록이 나타납니다.

4. 새 SQL 데이터 동기화 에이전트 마법사를 시작하려면 **새 동기화 에이전트**를 클릭합니다.

5. 이전에 에이전트를 추가한 적이 없는 경우 **여기로 다운로드를 클릭**합니다.

	![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## 2단계: 클라이언트 에이전트 추가
이 단계는 온-프레미스 SQL Server 데이터베이스를 동기화 그룹에 포함하려는 경우에만 필요합니다. 동기화 그룹에 SQL 데이터베이스 인스턴스만 있는 경우 4단계: 동기화 그룹 만들기로 건너뛸 수 있습니다.

<h3><a id="InstallRequiredSoftware"></a>2a 단계: 필수 소프트웨어 설치</h3>
클라이언트 에이전트를 설치하는 컴퓨터에 다음 소프트웨어가 설치되어 있는지 확인해야 합니다.

- **.NET Framework 4.0** 

 .NET Framework 4.0은 [여기](http://go.microsoft.com/fwlink/?linkid=205836)서 설치할 수 있습니다.

- **Microsoft SQL Server 2008 R2 SP1 System CLR Types(x86)**

 Microsoft SQL Server 2008 R2 SP1 System CLR Types(x86)는 [여기](http://www.microsoft.com/download/en/details.aspx?id=26728)서 설치할 수 있습니다.

- **Microsoft SQL Server 2008 R2 SP1 공유 관리 개체(x86)**

 Microsoft SQL Server 2008 R2 SP1 공유 관리 개체(x86)는 [여기](http://www.microsoft.com/download/en/details.aspx?id=26728)서 설치할 수 있습니다.


<h3><a id="InstallClient"></a>2b 단계: 새 클라이언트 에이전트 설치</h3>

[클라이언트 에이전트 설치(SQL 데이터 동기화)](http://msdn.microsoft.com/library/jj823137.aspx)의 지침에 따라 에이전트를 설치합니다. 


<h3><a id="RegisterSSDb"></a>2c 단계: 새 SQL 데이터 동기화 에이전트 마법사 완료</h3> 

1. 	새 SQL 데이터 동기화 에이전트 마법사로 돌아갑니다.
2.	에이전트에 의미 있는 이름을 지정합니다.
3.	드롭다운에서 이 에이전트를 호스트할 **지역**(데이터 센터)을 선택합니다.
4.	드롭다운에서 이 에이전트를 호스트할 **구독**을 선택합니다.
5.	오른쪽 화살표를 클릭합니다.



## 3단계: 클라이언트 에이전트에 SQL Server 데이터베이스 등록

클라이언트 에이전트가 설치된 후 동기화 그룹에 포함하려는 모든 온-프레미스 SQL Server 데이터베이스를 에이전트에 등록합니다.
에이전트에 데이터베이스를 등록하려면 [클라이언트 에이전트에 SQL Server 데이터베이스 등록](http://msdn.microsoft.com/library/jj823138.aspx)의 지침을 따릅니다.



## 4단계: 동기화 그룹 만들기

<h3><a id="StartNewSGWizard"></a>4a 단계: 새 동기화 그룹 마법사 시작</h3>
1.	[관리 포털](http://manage.windowsazure.com)로 돌아갑니다.
2.	**SQL 데이터베이스**를 클릭합니다.
3.	페이지 아래쪽에서 **동기화 추가**를 클릭하고 서랍에서 새 동기화 그룹을 선택합니다.

	![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)


<h3><a id=""></a>4b 단계: 기본 설정 입력</h3>	
1.	동기화 그룹의 의미 있는 이름을 입력합니다.
2.	드롭다운에서 이 동기화 그룹을 호스트할 **지역**(데이터 센터)을 선택합니다.
3. 오른쪽 화살표를 클릭합니다.

	![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)
 
<h3><a id="DefineHubDB"></a>4c 단계: 동기화 허브 정의</h3>
1. 드롭다운에서 동기화 그룹 허브로 사용할 SQL 데이터베이스 인스턴스를 선택합니다.
2. 이 SQL 데이터베이스 인스턴스의 자격 증명인 **허브 사용자 이름** 및 **허브 암호**를 입력합니다.
3. SQL 데이터 동기화에서 사용자 이름 및 암호를 확인할 때까지 기다립니다. 자격 증명이 확인되면 암호 오른쪽에 녹색 확인 표시가 나타납니다.
4. 드롭다운에서 **충돌 해결** 정책을 선택합니다.

 **허브 우선** - 허브 데이터베이스에 기록된 변경 내용이 참조 데이터베이스에 기록되고 동일한 참조 데이터베이스 레코드의 변경 내용을 덮어씁니다. 기능적으로 허브에 기록된 첫 번째 변경 사항이 다른 데이터베이스로 전파됨을 의미합니다.


 **클라이언트 우선** - 참조 데이터베이스의 변경 내용이 허브에 기록된 변경 내용을 덮어씁니다. 기능적으로 허브에 기록된 마지막 변경 사항이 유지되고 다른 데이터베이스로 전파됨을 의미합니다.

5.	오른쪽 화살표를 클릭합니다.

	![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<h3><a id="AddRefDB"></a>4d 단계: 참조 데이터베이스 추가</h3>

동기화 그룹에 추가할 데이터베이스마다 이 단계를 반복합니다.

1. 드롭다운에서 추가할 데이터베이스를 선택합니다.

	드롭다운의 데이터베이스에는 에이전트에 등록된 SQL Server 데이터베이스와 SQL 데이터베이스 인스턴스가 모두 포함되어 있습니다.
2.	이 데이터베이스의 자격 증명인 **사용자 이름** 및 **암호**를 입력합니다.
3.	드롭다운에서 이 데이터베이스의 **동기화 방향**을 선택합니다.

	**양방향** - 참조 데이터베이스의 변경 내용이 허브 데이터베이스에 기록되고 허브 데이터베이스의 변경 내용이 참조 데이터베이스에 기록됩니다.

	**허브에서 동기화** - 데이터베이스가 허브에서 업데이트를 받습니다. 데이터베이스는 허브로 변경 사항을 보내지 않습니다.

	**허브로 동기화** - 데이터베이스가 허브로 업데이트를 보냅니다. 허브의 변경 사항은 이 데이터베이스에 기록되지 않습니다.

4.	동기화 그룹 만들기를 완료하려면 마법사 오른쪽 아래의 확인 표시를 클릭합니다. SQL 데이터 동기화에서 자격 증명을 확인할 때까지 기다립니다. 녹색 확인은 자격 증명이 확인되었음을 나타냅니다.

5.	확인 표시를 두 번째로 클릭합니다. 이렇게 하면 SQL 데이터베이스 아래의 **동기화** 페이지로 돌아갑니다. 이 동기화 그룹은 이제 다른 동기화 그룹 및 에이전트와 함께 나열되어 있습니다.

	![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## 5단계: 동기화할 데이터 정의

Azure SQL 데이터 동기화에서는 동기화할 테이블과 열을 선택할 수 있습니다. 특정 값(예: Age>=65)이 있는 행만 동기화되도록 열을 필터링하려는 경우에도 Azure의 SQL 데이터 동기화 포털과 동기화할 테이블, 열 및 행 선택의 설명서를 사용하여 동기화할 데이터를 정의합니다.

1.	[관리 포털](http://manage.windowsazure.com)로 돌아갑니다.
2.	**SQL 데이터베이스**를 클릭합니다.
3.	**동기화** 탭을 클릭합니다.
4.	이 동기화 그룹의 이름을 클릭합니다.
5.	**동기화 규칙** 탭을 클릭합니다.
6.	동기화 그룹 스키마를 제공하려는 데이터베이스를 선택합니다.
7.	오른쪽 화살표를 클릭합니다.
8.	**스키마 새로 고침**을 클릭합니다.
9.	데이터베이스의 테이블마다 동기화에 포함할 열을 선택합니다. 
	- 지원되지 않는 데이터 형식이 사용된 열은 선택할 수 없습니다. 
	- 테이블의 열이 하나도 선택되지 않으면 해당 테이블은 동기화 그룹에 포함되지 않습니다. 
	- 테이블을 모두 선택/선택 취소하려면 화면 아래쪽의 선택을 클릭합니다.
10.	**저장**을 클릭하고 동기화 그룹에서 프로비전을 완료하기를 기다립니다.
11.	데이터 동기화 방문 페이지로 돌아가려면 화면 왼쪽 위(동기화 그룹 이름 위)에 있는 뒤로 화살표를 클릭합니다.

	![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## 6단계: 동기화 그룹 구성

언제든 데이터 동기화 방문 페이지 아래쪽의 동기화를 클릭하여 동기화 그룹을 동기화할 수 있습니다.
동기화 그룹을 일정에 따라 동기화하려면 동기화 그룹을 구성합니다.

1.	[관리 포털](http://manage.windowsazure.com)로 돌아갑니다.
2.	**SQL 데이터베이스**를 클릭합니다.
3.	**동기화** 탭을 클릭합니다.
4.	이 동기화 그룹의 이름을 클릭합니다.
5.	**구성** 탭을 클릭합니다.
6.	**자동 동기화**
	- 동기화 그룹이 설정된 주기에 동기화하도록 구성하려면 **설정**을 클릭합니다. 동기화를 클릭하면 요청 시 계속 동기화할 수 있습니다.
	- 동기화 그룹이 동기화를 클릭하는 경우에만 동기화하도록 구성하려면 **해제**를 클릭합니다.
7.	**동기화 빈도**
	- 자동 동기화가 설정된 경우 동기화 주기를 설정합니다. 주기는 5분에서 1개월 사이여야 합니다.
8.	**저장**을 클릭합니다.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

축하합니다. SQL 데이터베이스 인스턴스와 SQL Server 데이터베이스가 모두 포함된 동기화 그룹을 만들었습니다.

## 다음 단계
SQL 데이터베이스와 SQL 데이터 동기화에 대한 자세한 내용은 다음을 참조하세요.

* [SQL 데이터베이스에 프리미엄 서비스 등록] (sign-up-for-sql-database-premium.md)
* [SQL 데이터 동기화(WA 포털)](http://msdn.microsoft.com/library/windowsazure/jj856263.aspx)
* [Azure SQL 데이터베이스 시작](../getting-started-w-sql-databases.md)
* [SQL Server 데이터베이스 수명 주기](http://go.microsoft.com/fwlink/?LinkId=275193)










<!--HONumber=47-->
 