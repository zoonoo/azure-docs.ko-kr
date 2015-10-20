<properties
	pageTitle="TSQL를 사용하여 SQL 데이터 웨어하우스 만들기 | Microsoft Azure"
	description="TSQL를 사용하여 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다."
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/07/2015"
   ms.author="lodipalm"/>

#TSQL을 사용하여 SQL 데이터 웨어하우스 만들기 

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-TSQL.md)
- [PowerShell](sql-data-warehouse-get-started-create-powershell.md)

이 문서에서는 Transact SQL을 사용하여 SQL 데이터 웨어하우스를 만드는 방법을 보여줍니다. 이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- 있습니다. Visual Studio의 무료 버전은 [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs) 페이지를 참조하세요.
- V12 SQL Server. SQL 데이터 웨어하우스에 만들려면 V12 SQL server가 필요합니다. V12 SQL 서버를 사용할 수 없는 경우 미리 보기 포털에서 만들어서 새 서버에서 SQL 데이터 웨어하우스를 만들 수 있도록 하는 것이 좋습니다.

이 문서에서는 Visual Studio를 사용하여 올바르게 설치 및 연결하는 방법을 다루지 않습니다. 해당 사항을 수행하는 방법에 대한 전체 설명은 [연결 및 쿼리][] 설명서를 참조하세요. 시작하려면 Visual Studio에서 SQL Server 개체 탐색기를 열고 SQL 데이터 웨어하우스 데이터베이스를 만드는 데 사용할 서버에 연결해야 합니다. 이렇게 하면 마스터 데이터베이스에 대해 다음 명령을 실행하여 SQL 데이터 웨어하우스를 만들 수 있습니다.

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

또한 명령줄을 열고 다음을 실행하여 SQL 데이터 웨어하우스를 만들 수 있습니다.

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

위의 TSQL 문을 실행할 때 MAXSIZE 및 SERVICE\_OBJECTIVE 매개 변수가 초기 저장소 크기를 결정하고 데이터 웨어하우스 인스턴스에 할당되도록 계산합니다. MAXSIZE는 다음 크기를 허용하며 증가할 공간을 생각하면 큰 크기를 선택하는 것이 좋습니다.

+ 250GB
+ 500GB
+ 750GB
+ 1024GB
+ 5120GB
+ 10240GB
+ 20480GB
+ 30720GB
+ 40960GB
+ 51200GB

SERVICE\_OBJECTIVE는 인스턴스에서 시작할 DWU 값을 나타내고 다음 값을 수락합니다.

+ DW100
+ DW200
+ DW300
+ DW400
+ DW500
+ DW600
+ DW1000
+ DW1200
+ DW1500
+ DW2000

이러한 매개 변수로 인한 요금 청구 영향에 대한 정보는 [가격 책정 페이지][]를 참조하세요.

## 다음 단계
SQL 데이터 웨어하우스에서 프로비전을 완료한 후 [샘플 데이터를 로드][]하거나 [개발][], [로드][] 또는 [마이그레이션][] 방법을 확인할 수 있습니다.

[연결 및 쿼리]: ./sql-data-warehouse-get-started-connect-query.md
[마이그레이션]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-overview-migrate/
[개발]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-overview-develop/
[로드]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-overview-load/
[샘플 데이터를 로드]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[가격 책정 페이지]: https://azure.microsoft.com/ko-KR/pricing/details/sql-data-warehouse/

<!---HONumber=Oct15_HO3-->