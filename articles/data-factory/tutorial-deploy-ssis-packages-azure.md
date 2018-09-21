---
title: Azure-SSIS 통합 런타임 프로비전 | Microsoft Docs
description: Azure에 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Data Factory에서 Azure-SSIS 통합 런타임을 프로비전하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 5880073706335d0524d0a8b4c8408b65f210395c
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575918"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure-SSIS 통합 런타임 프로비전
이 자습서에서는 Azure Portal을 사용하여 Azure Data Factory에서 Azure-SSIS IR(통합 런타임)을 프로비전하는 단계를 제공합니다. 그런 다음, SQL Server Data Tools 또는 SQL Server Management Studio를 사용하여 Azure에서 이 런타임에 SSIS(SQL Server Integration Services) 패키지를 배포하고 실행할 수 있습니다. Azure-SSIS IR의 개념 정보는 [Azure-SSIS 통합 런타임 개요](concepts-integration-runtime.md#azure-ssis-integration-runtime)를 참조하세요.

이 자습서에서는 다음 단계를 완료합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 프로비전

## <a name="prerequisites"></a>필수 조건
- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다. 
- **Azure SQL Database 서버**. 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. Azure Data Factory는 이 데이터베이스 서버에 SSIS 카탈로그(SSISDB 데이터베이스)를 만듭니다. Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 Integration Runtime에서 Azure 지역을 벗어나지 않고 SSISDB 데이터베이스에 실행 로그를 쓸 수 있습니다. 
- SSISDB는 선택한 데이터베이스 서버에 따라 사용자를 대신하여 공용 네트워크에서 액세스하거나 가상 네트워크를 조인하여 단일 데이터베이스, 탄력적 풀의 일부 또는 Managed Instance(미리 보기)로 만들 수 있습니다. Azure SQL Database를 가상 네트워크 서비스 엔드포인트/Managed Instance(미리 보기)와 함께 사용하여 SSISDB를 호스트하거나 온-프레미스 데이터에 액세스하도록 요구하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 
- 데이터베이스 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. Azure SQL Database를 가상 네트워크 서비스 엔드포인트/Managed Instance(미리 보기)와 함께 사용하여 SSISDB를 호스트하는 경우 이것이 적용되지 않습니다. 자세한 내용은 [Azure SQL Database 보호](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)을 참조하세요. 
- 데이터베이스 서버에 대한 방화벽 설정에서 클라이언트 IP 주소 목록에 클라이언트 컴퓨터의 IP 주소를 포함하는 클라이언트 컴퓨터의 IP 주소 또는 IP 주소의 범위를 추가합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../sql-database/sql-database-firewall-configure.md)을 참조하세요. 
- 서버 관리자 자격 증명을 통한 SQL 인증을 사용하여 또는 AAD(Azure Active Directory) 인증을 사용하여 데이터베이스 서버를 ADF(Azure Data Factory) MSI(관리 서비스 ID)에 연결할 수 있습니다.  후자의 경우 ADF MSI를 데이터베이스 서버에 대한 액세스 권한이 있는 AAD 그룹에 추가해야 합니다. [AAD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 
- Azure SQL Database 서버에 SSIS 카탈로그(SSISDB 데이터베이스)가 없는지 확인합니다. Azure-SSIS IR 프로비전은 기존 SSIS 카탈로그 사용을 지원하지 않습니다. 

> [!NOTE]
> - Data Factory를 현재 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics**를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 
> - Azure-SSIS Integration Runtime을 현재 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics**를 펼쳐서 **Azure-SSIS Integration Runtime**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다. 
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. 
1. 왼쪽 메뉴에서 **새로 만들기**를 선택하고 **데이터 + 분석**을 선택한 다음 **데이터 팩터리**를 선택합니다. 

   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. **새 데이터 팩터리** 페이지에서 **이름**에 **MyAzureSsisDataFactory**를 입력합니다. 

   !["새 데이터 팩터리" 페이지](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure Data Factory의 이름은 *전역적으로 고유*해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: **&lt;yourname&gt;MyAzureSsisDataFactory**) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. **구독**에 대해 데이터 팩터리를 만들려는 위치에 Azure 구독을 선택합니다. 
1. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다. 

   - **기존 항목 사용**을 선택하고 목록에서 기존 리소스 그룹을 선택합니다. 
   - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 

   리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요. 
1. **버전**에 **V2(미리 보기)** 를 선택합니다. 
1. **위치**에 데이터 팩터리의 위치를 선택합니다. 데이터 팩터리 만들기를 지원하는 위치만 목록에 표시됩니다. 
1. **대시보드에 고정**을 선택합니다. 
1. **만들기**를 선택합니다. 
1. 대시보드에서 **데이터 팩터리 배포 중** 상태의 다음과 같은 타일이 표시됩니다. 

   !["데이터 팩터리 배포 중" 타일](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 만들기가 완료되면 **데이터 팩터리** 페이지가 표시됩니다. 

   ![데이터 팩터리의 홈 페이지](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. **작성 및 모니터링 관리**를 선택하여 별도의 탭에서 Data Factory UI(사용자 인터페이스)를 엽니다. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비전

1. **시작** 페이지에서 **SSIS Integration Runtime 구성** 타일을 선택합니다. 

   !["SSIS Integration Runtime 구성" 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. **Integration Runtime 설정**의 **일반 설정** 페이지에서 다음 단계를 완료합니다. 

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. **이름**에는 통합 런타임의 이름을 입력합니다. 

   b. **설명**에는 통합 런타임에 대한 설명을 입력합니다. 

   다. **위치**에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다. 

   d. **노드 크기**로는 통합 런타임 클러스터의 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 계산/메모리 사용량이 많은 패키지를 실행하려는 경우 큰 노드 크기(강화)를 선택합니다. 

   e. **노드 수**로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려는 경우 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다. 

   f. **버전/라이선스**로는 통합 런타임의 SQL Server 버전/라이선스를 선택합니다(Standard 또는 Enterprise). 통합 런타임에서 고급/프리미엄 기능을 사용하려는 경우 Enterprise를 선택합니다. 

   g. **비용 절감**으로는 통합 런타임용 AHB(Azure 하이브리드 혜택) 옵션을 선택합니다(예 또는 아니요). Software Assurance에 사용자 고유의 SQL Server 라이선스를 사용하여 하이브리드의 비용 절감 혜택을 얻으려면 [예]를 선택합니다. 

   h. **다음**을 클릭합니다. 

1. **SQL 설정** 페이지에서 다음 단계를 완료합니다. 

   ![SQL 설정](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. **구독**에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

   b. **위치**에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다. 

   다. **카탈로그 데이터베이스 서버 엔드포인트**로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 선택한 데이터베이스 서버에 따라, 가상 네트워크를 조인하여 SSISDB를 자동으로 독립 실행형 데이터베이스로, 탄력적 풀의 일부로 또는 Managed Instance(미리 보기)에 만들고 공용 네트워크에서 액세스할 수 있습니다. SSISDB를 호스팅할 데이터베이스 서버의 유형을 선택하는 방법에 대한 지침은 [SQL Database 및 Managed Instance(미리 보기) 비교](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview)를 참조하세요. 가상 네트워크 서비스 엔드포인트/Managed Instance(미리 보기)가 포함된 Azure SQL Database를 선택하여 SSISDB를 호스팅하거나 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 

   d. **AAD 인증 사용...** 확인란에서 SSISDB를 호스트할 데이터베이스 서버의 인증 방법(SQL 또는 AAD(Azure Active Directory)와 ADF(Azure Data Factory) MSI(관리 서비스 ID))을 선택합니다. 이 방법을 선택하면 ADF MSI를 데이터베이스 서버에 대한 액세스 권한이 있는 AAD 그룹에 추가해야 합니다. [AAD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 

   e. **관리 사용자 이름**으로는 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

   f. **관리자 암호**로는 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

   g. **카탈로그 데이터베이스 서비스 계층**으로는 SSISDB를 호스트하는 데이터베이스 서버의 서비스 계층을 선택합니다(Basic/Standard/Premium 계층 또는 탄력적 풀 이름). 

   h. **연결 테스트**를 클릭하고, 테스트가 성공하면 **다음**을 클릭합니다. 

1. **고급 설정** 페이지에서 다음 단계를 완료합니다. 

   ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **노드당 최대 병렬 실행 수**로는 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 계산/메모리 사용량이 많은 단일 대형/무거운 패키지를 실행하는 데 코어를 2개 이상 사용하려는 경우 낮은 값을 선택합니다. 단일 코어에서 소형/가벼운 패키지를 하나 이상 실행하려는 경우 높은 값을 선택합니다. 

   b. **사용자 지정 설치 컨테이너 SAS URI**에는 필요에 따라 설치 스크립트 및 관련 파일이 저장되는 Azure Storage Blob 컨테이너의 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 입력합니다. [Azure-SSIS IR에 대한 사용자 지정 설치](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)를 참조하세요. 

   다. **VNet 선택...** 확인란에서 통합 런타임을 가상 네트워크에 조인할 것인지 선택합니다. Azure SQL Database를 가상 네트워크 서비스 엔드포인트/Managed Instance(미리 보기)와 함께 사용하여 SSISDB를 호스트할 것인지 아니면 온-프레미스 데이터에 액세스하도록 요구할 것인지 선택해야 합니다. [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 

1. **마침**을 클릭하여 통합 런타임 만들기를 시작합니다. 

   > [!IMPORTANT]
   > 이 프로세스를 완료하는 데 약 20~30분이 걸립니다.
   >
   > Data Factory 서비스는 Azure SQL Database 서버에 연결하여 SSIS 카탈로그(SSISDB 데이터베이스)를 준비합니다. 
   > 
   > Azure-SSIS IR의 인스턴스를 프로비전하는 경우 Azure Feature Pack for SSIS 및 Access 재배포 가능 패키지도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소가 지원하는 데이터 원본 외에도 Excel 및 Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 추가 구성 요소를 설치할 수도 있습니다. 자세한 내용은 [Azure-SSIS 통합 런타임 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요. 

1. 필요한 경우 **연결** 탭에서 **Integration Runtime**으로 전환합니다. **새로 고침**을 선택하여 상태를 새로 고칩니다. 

   !["새로 고침" 단추와 생성 상태](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. **작업** 열의 링크를 사용하여 통합 런타임을 중지/시작, 편집 또는 삭제합니다. 마지막 링크를 사용하여 통합 런타임에 대한 JSON 코드를 살펴봅니다. 편집 및 삭제 단추는 IR이 중지된 경우에만 활성화됩니다. 

   !["작업" 열의 링크](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 만들기

1. Azure Data Factory UI에서 **편집** 탭으로 전환하고, **연결**을 선택한 다음, **Integration Runtime** 탭으로 전환하여 데이터 팩터리에서 기존 통합 런타임을 살펴봅니다. 

   ![기존 IR 보기 선택](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. **새로 만들기**를 선택하여 Azure-SSIS IR을 만듭니다. 

   ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **Integration Runtime 설정** 창에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트합니다.)를 선택하고, **다음**을 선택합니다. 

   ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포
이제 SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)를 사용하여 Azure에 SSIS 패키지를 배포합니다. SSIS 카탈로그(SSISDB 데이터베이스)를 호스트하는 Azure SQL Database 서버에 연결합니다. Azure SQL Database 서버의 이름은 `<servername>.database.windows.net`형식입니다. 

SSIS 설명서에서 다음 문서를 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure에서 SSIS 카탈로그에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 프로비전

온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 이동하세요. 

> [!div class="nextstepaction"]
> [클라우드에 데이터 복사](tutorial-copy-data-portal.md)
