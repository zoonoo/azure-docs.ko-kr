<properties
	pageTitle="SQL Server 백업 및 복원에 Azure 저장소를 사용하는 방법 | Microsoft Azure"
	description="SQL Server 및 SQL 데이터베이스를 Azure 저장소에 백업합니다. SQL 데이터베이스를 Azure 저장소에 백업할 경우의 이점과 필요한 SQL Server 및 Azure 저장소 구성 요소를 설명합니다."
	services="sql-database, virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor="tysonn"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="vm-windows-sql-server"
	ms.topic="article"
	ms.date="02/05/2015"
	ms.author="carlrab"/>



# SQL Server 백업 및 복원에 Azure 저장소를 사용하는 방법

## 개요

SQL Server 백업을 Azure Blob 저장소 서비스에 쓸 수 있는 기능은 SQL Server 2012 SP1 CU2에서 배포되었습니다. 이 기능을 사용하여 온-프레미스 SQL Server 데이터베이스 또는 Azure 가상 컴퓨터의 SQL Server 데이터베이스를 사용하여 Azure Blob 서비스에 백업하고 Azure Blob 서비스에서 복원할 수 있습니다. 클라우드로 백업하면 가용성, 무제한으로 지역에서 복제되는 오프사이트 저장소, 클라우드로/에서 쉬운 데이터 마이그레이션 등의 이점이 있습니다. Transact-SQL 또는 SMO를 사용하여 BACKUP 또는 RESTORE 문을 실행할 수 있습니다. 또한 데이터베이스 파일이 Azure Blob에 저장되고 SQL Server 2016을 사용하는 경우 [파일-스냅숏 백업](http://msdn.microsoft.com/library/mt169363.aspx)을 사용하여 거의 즉각적인 백업 및 매우 빠른 복원을 수행할 수 있습니다.

## SQL Server 백업에 Azure Blob 서비스를 사용할 경우의 이점

저장소 관리, 저장소 오류 위험, 오프사이트 저장소에 대한 액세스, 장치 구성 등은 일반적인 백업 과제 중 일부입니다. 이러한 해결 과제는 온-프레미스 데이터베이스 인스턴스 및 Azure 가상 컴퓨터 데이터베이스 인스턴스 모두에 존재합니다. 다음은 SQL Server 백업에 Azure Blob 저장소 서비스 저장소를 사용할 경우 얻을 수 있는 주요 이점 중 일부를 나열합니다.

* 유연하고 안정적인 무제한 오프사이트 저장소: Azure Blob에 백업을 저장하면 오프사이트 옵션에 쉽고 유연하며 간편하게 액세스할 수 있습니다. SQL Server 백업을 위한 오프사이트 저장소를 만들 때 **BACKUP TO URL** 구문을 사용하면 기존 스크립트/작업을 수정하는 것만큼 쉽습니다. 오프사이트와 프로덕션 데이터베이스 위치 둘 다에 영향을 줄 수 있는 단일 재해를 방지하려면 오프사이트 저장소는 일반적으로 프로덕션 데이터베이스 위치와 충분히 멀리 떨어져 있어야 합니다. [Azure Blob에 대해 지역에서 복제](../storage/storage-redundancy.md)를 선택하면 전 지역에 영향을 줄 수 있는 재해가 발생하는 경우에도 추가적인 보호 방법을 확보할 수 있습니다. 
* 백업 보관: Azure Blob 저장소 서비스는 백업을 보관하는 데 흔히 사용되는 테이프 옵션보다 탁월한 대안을 제공합니다. 테이프 저장소를 사용하려면 미디어를 보호하기 위해 오프사이트 시설 및 수단까지 테이프를 물리적으로 운반해야 할 수 있습니다. Azure Blob 저장소에 백업 저장은 즉각적이고 내구성 있는 고가용성 보관 옵션을 제공합니다.
* 하드웨어 관리의 오버헤드가 없음: Azure 서비스를 사용하면 하드웨어 관리의 오버헤드가 없습니다. Azure 서비스에서 하드웨어를 관리하며 하드웨어 오류 방지와 중복을 위해 지역에서 복제 옵션을 제공합니다.
* 현재 Azure 가상 컴퓨터에서 실행 중인 SQL Server의 인스턴스에 대해 Azure Blob 저장소 서비스로 백업하는 작업은 연결된 디스크를 만들어 수행할 수 있습니다. 그러나 백업을 위해 Azure 가상 컴퓨터에 연결할 수 있는 디스크의 수에는 제한이 있습니다. 매우 큰 인스턴스의 경우 16개의 디스크로 제한되고 작은 인스턴스의 경우에는 디스크의 수가 더 적습니다. Azure Blob에 직접 백업할 수 있도록 설정하면 무제한 저장소에 가상으로 액세스할 수 있습니다.
* Azure Blob에 저장된 백업은 데이터베이스 연결/분리 또는 VHD 다운로드 및 연결 과정 없이도 언제 어디서나 사용 가능하며 온-프레미스 SQL Server 또는 Azure 가상 컴퓨터에서 실행되는 다른 SQL Server에 대한 복원을 위해 쉽게 액세스할 수 있습니다.
* 비용 이점: 사용하는 서비스에 대해서만 결제합니다. 오프사이트 및 백업 보관 옵션으로 비용 효율성을 추구할 수 있습니다. 자세한 내용은[ Azure 가격 계산기](http://go.microsoft.com/fwlink/?LinkId=277060 "Azure 가격 계산기") 및 [Azure 가격 책정 문서](http://go.microsoft.com/fwlink/?LinkId=277059 "가격 책정 문서")를 참조하세요.
* 스냅숏 저장소 활용: 데이터베이스 파일이 Azure Blob에 저장되고 SQL Server 2016을 사용하는 경우 [파일-스냅숏 백업](http://msdn.microsoft.com/library/mt169363.aspx)을 사용하여 거의 즉각적인 백업 및 매우 빠른 복원을 수행할 수 있습니다.

자세한 내용은 [Azure Blob 저장소 서비스로 SQL Server 백업 및 복원](http://go.microsoft.com/fwlink/?LinkId=271617)을 참조하십시오.

다음 두 섹션에서는 Azure Blob 저장소 서비스와 Azure Blob 저장소 서비스로 백업하거나 저장소 서비스에서 복원할 때 사용하는 SQL Server 구성 요소에 대해 소개합니다. Azure Blob 저장소 서비스로 백업하거나 서비스에서 복원하기 위해서는 구성 요소와 구성 요소 간 조작에 대해 이해하는 것이 중요합니다.

이 프로세스의 첫 단계는 Azure 계정을 만드는 것입니다. SQL Server 2014를 사용하여 저장소 계정을 만들고 간단한 복원을 수행하는 완벽한 연습에 대해서는 [Azure 저장소 서비스로 SQL Server 백업 및 복원 시작](https://msdn.microsoft.com/library/jj720558(v=sql.120).aspx)을 참조하세요. SQL Server 2014를 사용하여 저장소 계정을 만들고 간단한 복원을 수행하는 완벽한 연습에 대해서는 [자습서: SQL Server 2016 데이터베이스에서 Microsoft Azure Blob 저장소 서비스 사용](https://msdn.microsoft.com/library/dn466438.aspx)을 참조하세요.

## Azure Blob 저장소 서비스 구성 요소

* 저장소 계정: 저장소 계정은 모든 저장소 서비스를 사용하기 위한 출발점입니다. Azure Blob 저장소 서비스에 액세스하려면 먼저 Azure 저장소 계정을 만드세요. Azure Blob 저장소 서비스에 대한 자세한 내용은 [Azure Blob 저장소 서비스를 사용하는 방법](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)(영문)을 참조하십시오.

* 컨테이너: 컨테이너는 Blob 집합의 그룹화를 제공하며 Blob을 개수에 제한 없이 저장할 수 있습니다. SQL Server 백업을 Azure Blob 서비스에 쓰려면 최소한 루트 컨테이너를 만들어야 합니다.

* Blob: 모든 형식과 크기의 파일입니다. Blob에 다음 URL 형식을 사용하여 주소를 지정할 수 있습니다. `https://<storage account>.blob.core.windows.net/<container>/<blob>` 페이지 Blob에 대한 자세한 내용은 [블록 및 페이지 Blob 이해](http://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.

## SQL Server 구성 요소

* URL: URL은 고유한 백업 파일에 대한 URI(Uniform Resource Identifier)를 지정합니다. URL은 SQL Server 백업 파일의 위치 및 이름을 지정하는 데 사용합니다. URL은 컨테이너가 아닌 실제 Blob을 가리켜야 합니다. Blob이 없는 경우 새로 만듭니다. 기존 Blob이 지정된 경우 > WITH FORMAT 옵션을 지정하지 않으면 BACKUP이 실패합니다. 다음은 BACKUP 명령에 지정할 수 있는 URL의 예입니다. ****`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>참고:</b> HTTPS는 필수가 아니지만 사용하는 것이 좋습니다. <b>중요</b> Azure Blob 저장소 서비스로 백업 파일을 복사하고 업로드하도록 선택한 경우 이 파일을 복원 작업에 사용하려면 저장소 옵션으로 페이지 Blob 유형을 사용해야 합니다. 블록 Blob 유형에서 RESTORE를 사용하면 오류를 일으키며 실패합니다.

* 자격 증명: Azure Blob 저장소 서비스에 연결하고 인증하는 데 필요한 정보는 자격 증명으로 저장됩니다. SQL Server가 백업을 Azure Blob에 쓰거나 Azure Blob에서 복원하려면 SQL Server 자격 증명을 만들어야 합니다. 자세한 내용은 [SQL Server 자격 증명](https://msdn.microsoft.com/library/ms189522.aspx)을 참조하세요.

## Azure Blob으로 SQL Server 데이터베이스 백업 및 복원 - 개념 및 작업:

**개념, 고려 사항 및 코드 샘플:**

[Azure Blob 저장소 서비스로 SQL Server 백업 및 복원](http://go.microsoft.com/fwlink/?LinkId=271617)

**자습서 시작:**

[자습서:SQL Server 2016 데이터베이스에서 Microsoft Azure Blob 저장소 서비스 사용](https://msdn.microsoft.com/library/dn466438.aspx)

**모범 사례, 문제 해결:**

[백업 및 복원 모범 사례(Azure Blob 저장소 서비스)](http://go.microsoft.com/fwlink/?LinkId=272394)

<!---HONumber=AcomDC_0211_2016-->