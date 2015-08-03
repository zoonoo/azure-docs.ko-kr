<properties
	pageTitle="SQL 데이터베이스 시작"
	description="클라우드에 제공되는 Microsoft의 RDBMS(관계형 데이터베이스 관리 서비스)인 Azure SQL 데이터베이스에서 Azure 포털 및 AdventureWorks 샘플 데이터베이스를 사용하여 불과 몇 분이면 여러분의 첫 번째 클라우드 데이터베이스를 만들 수 있습니다."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/15/2015"
	ms.author="genemi"/>


# 첫 Azure SQL 데이터베이스 만들기


이 문서에서는 5분 안에 샘플 Azure SQL 데이터베이스를 만드는 방법을 보여줍니다. 이 문서에서 배울 내용은 다음과 같습니다.


- [Azure 포털](http://portal.azure.com/)을 사용하여 논리 서버 프로비전
- 예제 데이터로 채워진 데이터베이스 만들기
- 데이터베이스에 액세스할 수 있는 IP를 구성하기 위한 데이터베이스 방화벽 규칙 설정


이 자습서는 여러분이 Azure를 구독 중이라고 가정합니다. 아직 구독하지 않는 분은 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.


## 1단계: 로그인


1. [Azure 포털](http://portal.azure.com/)에 로그인합니다.
2. **새로 만들기** > **데이터 + 저장소** > **SQL 데이터베이스**를 클릭합니다.


![새 SQL 데이터베이스][1]


## 2단계: 논리 서버 만들기



1. SQL 데이터베이스 블레이드에서 데이터베이스 **이름**을 선택합니다. 이 예의 경우 **AdventureWorks**입니다.
2. 데이터베이스에 대한 논리 서버를 만들려면 **서버**를 클릭한 다음 **새 서버 만들기**를 클릭합니다.


## 3단계: 서버 구성


1. **서버** 블레이드에서 기억하기 쉬운 고유한 **서버 이름**을 입력합니다.
2. **서버 관리자 로그인**으로 **AdventureAdmin**을 입력합니다.
3. **암호** 및 **암호 확인**에 올바른 값을 입력합니다.
4. 기본 지리적 **위치**를 선택합니다. 일반적으로 응용 프로그램이 실행되는 위치와 가까운 위치여야 합니다.
5. **확인**을 클릭합니다.


![서버 만들기][2]


## 4단계: 데이터베이스 만들기


1. SQL 데이터베이스 블레이드에서 **원본 선택**을 클릭하여 데이터베이스 원본을 지정합니다.
 - 이 단계를 건너뛰면 빈 데이터베이스가 생성됩니다.
2. **샘플**을 선택합니다.
 - 그러면 **AdventureWorks**라는 이름의 표준 샘플 데이터베이스 복사본이 생성됩니다.
 - Azure SQL 데이터베이스에서는 AdventureWorks의 *라이트 스키마* 버전이 사용됩니다.
3. 블레이드 하단의 **만들기**를 클릭합니다.


## 5단계: 방화벽 구성


다음 단계는 데이터베이스에 액세스할 수 있는 IP 주소 범위를 지정하는 방법을 보여줍니다.


![서버 찾아보기][3]


1. 화면 왼쪽의 리본 메뉴에서 **찾아보기**를 클릭한 다음 **SQL 서버**를 클릭합니다.
2. 제공되는 옵션 중에서 이전에 만든 SQL 서버를 클릭합니다.
3. **설정**을 클릭한 다음 **방화벽**을 클릭합니다.
4. [Bing](http://www.bing.com/search?q=my%20ip%20address)에서 현재 IP 주소를 확인하려면 이 링크를 클릭하세요.
5. 방화벽 설정에서 **규칙 이름**을 입력하고, 이전 단계에서 확인한 공용 IP 주소를 **시작 IP** 및 **끝 IP** 필드에 붙여 넣습니다.
6. 작업을 완료한 후 페이지 상단의 **저장**을 클릭합니다.


![방화벽][4]


## 다음 단계


이제 데이터베이스에 연결할 수 있는 소형 클라이언트 프로그램을 작성할 준비가 되었습니다. 빠른 시작 코드 샘플을 얻으려면 다음 링크 중 하나를 클릭하세요.


- [C#을 사용하여 SQL 데이터베이스에 연결 및 쿼리하기](sql-database-connect-query.md)
- *출시 예정:* SQL 데이터베이스에 대한 클라이언트 개발 및 빠른 시작 샘플


<!-- Media references. -->
[1]: ./media/sql-database-get-started/GettingStarted_NewDB.PNG
[2]: ./media/sql-database-get-started/GettingStarted_CreateServer.png
[3]: ./media/sql-database-get-started/GettingStarted_BrowseServer.png
[4]: ./media/sql-database-get-started/GettingStarted_FireWall.png
 

<!---HONumber=July15_HO4-->