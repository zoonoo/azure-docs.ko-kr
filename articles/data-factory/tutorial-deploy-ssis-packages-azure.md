---
title: Azure-SSIS 통합 런타임 프로비전
description: Azure에 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Data Factory에서 Azure-SSIS 통합 런타임을 프로비전하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 2cec7fdae1d3a2a336decc11347ef9bd1039ce7f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926559"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure-SSIS 통합 런타임 프로비저닝

이 자습서에서는 Azure Portal을 사용하여 Azure Data Factory에서 Azure-SSIS(SQL Server Integration Services) IR(통합 런타임)을 프로비저닝하는 단계를 제공합니다. Azure-SSIS IR에서 지원하는 작업은 다음과 같습니다.

- Azure SQL Database 서버 또는 관리형 인스턴스(프로젝트 배포 모델)에서 호스팅하는 SSIS 카탈로그(SSISDB)에 배포된 패키지를 실행합니다.
- 파일 시스템, 파일 공유 또는 Azure Files(패키지 배포 모델)에 배포된 패키지를 실행합니다. 

Azure-SSIS IR이 프로비저닝되면 익숙한 도구를 사용하여 Azure에서 패키지를 배포하고 실행할 수 있습니다. 이러한 도구에는 SQL Server Data Tools, SQL Server Management Studio 및 명령줄 도구(예: `dtinstall`, `dtutil` 및 `dtexec`)가 포함되어 있습니다.

Azure-SSIS IR의 개념 정보는 [Azure-SSIS 통합 런타임 개요](concepts-integration-runtime.md#azure-ssis-integration-runtime)를 참조하세요.

이 자습서에서는 다음 단계를 완료합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 프로비전

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- **Azure SQL Database 서버(선택 사항)** 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. 그러면 Data Factory에서 SSISDB 인스턴스를 이 데이터베이스 서버에 만듭니다. 

  Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 통합 런타임에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다.

  다음 사항에 유의하세요.

  - 선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스팅할 데이터베이스 서버 유형을 선택하기 위한 지침은 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리형 인스턴스 비교](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)를 참조하세요. 
  
    가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.
  - 데이터베이스 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. 가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하여 SSISDB를 호스팅하는 경우에는 이 설정이 적용되지 않습니다. 자세한 내용은 [Azure SQL 데이터베이스 보호](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)을 참조하세요.
  - 클라이언트 머신의 IP 주소 또는 이러한 주소가 포함된 IP 주소의 범위를 데이터베이스 서버에 대한 방화벽 설정의 클라이언트 IP 주소 목록에 추가합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../sql-database/sql-database-firewall-configure.md)을 참조하세요.
  - 서버 관리자 자격 증명을 통한 SQL 인증을 사용하거나 데이터베이스 서버에 연결하거나 데이터 팩터리의 관리 ID를 통한 Azure AD 인증을 사용하여 데이터베이스 서버에 연결할 수 있습니다. 후자의 경우 데이터 팩터리의 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure AD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.
  - 데이터베이스 서버에 SSISDB 인스턴스가 아직 없는지 확인합니다. Azure-SSIS IR 프로비저닝은 기존 SSISDB 인스턴스 사용을 지원하지 않습니다.


> [!NOTE]
> Data Factory 및 Azure-SSIS IR을 현재 사용할 수 있는 Azure 지역의 목록은 [지역별 사용 가능한 Data Factory 및 SSIS IR](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)을 참조하세요. 

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

Azure Portal을 통해 데이터 팩터리를 만들려면 [UI를 통해 데이터 팩터리 만들기](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)의 단계별 지침을 따릅니다. 만든 후에 빠르게 액세스할 수 있도록 하려면 이 작업을 수행하면서 **대시 보드에 고정**을 선택합니다. 

데이터 팩터리가 만들어지면 Azure Portal에서 개요 페이지를 엽니다. **작성자 및 모니터링** 타일을 선택하여 별도의 탭에서 **같이 시작해 볼까요?** 페이지를 엽니다. 여기서 Azure-SSIS IR을 계속 만들 수 있습니다.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 만들기

### <a name="from-the-data-factory-overview"></a>Data Factory 개요에서

1. **시작** 페이지에서 **SSIS Integration Runtime 구성** 타일을 선택합니다. 

   !["SSIS Integration Runtime 구성" 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

### <a name="from-the-authoring-ui"></a>작성 UI에서

1. Azure Data Factory UI에서 **편집** 탭으로 전환하여 **연결**을 선택합니다. 그런 다음, **통합 런타임** 탭으로 전환하여 데이터 팩터리의 기존 통합 런타임을 확인합니다. 

   ![기존 IR 보기 선택](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. **새로 만들기**를 선택하여 Azure-SSIS IR을 만듭니다. 

   ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **Integration Runtime 설정** 창에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트합니다.)를 선택하고, **다음**을 선택합니다. 

   ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비전

1. **통합 런타임 설치**의 **일반 설정** 페이지에서 다음 단계를 완료합니다. 

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. **이름**에는 통합 런타임의 이름을 입력합니다. 

   b. **설명**에는 통합 런타임에 대한 설명을 입력합니다. 

   다. **위치**에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다. 

   d. **노드 크기**로는 통합 런타임 클러스터의 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 여러 개의 계산 집약적 또는 메모리 집약적 패키지를 실행하려면 큰 노드 크기(강화)를 선택합니다. 

   e. **노드 수**로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려면 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다. 

   f. **버전/라이선스**의 경우 통합 런타임에 대한 SQL Server 버전, 즉 Standard 또는 Enterprise를 선택합니다. 통합 런타임에서 고급 기능을 사용하려면 Enterprise를 선택합니다. 

   g. **비용 절감**의 경우 통합 런타임에 대한 AHB(Azure 하이브리드 혜택) 옵션에서 **예** 또는 **아니요**를 선택합니다. Software Assurance를 통해 사용자 고유의 SQL Server 라이선스를 가져와서 하이브리드를 사용함으로써 비용을 절감하려면 **예**를 선택합니다. 

   h. **다음**을 선택합니다. 

1. **SQL 설정** 페이지에서 다음 단계를 완료합니다. 

   ![SQL 설정](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Azure-SSIS IR에서 실행할 패키지의 배포 모델을 선택하려면 **SSIS 카탈로그 만들기...** 확인란을 선택합니다. 패키지를 데이터베이스 서버에서 호스팅하는 SSISDB에 배포하는 프로젝트 배포 모델 또는 패키지를 파일 시스템, 파일 공유 또는 Azure Files로 배포하는 패키지 배포 모델을 선택합니다.
   
   확인란을 선택하면 사용자를 대신하여 만들고 관리하는 SSISDB를 호스팅하기 위해 사용자 고유의 데이터베이스 서버를 가져와야 합니다.
   
   b. **구독**에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

   다. **위치**에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다.

   d. **카탈로그 데이터베이스 서버 엔드포인트**로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 
   
   선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스팅할 데이터베이스 서버 유형을 선택하기 위한 지침은 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리형 인스턴스 비교](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)를 참조하세요.   

   가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 선택하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

   e. SSISDB를 호스팅할 데이터베이스 서버의 인증 방법을 선택하려면 **ADF의 관리 ID를 통한 AAD 인증 사용** 확인란을 선택합니다. 데이터 팩터리의 관리 ID를 통한 SQL 인증 또는 Azure AD 인증을 선택합니다.

   확인란을 선택하면 데이터 팩터리의 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure AD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.
   
   f. **관리 사용자 이름**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

   g. **관리자 암호**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

   h. **카탈로그 데이터베이스 서비스 계층**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 서비스 계층을 선택합니다. 기본, 표준 또는 프리미엄 계층을 선택하거나 탄력적 풀 이름을 선택합니다.

   i. **연결 테스트**를 클릭합니다. 테스트가 성공하면 **다음**을 선택합니다. 

1. **고급 설정** 페이지에서 다음 단계를 완료합니다. 

   ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **노드당 최대 병렬 실행 수**에 대해 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 둘 이상의 코어를 사용하여 계산 집약적이거나 메모리 집약적인 하나의 대형 패키지를 실행하려면 낮은 숫자를 선택합니다. 하나의 코어에서 하나 이상의 소형 패키지를 실행하려면 높은 숫자를 선택합니다. 

   b. **사용자 지정 설치 컨테이너 SAS URI**에는 필요에 따라 설치 스크립트 및 관련 파일이 저장되는 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 입력합니다. 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)를 참조하세요.
   
   다. 통합 런타임을 가상 네트워크에 조인할지 여부를 선택하려면 **조인할 Azure-SSIS Integration Runtime에 대한 VNet을 선택하고 ADF에서 특정 네트워크 리소스를 만들도록 허용** 확인란을 선택합니다.

   가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버를 사용하거나 SSISDB를 호스팅하기 위해 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하는 경우 또는 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 선택합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 
   
   d. 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성할지 여부를 선택하려면 **자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime의 프록시로 설정** 확인란을 선택합니다. 자세한 내용은 [자체 호스팅 IR을 프록시로 설정](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)을 참조하세요.   

1. **다음**을 선택합니다. 

1. **요약** 페이지에서 모든 프로비저닝 설정을 검토하고, 추천 설명서 링크를 책갈피로 설정하고, **마침**을 선택하여 통합 런타임을 만들기 시작합니다. 

   > [!NOTE]
   > 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다.
   >
   > SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 
   > 
   > Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 설치할 수 있는 다른 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

1. 필요한 경우 **연결** 탭에서 **Integration Runtime**으로 전환합니다. **새로 고침**을 선택하여 상태를 새로 고칩니다. 

   !["새로 고침" 단추와 생성 상태](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. **작업** 열의 링크를 사용하여 통합 런타임을 중지/시작, 편집 또는 삭제합니다. 마지막 링크를 사용하여 통합 런타임에 대한 JSON 코드를 살펴봅니다. 편집 및 삭제 단추는 IR이 중지된 경우에만 활성화됩니다. 

   !["작업" 열의 링크](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포

SSISDB를 사용하는 경우 SQL Server Data Tools 또는 SQL Server Management Studio 도구를 사용하여 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 이러한 도구는 다음과 같은 서버 엔드포인트를 통해 데이터베이스 서버에 연결됩니다. 

- 프라이빗 엔드포인트가 있는 Azure SQL Database 서버의 경우 서버 엔드포인트 형식은 `<server name>.database.windows.net`입니다.
- 프라이빗 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.<dns prefix>.database.windows.net`입니다.
- 공용 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.public.<dns prefix>.database.windows.net,3342`입니다. 

SSISDB를 사용하지 않는 경우 패키지를 파일 시스템, 파일 공유 또는 Azure Files에 배포할 수 있습니다. 그런 다음, `dtinstall`, `dtutil` 및 `dtexec` 명령줄 도구를 사용하여 Azure-SSIS IR에서 이러한 도구를 실행할 수 있습니다. 자세한 내용은 [SSIS 패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)를 참조하세요. 

또한 두 경우 모두 Data Factory 파이프라인에서 SSIS 패키지 실행 활동을 사용하여 배포된 패키지를 Azure-SSIS IR에서 실행할 수도 있습니다. 자세한 내용은 [SSIS 패키지 실행을 Data Factory 첫 번째 클래스 활동으로 호출](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조하세요.

다음 SSIS 설명서도 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>다음 단계

Azure-SSIS 통합 런타임을 사용자 지정하는 방법에 대해 알아보려면 다음 문서로 이동하세요. 

> [!div class="nextstepaction"]
> [Azure-SSIS IR 사용자 지정](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)