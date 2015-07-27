<properties
   pageTitle="시작: SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에 연결 및 일부 쿼리 실행 시작"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>
   
# 시작: SQL 데이터 웨어하우스에 연결
이 빠른 시작 문서에서는 다른 두 가지 도구를 사용한 SQL 데이터 웨어하우스의 인스턴스에 연결 및 쿼리를 소개합니다.

**Visual Studio** - Visual Studio의 통합된 코드 편집기와 디버거인 SQL Server 데이터 도구(SSDT)는 SQL DW과 완벽하게 호환되어 SQL 데이터 웨어하우스에 쉽게 연결하고, 쿼리하고, 관리할 수 있습니다.

**sqlcmd** - sqlcmd는 간단한 연결 및 쿼리 기능을 제공하는 명령줄 도구입니다.

이 문서를 완료하면 다음이 수행되어 있습니다.

1. 설치 및 업데이트된 Visual Studio 2013
2. SSDT에서 SQL 데이터 웨어하우스에 대 한 연결을 만듦
3. SQL 데이터 웨어하우스 데이터베이스에 대해 실행된 쿼리

>[AZURE.NOTE]프로비전 가이드를 완료하거나 SQL 데이터 웨어하우스를 사용할 수 있다고 가정합니다. SQL 데이터 웨어하우스를 프로비전하지 않은 경우 [프로비전 빠른 시작]을 다시 참조하세요.

## 개발을 위한 Visual Studio 설정##
개발을 위해 SQL 데이터 웨어하우스 팀은 SQL Server Data Tools와 함께 Visual Studio 2013 이상을 사용할 것을 권장합니다. 다음은 visual studio의 실행 가능한 버전이 아직 설치되지 않은 경우 Visual Studio 2013을 다운로드 및 업데이트하는 방법을 간략하게 설명합니다.

자세한 내용을 찾으려는 경우, [전체 SSDT 설명서](https://msdn.microsoft.com/library/azure/hh272686.aspx)를 사용하여 일반 SSDT 질문을 해결할 수 있습니다.

### Visual Studio 2013 가져오기 ###
Visual Studio 2013 웹 사이트로 이동하여 Visual Studio의 복사본을 다운로드하고 설치합니다. SQL 데이터 웨어하우스의 경우 무료 버전이 더욱 적합합니다. 필요에 맞게 하나를 선택합니다.

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Visual Studio 2013 가져오기</a>

### Visual Studio 2013 업데이트 ###
이미 Visual Studio 2013이 설치되어 있습니까? 잘하셨습니다. 최신임을 확인하려면 다음 단계를 수행합니다. 다음 단계로 이동할 수 있습니다.

1. Visual Studio 2013을 엽니다.
2. "도구" 메뉴를 선택하고 "확장 및 업데이트..."를 선택합니다.
3. 트리 컨트롤을 통해 "업데이트" 및 "제품 업데이트"로 이동합니다.
4. 사용 가능한 업데이트가 없는 경우 "확장 및 업데이트" 창을 안전하게 닫고 이 빠른 시작에서 다음 작업을 계속 진행합니다.

그러나 Visual Studio 자체에 대한 업데이트가 있으면 업데이트 단추를 클릭합니다. 그러면 Visual Studio 2013에 대한 최신 업데이트의 다운로드를 시작합니다.

"확장 및 업데이트" 창을 닫고 계속하기 전에 Visual Studio 2013도 종료합니다.

5. "실행" 단추를 클릭하여 Visual Studio 2013에 최신 업데이트를 설치합니다.

6. 사용 조건에 동의하고 모든 사용자 계정 컨트롤(UAC) 프롬프트를 허용하는 설치 단추를 클릭합니다.

7. 설치를 완료하려면 "시작" 단추를 클릭합니다.

Visual Studio 2013의 업데이트가 완료됩니다.

### SSDT 업데이트 
또한 SSDT를 업데이트해야 할 수 있습니다. 이는 상당히 정상적인 것입니다. SSDT 엔지니어는 새로운 기능으로 매우 자주 플러그인을 업데이트하므로 가끔 업데이트해야 합니다. 이 또한 매우 간단한 프로세스입니다. SSDT를 업데이트해야 하는 지 확인하려면 다음 단계를 수행합니다.

1. Visual Studio 2013을 엽니다.  
2. "도구" 메뉴를 선택하고 "확장 및 업데이트..."를 선택합니다.
3. 트리 컨트롤을 통해 "업데이트" 및 "제품 업데이트"로 이동합니다.
4. 사용 가능한 업데이트가 없는 경우 "확장 및 업데이트" 창을 안전하게 닫고 이 빠른 시작에서 다음 작업을 계속 진행합니다.

그러나 "데이터베이스 도구에 대한 Microsoft SQL Server 업데이트"라는 업데이트가 존재하는 경우 업데이트 단추를 클릭합니다.

그러면 최신 SSDT 버전의 다운로드를 시작합니다. 아래 이미지는 Internet Explorer 다운로드 관리자에서 SSDTSetup.exe를 보여줍니다.

5. "실행" 단추를 클릭하여 최신 버전의 SSDT를 설치합니다.

6. 사용 조건에 동의하고 설치 단추를 클릭합니다.

7. SSDT 업데이트를 완료하려면 "닫기" 단추를 클릭합니다.

8. "확장 및 업데이트" 창 닫기

이제 바탕 화면에는 최신 SSDT 확장명이 있는 최신 버전의 Visual Studio 2013이 있습니다.

> [AZURE.NOTE]현재 [Visual Studio 2013용 SSDT 미리 보기](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409) 사용을 권장합니다.

## Visual Studio 2013을 사용하여 연결
원하는 버전의 Visual Studio를 실행하는 경우 두 가지 방법으로 SQL 데이터 웨어하우스 인스턴스에 연결할 수 있습니다.

### Visual Studio에서
연결하려면, SQL Server 개체 탐색기를 열고 연결 정보에서 전달하기만 하면 됩니다.

1. Visual Studio 열기
2. "보기" 메뉴를 선택하고 드롭다운 목록에서 "SQL Server 개체 탐색기"를 선택합니다.

> [AZURE.NOTE]SQL Server 개체 탐색기를 선택하고 서버 탐색기는 선택하지 ***마세요*** . 이름은 비슷하게 들리지만 매우 다른 컨트롤입니다. 서로 옆에 있으므로 주의해야 하고 올바르게 선택해야 합니다!

이제 SQL Server 개체 탐색기를 볼 수 있습니다.


3. SQL Server 개체 탐색기에서 "서버 추가" 단추를 클릭합니다. 아래 이미지에서 강조 표시됩니다.

4. 서버에 연결 대화 상자 채우기

논리 서버를 만들 때 선택한 값을 사용합니다.

원하는 경우 "암호 저장" 눈금 확인란을 확인합니다. 좋은 시간 보호기이지만 사용자 프로파일에 대한 실제 액세스 권한을 가진 모든 사람이 이 계정을 사용하여 쿼리를 실행할 수 있습니다.

> [AZURE.NOTE]서버 이름을 정규화해야 합니다. 따라서 사용자가 서버 이름 값은 이 규칙을 따라야 합니다. ***ServerName***.database.windows.net, 여기서 ***ServerName***은 논리 서버에 지정한 이름입니다.

자격 증명을 완전하게 입력하면 연결을 클릭합니다.

이제 연결을 완료하고 SQL Server 개체 탐색기에서 SQLDW 논리 서버를 등록했습니다.
    
### Azure 포털
Azure 포털에서 직접 Visual Studio로 이동합니다.

1. [Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다.
2. '찾아보기' 블레이드 내에서 원하는 SQL DW 인스턴스를 선택합니다. 
3. SQL DW 블레이드 위에서 'Visual에서 열기...'를 선택합니다.
4. 클라이언트 컴퓨터의 IP로 방화벽을 구성하지 않은 경우 '방화벽 구성'을 선택합니다.
	1. '규칙 이름', '시작 IP' 및 '끝 IP'를 입력합니다. 
	2. 블로드 위에서 '저장'을 클릭합니다.   
5. 'Visual Studio에서 열기'를 클릭합니다. 
6. Visual Studio가 열리고 자격 증명을 묻는 메시지가 표시됩니다. 
7. 자격 증명 및 연결을 입력한 후, 서버 및 DW 인스턴스가 이제 SQL Server 개체 탐색기 창에 표시됩니다.  

## sqlcmd를 사용하여 SQL 데이터 웨어하우스에 연결

SQL Server 또는 [SQL Server용 Microsoft 명령줄 유틸리티 11](http://go.microsoft.com/fwlink/?LinkId=321501)에 포함된 [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) 명령 프롬프트로 SQL DW에 연결할 수도 있습니다. Sqlcmd 유틸리티를 사용하면 TRANSACT-SQL 문, 시스템 프로시저 및 명령 프롬프트에 스크립트 파일을 입력할 수 있습니다.

sqlcmd를 사용하는 경우 SQL DW의 특정 인스턴스에 연결하려면 명령 프롬프트를 열고 SQL DW 데이터베이스에 대한 연결 문자열이 뒤에 나오는 *sqlcmd*를 입력합니다. 연결 문자열은 다음 매개 변수를 포함해야 합니다.

+ **사용자 (-U):** `<`사용자`>`@`<`서버 이름`>`.database.windows.net 형식의 서버 사용자
+ **암호(-P):** 사용자와 연결된 암호
+ **서버(-S):**`<`서버 이름`>`.database.windows.net 형식의 서버
+ **데이터베이스(-D):** 데이터베이스 이름 
+ **따옴표 붙은 식별자 설정(-I):** SQL DW 인스턴스에 연결하기 위해 따옴표 붙은 식별자를 사용할 수 있어야 합니다. 

따라서 SQL DW 인스턴스에 연결하려면 다음을 입력합니다.

```
C:>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
```

연결 후, 인스턴스에 대해 지원되는 모든 TRANSACT-SQL 문을 실행할 수 있습니다. 예를 들어, 아래 문은 [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) 문을 활용하여 새 테이블을 만듭니다.

```
C:>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```
	
Sqlcmd 대한 추가 정보는 [sqlcmd 설명서](https://msdn.microsoft.com/library/azure/ms162773.aspx)를 참조하세요.

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## 예제 쿼리 실행 ##

이제 서버를 등록했으므로 계속해서 쿼리를 작성합니다.

1. SSDT에서 사용자 데이터베이스를 클릭합니다.

2. 새 쿼리 단추를 클릭합니다.

   새 창이 이제 열립니다.

3. 쿼리 작성

	쿼리 창에 다음 코드를 입력합니다.

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. 쿼리 실행

	쿼리를 실행하려면, 아래 녹색 화살표를 클릭하거나 다음 바로 가기를 사용합니다. `CTRL`+`SHIFT`+`F5`:

## 다음 단계 ##
[Start developing code]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=July15_HO3-->