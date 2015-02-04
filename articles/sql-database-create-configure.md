<properties urlDisplayName="How to create and configure an Azure SQL DB" pageTitle="Azure SQL 데이터베이스를 만들고 구성하는 방법 - Azure 자습서" metaKeywords="Create Azure SQL Database, Configure Azure SQL Database" description="Azure SQL 데이터베이스를 만들고 구성하는 방법입니다." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure an Azure SQL Database" authors="sidneyh" solutions="" manager="jhubbard" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

<h1><a id="configLogical"></a>Azure SQL 데이터베이스를 만들고 구성하는 방법</h1>

이 항목에서는 Azure 관리 포털을 사용하여 Azure SQL 데이터베이스를 만들고 구성합니다. 이 워크플로에서는 먼저 서버를 만듭니다. 업로드할 기존 SQL Server 데이터베이스가 있는 경우 이 접근 방법을 선호할 수 있습니다.

## 목차##
* [방법: 논리 서버 만들기](#createLogical)
* [방법: 논리 서버용 방화벽 구성](#configFWLogical)

<h2><a id="createLogical"></a>방법: 논리 서버 만들기</h2>

1. [관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. 페이지 맨 아래에 있는 **새로 만들기**를 클릭합니다.

	![Click SQL Databases][1]

3. **데이터 서비스**, **SQL 데이터베이스**, **빠른 생성**을 차례로 클릭합니다.

	![Click New, Data Services, and Quick Create][2]
	 
5. **SQL 데이터베이스 서버 설정**에서 구독을 선택합니다.

	![Select a subscription][3]

6. **SQL 데이터베이스 설정**에서 로그인 이름을 공백 없이 한 단어로 입력합니다. 

	SQL 데이터베이스는 암호화된 연결을 통해 SQL 인증을 사용합니다. sysadmin 고정 서버 역할에 할당된 새 SQL Server 인증 로그인은 사용자가 제공한 이름을 사용하여 만들어집니다. 

	이 로그인은 메일 주소, Windows 사용자 계정 또는 Windows Live ID일 수 없습니다. SQL 데이터베이스에서는 클레임과 Windows 인증 모두 지원되지 않습니다.
	
7. 대문자 및 소문자 값과 숫자 또는 기호를 조합하여 8자 이상의 강력한 암호를 입력합니다.

7. 지역을 선택합니다. 지역은 서버의 지리적 위치를 결정합니다. 지역은 쉽게 바꿀 수 없으므로 이 서버에 맞는 지역을 선택하세요. 사용자에게 가장 가까운 위치를 선택합니다. Azure 응용 프로그램 및 데이터베이스를 동일한 지역에 유지하면 발신용 대역폭 비용과 데이터 대기 시간이 절약됩니다.

8. 관리 포털, 저장소 서비스 및 Azure의 기타 서비스를 사용하여 이 데이터베이스에 연결할 수 있도록 **Microsoft Azure 서비스가 서버에 액세스할 수 있도록 허용합니다.** 옵션을 선택된 상태로 두어야 합니다. 

9. 완료하면 페이지 맨 아래에 있는 확인 표시를 클릭합니다.

### 서버 이름 자동 생성

서버 이름을 지정하지 않았다는 점에 유의하세요. SQL 데이터베이스는 서버 이름을 자동 생성하여 중복 DNS 항목이 없도록 합니다. 서버 이름은 10자로 된 영숫자 문자열입니다. SQL 데이터베이스 서버의 이름은 변경할 수 없습니다.

다음 단계에서는 네트워크에서 실행 중인 응용 프로그램의 연결이 허용된 액세스가 되도록 방화벽을 구성합니다.

## 방법: 논리 서버용 방화벽 구성

1. [관리 포털](http://manage.windowsazure.com)에서 **SQL 데이터베이스**를 클릭한 다음 **서버**를 클릭합니다.

	![Click Servers][4]
2. 목록에서 방금 만든 서버를 클릭합니다.

2. **구성**을 클릭합니다.

	![Click Configure][5]

3. **허용된 IP 주소** 섹션에서 **허용된 IP 주소에 추가**를 클릭합니다. 이 주소는 라우터 또는 프록시 서버가 현재 수신 대기 중인 IP 주소입니다. SQL 데이터베이스는 현재 연결에 사용되는 IP 주소를 검색하고 이 장치의 연결 요청을 허용하는 방화벽 규칙을 만듭니다. 
	![Click Add to the allowed IP addresses][6]

	규칙에 대한 기본 이름이 생성됩니다. 원하는 대로 이름을 편집합니다. 
	

4. 다른 컴퓨터에서 데이터베이스에 연결하는 경우에는 이 컴퓨터의 IP 주소에서 연결하도록 허용하는 새 규칙을 만들어야 합니다. 시작 및 끝 상자를 사용하여 IP 주소 범위를 만듭니다.

	클라이언트 컴퓨터가 동적으로 할당된 IP 주소를 사용하는 경우 네트워크의 컴퓨터에 할당된 IP 주소를 포함할 만큼 범위를 넓게 지정할 수 있습니다. 좁은 범위로 시작한 후 필요한 경우에만 확장합니다.

7. 페이지 아래쪽에서 **저장**을 클릭하여 단계를 완료합니다. 

이제 논리 서버, IP 주소의 인바운드 연결을 허용하는 방화벽 규칙 및 관리자 로그인이 있습니다. 

**참고:** 방금 만든 논리 서버는 가상이며 데이터 센터의 물리적 서버에서 동적으로 호스트됩니다. 서버를 삭제하면 복구할 수 없습니다. 이후에 서버에 업로드할 모든 데이터베이스를 백업해야 합니다. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png

