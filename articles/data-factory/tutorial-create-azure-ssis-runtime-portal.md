---
title: "포털을 사용하여 Azure SSIS 통합 런타임 프로비전 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 Azure-SSIS 통합 런타임을 만드는 방법을 설명합니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: b6a795f8a26340f24f9e09aea371ba90afe50101
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Data Factory UI를 사용하여 Azure SSIS 통합 런타임 프로비전
이 자습서에서는 Azure Portal을 사용하여 Azure Data Factory에서 Azure-SSIS IR(통합 런타임)을 프로비전하는 단계를 제공합니다. 그런 다음 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에서 이 런타임에 SSIS(SQL Server Integration Services) 패키지를 배포할 수 있습니다. Azure-SSIS IR의 개념 정보는 [Azure-SSIS 통합 런타임 개요](concepts-integration-runtime.md#azure-ssis-integration-runtime)를 참조하세요.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임을 만들고 시작합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.


## <a name="prerequisites"></a>필수 조건
- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다. 
- **Azure SQL Database 서버**. 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. Azure Data Factory는 이 데이터베이스 서버에 SSISDB(SSIS 카탈로그 데이터베이스)를 만듭니다. Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 Integration Runtime에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다. 
    - 데이터베이스 서버에 대해 "**Azure 서비스 방문 허용**" 설정을 사용하도록 설정되어 있는지 확인합니다. 자세한 내용은 [Azure SQL Database 보호](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)을 참조하세요.
    - 데이터베이스 서버에 대한 방화벽 설정에서 클라이언트 IP 주소 목록에 클라이언트 컴퓨터의 IP 주소를 포함하는 클라이언트 컴퓨터의 IP 주소 또는 IP 주소의 범위를 추가합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../sql-database/sql-database-firewall-configure.md)을 참조하세요. 
 
## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.    
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **MyAzureSsisDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameMyAzureSsisDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)**를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 데이터 팩터리 만들기를 지원하는 위치만 목록에 표시됩니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. **작성 및 모니터링**을 클릭하여 별도의 탭에서 Data Factory UI(사용자 인터페이스)를 시작합니다. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임 프로비전

1. 시작 페이지에서 **SSIS Integration Runtime 구성** 타일을 클릭합니다. 

   ![SSIS Integration Runtime 구성 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. **Integration Runtime 설정**의 **일반 설정** 페이지에서 다음 단계를 수행합니다. 

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. 통합 런타임에 대한 **이름**을 지정합니다.
    2. 통합 런타임에 대한 **위치**를 선택합니다. 지원되는 위치만 표시됩니다.
    3. SSIS 런타임으로 구성할 **노드 크기**를 선택합니다.
    4. 클러스터의 **노드 수**를 지정합니다.
    5. **다음**을 클릭합니다. 
1. **SQL 설정**에서 다음 단계를 수행합니다. 

    ![SQL 설정](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Azure SQL 서버가 있는 Azure **구독**을 지정합니다. 
    2. **카탈로그 데이터베이스 서버 엔드포인트**에 대한 Azure SQL 서버를 선택합니다.
    3. **관리자** 사용자 이름을 입력합니다.
    4. 관리자에 대한 **암호**를 입력합니다.  
    5. SSISDB 데이터베이스에 대한 **서비스 계층**을 선택합니다. 기본값은 [기본]입니다.
    6. **다음**을 클릭합니다. 
1.  **고급 설정** 페이지에서 **노드당 최대 병렬 실행 횟수**에 대한 값을 선택합니다.   

    ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. 이 단계는 **선택 사항**입니다. 통합 런타임을 조인하려는 클래식 VNet(가상 네트워크)이 있는 경우, **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings**(Azure-SSIS 통합 런타임용 VNet을 선택하고 Azure 서비스에서 VNet 권한/설정을 구성하도록 허용) 옵션을 선택하고 다음 단계를 수행합니다. 

    ![VNet이 포함된 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. 클래식 VNet이 있는 **구독**을 지정합니다. 
    2. **VNet**을 선택합니다. <br/>
    4. **서브넷**을 선택합니다.<br/> 
1. **마침**을 클릭하여 Azure-SSIS 통합 런타임을 만들기 시작합니다. 

    > [!IMPORTANT]
    > - 이 프로세스를 완료하는 데 약 20분이 걸립니다.
    > - Data Factory 서비스는 Azure SQL Database에 연결하여 SSISDB(SSIS 카탈로그 데이터베이스)를 준비합니다. 또한 이 스크립트는 VNet에 대한 권한 및 설정을 구성하고(지정된 경우) Azure-SSIS Integration Runtime의 새 인스턴스를 VNet에 조인합니다.
7. 필요한 경우 **연결** 창에서 **Integration Runtime**으로 전환합니다. **새로 고침**을 클릭하여 상태를 새로 고칩니다. 

    ![만들기 상태](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. **작업** 열 아래의 링크를 사용하여 통합 런타임을 모니터링, 중지/시작, 편집 또는 삭제합니다. 마지막 링크를 사용하여 통합 런타임에 대한 JSON 코드를 살펴봅니다. 편집 및 삭제 단추는 IR이 중지된 경우에만 활성화됩니다. 

    ![Azure SSIS IR - 작업](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. **작업** 아래에서 **모니터** 링크를 클릭합니다.  

    ![Azure SSIS IR - 세부 정보](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Azure SSIS IR과 관련된 **오류**가 있는 경우, 이 페이지의 오류 수와 오류에 대한 세부 정보를 볼 수 있는 링크가 표시됩니다. 예를 들어, SSIS 카탈로그가 이미 데이터베이스 서버에 있는 경우 SSISDB 데이터베이스의 존재 여부를 나타내는 오류가 표시됩니다.  
11. 위쪽의 **Integration Runtime**을 클릭하여 이전 페이지로 돌아가서 데이터 팩터리와 관련된 모든 통합 런타임을 확인합니다.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>포털의 Azure SSIS 통합 런타임

1. Azure Data Factory UI에서 **편집** 탭으로 전환하고, **연결**을 클릭한 다음, **Integration Runtime** 탭으로 전환하여 데이터 팩터리에서 기존 통합 런타임을 살펴봅니다. 
    ![기존 IR 보기](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. **새로 만들기**를 클릭하여 새운 Azure-SSIS IR을 만듭니다. 

    ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Azure-SSIS 통합 런타임을 만들려면 이미지에서 표시한 대로 **새로 만들기**를 클릭합니다. 
3. [Integration Runtime 설정] 창에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트합니다.)를 선택하고, **다음**을 클릭합니다.

    ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

    
## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포
이제 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에 SSIS 패키지를 배포합니다. SSISDB(SSIS 카탈로그 데이터베이스)를 호스팅하는 Azure SQL Server에 연결합니다. Azure SQL Server 이름의 형식은 `<servername>.database.windows.net`(Azure SQL Database의 경우)입니다. 

SSIS 설명서에서 다음 문서를 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSIS 카탈로그에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임을 만들고 시작합니다.

온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 진행하세요. 

> [!div class="nextstepaction"]
>[클라우드에서 데이터 복사](tutorial-copy-data-portal.md)
