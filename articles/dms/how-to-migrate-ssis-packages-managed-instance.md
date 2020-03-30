---
title: SSIS 패키지를 SQL 관리 인스턴스로 마이그레이션
titleSuffix: Azure Database Migration Service
description: Azure 데이터베이스 마이그레이션 서비스 또는 데이터 마이그레이션 도우미를 사용하여 SQL Server 통합 서비스(SSIS) 패키지 및 프로젝트를 Azure SQL Database 관리 인스턴스로 마이그레이션하는 방법을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297177"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>SQL Server Integration Services 패키지를 Azure SQL Database  Managed Instance로 마이그레이션
SQL Server 통합 서비스(SSIS)를 사용하고 SQL Server에서 호스팅하는 SSISDB 원본 SSISDB에서 Azure SQL Database 관리 인스턴스에서 호스팅하는 대상 SSISDB로 SSIS 프로젝트/패키지를 마이그레이션하려는 경우 Azure 데이터베이스 마이그레이션 서비스를 사용할 수 있습니다.

사용하는 SSIS 버전이 2012년 이전이거나 SSISDB 패키지가 아닌 저장소 형식을 사용하는 경우 SSIS 프로젝트/패키지를 마이그레이션하기 전에 SSMS에서 시작할 수도 있는 통합 서비스 프로젝트 변환 마법사를 사용하여 변환해야 합니다. 자세한 내용은 문서 [프로젝트를 프로젝트 배포 모델로 변환](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)을 참조하세요.

> [!NOTE]
> Azure 데이터베이스 마이그레이션 서비스(DMS)는 현재 Azure SQL Database를 대상 마이그레이션 대상으로 지원하지 않습니다. SSIS 프로젝트/패키지를 Azure SQL 데이터베이스에 다시 배포하려면 [SQL Server 통합 서비스 패키지를 Azure SQL Database에 다시 배포하는](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)문서를 참조하십시오.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 원본 SSIS 프로젝트/패키지를 평가합니다.
> * Azure에 SSIS 프로젝트/패키지를 마이그레이션합니다.

## <a name="prerequisites"></a>사전 요구 사항

이러한 단계를 완료하려면 다음이 필요합니다.

* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)사용하여 온-프레미스 소스 서버에 대한 사이트 간 연결을 제공하는 Azure 리소스 관리자 배포 모델을 사용하여 Azure 데이터베이스 마이그레이션 서비스에 대한 Microsoft Azure 가상 네트워크를 만들려면 자세한 내용은 [Azure 데이터베이스 마이그레이션 서비스를 사용하여 Azure SQL Database 관리 인스턴스 마이그레이션에 대한 네트워크 토폴로지]( https://aka.ms/dmsnetworkformi)문서를 참조하십시오. 가상 네트워크 만들기에 대한 자세한 내용은 [가상 네트워크 설명서](https://docs.microsoft.com/azure/virtual-network/)및 특히 단계별 세부 정보가 있는 빠른 시작 문서를 참조하십시오.
* 가상 네트워크 네트워크 보안 그룹 규칙이 Azure 데이터베이스 마이그레이션 서비스에 대한 다음 인바운드 통신 포트를 차단하지 않도록 하려면 443, 53, 9354, 445, 12000 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹과 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)문서를 참조하십시오.
* [원본 데이터베이스 엔진 액세스에 대한 Windows 방화벽을](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)구성하려면 .
* Windows 방화벽을 열어 Azure 데이터베이스 마이그레이션 서비스가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스할 수 있도록 허용합니다.
* 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우, SQL Browser 서비스를 사용하도록 설정하고 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service에서 마이그레이션을 위해 445 SMB 포트를 통해 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure SQL Database는 SSISDB를 호스트하기 위해 인스턴스를 관리했습니다. 하나를 만들어야 하는 경우 [Azure SQL 데이터베이스 관리 인스턴스 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)문서의 세부 정보를 따르십시오.
* 원본 SQL Server 및 대상 관리 인스턴스를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 구성원인지 확인합니다.
* SSIS가 Azure-SSIS 통합 런타임(IR)을 포함하는 Azure-SSIS 통합 런타임(IR)을 포함하는 Azure SQL Database 관리 인스턴스에서 호스팅되는 대상 SSISDB(Azure-SSIS 통합 런타임)에서 프로비전되는지 확인하려면(문서에 설명된 대로 [Azure-SSIS 통합 런타임을 Azure Data Factory에서 만들기).](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

## <a name="assess-source-ssis-projectspackages"></a>원본 SSIS 프로젝트/패키지 평가

원본 SSISDB에 대한 평가가 아직 DMA(데이터베이스 마이그레이션 도우미)에 통합되지 않았지만 Azure SQL Database 관리 인스턴스에서 호스팅되는 대상 SSISDB에 다시 배포될 때 SSIS 프로젝트/패키지가 평가/유효성 을 검사합니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

    ![포털 구독 표시](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **레지스터를**선택합니다.

    ![리소스 공급자 등록](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure 포털에서 + **리소스 만들기,** **Azure 데이터베이스 마이그레이션 서비스**검색 및 드롭다운 목록에서 Azure 데이터베이스 마이그레이션 **서비스를** 선택합니다.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. DMS 인스턴스를 만들려는 위치를 선택합니다.

5. 기존 가상 네트워크를 선택하거나 가상 네트워크를 만듭니다.

    가상 네트워크는 Azure 데이터베이스 마이그레이션 서비스에 원본 SQL Server 및 대상 Azure SQL Database 관리 인스턴스에 대한 액세스를 제공합니다.

    Azure 포털에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure 포털을 사용하여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조하십시오.

    자세한 내용은 [Azure Database Migration Service를 사용한 Azure SQL DB 관리되는 인스턴스 마이그레이션에 대한 네트워크 토폴로지](https://aka.ms/dmsnetworkformi) 문서를 참조하세요.

6. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지를](https://aka.ms/dms-pricing)참조하십시오.

    ![DMS 서비스 만들기](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스 인스턴스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Azure **데이터베이스 마이그레이션 서비스** 화면에서 만든 인스턴스의 이름을 검색한 다음 인스턴스를 선택합니다.

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.

4. 새 **마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고 **소스 서버 유형** 텍스트 상자에서 SQL **Server를**선택하고 대상 **서버 유형** 텍스트 상자에서 Azure SQL Database 관리 **인스턴스를**선택한 다음 **활동 유형 선택에**대해 **SSIS 패키지 마이그레이션을**선택합니다.

   ![DMS 프로젝트 만들기](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. **만들기**를 선택하여 프로젝트를 만듭니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **마이그레이션 원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

2. 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명된 인증서를 사용하여 암호화된 TLS 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명된 인증서를 사용하여 TLS에 의존해서는 안 됩니다.

   ![원본 세부 정보](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. 마이그레이션 **대상 세부 정보** 화면에서 대상에 대한 연결 세부 정보를 지정합니다.

     ![대상 세부 정보](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. **저장**을 선택합니다.

## <a name="review-the-migration-summary"></a>마이그레이션 요약 검토

1. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

2. **SSIS 프로젝트 및 환경 덮어쓰기 옵션의**경우 기존 SSIS 프로젝트 및 환경을 덮어쓰거나 무시할지 여부를 지정합니다.

    ![마이그레이션 프로젝트 요약](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.
