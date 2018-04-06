---
title: Azure Database Migration Service를 사용하여 SQL Server를 Azure SQL Database 관리되는 인스턴스로 마이그레이션 | Microsoft Docs
description: Azure Database Migration Service를 사용하여 SQL Server 온-프레미스에서 Azure SQL Database 관리되는 인스턴스로 마이그레이션하는 방법에 대해 알아봅니다.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 8abf3bae3a2274ed5514a5c621675b4c9ec27ae2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>Azure SQL Database 관리되는 인스턴스로 SQL Server 마이그레이션
Azure Database Migration Service를 사용하여 온-프레미스 SQL Server 인스턴스에서 Azure SQL Database로 데이터베이스를 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service를 사용하여 **Adventureworks2012** 데이터베이스를 SQL Server의 온-프레미스 인스턴스에서 Azure SQL Database로 마이그레이션합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 VNET을 만듭니다. 이를 통해 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용하여 온-프레미스 원본 서버에서 사이트 간 연결을 제공합니다. [Azure Database Migration Service를 사용한 Azure SQL DB 관리되는 인스턴스 마이그레이션에 대한 네트워크 토폴로지에 대해 알아봅니다](https://aka.ms/dmsnetworkformi).
- Azure VNET(Virtual Network) 네트워크 보안 그룹 규칙이 443, 53, 9354, 445, 12000과 같은 통신 포트를 차단하지 않는지 확인합니다. Azure VNET NSG 트래픽 필터링에 대한 자세한 정보는 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) 문서를 참조하세요.
- [소스 데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
- 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있도록 방화벽을 통해 UDP 포트 1434에 액세스할 수 있습니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 SMB 포트 445를 통한 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
- [Azure Portal에서 Azure SQL Database 관리되는 인스턴스 만들기](https://aka.ms/sqldbmi) 문서의 세부 지침에 따라 Azure SQL Database 관리되는 인스턴스를 만듭니다.
- 원본 SQL Server와 대상 관리되는 인스턴스를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 구성원인지 확인합니다.
- Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 네트워크 공유를 만듭니다.
- 원본 SQL Server 인스턴스를 실행하는 서비스 계정이 방금 만든 네트워크 공유에 대한 쓰기 권한을 갖고 있는지 확인합니다.
- 위에서 만든 네트워크 공유에 대한 전체 제어 권한을 갖고 있는 Windows 사용자(및 암호)를 메모해 둡니다. Azure Database Migration Service는 사용자 자격 증명을 가장하여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드합니다.
- [저장소 탐색기를 사용하여 Azure Blob Storage 리소스 관리(미리 보기)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 문서의 단계를 사용하여 Blob 컨테이너를 만들고 해당 SAS URI를 검색합니다. SAS URI를 만드는 동안 정책 창에서 모든 권한(읽기, 쓰기, 삭제, 나열)을 선택해야 합니다. 그러면 Azure SQL Database 관리되는 인스턴스로 데이터베이스를 마이그레이션하는 데 사용된 백업 파일을 업로드할 수 있도록 저장소 계정 컨테이너에 대한 액세스 권한이 있는 Azure Database Migration Service 제공

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1.  Azure Portal에 로그인하고 **모든 서비스**를 선택한 후 **구독**을 선택합니다.
![포털 구독 표시](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음 **리소스 공급자**를 선택합니다.
![리소스 공급자 표시](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.
![리소스 공급자 등록](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>인스턴스 만들기

1.  Azure Portal에서 **+ 리소스 만들기**를 선택하고, **Azure Database Migration Service**를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  **Azure Database Migration Service(미리 보기)** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  **Database Migration Service** 화면에서 서비스, 구독, 리소스 그룹, 가상 네트워크 및 가격 책정 계층의 이름을 지정합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요. *Azure Database Migration Service는 현재 미리 보기로 제공되며, 비용은 청구되지 않습니다.*

    **네트워크:** 기존 VNET을 선택하거나 새로운 VNET을 만듭니다. 원본 SQL Server 및 대상 Azure SQL Database 관리되는 인스턴스에 대한 액세스 권한이 있는 Azure Database Migration Service가 제공됩니다. [Azure Database Migration Service를 사용한 Azure SQL DB 관리되는 인스턴스 마이그레이션에 대한 네트워크 토폴로지에 대해 알아봅니다](https://aka.ms/dmsnetworkformi).

    Azure Portal에서 VNET을 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 여러 서브넷이 있는 가상 네트워크 만들기](https://aka.ms/DMSVnet)를 참조하세요.

    ![DMS 서비스 만들기](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  **만들기**를 선택하여 서비스를 만듭니다.


## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 엽니다.

1.  **+ 새 마이그레이션 프로젝트**를 선택합니다.

1.  **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택한 다음, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database 관리되는 인스턴스**를 선택합니다.

    ![DMS 프로젝트 만들기](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  **만들기**를 선택하여 프로젝트를 만듭니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1.  **원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

    ![원본 세부 정보](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  **저장**을 선택한 다음, 마이그레이션을 위해 **Adventureworks2012** 데이터베이스를 선택합니다.

    ![원본 데이터베이스 선택](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>대상 세부 정보 지정

1.  **저장**을 선택한 다음, **대상 세부 정보** 화면에서 대상에 대한 연결 세부 정보를 지정합니다. 대상은 **AdventureWorks2012** 데이터베이스가 마이그레이션될 미리 프로비전된 Azure SQL Database 관리되는 인스턴스입니다.

    ![대상 선택](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  **저장**을 선택합니다.

1.  **프로젝트 요약** 화면에서 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

## <a name="run-the-migration"></a>마이그레이션 실행

1.  최근에 저장된 프로젝트를 선택하고, **+ 새 활동**을 선택한 다음, **마이그레이션 실행**을 선택합니다.

    ![새 활동 만들기](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  메시지가 표시되면 원본 및 대상 서버에 대한 자격 증명을 입력한 다음, **저장**을 선택합니다.

1.  **대상 데이터베이스에 매핑** 화면에서 마이그레이션할 원본 데이터베이스를 선택합니다.

    ![원본 데이터베이스 선택](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  **저장**을 선택하고 **마이그레이션 설정 구성** 화면에서 다음 세부 정보를 입력합니다.

    | | |
    |--------|---------|
    |**서버 백업 위치** | Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 로컬 네트워크 공유입니다. 원본 SQL Server 인스턴스를 실행하는 서비스 계정에는 이 네트워크 공유에 대한 쓰기 권한이 있어야 합니다. |
    |**사용자 이름** | Azure Database Migration Service가 가장하여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드할 수 있는 Windows 사용자 이름입니다. |
    |**암호** | 위의 사용자에 대한 암호입니다. |
    |**저장소 SAS URI** | Azure SQL Database 관리되는 인스턴스로 데이터베이스를 마이그레이션하는 데 사용되며 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공하는 SAS URI입니다. [Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아봅니다](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![마이그레이션 설정 구성](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  **저장**을 선택하고 마이그레이션 요약 화면의 **활동 이름** 텍스트 상자에서 마이그레이션 활동의 이름을 지정합니다.

    ![마이그레이션 요약](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1.  활동 상태를 검토할 마이그레이션 활동을 선택합니다.

1.  마이그레이션이 완료된 후 대상 Azure SQL Database 관리되는 인스턴스에서 대상 데이터베이스를 확인합니다.

    ![마이그레이션 모니터링](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

