---
title: Azure Data Factory에서 Azure-SSIS Integration Runtime 만들기 | Microsoft Docs
description: Azure에 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Data Factory에서 Azure-SSIS Integration Runtime을 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: fb5335c8dfd94006ba3f0d8d6b890869dd9f3717
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484817"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure-SSIS Integration Runtime 만들기

이 자습서는 Azure SQL Server Integration Services (SSIS) IR (Integration Runtime)에서 Azure 데이터 팩터리 (ADF)를 프로 비전 하는 단계를 설명 합니다. SSIS 카탈로그 (SSISDB) Azure SQL Database 서버/관리 되는 인스턴스 (프로젝트 배포 모델)에서 호스트 및 파일 시스템/파일에 배포 된 배포 패키지를 실행 하는 AZURE-SSIS IR 지원 공유/Azure Files (패키지 배포 모델). AZURE-SSIS IR 프로 비전 되 면 SQL Server 데이터 도구 (SSDT)와 같은 친숙 한 도구를 사용할 수 / SQL Server Management Studio (SSMS) 및 명령 줄 유틸리티와 같은 `dtinstall` / `dtutil` / `dtexec`를 배포 하 고 Azure에서 패키지를 실행 합니다.

[자습서: Azure SSIS IR을 프로 비전](tutorial-create-azure-ssis-runtime-portal.md) Azure 포털/ADF 앱을 통해 Azure SSIS IR을 만들고 필요에 따라 Azure SQL Database 서버/관리 되는 인스턴스 SSISDB 호스트할을 사용 하는 방법을 보여 줍니다. 이 문서는 자습서를 확장하고 다음 작업을 수행하는 방법을 보여줍니다.

- 필요에 따라 가상 네트워크 서비스 끝점/관리 되는 인스턴스 SSISDB 호스트할 가상 네트워크에서를 사용 하 여 Azure SQL Database 서버를 사용 합니다. 필수 요소로, AZURE-SSIS ir을 가상 네트워크에 가입 하도록 가상 네트워크 권한/설정을 구성 해야 합니다.

- 필요에 따라 Azure SQL Database 서버/관리 인스턴스를 연결할 ADF에 대 한 관리 되는 id를 사용 하 여 Azure Active Directory (AAD) 인증을 사용 합니다. 필수 요소로, SSISDB를 만들 수 있는 데이터베이스 사용자로 ADF에 대 한 관리 되는 id를 추가 해야 합니다.

## <a name="overview"></a>개요

이 문서에서는 Azure-SSIS IR을 프로비전하는 다양한 방법을 보여줍니다.

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager 템플릿](#azure-resource-manager-template)

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**. 구독이 없는 경우 [평가판](https://azure.microsoft.com/pricing/free-trial/) 계정을 만들 수 있습니다.
- **Azure SQL Database 서버/관리 되는 인스턴스 (선택 사항)** 합니다. 데이터베이스 서버가 아직 없는 경우 만들어야 Azure portal에서 시작 하기 전에 합니다. ADF이 데이터베이스 서버에 SSISDB에 만듭니다. Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 통해 integration runtime에서를 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다. 
    - 선택한 데이터베이스 서버에 따라 사용자를 대신하여 단일 데이터베이스로, 탄력적 풀의 일부분으로, 또는 Managed Instance에서 SSISDB를 만들 수 있습니다. 이러한 SSISDB는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB 호스트할 데이터베이스 서버의 유형을 선택 지침은 [비교 Azure SQL Database 단일 데이터베이스/탄력적인 풀/관리 인스턴스](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)합니다. 내용은 여 AZURE-SSIS IR을 가상 네트워크에 조인 해야 가상 네트워크 서비스 끝점/관리 되는 인스턴스는 가상 네트워크에서 사용 하 여 Azure SQL Database 서버를 사용 하 여 SSISDB를 호스트 하거나 온-프레미스 데이터에 액세스 해야 하는 경우 [Azure SSIS IR이 조인을 가상 네트워크](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)합니다.
    - 데이터베이스 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. 이 가상 네트워크 서비스 끝점/관리 되는 인스턴스 SSISDB 호스트할 가상 네트워크에서를 사용 하 여 Azure SQL Database 서버를 사용 하는 경우 적용 되지 않습니다. 자세한 내용은 [Azure SQL 데이터베이스 보호](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)을 참조하세요.
    - 클라이언트 컴퓨터의 IP 주소 또는 데이터베이스 서버용 방화벽 설정에서 클라이언트 IP 주소 목록에 클라이언트 컴퓨터의 IP 주소를 포함 하는 범위의 IP 주소를 추가 합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../sql-database/sql-database-firewall-configure.md)을 참조하세요.
    - 관리 되는 id를 사용 하 여 ADF에 대 한 서버 관리자 자격 증명을 사용 하 여 SQL 인증 또는 Azure Active Directory (AAD) 인증을 사용 하는 데이터베이스 서버에 연결할 수 있습니다.  후자의 경우 해야 ADF에 대 한 데이터베이스 서버에 대 한 액세스 권한이 있는 AAD 그룹에 관리 되는 id를 추가, 참조 [AZURE-SSIS IR에 대 한 사용 하도록 설정 하는 AAD 인증](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)합니다.
    - 데이터베이스 서버에 없는지를 SSISDB 이미 확인 합니다. AZURE-SSIS IR의 프로 비전을 기존 SSISDB를 사용 하 여 지원 하지 않습니다.
- **Azure Resource Manager 가상 네트워크(선택 사항)** . 다음 조건 중 하나 이상에 해당하는 경우 Azure Resource Manager 가상 네트워크가 있어야 합니다.
    - 가상 네트워크 서비스 끝점/관리 되는 인스턴스는 가상 네트워크에서를 사용 하 여 Azure SQL Database 서버의 SSISDB를 호스팅하는 합니다.
    - Azure-SSIS IR에서 실행되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결하려고 합니다.
- **(선택 사항) azure PowerShell**합니다. 지침을 따르세요 [Azure PowerShell 설치 및 구성 하는 방법을](/powershell/azure/install-az-ps)에 Azure-. 프로 비전 하는 PowerShell 스크립트를 실행 하려는 경우

### <a name="region-support"></a>지역 지원

ADF 및 Azure-SSIS IR이 현재 사용 가능한 Azure 지역의 목록은 [지역별 ADF + SSIS IR 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)을 참조하세요.

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>SQL Database 단일 데이터베이스/탄력적 풀 및 SQL Database Managed Instance 비교

다음 표에서는 Azure-SSIR IR과 관련된 SQL Database 서버 및 Managed Instance의 특정 기능을 비교합니다.

| 기능 | 단일 데이터베이스/탄력적 풀| 관리되는 인스턴스 |
|---------|--------------|------------------|
| **일정 계획** | SQL Server 에이전트를 사용할 수 없습니다.<br/><br/>[ADF 파이프라인의 패키지 실행 예약](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)을 참조하세요.| Managed Instance 에이전트를 사용할 수 있습니다. |
| **인증** | ADF의 관리 ID를 사용하여 모든 AAD 그룹을 **db_owner** 역할의 구성원으로 나타내는 포함된 데이터베이스 사용자로 SSISDB를 만들 수 있습니다.<br/><br/>[Azure SQL Database 서버의 SSISDB 생성을 위한 Azure AD 인증 활성화](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)를 참조하세요. | ADF의 관리 ID를 나타내는 포함된 데이터베이스 사용자를 통해 SSISDB를 만들 수 있습니다. <br/><br/>[Azure SQL Database Managed Instance의 SSISDB 생성을 위한 Azure AD 인증 활성화](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)를 참조하세요. |
| **서비스 계층** | Azure SQL Database 서버를 통해 Azure-SSIS IR을 만들 때 SSISDB의 서비스 계층을 선택할 수 있습니다. 여러 서비스 계층이 있습니다. | Managed Instance를 통해 Azure-SSIS IR을 만들 때는 SSISDB의 서비스 계층을 선택할 수 없습니다. Managed Instance의 모든 데이터베이스는 해당 인스턴스에 할당된 동일한 리소스를 공유합니다. |
| **가상 네트워크** | 가상 네트워크 서비스 엔드포인트가 포함된 Azure SQL Database 서버를 사용하거나 온-프레미스 데이터 저장소에 액세스해야 하는 경우 Azure-SSIS IR에서 조인할 Azure Resource Manager 가상 네트워크만 지원됩니다. | Azure-SSIS IR에서 조인할 Azure Resource Manager 가상 네트워크만 지원됩니다. 가상 네트워크는 항상 필요합니다.<br/><br/>Azure-SSIS IR을 Managed Instance와 동일한 가상 네트워크에 조인하는 경우 Azure-SSIS IR은 Managed Instance와 다른 서브넷에 있어야 합니다. Azure-SSIS IR을 Managed Instance와 다른 가상 네트워크에 조인하는 경우에는 가상 네트워크 피어링 또는 가상 네트워크 간 연결을 사용하는 것이 좋습니다. [애플리케이션을 Azure SQL Database Managed Instance에 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조하세요. |
| **분산 트랜잭션** | 탄력적 트랜잭션을 통해 지원됩니다. MSDTC(Microsoft Distributed Transaction Coordinator) 트랜잭션은 지원되지 않습니다. SSIS 패키지가 MSDTC를 사용하여 분산형 트랜잭션을 조정할 경우 Azure SQL Database용 탄력적 트랜잭션으로 마이그레이션하는 것을 고려하세요. 자세한 내용은 [클라우드 데이터베이스의 분산 트랜잭션](../sql-database/sql-database-elastic-transactions-overview.md)을 참조하세요. | 지원되지 않습니다. |
| | | |

## <a name="azure-portal"></a>Azure portal

이 섹션에서는 Azure Portal, 특히 ADF UI(사용자 인터페이스)/앱을 사용하여 Azure-SSIS IR을 만듭니다.

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다.

   ![새로 만들기->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **MyAzureSsisDataFactory**를 입력합니다.

   ![새 데이터 팩터리 페이지](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameMyAzureSsisDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다.
1. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.

   - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
   - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.

   리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

1. **버전**에 대해 **V2**를 선택합니다.
1. 데이터 팩터리의 **위치** 를 선택합니다. 데이터 팩터리 만들기를 지원하는 위치만 목록에 표시됩니다.
1. **대시보드에 고정**을 선택합니다.
1. **만들기**를 클릭합니다.
1. 대시보드에서 다음과 같은 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다.

    ![데이터 팩터리 배포 중 타일](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.

    ![데이터 팩터리 홈페이지](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. **작성 및 모니터링**을 클릭하여 별도의 탭에서 Data Factory UI(사용자 인터페이스)를 시작합니다.

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임 프로비전

1. 시작 페이지에서 **SSIS Integration Runtime 구성** 타일을 클릭합니다.

   ![SSIS Integration Runtime 구성 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. **Integration Runtime 설정**의 **일반 설정** 페이지에서 다음 단계를 완료합니다.

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. **이름**에는 통합 런타임의 이름을 입력합니다.

    b. **설명**에는 통합 런타임에 대한 설명을 입력합니다.

    다. **위치**에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다.

    d. **노드 크기**로는 통합 런타임 클러스터의 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 계산/메모리 사용량이 많은 패키지를 실행하려는 경우 큰 노드 크기(강화)를 선택합니다.

    e. **노드 수**로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려는 경우 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다.

    f. **버전/라이선스**의 경우 통합 런타임에 대한 SQL Server 버전/라이선스, 즉 Standard 또는 Enterprise를 선택합니다. 통합 런타임에서 고급/프리미엄 기능을 사용하려는 경우 Enterprise를 선택합니다.

    g. **비용 절감**의 경우 통합 런타임에 대한 AHB(Azure 하이브리드 혜택) 옵션에서 예 또는 아니요를 선택합니다. Software Assurance에 사용자 고유의 SQL Server 라이선스를 사용하여 하이브리드의 비용 절감 혜택을 얻으려면 [예]를 선택합니다.

    h. **다음**을 클릭합니다.

3. **SQL 설정** 페이지에서 다음 단계를 완료합니다.

   ![SQL 설정](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. **만드는 SSIS 카탈로그는 중...**  확인란을 선택 하 여 AZURE-SSIS IR에서 실행 하는 패키지에 대 한 배포 모델: 프로젝트 배포 모델로 패키지 데이터베이스 서버에서 호스팅되는 SSISDB에 배포 되는 위치 또는 패키지를 파일 시스템/파일 공유/Azure에 배포 된 경우 패키지 배포 모델 파일입니다. 을 확인 하는 경우 사용자를 대신해 고유한 데이터베이스 서버 만들기를 관리 하는 SSISDB 호스트할 상태로 전환 해야 합니다.
   
    b. **구독**에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

    c. **위치**에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다. 

    d. **카탈로그 데이터베이스 서버 엔드포인트**로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 선택한 데이터베이스 서버에 따라 사용자를 대신하여 단일 데이터베이스로, 탄력적 풀의 일부분으로, 또는 Managed Instance에서 SSISDB를 만들 수 있습니다. 이러한 SSISDB는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB 호스트할 데이터베이스 서버의 유형을 선택 지침은 [비교 Azure SQL Database 단일 데이터베이스/탄력적인 풀/관리 인스턴스](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)합니다. 내용은 여 AZURE-SSIS IR을 가상 네트워크에 조인 해야 SSISDB를 호스트 하거나 온-프레미스 데이터에 액세스 해야 하는 가상 네트워크에서 가상 네트워크 서비스 끝점/관리 인스턴스를 사용 하 여 Azure SQL Database 서버를 선택 하면 [조인 AZURE-SSIS IR 가상 네트워크에](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)입니다. 

    e. **AAD 인증 사용...** 확인란에서 SSISDB를 호스트할 데이터베이스 서버에 대한 인증 방법을 선택합니다. SQL 인증 또는 관리 되는 id 사용 하 여 ADF에 대 한 AAD 인증 합니다. 내용은 ADF에 대 한 데이터베이스 서버에 대 한 액세스 권한이 있는 AAD 그룹에 관리 되는 id를 추가 해야를 확인 하는 경우 [AZURE-SSIS IR에 대 한 사용 하도록 설정 하는 AAD 인증](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)합니다. 

    f. **관리 사용자 이름**으로는 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

    g. **관리자 암호**로는 SSISDB를 호스트하는 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

    h. **카탈로그 데이터베이스 서비스 계층**의 경우 SSISDB를 호스팅할 데이터베이스 서버에 대한 서비스 계층, 즉 기본/표준/프리미엄 계층 또는 탄력적 풀 이름을 선택합니다. 

    i. **연결 테스트**를 클릭하고, 테스트가 성공하면 **다음**을 클릭합니다. 

4. **고급 설정** 페이지에서 다음 단계를 완료합니다.

    ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. **노드당 최대 병렬 실행 수**로는 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 계산/메모리 사용량이 많은 단일 대형/무거운 패키지를 실행하는 데 코어를 2개 이상 사용하려는 경우 낮은 값을 선택합니다. 단일 코어에서 소형/가벼운 패키지를 하나 이상 실행하려는 경우 높은 값을 선택합니다.

    b. **사용자 지정 설치 컨테이너 SAS URI**에는 필요에 따라 설치 스크립트 및 관련 파일이 저장되는 Azure Storage Blob 컨테이너의 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 입력합니다. [Azure-SSIS IR에 대한 사용자 지정 설치](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)를 참조하세요.

5. **가상 네트워크 선택...** 확인란에서 통합 런타임을 가상 네트워크에 조인할 것인지 선택합니다. 가상 네트워크 서비스 끝점/관리 되는 인스턴스 SSISDB 호스트할 가상 네트워크에서를 사용 하 여 Azure SQL Database 서버를 사용 하거나 온-프레미스 데이터에 액세스 해야 하는 경우 확인 즉, 온-프레미스 데이터 소스/대상이 SSIS 패키지에서 참조 하세요 [가상 네트워크에 AZURE-SSIS IR 조인](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)합니다. 선택한 경우 다음 단계를 완료합니다.

   ![가상 네트워크 관련 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. **구독**에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

    b. **위치**에는 통합 런타임의 동일한 위치가 선택됩니다.

    다. **유형**에는 가상 네트워크의 유형을 선택합니다. 클래식 또는 Azure Resource Manager. 클래식 가상 네트워크는 곧 사용이 중단될 예정이므로 Azure Resource Manager 가상 네트워크를 선택하는 것이 좋습니다.

    d. **VNet 이름**에서 가상 네트워크의 이름을 선택합니다. 이 가상 네트워크는 가상 네트워크 서비스 끝점/관리 되는 인스턴스는 가상 네트워크에서 사용 하 여 Azure SQL Database 서버의 SSISDB 또는 온-프레미스 네트워크에 연결 하는 것을 호스트 하기 사용 된 것과 있어야 합니다.

    e. **서브넷 이름**에서 가상 네트워크의 서브넷 이름을 선택합니다. 이 관리 되는 인스턴스의 SSISDB 호스트할 가상 네트워크에서 사용 되는 다른 서브넷에 있어야 합니다.

6. **VNet 유효성 검사**를 클릭하고, 성공하면 **마침**을 클릭하여 Azure-SSIS 통합 런타임의 만들기를 시작합니다.

    > [!NOTE]
    > 사용자 지정 설치 든을 제외 하 고이 프로세스는 5 분 내에 완료 해야 하지만 AZURE-SSIS IR을 가상 네트워크에 연결에 대 한 20 ~ 30 분 정도 걸릴 수 있습니다.
    >
    > SSISDB를 사용 하는 경우 ADF 서비스는 SSISDB를 준비 하려면 데이터베이스 서버에 연결 합니다. 또한 지정 하는 경우 사용 권한 및 가상 네트워크에 대 한 설정을 구성 하 고 가상 네트워크에 AZURE-SSIS IR을 조인 합니다.
    > 
    > AZURE-SSIS IR을 프로 비전, Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치 됩니다. 이러한 구성 요소는 Excel/액세스 파일 및 기본 제공 구성 요소에서 이미 지 원하는 데이터 원본 외에도 다양 한 Azure 데이터 원본에 대 한 연결을 제공 합니다. 추가 구성 요소를 설치할 수도 있습니다 참조 [AZURE-SSIS IR에 대 한 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)합니다.

7. 필요한 경우 **연결** 창에서 **Integration Runtime**으로 전환합니다. **새로 고침**을 클릭하여 상태를 새로 고칩니다.

   ![만들기 상태](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. **작업** 열 아래의 링크를 사용하여 통합 런타임을 중지/시작, 편집 또는 삭제합니다. 마지막 링크를 사용하여 통합 런타임에 대한 JSON 코드를 살펴봅니다. 편집 및 삭제 단추는 IR이 중지된 경우에만 활성화됩니다.

   ![Azure SSIS IR - 작업](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>포털의 Azure SSIS 통합 런타임

1. Azure Data Factory UI에서 **편집** 탭으로 전환하고, **연결**을 클릭한 다음, **Integration Runtime** 탭으로 전환하여 데이터 팩터리에서 기존 통합 런타임을 살펴봅니다.

   ![기존 IR 보기](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. **새로 만들기**를 클릭하여 새운 Azure-SSIS IR을 만듭니다.

   ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Azure-SSIS 통합 런타임을 만들려면 이미지에서 표시한 대로 **새로 만들기**를 클릭합니다.

4. [Integration Runtime 설정] 창에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트합니다.)를 선택하고, **다음**을 클릭합니다.

   ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell

이 섹션에서는 Azure PowerShell을 사용하여 Azure-SSIS IR을 만듭니다.

### <a name="create-variables"></a>변수 만들기

복사 하 고 다음 스크립트를 붙여넣습니다-변수에 대 한 값을 지정 합니다. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>로그인 및 구독 선택

다음 코드에 로그인 하 고 Azure 구독을 선택 하는 스크립트를 추가 합니다.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>데이터베이스 서버에 연결 유효성 검사

Azure SQL Database 서버에 / 관리 인스턴스 유효성을 검사 하려면 다음 스크립트를 추가 합니다.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-virtual-network"></a>가상 네트워크 구성

다음 스크립트를 추가하여 조인할 Azure-SSIS Integration Runtime에 대한 가상 네트워크 권한/설정을 자동으로 구성합니다.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

리소스 그룹에 이미 있는 경우 스크립트에이 코드를 복사 하지 않습니다. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

다음 명령을 실행하여 데이터 팩터리를 만듭니다.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integration Runtime 만들기

다음 명령을 실행하여 Azure에서 SSIS 패키지를 실행하는 Azure-SSIS 통합 런타임을 만듭니다.

SSISDB를 사용 하지 않는 경우 CatalogServerEndpoint, CatalogPricingTier, 및 CatalogAdminCredential 매개 변수를 생략할 수 있습니다.

가상 네트워크 서비스 끝점/관리 되는 인스턴스 SSISDB 호스트할 가상 네트워크에서를 사용 하 여 Azure SQL Database 서버를 사용 하지 않거나 온-프레미스 데이터에 액세스 해야 하는 경우에 함께 VNetId 및 서브넷 매개 변수를 생략할 수도 있고에 빈 값을 전달할 수 있습니다. 그렇지 않으면 생략할 수 없으며 가상 네트워크 구성에서 유효한 값을 전달해야 합니다. [Azure-SSIS IR을 가상 네트워크에 조인](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)을 참조하세요.

Managed Instance를 사용하여 SSISDB를 호스팅하는 경우 CatalogPricingTier 매개 변수를 생략하거나 해당 매개 변수에 빈 값을 전달할 수 있습니다. 그렇지 않으면 생략할 수 없으며 Azure SQL Database에 지원되는 가격 책정 계층 목록에서 유효한 값을 전달해야 합니다. [SQL Database 리소스 제한](../sql-database/sql-database-resource-limits.md)을 참조하세요.

데이터베이스 서버에 연결 하 여 ADF에 대 한 관리 되는 id를 사용 하 여 Azure Active Directory (AAD) 인증을 사용 하는 경우 CatalogAdminCredential 매개 변수를 생략할 수 있지만 관리 되는 id ADF에 대 한 액세스 권한이 있는 AAD 그룹에 추가 해야 합니다. 데이터베이스 서버에 대 한 사용 권한 참조 [AZURE-SSIS IR에 대 한 사용 하도록 설정 하는 AAD 인증](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)합니다. 그렇지 않으면 생략할 수 없으며 SQL 인증을 위해 서버 관리 사용자 이름과 암호로 구성된 유효한 개체를 전달해야 합니다.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>Integration Runtime 시작

AZURE-SSIS integration runtime을 시작 하려면 다음 명령을 실행 합니다.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> 사용자 지정 설치 든을 제외 하 고이 프로세스는 5 분 내에 완료 해야 하지만 AZURE-SSIS IR을 가상 네트워크에 연결에 대 한 20 ~ 30 분 정도 걸릴 수 있습니다.
>
> SSISDB를 사용 하는 경우 ADF 서비스는 SSISDB를 준비 하려면 데이터베이스 서버에 연결 합니다. 또한 지정 하는 경우 사용 권한 및 가상 네트워크에 대 한 설정을 구성 하 고 가상 네트워크에 AZURE-SSIS IR을 조인 합니다.
> 
> AZURE-SSIS IR을 프로 비전, Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치 됩니다. 이러한 구성 요소는 Excel/액세스 파일 및 기본 제공 구성 요소에서 이미 지 원하는 데이터 원본 외에도 다양 한 Azure 데이터 원본에 대 한 연결을 제공 합니다. 추가 구성 요소를 설치할 수도 있습니다 참조 [AZURE-SSIS IR에 대 한 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)합니다.

### <a name="full-script"></a>전체 스크립트

다음은 Azure-SSIS 통합 런타임을 만드는 전체 스크립트입니다.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 Azure-SSIS 통합 런타임을 만듭니다. 다음은 샘플 연습입니다.

1. 다음 Azure Resource Manager 템플릿을 사용하여 JSON 파일을 만듭니다. 꺾쇠 괄호(자리 표시자)의 값을 사용자 고유의 값으로 바꿉니다.

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Azure Resource Manager 템플릿을 배포 하려면 다음 예제에서 ADFTutorialResourceGroup은 리소스 그룹의 이름에 대 한 JSON 정의 포함 하는 파일이 ADFTutorialARM.json 않았고에 표시 된 대로 새 AzResourceGroupDeployment 명령 실행 데이터 팩터리 및 Azure 기능.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    이 명령은 데이터 팩터리를 만들고 그 안에 Azure-SSIS IR을 만들지만 IR을 시작하지는 않습니다.

3. Azure SSIS IR 프로그램을 시작 하려면 시작 AzDataFactoryV2IntegrationRuntime 명령을 실행 합니다.

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> 사용자 지정 설치 든을 제외 하 고이 프로세스는 5 분 내에 완료 해야 하지만 AZURE-SSIS IR을 가상 네트워크에 연결에 대 한 20 ~ 30 분 정도 걸릴 수 있습니다.
>
> SSISDB를 사용 하는 경우 ADF 서비스는 SSISDB를 준비 하려면 데이터베이스 서버에 연결 합니다. 또한 지정 하는 경우 사용 권한 및 가상 네트워크에 대 한 설정을 구성 하 고 가상 네트워크에 AZURE-SSIS IR을 조인 합니다.
> 
> AZURE-SSIS IR을 프로 비전, Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치 됩니다. 이러한 구성 요소는 Excel/액세스 파일 및 기본 제공 구성 요소에서 이미 지 원하는 데이터 원본 외에도 다양 한 Azure 데이터 원본에 대 한 연결을 제공 합니다. 추가 구성 요소를 설치할 수도 있습니다 참조 [AZURE-SSIS IR에 대 한 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)합니다.

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포

SSISDB를 사용 하는 경우에 패키지를 배포 하 고 해당 서버 끝점을 통해 데이터베이스 서버에 연결 하는 SSDT/SSMS 도구를 사용 하 여 Azure SSIS IR에서 실행할 수 있습니다.  Azure SQL Database 서버/관리 되는 인스턴스는 공용 끝점을 사용 하 여 가상 네트워크/관리 되는 인스턴스에서에 대 한 서버 끝점 형식은 `<server name>.database.windows.net` / `<server name>.<dns prefix>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, 각각. SSISDB를 사용 하지 않는 경우 파일 시스템/파일 공유/Azure에 패키지를 배포할 수 있는 파일 사용 하 여 Azure SSIS IR에서 실행할 `dtinstall` / `dtutil` / `dtexec` 명령줄 유틸리티입니다. 자세한 내용은 [SSIS 패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)합니다. 두 경우 모두에서 실행할 수도 있습니다에 배포 된 패키지 AZURE-SSIS IR에서 SSIS 패키지 실행 작업을 사용 하 여 ADF 파이프라인의를 참조 하세요 [호출할 SSIS 패키지 실행 ADF 활동을 최우선으로](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)입니다.

## <a name="next-steps"></a>다음 단계

이 설명서의 다른 AZURE-SSIS IR 항목을 참조 하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-. 비롯 한 일반적인 통합 런타임에 대 한 정보를 제공 합니다.
- [AZURE-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime)합니다. 이 문서에서는 검색 및 사용자 Azure-.에 대 한 정보를 이해 하는 방법을 보여 줍니다.
- [AZURE-SSIS IR 관리](manage-azure-ssis-integration-runtime.md)합니다. 이 아티클에서 중지, 시작 또는 Azure SSIS IR 프로그램을 삭제 하는 방법-것도 하는 방법을 더 많은 노드를 추가 하 여 Azure SSIS IR 프로그램을 확장 합니다.
- [가상 네트워크에 AZURE-SSIS IR을 조인할](join-azure-ssis-integration-runtime-virtual-network.md)합니다. 이 문서에서는 가상 네트워크에 AZURE-SSIS IR을 조인 하는 방법에 대 한 정보를 제공 합니다.