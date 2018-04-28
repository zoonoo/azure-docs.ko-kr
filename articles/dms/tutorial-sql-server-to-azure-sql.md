---
title: Azure Database Migration Service를 사용하여 SQL Server를 Azure SQL Database로 마이그레이션 | Microsoft Docs
description: Azure Database Migration Service를 사용하여 SQL Server 온-프레미스에서 Azure SQL로 마이그레이션하는 방법에 대해 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 48f1b3715f300fea7bfc0590e6d2e6c6622e83aa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Azure SQL Database로 SQL Server 마이그레이션
Azure Database Migration Service를 사용하여 온-프레미스 SQL Server 인스턴스에서 Azure SQL Database로 데이터베이스를 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service를 사용하여 SQL Server 2016 이상의 온-프레미스 인스턴스로 복원된 **Adventureworks2012** 데이터베이스를 Azure SQL Database로 마이그레이션합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Data Migration Assistant를 사용하여 온-프레미스 데이터베이스를 평가합니다.
> * Data Migration Assistant를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

- [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads)(모든 버전)을 다운로드 및 설치합니다.
- 문서 [서버 네트워크 프로토콜 사용 또는 사용 안 함](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)의 지침을 수행하여 SQL Server Express를 설치하는 동안 기본적으로 사용 안 함으로 설정되어 있는 TCP/IP 프로토콜을 사용하도록 설정합니다.
- [Azure Portal에서 Azure SQL Database 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) 문서의 세부 지침을 수행하여 Azure SQL Database 인스턴스를 만듭니다.
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상을 다운로드 및 설치합니다.
- Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 VNET을 만듭니다. 이를 통해 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용하여 온-프레미스 원본 서버에서 사이트 간 연결을 제공합니다.
- Azure VNET(Virtual Network) 네트워크 보안 그룹 규칙이 443, 53, 9354, 445, 12000과 같은 통신 포트를 차단하지 않는지 확인합니다. Azure VNET NSG 트래픽 필터링에 대한 자세한 정보는 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 문서를 참조하세요.
- [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
- 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있도록 방화벽을 통해 UDP 포트 1434에 액세스할 수 있습니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
- 대상 데이터베이스에 대한 Azure Database Migration Service 액세스를 허용하도록 Azure SQL Database 서버에 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용되는 VNET의 서브넷 범위를 제공합니다.
- 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에는 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있어야 합니다.
- 대상 Azure SQL Database 인스턴스에 연결하는 데 사용되는 자격 증명에는 대상 Azure SQL DB에 대한 CONTROL DATABASE 권한이 있어야 합니다.

## <a name="assess-your-on-premises-database"></a>온-프레미스 데이터베이스 평가
온-프레미스 SQL Server 인스턴스에서 Azure SQL Database로 데이터를 마이그레이션하려면 먼저 마이그레이션을 차단할 수 있는 차단 문제가 있는지 SQL Server 데이터베이스를 평가해야 합니다. Data Migration Assistant v3.3 이상에서 [SQL Server 마이그레이션 평가 수행](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) 문서에 설명된 단계를 수행하여 온-프레미스 데이터베이스 평가를 완료합니다. 필수 단계의 요약은 다음과 같습니다.
1.  Data Migration Assistant에서 새로 만들기(+) 아이콘을 선택하고 **평가** 프로젝트 형식을 선택합니다.
2.  프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택합니다.
3.  **만들기**를 선택하여 프로젝트를 만듭니다.

    Azure SQL Database에 대한 원본 SQL Server 데이터베이스 마이그레이션을 평가 중인 경우 다음 평가 보고서 유형 중 하나를 선택하거나 둘 다 선택할 수 있습니다.
    - 데이터베이스 호환성 확인
    - 기능 패리티 확인

    두 보고서 유형이 모두 기본적으로 선택됩니다.
4.  Data Migration Assistant의 **옵션** 화면에서 **다음**을 선택합니다.
5.  **원본 선택** 화면의 **서버에 연결** 대화 상자에서 SQL Server에 대한 연결 세부 정보를 제공하고 **연결**을 선택합니다.
6.  **원본 추가** 대화 상자에서 **AdventureWorks2012**를 선택하고 **추가**를 선택한 후 **평가 시작**을 선택합니다.

    평가가 완료되면 다음 그래픽에 표시된 대로 결과가 표시됩니다.

    ![데이터 마이그레이션 평가](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Azure SQL Database의 경우 평가를 통해 마이그레이션 차단 문제와 기능 패리티 문제를 식별합니다.

7.  특정 옵션을 선택하여 마이그레이션 차단 문제와 기능 패리티 문제에 대한 평가 결과를 검토합니다.
    - **SQL Server 기능 패리티** 범주는 전체 권장 사항 집합, Azure에서 사용 가능한 대체 방법 및 마이그레이션 프로젝트에 대한 작업을 계획하는 데 도움이 되는 완화 단계를 제공합니다.
    - **호환성 문제** 범주는 온-프레미스 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하는 작업을 차단할 수 있는 호환성 문제를 반영하는 기능을 부분적으로 지원되거나 지원되지 않는 기능을 식별합니다. 해당 문제를 해결하는 데 도움이 되는 권장 사항도 제공됩니다.


## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션
평가가 만족스럽고 선택한 데이터베이스가 Azure SQL Database로 마이그레이션할 수 있는 좋은 후보임을 확신한 후 Data Migration Assistant를 사용하여 스키마를 Azure SQL Database로 마이그레이션합니다.

> [!NOTE]
> Data Migration Assistant에서 마이그레이션 프로젝트를 만들기 전에 필수 구성 요소에 설명된 대로 이미 Azure SQL Database를 프로비전했어야 합니다. 이 자습서에서는 Azure SQL Database의 이름은 **AdventureWorksAzure**로 가정하지만, 원하는 경우 이름을 다르게 지정할 수 있습니다.

**AdventureWorks2012** 스키마를 Azure SQL Database로 마이그레이션하려면 다음 단계를 수행합니다.

1.  Data Migration Assistant에서 새로 만들기(+) 아이콘을 선택하고 **속성 유형**에서 **마이그레이션**을 선택합니다.
3.  프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택합니다.
4.  **마이그레이션 범위**에서 **스키마만**을 선택합니다.

    이전 단계를 수행한 후 다음 그래픽에 표시된 대로 Data Migration Assistant 인터페이스가 표시됩니다.
    
    ![Data Migration Assistant 프로젝트 만들기](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  **만들기**를 선택하여 프로젝트를 만듭니다.
6.  Data Migration Assistant에서 SQL Server에 대한 원본 연결 세부 정보를 지정하고, **연결**을 선택하고 나서, **AdventureWorks2012** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 원본 연결 세부 정보](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  **대상 서버에 연결**에서 **다음**을 선택하고, Azure SQL Database에 대한 대상 연결 세부 정보를 지정하고, **연결**을 선택하고 나서, Azure SQL Database에서 미리 프로비전된 **AdventureWorksAzure** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 대상 연결 세부 정보](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  **다음**을 선택하여 **개체 선택** 화면으로 이동합니다. 여기에서 Azure SQL Database에 배포해야 하는 **AdventureWorks2012** 데이터베이스의 스키마 개체를 지정할 수 있습니다.

    기본적으로 모든 개체가 선택됩니다.

    ![SQL 스크립트 생성](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  **SQL 스크립트 생성**을 선택하여 SQL 스크립트를 만들고 스크립트에 오류가 있는지 검토합니다.

    ![스키마 스크립트](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. **스키마 배포**를 선택하여 Azure SQL Database에 스키마를 배포하고, 스키마가 배포된 후 대상 서버에 이상이 있는지 확인합니다.

    ![스키마 배포](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록
1. Azure Portal에 로그인하고 **모든 서비스**를 선택한 후 **구독**을 선택합니다.
 
   ![포털 구독 표시](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음 **리소스 공급자**를 선택합니다.
 
    ![리소스 공급자 보기](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.
 
    ![리소스 공급자 등록](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>인스턴스 만들기
1.  Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색하고 나서, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  **Azure Database Migration Service(미리 보기)** 화면에서 **만들기**를 선택합니다.
 
    ![Azure Database Migration Service 인스턴스 만들기](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  **Database Migration Service** 화면에서 서비스, 구독, 가상 네트워크 및 가격 책정 계층의 이름을 지정합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

     ![Azure Database Migration Service 인스턴스 설정 구성](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기
서비스가 생성된 후 Azure Portal에서 서비스를 찾고 마이그레이션 프로젝트를 만듭니다.
1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.
 
      ![Azure Database Migration Service의 모든 인스턴스 찾기](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. **Azure Database Migration Services** 화면에서 방금 만든 Azure DMS 인스턴스를 검색하고 인스턴스를 선택합니다.
 
     ![Azure Database Migration Service 인스턴스 찾기](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. **+ 새 마이그레이션 프로젝트**를 선택합니다.
4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택합니다.

    ![Database Migration Service 프로젝트 만들기](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  **만들기**를 선택하여 프로젝트를 만듭니다.


## <a name="specify-source-details"></a>원본 세부 정보 지정
1. **원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

    ![원본 선택](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. **저장**을 선택하고 마이그레이션을 위해 **AdventureWorks2012** 데이터베이스를 선택합니다.

    ![원본 DB 선택](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>대상 세부 정보 지정
1. **저장**을 선택하고 **대상 세부 정보** 화면에서 대상에 대한 연결 세부 정보를 지정합니다. 대상은 Data Migration Assistant를 통해 **AdventureWorks2012** 스키마가 배포된 미리 프로비전된 Azure SQL Database입니다.

    ![대상 선택](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. **저장**을 선택하여 프로젝트를 저장합니다.
3. **마이그레이션 요약** 화면에서 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

    ![DMS 요약](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. **저장**을 선택합니다.

## <a name="run-the-migration"></a>마이그레이션 실행
1.  최근에 저장된 프로젝트를 선택하고, **+ 새 활동**을 선택한 다음, **데이터 마이그레이션 실행**을 선택합니다.

    ![새 활동](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  메시지가 표시되면 원본 및 대상 서버에 대한 자격 증명을 입력하고 **저장**을 선택합니다.
3.  **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure DMS는 기본적으로 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. **저장**을 선택하고, **테이블 선택** 화면에서 테이블 목록을 확장하고, 영향받는 필드 목록을 검토합니다.

    ![테이블 선택](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  **저장**을 선택하고 **마이그레이션 요약** 화면의 **활동 이름** 텍스트 상자에서 마이그레이션 활동의 이름을 지정합니다.

    이 화면에서는 마이그레이션된 데이터베이스의 유효성을 검사하도록 지정하는 데 사용할 수 있는 **유효성 검사 옵션** 화면을 확장할 수도 있습니다.
    - 스키마
    - 데이터 일관성
    - 쿼리 정확성 및 성능

    ![유효성 검사 옵션 선택](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  **저장**을 선택하고 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 것과 일치하는지 확인합니다.

    ![마이그레이션 요약](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  **마이그레이션 실행**을 선택하여 마이그레이션 활동을 시작하고 **새로 고침**을 선택하여 현재 상태를 검토합니다.

    ![활동 상태](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링
1. 활동 상태를 검토할 마이그레이션 활동을 선택합니다.
2. 마이그레이션이 완료된 후 대상 Azure SQL Database를 확인합니다.

    ![Completed](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
