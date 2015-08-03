<properties
   pageTitle="SQL 데이터 웨어하우스와 함께 Azure 스트림 분석 사용 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스와 함께 Azure 스트림 분석 사용을 위한 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# SQL 데이터 웨어하우스와 함께 Azure 스트림 분석 사용

Azure 스트림 분석은 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. [Azure 스트림 분석 소개][]를 읽어 기본 사항을 배울 수 있습니다. [자습서 시작][]에서 스트림 분석으로 종단간 솔루션을 만드는 방법에 대해 알 수 있습니다.

이 문서에서 스크림 분석 작업에 대한 출력 싱크로 Azure SQL 데이터 웨어하우스 데이터베이스를 사용하는 방법에 대해 배웁니다.

## 필수 조건

먼저, [자습서 시작][] 자습서에서 다음 단계를 실행합니다.

1. 이벤트 허브 입력 만들기
2. 이벤트 생성기 응용 프로그램 구성 및 시작
3. 스트림 분석 작업 프로비전
4. 작업 입력 및 쿼리 지정

그런 다음 SQL 데이터 웨어하우스 데이터베이스를 만듭니다.

## 작업 출력 지정: Azure SQL 데이터 웨어하우스 데이터베이스

### 1단계
스트림 분석 작업에서 페이지 위쪽의 **출력**을 클릭한 다음 **출력 추가**를 클릭합니다.

### 2단계
SQL 데이터베이스를 선택하고 다음을 클릭합니다. ![][Add Output]

### 3단계
다음 페이지에서 다음 값을 입력합니다. - 출력 별칭: 이 작업 출력에 대한 이름을 입력합니다. - 구독:- SQL 데이터 웨어하우스 데이터베이스가 스트림 분석 작업과 동일한 구독에 포함된 경우 현재 구독에서 SQL 데이터베이스 사용을 선택합니다. - 데이터베이스가 다른 구독에 있는 경우 다른 구독에서 SQL 데이터베이스 사용을 선택합니다. - 데이터베이스: 대상 데이터베이스의 이름을 지정합니다. - 서버 이름: 방금 지정한 데이터베이스에 대한 서버 이름을 지정합니다. Azure 포털을 사용하여 찾을 수 있습니다.

![][Server Name]

- 사용자 이름: 데이터베이스에 대한 쓰기 권한이 있는 계정의 사용자 이름을 입력합니다.
- 암호: 지정된 사용자 계정에 대한 암호를 제공합니다.
- 테이블: 데이터베이스에서 대상 테이블의 이름을 지정합니다.

![][Add Database]

### 4단계
확인 단추를 클릭하여 이 작업 출력을 추가하고 스트림 분석이 데이터베이스에 성공적으로 연결될 수 있는지 확인합니다.

![][Test Connection]

데이터베이스에 대한 연결이 성공하면 포털의 맨 아래에 알림이 표시됩니다. 아래의 연결 테스트를 클릭하여 데이터베이스에 대한 연결을 테스트할 수 있습니다.




## 다음 단계
통합 개요는 [SQL 데이터 웨어하우스 통합 개요][]를 참조하세요. 더 많은 개발 팁은 [SQL 데이터 웨어하우스 개발 개요][]를 참조하세요.

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Azure 스트림 분석 소개]: ./stream-analytics-introduction/
[자습서 시작]: ./articles/stream-analytics-get-started/
[SQL 데이터 웨어하우스 개발 개요]: ./sql-data-warehouse-overview-develop/
[SQL 데이터 웨어하우스 통합 개요]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=July15_HO4-->