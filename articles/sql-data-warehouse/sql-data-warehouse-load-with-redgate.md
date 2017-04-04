---
title: "Redgate를 사용하여 Azure Data Warehouse에 데이터 로드 | Microsoft Docs"
description: "데이터 웨어하우징 시나리오에 대해 Redgate의 Data Platform Studio를 사용하는 방법을 알아봅니다."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: cb0b5489ccfabb0e2a4ee412162a18930073b309
ms.lasthandoff: 02/16/2017



---
# <a name="load-data-with-redgate-data-platform-studio"></a>Redgate Data Platform Studio를 사용하여 데이터 로드
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [데이터 팩터리](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

이 자습서는 [Redgate의 Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/)(DPS)를 사용하여 온-프레미스 SQL Server에서 Azure SQL Data Warehouse로 데이터를 이동하는 방법을 보여 줍니다. Data Platform Studio는 가장 적합한 호환성 수정 및 최적화를 적용하므로 SQL Data Warehouse를 시작하는 가장 빠른 방법입니다.

> [!NOTE]
> [Redgate](http://www.red-gate.com)는 다양한 SQL Server 도구를 제공하는 오래된 Microsoft 파트너입니다. Data Platform Studio의 이 기능은 상업 및 비상업적 용도 모두에서 무료로 제공됩니다.
> 
> 

## <a name="before-you-begin"></a>시작하기 전에
### <a name="create-or-identify-resources"></a>리소스 만들기 또는 식별
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **온-프레미스 SQL Server Database**: SQL Data Warehouse로 가져올 데이터를 온-프레미스 SQL Server(버전 2008R2 이상)에서 가져와야 합니다. Data Platform Studio는 Azure SQL Database 또는 텍스트 파일에서 직접 가져올 수 없습니다.
* **Azure Storage 계정**: Data Platform Studio는 SQL Data Warehouse에 데이터를 로드하기 전에 Azure Blob Storage에 데이터를 준비합니다. 저장소 계정으로는 "클래식" 배포 모델보다는 "Resource Manager" 배포 모델(기본값)을 사용해야 합니다. 저장소 계정이 없을 경우 저장소 계정을 만드는 방법을 알아보세요 
* **SQL Data Warehouse**: 이 자습서는 온-프레미스 SQL Server에서 SQL Data Warehouse로 데이터를 이동하므로 데이터 웨어하우스 온라인이 필요합니다. 데이터 웨어하우스가 아직 없는 경우 Azure SQL Data Warehouse를 만드는 방법을 알아보세요.

> [!NOTE]
> 저장소 계정 및 데이터 웨어하우스를 동일한 지역에 만들면 성능이 향상됩니다.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>1단계: Azure 계정으로 Data Platform Studio에 로그인
웹 브라우저를 열고 [Data Platform Studio](https://www.dataplatformstudio.com/) 웹 사이트로 이동합니다. 저장소 계정 및 데이터 웨어하우스를 만드는 데 사용했던 것과 동일한 Azure 계정으로 로그인합니다. 전자 메일 주소가 회사 및 학교 계정 및 Microsoft 계정과 연결된 경우 리소스에 대한 액세스 권한이 있는 계정을 선택해야 합니다.

> [!NOTE]
> 처음으로 Data Platform Studio를 사용하는 경우 Azure 리소스를 관리할 수 있는 응용 프로그램 권한을 부여하라는 메시지가 표시됩니다.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>2단계: 가져오기 마법사 시작
DPS 주 화면에서 Azure SQL Data Warehouse로 가져오기 링크를 선택하여 가져오기 마법사를 시작합니다.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>3단계: Data Platform Studio 게이트웨이 설치
온-프레미스 SQL Server 데이터베이스에 연결하려면 DPS 게이트웨이를 설치해야 합니다. 게이트웨이는 온-프레미스 환경에 대한 액세스를 제공하는 클라이언트 에이전트로, 데이터를 추출하고 저장소 계정에 업로드합니다. 데이터는 Redgate 서버를 통과하지 않습니다. 게이트웨이를 설치하려면:

1. **게이트웨이 만들기** 링크를 클릭합니다.
2. 제공된 설치 관리자를 사용하여 게이트웨이를 다운로드하여 설치합니다.

![][2]

> [!NOTE]
> 게이트웨이는 원본 SQL Server 데이터베이스에 대한 네트워크 액세스 권한이 있는 모든 컴퓨터에 설치할 수 있습니다. 현재 사용자의 자격 증명으로 Windows 인증을 사용하여 SQL Server 데이터베이스에 액세스합니다.
> 
> 

설치한 후에는 게이트웨이 상태가 연결됨으로 변경되고 다음을 선택할 수 있습니다.

## <a name="step-4-identify-the-source-database"></a>4단계: 원본 데이터베이스 식별
*서버 이름 입력* 텍스트 상자에 데이터베이스를 호스트하는 서버 이름을 입력하고 **다음**을 선택합니다. 그런 다음 드롭다운 메뉴에서 데이터를 가져올 데이터베이스를 선택합니다.

![][3]

DPS가 선택한 데이터베이스에서 가져올 테이블을 검사합니다. 기본적으로 DPS는 데이터베이스에 있는 모든 테이블을 가져옵니다. 모든 테이블 링크를 확장하여 테이블을 선택 또는 선택 취소합니다. 다음 단추를 선택하여 계속합니다.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>5단계: 데이터를 준비할 저장소 계정 선택
DPS는 데이터를 준비할 위치를 묻는 메시지를 표시합니다. 구독에서 기존 저장소 계정을 선택하고 **다음**을 선택합니다.

> [!NOTE]
> DPS가 선택한 저장소 계정에 새 Blob 컨테이너를 만들고 각 가져오기에 고유한 폴더를 사용합니다.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>6단계: 데이터 웨어하우스 선택
다음으로 데이터를 가져올 온라인 [Azure SQL Data Warehouse](http://aka.ms/sqldw) 데이터베이스를 선택합니다. 데이터베이스를 선택했으면 데이터베이스에 연결할 자격 증명을 입력하고 **다음**을 선택해야 합니다.

![][5]

> [!NOTE]
> DPS는 원본 데이터 테이블을 데이터 웨어하우스에 병합합니다. DPS는 테이블 이름이 데이터 웨어하우스에 있는 기존 테이블을 덮어써야 하는 경우 이를 경고합니다. 선택적으로 가져오기 전에 기존의 모든 개체 삭제를 선택하여 데이터 웨어하우스에서 기존 개체를 삭제할 수 있습니다.
> 
> 

## <a name="step-7-import-the-data"></a>7단계: 데이터 가져오기
DPS는 데이터를 가져오려고 하는 것을 확인합니다. 가져오기 시작 단추를 클릭하면 데이터 가져오기가 시작됩니다.

![][6]

DPS에 데이터를 온-프레미스 SQL Serer에서 추출 및 업로드하는 진행 상태와 SQL Data Warehouse로 가져오는 진행 상태를 보여 주는 시각화가 표시됩니다.

![][7]

가져오기가 완료되면 DPS는 수행된 데이터 가져오기 및 호환성 수정의 변경 보고를 요약하여 보여줍니다.

![][8]

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse 내에서 데이터를 탐색하려면 다음을 확인하여 시작합니다.

* [Azure SQL Data Warehouse 쿼리(Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Power BI를 사용하여 데이터 시각화][Visualize data with Power BI]

Redgate의 Data Platform Studio에 대해 자세히 알아보려면:

* [DPS 홈 페이지 방문](http://www.dataplatformstudio.com/)
* [Channel9에서 DPS 데모 보기](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

SQL Data Warehouse에서 데이터를 마이그레이션 및 로드하는 다른 방법에 대한 개요는 다음을 참조하세요.

* [SQL Data Warehouse에 솔루션 마이그레이션][Migrate your solution to SQL Data Warehouse]
* [Azure SQL 데이터 웨어하우스에 데이터 로드](sql-data-warehouse-overview-load.md)

더 많은 개발 팁은 [SQL Data Warehouse 개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

