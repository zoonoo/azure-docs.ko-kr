---
title: SQL Server Integration Services 패키지를 Azure SQL Database 관리 되는 인스턴스로 마이그레이션 | Microsoft Docs
description: Azure SQL Database 관리 되는 인스턴스로 SQL Server Integration Services 패키지를 마이그레이션하는 방법에 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083185"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>SQL Server Integration Services 패키지를 Azure SQL Database 관리 되는 인스턴스로 마이그레이션
SQL Server Integration Services (SSIS)를 사용 하 고 대상 Azure SQL Database 관리 되는 인스턴스에서 호스팅되는 SSISDB에 SQL Server에서 호스팅되는 SSISDB 원본에서 SSIS 프로젝트/패키지를 마이그레이션할 경우에 Azure Database Migration Service를 사용할 수 있습니다.

SSIS 사용 하면 버전은 2012 이전 버전 또는 SSIS 마이그레이션 프로젝트/패키지 하기 전에 SSISDB이 아닌 패키지 저장소 형식을 사용 하는 경우 SSMS에서 시작할 수도 있습니다는 Integration Services 프로젝트 변환 마법사를 사용 하 여 변환 해야 합니다. 자세한 내용은 문서 [프로젝트를 프로젝트 배포 모델로 변환](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)을 참조하세요.

> [!NOTE]
> 현재 azure 데이터베이스 마이그레이션 서비스 (DMS) 마이그레이션 대상으로 Azure SQL Database를 지원 하지 않습니다. Azure SQL Database에 SSIS 프로젝트/패키지를 재배포할 문서를 참조 [Azure SQL Database로 다시 배포할 SQL Server Integration Services 패키지](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)합니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 원본 SSIS 프로젝트/패키지를 평가합니다.
> * Azure에 SSIS 프로젝트/패키지를 마이그레이션합니다.

## <a name="prerequisites"></a>필수 조건

이러한 단계를 완료하려면 다음이 필요합니다.

* 사용 하 여 온-프레미스 원본 서버에 대 한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대 한 Azure Virtual Network (VNet)를 만들려면 [ExpressRoute ](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 나 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)합니다. 자세한 내용은 문서 참조 [Azure SQL Database에 대 한 네트워크 토폴로지 관리 Azure Database Migration Service를 사용 하 여 인스턴스 마이그레이션]( https://aka.ms/dmsnetworkformi)합니다. VNet을 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/) 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.
* VNet 네트워크 보안 그룹 규칙이 Azure Database Migration Service는 다음 인바운드 통신 포트를 차단 하지 확인 합니다. 443, 53, 9354, 445, 12000. Azure VNet NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) 문서를 참조하세요.
* 구성 하 여 [원본 데이터베이스 엔진 액세스에 대 한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)합니다.
* SQL Server, 기본적으로 TCP 포트 1433 인 원본에 액세스 하려면 Azure Database Migration Service를 허용 하도록 Windows 방화벽을 엽니다.
* 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우, SQL Browser 서비스를 사용하도록 설정하고 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service에서 마이그레이션을 위해 445 SMB 포트를 통해 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure SQL Database 인스턴스에 SSISDB 호스트할 관리 합니다. 하나를 생성 해야 하는 경우 문서의 세부 지침에 따라 [Azure SQL Database Managed Instance 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)합니다.
* 원본에 연결 하는 데 로그인 되도록 SQL Server 및 대상 관리 되는 인스턴스에서 sysadmin 서버 역할의 멤버입니다.
* SSIS에서 Azure 데이터 팩터리 (ADF) 대상 Azure SQL Database에서 호스팅되는 SSISDB 사용 하 여 Azure SSIS IR (Integration Runtime)을 포함 하는 프로 비전 확인 하려면 관리 되는 인스턴스 (이 문서에 설명 된 대로 [AZURE-SSIS-만들기 Azure Data Factory의 integration runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>원본 SSIS 프로젝트/패키지 평가

SSISDB 원본의 평가 데이터베이스 마이그레이션 길잡이 (DMA)에 아직 통합 되지 않습니다, 하지만 SSIS 프로젝트/패키지는 평가/유효성을 검사할 수 SSISDB 호스팅되는 Azure SQL Database 관리 되는 인스턴스에서 대상에 재배포 하는 것입니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

    ![포털 구독 표시](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, **Azure Database Migration Service**를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. DMS 인스턴스를 만들려는 위치를 선택합니다.

5. 기존 VNet을 선택하거나 새로 만듭니다.

    VNet 원본 SQL Server 및 대상 Azure SQL Database 관리 되는 인스턴스에 대 한 액세스를 사용 하 여 Azure Database Migration Service를 제공합니다.

    Azure Portal에서 VNet을 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](https://aka.ms/DMSVnet) 문서를 참조하세요.

    자세한 내용은 [Azure Database Migration Service를 사용한 Azure SQL DB 관리되는 인스턴스 마이그레이션에 대한 네트워크 토폴로지](https://aka.ms/dmsnetworkformi) 문서를 참조하세요.

6. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![DMS 서비스 만들기](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스 인스턴스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. **Azure Database Migration Services** 화면에서 직접 만든 인스턴스의 이름을 검색한 다음, 인스턴스를 선택합니다.

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.

4. 에 **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정 합니다 **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 **대상 서버 형식** 텍스트 상자 **Azure SQL Database Managed Instance**, 한 후 **작업 유형 선택**선택, **SSIS 패키지 마이그레이션**.

   ![DMS 프로젝트 만들기](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. **만들기**를 선택하여 프로젝트를 만듭니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **마이그레이션 원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

2. 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 SSL 연결은 강력한 보안을 제공하지 않습니다. 메시지 가로채기(man-in-the-middle) 공격을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명 인증서를 사용하는 SSL을 신뢰해서는 안 됩니다.

   ![원본 세부 정보](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. 에 **마이그레이션 대상 세부 정보** 화면에서 대상에 대 한 연결 정보를 지정 합니다.

     ![대상 세부 정보](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. **저장**을 선택합니다.

## <a name="review-the-migration-summary"></a>마이그레이션 요약 검토

1. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

2. 에 대 한 합니다 **SSIS 프로젝트 및 환경을 덮어쓰기 옵션**를 덮어쓰거나 기존 SSIS 프로젝트 및 환경이 무시 여부를 지정 합니다.

    ![마이그레이션 프로젝트 요약](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.
