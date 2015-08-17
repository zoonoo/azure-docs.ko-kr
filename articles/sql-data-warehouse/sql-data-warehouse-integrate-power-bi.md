<properties
   pageTitle="SQL 데이터 웨어하우스와 함께 Power BI 사용 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스와 함께 Power BI 사용을 위한 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="lodipalm"/>

# SQL 데이터 웨어하우스와 함께 Power BI 사용
Azure SQL 데이터베이스와 함께, SQL 데이터 웨어하우스 직접 연결을 사용하면 사용자는 Power BI의 분석 기능과 함께 강력한 논리 푸시 다운을 활용할 수 있습니다. 직접 연결을 사용하여 쿼리는 데이터를 탐색할 때 실시간으로 Azure SQL 데이터 웨어하우스로 다시 전송됩니다. SQL 데이터 웨어하우스의 크기와 결합되어 사용자는 테라바이트 규모의 데이터에 대해 동적 보고서를 몇 분 내에 만들 수 있습니다. 또한 Power BI 단추에서 열기 소개를 사용하면 사용자는 Azure의 다른 부분에서 정보를 수집하지 않고 Power BI를 직접 SQL 데이터 웨어하우스에 연결할 수 있습니다.

직접 연결을 사용하는 경우 다음을 참고하세요.

+ 연결할 때 정규화된 서버 이름을 지정합니다(자세한 내용은 아래 참조).
+ 데이터베이스에 대한 방화벽 규칙은 "Azure 서비스에 액세스 허용"으로 구성되어야 합니다.
+ 열 선택 또는 필터 추가와 같은 모든 작업은 데이터 웨어하우스에 직접 쿼리됩니다. 
+ 타일은 약 15분 마다 새로 고쳐집니다(새로 고침은 예약하지 않아도 됨).
+ Q&A는 직접 연결 데이터 집합에 대해 사용할 수 없습니다.
+ 스키마 변경 내용은 자동으로 선택되지 됩니다.

계속 환경이 개선되면서 이 제한 사항 및 참고 사항은 변경될 수 있습니다. 연결 단계는 다음과 같습니다.

## 'Power BI에서 열기' 단추 사용
SQL 데이터 웨어하우스와 Power BI 간에 이동 하는 가장 쉬운 방법은 Power BI에서 열기 단추를 사용하는 것입니다. 이 단추를 사용하면 원활하게 Power BI에서 새 대시보드 만들기를 시작할 수 있습니다.

1.	시작하려면 Azure 포털에서 SQL 데이터 웨어하우스 인스턴스로 이동합니다.
2.	Power BI에서 열기 단추를 클릭합니다.
3.	직접 로그인 할 수 없거나 Power BI 계정이 없는 경우 로그인해야 합니다.  
4.	미리 완성된 SQL 데이터 웨어하우스의 정보를 사용하여 SQL 데이터 웨어하우스 연결 페이지로 이동됩니다.
5.  자격 증명을 입력하면, SQL 데이터 웨어하우스에 완전히 연결됩니다. 

## Power BI 포털을 통해 연결
Power BI에서 열기 단추를 사용 하는 것 외에도 사용자는 Power BI 포털을 통해 해당 SQL 데이터 웨어하우스에 연결할 수도 있습니다.

1.   탐색 창 아래쪽에서 데이터 얻기를 클릭합니다.
2.  빅 데이터 및 기타를 선택합니다.
3.  빅 데이터 및 기타 페이지에서 SQL 데이터 웨어하우스를 선택합니다.
4.  필요한 연결 정보를 입력합니다. 아래 섹션의 매개 변수 찾기에서 이 데이터를 찾을 수 있는 위치를 보여줍니다.  
5.	데이터 집합을 드릴인하면서 사용자 데이터베이스에서 모든 테이블 및 열을 탐색할 수 있습니다. 열을 선택하면 소스에 다시 쿼리를 전송하여 비주얼을 동적으로 만듭니다. 이 비주얼은 새 보고서에 저장되며 대시보드로 다시 고정할 수 있습니다.

## 매개 변수 값 찾기
정규화된 서버 이름과 데이터베이스 이름을 Azure 포털에서 찾을 수 있습니다. 이 때 SQL 데이터 웨어하우스만이 Azure Preview 포털에서 있음에 유의합니다.

## 다음 단계
통합 개요는 [SQL 데이터 웨어하우스 통합 개요][]를 참조하세요. 더 많은 개발 팁은 [SQL 데이터 웨어하우스 개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[SQL 데이터 웨어하우스 개발 개요]: ./sql-data-warehouse-overview-develop/
[SQL 데이터 웨어하우스 통합 개요]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->