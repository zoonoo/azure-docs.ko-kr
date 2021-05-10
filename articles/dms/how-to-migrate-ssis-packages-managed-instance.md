---
title: SSIS 패키지를 SQL Managed Instance로 마이그레이션
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service 또는 Data Migration Assistant를 사용하여 SSIS(SQL Server Integration Services) 패키지 및 프로젝트를 Azure SQL Managed Instance로 마이그레이션하는 방법을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1f6df64a66c0700f3a13a40ea5046515a2bf1a51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643847"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>SQL Server Integration Services 패키지를 Azure SQL Managed Instance로 마이그레이션
SSIS(SQL Server Integration Services)를 사용하고 SQL Server에서 호스트하는 SSISDB 원본에서 Azure SQL Managed Instance에서 호스트되는 대상 SSISDB에 SSIS 프로젝트/패키지를 마이그레이션하려는 경우 Azure Database Migration Service를 사용할 수 있습니다.

사용하는 SSIS 버전이 2012 이전이거나 비SSISDB 패키지 저장소 형식을 사용하는 경우 SSIS 프로젝트/패키지를 마이그레이션하기 전에 먼저 SSMS에서 시작할 수도 있는 Integration Services 프로젝트 변환 마법사를 사용하여 변환해야 합니다. 자세한 내용은 문서 [프로젝트를 프로젝트 배포 모델로 변환](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert)을 참조하세요.

> [!NOTE]
> Azure DMS(Database Migration Service)는 현재 Azure SQL Database를 대상 마이그레이션 대상으로 지원하지 않습니다. Azure SQL Database에 SSIS 프로젝트/패키지를 다시 배포하려면 [Azure SQL Database에 SQL Server Integration Services 패키지 다시 배포](./how-to-migrate-ssis-packages.md) 문서를 참조하세요.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 원본 SSIS 프로젝트/패키지를 평가합니다.
> * Azure에 SSIS 프로젝트/패키지를 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 단계를 완료하려면 다음이 필요합니다.

* Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 Microsoft Azure Virtual Network를 만듭니다. 그러면 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 사이트 간 연결이 제공됩니다. 자세한 내용은 [Azure Database Migration Service를 사용한 SQL Managed Instance 마이그레이션에 대한 네트워크 토폴로지]( https://aka.ms/dmsnetworkformi) 문서를 참조하세요. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](../virtual-network/index.yml)를 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.
* 가상 네트워크의 네트워크 보안 그룹 규칙이 ServiceBus, Storage 및 AzureMonitor용 ServiceTag의 아웃바운드 포트 443을 차단하지 않는지 확인합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
* [소스 데이터베이스 엔진 액세스를 위한 Windows 방화벽](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
* 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우, SQL Browser 서비스를 사용하도록 설정하고 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service에서 마이그레이션을 위해 445 SMB 포트를 통해 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
* SSISDB를 호스트하는 SQL Managed Instance 만들어야 하는 경우 [Azure SQL Managed Instance 만들기](../azure-sql/managed-instance/instance-create-quickstart.md) 문서에서 세부 정보를 따르세요.
* 원본 SQL Server와 대상 관리형 인스턴스를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 구성원인지 확인합니다.
* [Azure Data Factory에 Azure-SSIS 통합 런타임 만들기](../data-factory/create-azure-ssis-integration-runtime.md) 문서에 설명된 대로, SSIS가 SQL Managed Instance에서 호스트하는 대상 SSISDB와 함께 Azure-SSIS IR(Integration Runtime)이 포함된 ADF(Azure Data Factory)에서 프로비저닝되는지 확인합니다.

## <a name="assess-source-ssis-projectspackages"></a>원본 SSIS 프로젝트/패키지 평가

원본 SSISDB의 평가는 아직 DMA(Database Migration Assistant)에 통합되지 않았지만, SSIS 프로젝트/패키지는 Azure SQL Managed Instance에서 호스트하는 대상 SSISDB에 다시 배포될 때 평가/검증됩니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스** 를 선택한 다음, **구독** 을 선택합니다.

    ![포털 구독 표시](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자** 를 선택합니다.

    ![리소스 공급자 보기](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration** 의 오른쪽에서 **등록** 을 선택합니다.

    ![리소스 공급자 등록](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기** 를 선택하고, **Azure Database Migration Service** 를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service** 를 선택합니다.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기** 를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. DMS 인스턴스를 만들려는 위치를 선택합니다.

5. 기존 가상 네트워크를 선택하거나 새로 만듭니다.

    가상 네트워크는 원본 SQL Server와 대상 Azure SQL Managed Instance에 대한 액세스 권한이 있는 Azure Database Migration Service를 제공합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md) 문서를 참조하세요.

    자세한 내용은 [Azure Database Migration Service를 사용한 Azure SQL Managed Instance 마이그레이션에 대한 네트워크 토폴로지](./resource-network-topologies.md) 문서를 참조하세요.

6. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![DMS 서비스 만들기](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. **만들기** 를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스 인스턴스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스** 를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services** 를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. **Azure Database Migration Services** 화면에서 직접 만든 인스턴스의 이름을 검색한 다음, 인스턴스를 선택합니다.

3. **+ 새 마이그레이션 프로젝트** 를 선택합니다.

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 형식** 텍스트 상자에서 **SQL Server** 를 선택하고, **대상 서버 형식** 텍스트 상자에서 **Azure SQL Managed Instance** 를 선택한 다음, **작업 형식 선택** 에서 **SSIS 패키지 마이그레이션** 을 선택합니다.

   ![DMS 프로젝트 만들기](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. **만들기** 를 선택하여 프로젝트를 만듭니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **마이그레이션 원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

2. 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 TLS 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서는 자체 서명된 인증서를 사용한 TLS에 의존해서는 안 됩니다.

   ![원본 세부 정보](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. **저장** 을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **마이그레이션 대상 세부 정보** 화면에서 대상에 대한 연결 세부 정보를 지정합니다.

     ![대상 세부 정보](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. **저장** 을 선택합니다.

## <a name="review-the-migration-summary"></a>마이그레이션 요약 검토

1. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

2. **SSIS 프로젝트 및 환경 덮어쓰기 옵션** 의 경우 기존 SSIS 프로젝트 및 환경을 덮어쓸지 아니면 무시할지 지정합니다.

    ![마이그레이션 프로젝트 요약](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.