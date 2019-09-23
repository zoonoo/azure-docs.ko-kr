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
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009645"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure-SSIS 통합 런타임 프로비전

이 자습서에서는 ADF(Azure Data Factory)에서 Azure Portal을 사용하여 Azure-SSIS(SQL Server Integration Services) IR(Integration Runtime)을 프로비저닝하는 단계를 제공합니다. Azure-SSIS IR은 Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)에서 호스팅하는 SSIS 카탈로그(SSISDB)에 배포된 패키지와 파일 시스템/파일 공유/Azure Files(패키지 배포 모델)에 배포된 패키지를 실행하도록 지원합니다. Azure-SSIS IR이 프로비저닝된 후에는 SSDT(SQL Server Data Tools)/SSMS(SQL Server Management Studio)와 같은 익숙한 도구, `dtinstall`/`dtutil`/`dtexec` 같은 명령줄 유틸리티를 사용하여 Azure에서 패키지를 배포하고 실행할 수 있습니다. Azure-SSIS IR의 개념 정보는 [Azure-SSIS 통합 런타임 개요](concepts-integration-runtime.md#azure-ssis-integration-runtime)를 참조하세요.

이 자습서에서는 다음 단계를 완료합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 프로비전

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- **Azure SQL Database 서버(선택 사항)** 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. 그러면 ADF가 이 데이터베이스 서버에 SSISDB를 만듭니다. Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 통합 런타임에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다. 
    - 선택한 데이터베이스 서버에 따라 사용자를 대신하여 단일 데이터베이스로, 탄력적 풀의 일부분으로, 또는 Managed Instance에서 SSISDB를 만들 수 있습니다. 이러한 SSISDB는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버 유형을 선택하는 지침은 [Azure SQL Database 단일 데이터베이스/탄력적 풀/Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)를 참조하세요. 가상 네트워크 서비스 엔드포인트를 사용하는 Azure SQL Database 서버/프라이빗 엔드포인트를 사용하는 Managed Instance를 사용하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.
    - 데이터베이스 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. 가상 네트워크 서비스 엔드포인트를 사용하는 Azure SQL Database 서버/프라이빗 엔드포인트를 사용하는 Managed Instance를 사용하여 SSISDB를 호스팅하는 경우에는 위의 내용이 해당되지 않습니다. 자세한 내용은 [Azure SQL 데이터베이스 보호](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)을 참조하세요.
    - 데이터베이스 서버에 대한 방화벽 설정에서 클라이언트 IP 주소 목록에 클라이언트 머신의 IP 주소를 포함하는 클라이언트 머신의 IP 주소 또는 IP 주소의 범위를 추가합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../sql-database/sql-database-firewall-configure.md)을 참조하세요.
    - 서버 관리자 자격 증명으로 SQL 인증을 사용하여 데이터베이스 서버에 연결하거나 ADF에 대한 관리 ID로 AAD(Azure Active Directory) 인증을 사용하여 데이터베이스 서버에 연결할 수 있습니다. 후자의 경우 ADF의 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 AAD 그룹에 추가해야 합니다. [AAD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.
    - 데이터베이스 서버에 SSISDB가 아직 없는지 확인합니다. Azure-SSIS IR 프로비저닝은 기존 SSISDB 사용을 지원하지 않습니다.

> [!NOTE]
> - ADF 및 Azure-SSIS IR이 현재 사용 가능한 Azure 지역의 목록은 [지역별 ADF + SSIS IR 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)을 참조하세요. 

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

Azure Portal를 통해 ADF를 만들려면 [UI를 통해 ADF 만들기](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) 문서의 단계별 지침을 수행합니다. 그 과정에서 **대시보드에 고정**을 선택하면 ADF를 만든 후 신속하게 액세스할 수 있습니다. 

ADF를 만든 후에는 Azure Portal에서 개요 페이지를 열고, **작성자 및 모니터링** 타일을 클릭하여 별도의 탭에서 **시작** 페이지를 시작합니다. 이 페이지에서 Azure-SSIS IR 만들기를 계속 진행할 수 있습니다.   

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 만들기

### <a name="from-the-data-factory-overview"></a>Data Factory 개요에서

1. **시작** 페이지에서 **SSIS Integration Runtime 구성** 타일을 클릭합니다. 

   !["SSIS Integration Runtime 구성" 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

### <a name="from-the-authoring-ui"></a>작성 UI에서

1. Azure Data Factory UI에서 **편집** 탭으로 전환하고, **연결**을 선택한 다음, **Integration Runtime** 탭으로 전환하여 데이터 팩터리에서 기존 통합 런타임을 살펴봅니다. 

   ![기존 IR 보기 선택](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. **새로 만들기**를 선택하여 Azure-SSIS IR을 만듭니다. 

   ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **Integration Runtime 설정** 창에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트합니다.)를 선택하고, **다음**을 선택합니다. 

   ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비전

1. **Integration Runtime 설정**의 **일반 설정** 페이지에서 다음 단계를 완료합니다. 

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. **이름**에는 통합 런타임의 이름을 입력합니다. 

   b. **설명**에는 통합 런타임에 대한 설명을 입력합니다. 

   다. **위치**에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다. 

   d. **노드 크기**로는 통합 런타임 클러스터의 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 계산/메모리 사용량이 많은 패키지를 실행하려는 경우 큰 노드 크기(강화)를 선택합니다. 

   e. **노드 수**로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려는 경우 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다. 

   f. **버전/라이선스**의 경우 통합 런타임에 대한 SQL Server 버전/라이선스, 즉 Standard 또는 Enterprise를 선택합니다. 통합 런타임에서 고급/프리미엄 기능을 사용하려는 경우 Enterprise를 선택합니다. 

   g. **비용 절감**의 경우 통합 런타임에 대한 AHB(Azure 하이브리드 혜택) 옵션에서 예 또는 아니요를 선택합니다. Software Assurance에 사용자 고유의 SQL Server 라이선스를 사용하여 하이브리드의 비용 절감 혜택을 얻으려면 [예]를 선택합니다. 

   h. **다음**을 클릭합니다. 

1. **SQL 설정** 페이지에서 다음 단계를 완료합니다. 

   ![SQL 설정](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. **SSIS 카탈로그 만들기...** 확인란에서 패키지에 대한 배포 모델을 선택하여 Azure-SSIS IR에서 실행합니다. 패키지가 데이터베이스 서버에서 호스팅되는 SSISDB로 배포되는 프로젝트 배포 모델 또는 패키지가 파일 시스템/파일 공유/Azure Files로 배포되는 패키지 배포 모델 선택할 경우 사용자를 대신해 만들고 관리하는 SSISDB를 호스트할 사용자 고유의 데이터베이스 서버를 가져와야 합니다.
   
   b. **구독**에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

   다. **위치**에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다.

   d. **카탈로그 데이터베이스 서버 엔드포인트**로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 선택한 데이터베이스 서버에 따라 사용자를 대신하여 단일 데이터베이스로, 탄력적 풀의 일부분으로, 또는 Managed Instance에서 SSISDB를 만들 수 있습니다. 이러한 SSISDB는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버 유형을 선택하는 지침은 [Azure SQL Database 단일 데이터베이스/탄력적 풀/Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)를 참조하세요. 가상 네트워크 서비스 엔드포인트를 사용하는 Azure SQL Database 서버/프라이빗 엔드포인트를 사용하는 Managed Instance를 선택하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 

   e. **AAD 인증 사용...** 확인란에서 ADF에 대한 관리 ID를 사용하는 SQL 인증 또는 AAD 인증 선택할 경우 ADF의 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 AAD 그룹에 추가해야 합니다. [AAD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 

   f. **관리 사용자 이름**으로는 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

   g. **관리자 암호**로는 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

   h. **카탈로그 데이터베이스 서비스 계층**의 경우 SSISDB를 호스팅할 데이터베이스 서버에 대한 서비스 계층, 즉 기본/표준/프리미엄 계층 또는 탄력적 풀 이름을 선택합니다.

   i. **연결 테스트**를 클릭하고, 테스트가 성공하면 **다음**을 클릭합니다. 

1. **고급 설정** 페이지에서 다음 단계를 완료합니다. 

   ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **노드당 최대 병렬 실행 수**로는 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 계산/메모리 사용량이 많은 단일 대형/무거운 패키지를 실행하는 데 코어를 2개 이상 사용하려는 경우 낮은 값을 선택합니다. 단일 코어에서 소형/가벼운 패키지를 하나 이상 실행하려는 경우 높은 값을 선택합니다. 

   b. **사용자 지정 설치 컨테이너 SAS URI**에는 필요에 따라 설치 스크립트 및 관련 파일이 저장되는 Azure Storage Blob 컨테이너의 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 입력합니다. [Azure-SSIS IR에 대한 사용자 지정 설치](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)를 참조하세요. 

   다. **VNet 선택...** 확인란에서 통합 런타임을 가상 네트워크에 조인할 것인지 선택합니다. 가상 네트워크 서비스 엔드포인트를 사용하는 Azure SQL Database 서버/프라이빗 엔드포인트를 사용하는 Managed Instance를 사용하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는지 확인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 

   d. **자체 호스팅 설정...** 확인란에서 Azure-SSIS IR의 프록시로 자체 호스팅 IR을 구성할 것인지 선택합니다. 자세한 내용은 [자체 호스팅 IR을 프록시로 설정](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)을 참조하세요. 

1. **다음**을 클릭합니다. 

1. **요약** 페이지에서 모든 프로비저닝 설정을 검토하고, 권장 설명서 링크를 책갈피로 설정하고, **마침**을 클릭하여 통합 런타임 만들기를 시작합니다. 

   > [!NOTE]
   > 이 프로세스는 사용자 지정 설정 시간을 제외하고 5분 이내에 완료되어야 합니다.
   >
   > SSISDB를 사용하는 경우 ADF 서비스는 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 
   > 
   > Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소가 이미 지원하는 데이터 원본 외에도 Excel/Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 추가 구성 요소를 설치할 수도 있습니다. [Azure-SSIS IR 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요.

1. 필요한 경우 **연결** 탭에서 **Integration Runtime**으로 전환합니다. **새로 고침**을 선택하여 상태를 새로 고칩니다. 

   !["새로 고침" 단추와 생성 상태](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. **작업** 열의 링크를 사용하여 통합 런타임을 중지/시작, 편집 또는 삭제합니다. 마지막 링크를 사용하여 통합 런타임에 대한 JSON 코드를 살펴봅니다. 편집 및 삭제 단추는 IR이 중지된 경우에만 활성화됩니다. 

   !["작업" 열의 링크](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포

SSISDB를 사용하는 경우 서버 엔드포인트를 통해 데이터베이스 서버에 연결하는 SSDT/SSMS 도구를 사용하여 SSISDB에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 공용 엔드포인트를 사용하는 Azure SQL Database 서버/Managed Instance의 서버 엔드포인트 형식은 각각 `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`입니다. SSISDB를 사용하지 않을 경우 `dtinstall`/`dtutil`/`dtexec` 명령줄 유틸리티를 사용하여 파일 시스템/파일 공유/Azure Files에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 자세한 내용은 [SSIS 패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)를 참조하세요. 두 경우 모두 ADF 파이프라인에서 SSIS 패키지 실행 활동을 사용하여 Azure-SSIS IR에서 배포된 패키지를 실행할 수도 있습니다. [SSIS 패키지 실행을 첫 번째 클래스 ADF 활동으로 호출](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조하세요. 

SSIS 설명서에서 다음 문서도 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 프로비전
> * SSIS 패키지 배포

Azure-SSIS 통합 런타임을 사용자 지정하는 방법에 대해 알아보려면 다음 문서로 이동하세요. 

> [!div class="nextstepaction"]
> [Azure-SSIS IR 사용자 지정](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)