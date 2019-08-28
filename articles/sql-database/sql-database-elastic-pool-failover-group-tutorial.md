---
title: '자습서: 장애 조치 (failover) 그룹에 Azure SQL Database 탄력적 풀 추가 | Microsoft Docs'
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 장애 조치 (failover) 그룹에 Azure SQL Database 탄력적 풀을 추가 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 2d46e6f1d5c7079ab5bbfea39a85ea0a7592afc8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099255"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>자습서: 장애 조치 (failover) 그룹에 Azure SQL Database 탄력적 풀 추가

Azure Portal를 사용 하 여 Azure SQL Database 탄력적 풀 및 테스트 장애 조치 (failover) 그룹을 구성 합니다.  이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> - Azure SQL Database 단일 데이터베이스를 만듭니다.
> - 단일 데이터베이스를 탄력적 풀에 추가 합니다. 
> - 두 논리 SQL 서버 간에 탄력적 풀에 대 한 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다.
> - 테스트 장애 조치 (failover)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다. 

- Azure 구독. 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/) .


## <a name="1---create-a-single-database"></a>1-단일 데이터베이스 만들기 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-탄력적 풀에 단일 데이터베이스 추가

1. Azure Portal의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. 필드 **AZURE SQL** 옆의 별표를 선택 하 여 즐겨찾기를 선택 하 고 왼쪽 탐색에 항목으로 추가 합니다. 
1. **+ 추가** 를 선택 하 여 **SQL 배포 옵션 선택** 페이지를 엽니다. 데이터베이스 타일에 대 한 자세한 정보 표시를 선택 하 여 다른 데이터베이스에 대 한 추가 정보를 볼 수 있습니다.
1. **SQL 데이터베이스** 타일의 **리소스 종류** 드롭다운에서 **탄력적 풀** 을 선택 합니다. **만들기** 를 선택 하 여 탄력적 풀을 만듭니다. 

    ![탄력적 풀 선택](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. 다음 값을 사용 하 여 탄력적 풀을 구성 합니다.
   - **이름**: 탄력적 풀에 대 한 고유 이름 (예: `myElasticPool`)을 제공 합니다. 
   - **구독**: 드롭다운에서 구독을 선택합니다.
   - **ResourceGroup**: 섹션 `myResourceGroup` 1에서 만든 리소스 그룹인 드롭다운에서 선택 합니다. 
   - **서버**: 드롭다운에서 섹션 1에서 만든 서버를 선택 합니다.  

       ![탄력적 풀에 대 한 새 서버 만들기](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **컴퓨팅 및 스토리지**: **탄력적 풀 구성** 을 선택 하 여 계산 및 저장소를 구성 하 고 단일 데이터베이스를 탄력적 풀에 추가 합니다. **풀 설정** 탭에서 기본 Gen5을 2 개 vcores 및 32gb로 그대로 둡니다. 

1. **구성** 페이지에서 **데이터베이스** 탭을 선택한 다음 **데이터베이스 추가**를 선택 합니다. 섹션 1에서 만든 데이터베이스를 선택한 다음 **적용** 을 선택 하 여 탄력적 풀에 추가 합니다. 다시 **적용** 을 선택 하 여 탄력적 풀 설정을 적용 하 고 **구성** 페이지를 닫습니다. 

    ![탄력적 풀에 SQL DB 추가](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. **검토 + 만들기** 를 선택 하 여 탄력적 풀 설정을 검토 한 후 **만들기** 를 선택 하 여 탄력적 풀을 만듭니다. 


## <a name="3---create-the-failover-group"></a>3-장애 조치 (failover) 그룹 만들기 
이 단계에서는 기존 Azure SQL server와 다른 지역의 새 Azure SQL server 간에 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다. 그런 다음 장애 조치 (failover) 그룹에 탄력적 풀을 추가 합니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택 합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. 필드 **AZURE SQL** 옆의 별표를 선택 하 여 즐겨찾기를 선택 하 고 왼쪽 탐색에 항목으로 추가 합니다. 
1. 이전 섹션에서 만든 탄력적 풀 (예: `myElasticPool`)을 선택 합니다. 
1. **개요** 창에서 서버 **이름** 아래의 서버 이름을 선택 하 여 서버에 대 한 설정을 엽니다.
  
    ![탄력적 풀에 대 한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 **그룹 추가** 를 선택 하 여 새 장애 조치 그룹을 만듭니다. 

    ![새 장애 조치 (failover) 그룹 추가](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. **장애 조치 (Failover) 그룹** 페이지에서 다음 값을 입력 하거나 선택한 다음 **만들기**를 선택 합니다.
    - **장애 조치 (Failover) 그룹 이름**: 고유한 장애 조치 (failover) 그룹 이름 (예 `failovergrouptutorial`:)을 입력 합니다. 
    - **보조 서버**: *필수 설정을 구성* 하는 옵션을 선택한 다음 **새 서버를 만들도록**선택 합니다. 또는 기존 서버를 보조 서버로 선택할 수도 있습니다. 새 보조 서버에 대해 다음 값을 입력 한 후 **선택**을 선택 합니다. 
        - **서버 이름**: 보조 서버에 대 한 고유한 이름 (예: `mysqlsecondary`)을 입력 합니다. 
        - **서버 관리자 로그인**: 입력할`azureuser`
        - **암호**: 암호 요구 사항을 충족하는 복잡한 암호를 입력합니다.
        - **위치**: 드롭다운 목록에서 위치를 선택합니다(예: `East US`). 이 위치는 주 서버와 동일한 위치가 될 수 없습니다.

       > [!NOTE]
       > 서버 로그인 및 방화벽 설정은 주 서버의 이름과 일치 해야 합니다. 
    
       ![장애 조치 (failover) 그룹에 대 한 보조 서버 만들기](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. **그룹 내의 데이터베이스** 를 선택한 다음 섹션 2에서 만든 탄력적 풀을 선택 합니다. 보조 서버에서 탄력적 풀을 만들지 묻는 경고가 표시 됩니다. 경고를 선택한 다음 **확인** 을 선택 하 여 보조 서버에서 탄력적 풀을 만듭니다. 
        
    ![장애 조치 (failover) 그룹에 탄력적 풀 추가](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. **선택** 을 선택 하 여 탄력적 풀 설정을 장애 조치 (failover) 그룹에 적용 한 다음 **만들기** 를 선택 하 여 장애 조치 그룹을 만듭니다. 장애 조치 (failover) 그룹에 탄력적 풀을 추가 하면 지역에서 복제 프로세스가 자동으로 시작 됩니다. 


## <a name="4---test-failover"></a>4-테스트 장애 조치 
이 단계에서는 장애 조치 (failover) 그룹을 보조 서버로 장애 조치 (failover) 한 다음 Azure Portal를 사용 하 여 장애 복구 (failback) 합니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL** 을 선택 합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. 필드 **AZURE SQL** 옆의 별표를 선택 하 여 즐겨찾기를 선택 하 고 왼쪽 탐색에 항목으로 추가 합니다. 
1. 이전 섹션에서 만든 탄력적 풀 (예: `myElasticPool`)을 선택 합니다. 
1. 서버 **이름** 아래에서 서버 이름을 선택 하 여 서버에 대 한 설정을 엽니다.

    ![탄력적 풀에 대 한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 섹션 2에서 만든 장애 조치 (failover) 그룹을 선택 합니다. 
  
   ![포털에서 장애 조치 (failover) 그룹을 선택 합니다.](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. 주 서버와 보조 서버를 검토 합니다. 
1. 작업 창에서 **장애 조치 (Failover)** 를 선택 하 여 탄력적 풀이 포함 된 장애 조치 그룹을 장애 조치 (failover) 합니다. 
1. TDS 세션의 연결을 끊을 것을 알리는 경고에서 **예** 를 선택 합니다. 

   ![SQL database를 포함 하는 장애 조치 (failover) 그룹 장애 조치 (failover)](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. 주 서버가 보조 서버인 서버를 검토 합니다. 장애 조치 (failover)에 성공 하면 두 서버에 교환 된 역할이 있어야 합니다. 
1. 장애 조치 (failover)를 다시 선택 하 여 장애 조치 그룹을 다시 원래 설정으로 장애 조치 합니다. 

## <a name="clean-up-resources"></a>리소스 정리 
리소스 그룹을 삭제 하 여 리소스를 정리 합니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 합니다.
1. 리소스 그룹 자체 뿐만 아니라 그룹의 모든 리소스를 삭제 하려면 **리소스 그룹 삭제** 를 선택 합니다. 
1. 텍스트 상자에 리소스 그룹 `myResourceGroup`의 이름을 입력 하 고 **삭제** 를 선택 하 여 리소스 그룹을 삭제 합니다.  


## <a name="next-steps"></a>다음 단계

이 자습서에서는 장애 조치 (failover) 그룹에 Azure SQL Database 단일 데이터베이스를 추가 하 고 장애 조치 (failover)를 테스트 했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> - Azure SQL Database 단일 데이터베이스를 만듭니다.
> - 단일 데이터베이스를 탄력적 풀에 추가 합니다. 
> - 두 논리 SQL 서버 간에 탄력적 풀에 대 한 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다.
> - 테스트 장애 조치 (failover)

DMS를 사용한 마이그레이션 방법을 설명하는 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [자습서: DMS를 사용 하 여 풀링된 데이터베이스로 SQL Server 마이그레이션](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
