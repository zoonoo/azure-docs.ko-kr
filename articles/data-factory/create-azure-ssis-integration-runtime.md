---
title: Azure Data Factory에서 Azure Integration Runtime 만들기 | Microsoft Docs
description: Azure에 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Data Factory에서 Azure-SSIS 통합 런타임을 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 18555fbffbc48594793163894c010998094b3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336221"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure Integration Runtime 만들기 | Microsoft Docs

이 문서에서는 Azure 데이터 팩터리에서 Azure-SQL 서버 통합 서비스(SSIS) 통합 런타임(IR)을 프로비전하기 위한 단계를 제공합니다. Azure-SSIS IR에서 지원하는 작업은 다음과 같습니다.

- Azure SQL Database 서버 또는 관리형 인스턴스(프로젝트 배포 모델)에서 호스팅하는 SSIS 카탈로그(SSISDB)에 배포된 패키지를 실행합니다.
- 파일 시스템, 파일 공유 또는 Azure Files(패키지 배포 모델)에 배포된 패키지를 실행합니다. 

Azure-SSIS IR이 프로비저닝되면 익숙한 도구를 사용하여 Azure에서 패키지를 배포하고 실행할 수 있습니다. 이러한 도구에는 SSDT(SQL Server Data Tools), SSMS(SQL Server Management Studio) 및 명령줄 도구(예: `dtinstall`, `dtutil` 및 `dtexec`)가 있습니다.

[Azure-SSIS IR 프로비저닝](tutorial-create-azure-ssis-runtime-portal.md) 자습서에서는 Azure 포털 또는 데이터 팩터리 앱을 통해 Azure-SSIS IR을 만드는 방법을 보여 주며 있습니다. 또한 이 자습서에서는 Azure SQL Database 서버 또는 관리되는 인스턴스를 선택적으로 사용하여 SSISDB를 호스트하는 방법도 보여 주어도 있습니다. 이 문서에서는 자습서를 확장하고 이러한 선택적 작업을 수행하는 방법을 설명합니다.

- IP 방화벽 규칙/가상 네트워크 서비스 끝점또는 SSISDB를 호스트하기 위해 개인 끝점이 있는 관리되는 인스턴스가 있는 Azure SQL Database 서버를 사용합니다. 전제 조건으로 Azure-SSIS IR에 대한 가상 네트워크 권한 및 설정을 구성하여 가상 네트워크에 가입해야 합니다.

- Azure Active Directory(Azure AD) 인증을 데이터 팩터리에 대해 관리되는 ID와 함께 사용하여 Azure SQL Database 서버 또는 관리되는 인스턴스에 연결합니다. 전제 조건으로 SSISDB 인스턴스를 만들 수 있는 데이터베이스 사용자로 데이터 팩터리의 관리되는 ID를 추가해야 합니다.

- Azure-SSIS IR을 가상 네트워크에 가입하거나 Azure-SSIS IR의 프록시로 자체 호스팅 IR을 구성하여 온-프레미스 데이터에 액세스합니다.

이 문서에서는 Azure 포털, Azure PowerShell 및 Azure 리소스 관리자 템플릿을 사용하여 Azure-SSIS IR을 프로비전하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**. 구독이 아직 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 계정을 만들 수 있습니다.

- **Azure SQL Database 서버 또는 관리되는 인스턴스(선택 사항)**. 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. 그러면 Data Factory에서 SSISDB 인스턴스를 이 데이터베이스 서버에 만듭니다. 

  Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 통합 런타임에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다.

  다음 사항에 유의하세요.

  - 선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버 유형을 선택하는 방법에 대한 지침은 이 문서에서 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리되는 인스턴스 비교](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) 섹션을 참조하십시오. 
  
    IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인을](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)참조하십시오.

  - 데이터베이스 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하여 SSISDB를 호스팅하는 경우에는 이 설정이 적용되지 않습니다. 자세한 내용은 [Azure SQL 데이터베이스 보호](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)을 참조하세요.

  - 클라이언트 머신의 IP 주소 또는 이러한 주소가 포함된 IP 주소의 범위를 데이터베이스 서버에 대한 방화벽 설정의 클라이언트 IP 주소 목록에 추가합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../sql-database/sql-database-firewall-configure.md)을 참조하세요.

  - 서버 관리자 자격 증명을 통한 SQL 인증을 사용하거나 데이터베이스 서버에 연결하거나 데이터 팩터리의 관리 ID를 통한 Azure AD 인증을 사용하여 데이터베이스 서버에 연결할 수 있습니다. 후자의 경우 데이터 팩터리의 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure-SSIS IR에 대한 Azure AD 인증 을 참조하세요.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)

  - 데이터베이스 서버에 SSISDB 인스턴스가 아직 없는지 확인합니다. Azure-SSIS IR 프로비저닝은 기존 SSISDB 인스턴스 사용을 지원하지 않습니다.

- **Azure Resource Manager 가상 네트워크(선택 사항)**. 다음 조건 중 하나 이상에 해당하는 경우 Azure Resource Manager 가상 네트워크가 있어야 합니다.
  - IP 방화벽 규칙/가상 네트워크 서비스 끝점 또는 개인 끝점이 있는 관리되는 인스턴스가 있는 Azure SQL Database 서버에서 SSISDB를 호스팅하는 것입니다.
  - 자체 호스팅 IR을 구성하지 않고 Azure-SSIS IR에서 실행되는 SSIS 패키지의 온-프레미스 데이터 저장소에 연결하려고 합니다.

- **Azure PowerShell(선택 사항)**. PowerShell 스크립트를 실행하여 Azure-SSIS IR을 프로비저닝하려는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/install-az-ps)의 지침을 따릅니다.

### <a name="regional-support"></a>지역 지원

데이터 팩터리 및 Azure-SSIS IR을 사용할 수 있는 Azure 지역 목록은 [지역별 데이터 팩터리 및 SSIS IR 가용성을](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)참조하십시오.

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>SQL Database 단일 데이터베이스, 탄력적 풀 및 관리되는 인스턴스 비교

다음 표에서는 Azure SQL Database 서버및 관리되는 인스턴스의 특정 기능을 Azure-SSIR IR과 관련되어 비교합니다.

| 기능 | 단일 데이터베이스/탄력적 풀| 관리되는 인스턴스 |
|---------|--------------|------------------|
| **일정** | SQL 서버 에이전트를 사용할 수 없습니다.<br/><br/>[데이터 팩터리 파이프라인에서 패키지 실행 예약](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)을 참조하십시오.| 관리되는 인스턴스 에이전트를 사용할 수 있습니다. |
| **인증** | 데이터 팩터리의 관리되는 ID를 **db_owner** 역할의 구성원으로 사용하여 Azure AD 그룹을 나타내는 포함된 데이터베이스 사용자로 SSISDB 인스턴스를 만들 수 있습니다.<br/><br/>[Azure AD 인증 활성화를 참조하여 Azure SQL Database 서버에서 SSISDB 인스턴스를 만듭니다.](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database) | 데이터 팩터리의 관리되는 ID를 나타내는 포함된 데이터베이스 사용자로 SSISDB 인스턴스를 만들 수 있습니다. <br/><br/>[Azure AD 인증 활성화를 참조하여 Azure SQL Database 관리 인스턴스에서 SSISDB 인스턴스를 만듭니다.](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance) |
| **서비스 계층** | Azure SQL Database 서버를 사용하여 Azure-SSIS IR을 만들 때 SSISDB에 대한 서비스 계층을 선택할 수 있습니다. 여러 서비스 계층이 있습니다. | 관리되는 인스턴스를 사용하여 Azure-SSIS IR을 만들 때 SSISDB에 대한 서비스 계층을 선택할 수 없습니다. 관리되는 인스턴스의 모든 데이터베이스는 해당 인스턴스에 할당된 동일한 리소스를 공유합니다. |
| **가상 네트워크** | IP 방화벽 규칙/가상 네트워크 서비스 끝점이 있는 Azure SQL Database 서버를 사용하는 경우 Azure-SSIS IR은 Azure Resource Manager 가상 네트워크에 가입할 수 있습니다. | Azure-SSIS IR은 개인 끝점에서 관리되는 인스턴스를 사용하는 경우 Azure Resource Manager 가상 네트워크에 가입할 수 있습니다. 관리되는 인스턴스에 대한 공용 끝점을 사용하도록 설정하지 않으면 가상 네트워크가 필요합니다.<br/><br/>Azure-SSIS IR을 관리되는 인스턴스와 동일한 가상 네트워크에 조인하는 경우 Azure-SSIS IR이 관리되는 인스턴스와 다른 서브넷에 있는지 확인합니다. Azure-SSIS IR을 관리되는 인스턴스의 다른 가상 네트워크에 연결하는 경우 가상 네트워크 피어링 또는 네트워크 간 연결을 권장합니다. [응용 프로그램을 Azure SQL Database 관리 인스턴스에 연결](../sql-database/sql-database-managed-instance-connect-app.md)합니다. |
| **분산 트랜잭션** | 이 기능은 탄력적 트랜잭션을 통해 지원됩니다. MSDTC(Microsoft Distributed Transaction Coordinator) 트랜잭션은 지원되지 않습니다. SSIS 패키지가 MSDTC를 사용하여 분산 트랜잭션을 조정하는 경우 Azure SQL Database에 대한 탄력적 트랜잭션으로 마이그레이션하는 것이 좋습니다. 자세한 내용은 [클라우드 데이터베이스 간에 분산 트랜잭션을](../sql-database/sql-database-elastic-transactions-overview.md)참조하십시오. | 지원되지 않습니다. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Azure 포털을 사용하여 통합 런타임 만들기

이 섹션에서는 Azure 포털, 특히 데이터 팩터리 사용자 인터페이스(UI) 또는 앱을 사용하여 Azure-SSIS IR을 만듭니다.

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기

Azure Portal을 통해 데이터 팩터리를 만들려면 [UI를 통해 데이터 팩터리 만들기](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)의 단계별 지침을 따릅니다. 만든 후에 빠르게 액세스할 수 있도록 하려면 이 작업을 수행하면서 **대시 보드에 고정**을 선택합니다. 

데이터 팩터리가 만들어지면 Azure Portal에서 개요 페이지를 엽니다. 모니터 **작성자 &** 선택하여 별도의 탭에서 **시작 하기** 페이지를 엽니다. 거기에서 Azure-SSIS IR을 계속 만들 수 있습니다.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비전

1. **시작** 페이지에서 **SSIS Integration Runtime 구성** 타일을 선택합니다.

   ![SSIS Integration Runtime 구성 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. **Integration Runtime 설정** 패널의 **일반 설정** 섹션에서 다음 단계를 완료합니다.

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. **이름**에는 통합 런타임의 이름을 입력합니다.

   1. **설명**에는 통합 런타임에 대한 설명을 입력합니다.

   1. **위치**에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다.

   1. **노드 크기의**경우 통합 런타임 클러스터에서 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 여러 개의 계산 집약적 또는 메모리 집약적 패키지를 실행하려면 큰 노드 크기(강화)를 선택합니다.

   1. **노드 수**로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려면 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다.

   1. **에디션/라이선스의**경우 통합 런타임에 대한 SQL Server 버전( 표준 또는 엔터프라이즈)을 선택합니다. 통합 런타임에서 고급 기능을 사용하려면 Enterprise를 선택합니다.

   1. **비용 절감을**위해 통합 런타임에 대한 Azure 하이브리드 혜택 옵션( **예** 또는 **아니요)을**선택합니다. 소프트웨어 보증을 사용하여 자체 SQL Server 라이선스를 가져오려면 **예를** 선택하여 하이브리드 사용을 통해 비용 절감 효과를 얻을 수 있습니다.

   1. **다음**을 선택합니다.

1. **SQL 설정** 섹션에서 다음 단계를 완료합니다.

   ![SQL 설정](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. **Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs**(Azure SQL Database 서버/Managed Instance에서 호스팅하는 SSIS 카탈로그(SSISDB)를 만들어서 프로젝트/패키지/환경/실행 로그 저장) 확인란을 선택하여 Azure-SSIS IR에서 실행할 패키지에 대한 배포 모델을 선택합니다. 패키지를 데이터베이스 서버에서 호스팅하는 SSISDB에 배포하는 프로젝트 배포 모델 또는 패키지를 파일 시스템, 파일 공유 또는 Azure Files로 배포하는 패키지 배포 모델을 선택합니다. 
    
      확인란을 선택하면 사용자 대신 만들고 관리할 SSISDB 인스턴스를 호스트하기 위해 자체 데이터베이스 서버를 가져와야 합니다.
   
      1. **구독**에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

      1. **위치**에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다. 

      1. **카탈로그 데이터베이스 서버 엔드포인트**로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 
    
         선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버 유형을 선택하는 방법에 대한 지침은 이 문서에서 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리되는 인스턴스 비교](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) 섹션을 참조하십시오. 
    
         IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 선택하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인을](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)참조하십시오. 

      1. SSISDB를 호스팅할 데이터베이스 서버의 인증 방법을 선택하려면 **ADF의 관리 ID를 통한 AAD 인증 사용** 확인란을 선택합니다. 데이터 팩터리의 관리 ID를 통한 SQL 인증 또는 Azure AD 인증을 선택합니다. 
    
         확인란을 선택하면 데이터 팩터리의 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure-SSIS IR에 대한 Azure AD 인증 을 참조하세요.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir) 

      1. **관리 사용자 이름**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

      1. **관리자 암호**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

      1. **카탈로그 데이터베이스 서비스 계층**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 서비스 계층을 선택합니다. 기본, 표준 또는 프리미엄 계층을 선택하거나 탄력적 풀 이름을 선택합니다. 

      1. **테스트 연결을**선택합니다. 테스트가 성공하면 **다음**을 선택합니다. 

1. **고급 설정** 섹션에서 다음 단계를 완료합니다.

   ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **노드당 최대 병렬 실행 수**에 대해 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 둘 이상의 코어를 사용하여 계산 집약적이거나 메모리 집약적인 하나의 대형 패키지를 실행하려면 낮은 숫자를 선택합니다. 하나의 코어에서 하나 이상의 소형 패키지를 실행하려면 높은 숫자를 선택합니다.

   1. **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations**(추가 시스템 구성/구성 요소 설치를 통해 Azure-SSIS Integration Runtime 사용자 지정) 확인란을 선택하여 Azure-SSIS IR에 표준/빠른 사용자 지정 설치를 추가할지 여부를 선택합니다. 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)를 참조하세요.

      확인란을 선택하면 다음 단계를 완료합니다.

      ![사용자 지정 설정이 있는 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. **사용자 지정 설치 컨테이너 SAS URI의**경우 표준 사용자 지정 설정에 대한 스크립트 및 관련 파일을 저장하는 컨테이너의 SAS URI를 입력합니다.

      1. **Express 사용자 지정 설정의**경우 **새로를** 선택하여 **익스프레스 사용자 지정 설정 패널을** 연 다음 **Express 사용자 지정 설정 유형** 드롭다운 메뉴(예: **cmdkey 명령 실행,** **환경 변수 추가,** **라이선스가 부여된 구성 요소 설치**등)에서 모든 유형을 선택합니다.

         **라이센스가 부여된 구성 요소** 유형을 설치하려면 **구성 요소 이름** 드롭다운 메뉴에서 ISV 파트너의 통합 구성 요소를 선택하고 필요한 경우 라이센스 **키** 필드에 구입한 제품 라이센스 키를 입력할 수 있습니다.
  
         추가된 익스프레스 맞춤 설정이 **고급 설정** 섹션에 표시됩니다. 제거하려면 해당 확인란을 선택한 다음 **삭제를**선택할 수 있습니다.

   1. **Azure-SSIS 통합 런타임에 대한 VNet 선택을 선택하고, ADF가 특정 네트워크 리소스를 만들 수 있도록 허용하고, 선택적으로 정적 공용 IP 주소** 확인란을 가져와통합 런타임을 가상 네트워크에 조인할지 여부를 선택합니다. 

      IP 방화벽 규칙/가상 네트워크 서비스 끝점이 있는 Azure SQL Database 서버를 사용하거나 SSISDB를 호스트하기 위해 개인 엔드포인트가 있는 관리되는 인스턴스를 사용하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터 원본 또는 대상에 액세스해야 하는 경우 를 선택합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인을](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)참조하십시오. 

      확인란을 선택하면 다음 단계를 완료합니다.

      ![가상 네트워크 관련 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. **구독**에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

      1. **위치**에는 통합 런타임의 동일한 위치가 선택됩니다.

      1. **유형의**경우 가상 네트워크 유형(클래식 또는 Azure 리소스 관리자)을 선택합니다. 클래식 가상 네트워크가 곧 더 이상 사용되지 않으므로 Azure 리소스 관리자 가상 네트워크를 선택하는 것이 좋습니다.

      1. **VNet 이름**에서 가상 네트워크의 이름을 선택합니다. SSISDB를 호스트하기 위해 가상 네트워크 서비스 끝점 또는 개인 끝점이 있는 관리되는 인스턴스가 있는 Azure SQL Database 서버에 사용되는 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 같아야 합니다. 그렇지 않으면 Azure-SSIS IR에 대한 정적 공용 IP 주소를 가져오는 가상 네트워크가 될 수 있습니다.

      1. **서브넷 이름**에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트하기 위해 가상 네트워크 서비스 끝점이 있는 Azure SQL Database 서버에 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하기 위해 개인 끝점을 사용하여 관리되는 인스턴스에 사용되는 서브넷과 다른 서브넷이어야 합니다. 그렇지 않으면 Azure-SSIS IR에 대한 정적 공용 IP 주소를 가져오는 모든 서브넷이 될 수 있습니다.

      1. **Azure-SSIS 통합 런타임에 정적 공용 IP 주소 가져오기** 확인란을 선택하여 Azure-SSIS IR에 대해 정적 공용 IP 주소를 가져올지 여부를 선택하여 데이터 원본의 방화벽에서 허용할 수 있습니다.

         확인란을 선택하면 다음 단계를 완료합니다.

         1. **첫 번째 정적 공용 IP 주소의**경우 Azure-SSIS IR에 대한 요구 사항을 충족하는 첫 번째 정적 공용 IP 주소를 선택합니다. 없는 경우 새 링크 **만들기를** 클릭하여 Azure Portal에서 정적 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.
      
         1. **두 번째 정적 공용 IP 주소의**경우 Azure-SSIS IR에 대한 요구 사항을 충족하는 두 번째 정적 공용 IP 주소를 선택합니다. 없는 경우 새 링크 **만들기를** 클릭하여 Azure Portal에서 정적 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.

   1. **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime**(자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime의 프록시로 설정) 확인란을 선택하여 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성할지 여부를 선택합니다. 자세한 내용은 [자체 호스팅 IR을 프록시로 설정](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)을 참조하세요. 

      확인란을 선택하면 다음 단계를 완료합니다.

      ![자체 호스팅 IR을 갖춘 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. **자체 호스팅 통합 런타임의**경우 Azure-SSIS IR에 대한 프록시로 기존 자체 호스팅 IR을 선택합니다.

      1. **스테이징 저장소 링크된 서비스의**경우 기존 Azure Blob 저장소 연결 서비스를 선택하거나 스테이징을 위해 새 서비스를 만듭니다.

      1. **스테이징 경로의**경우 선택한 Azure Blob 저장소 계정에 Blob 컨테이너를 지정하거나 스테이징에 기본 컨테이너를 사용하도록 비워 둡니다.

   1. **VNet 유효성 검사** > **계속을 선택합니다.** 

1. **요약** 섹션에서 모든 프로비저닝 설정을 검토하고, 추천 설명서 링크를 책갈피로 설정하고, **마침**을 선택하여 통합 런타임을 만들기 시작합니다.

   > [!NOTE]
   > 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR이 가상 네트워크에 가입하는 데 20~30분이 걸릴 수 있습니다.
   >
   > SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 지정된 경우 가상 네트워크에 대한 권한 및 설정을 구성하고 Azure-SSIS IR을 가상 네트워크에 조인합니다.
   > 
   > Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 설치할 수 있는 다른 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

1. 필요한 경우 **연결** 탭에서 **Integration Runtime**으로 전환합니다. **새로 고침**을 선택하여 상태를 새로 고칩니다.

   ![만들기 상태](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. **작업** 열의 링크를 사용하여 통합 런타임을 중지/시작, 편집 또는 삭제합니다. 마지막 링크를 사용하여 통합 런타임에 대한 JSON 코드를 살펴봅니다. 편집 및 삭제 단추는 IR이 중지된 경우에만 활성화됩니다.

   ![Azure SSIS IR 작업](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>포털의 Azure SSIS 통합 런타임

1. Azure Data Factory UI에서 **편집** 탭으로 전환하여 **연결**을 선택합니다. 그런 다음, **통합 런타임** 탭으로 전환하여 데이터 팩터리의 기존 통합 런타임을 확인합니다.

   ![기존 IR 보기](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 새 Azure-SSIS IR을 만들려면 **새로 만들기를** 선택합니다.

   ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **Integration Runtime 설정** 패널에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트) 타일을 선택한 후 **다음**을 선택합니다.

   ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR을 설정하는 나머지 단계는 [Azure SSIS 통합 런타임 프로비저닝](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요.

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Azure PowerShell을 사용하여 통합 런타임 만들기

이 섹션에서는 Azure PowerShell을 사용하여 Azure-SSIS IR을 만듭니다.

### <a name="create-variables"></a>변수 만들기

다음 스크립트를 복사하여 붙여넣습니다. 변수에 대한 값을 지정합니다. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>로그인하고 구독을 선택합니다.

다음 스크립트를 추가하여 로그인하고 Azure 구독을 선택합니다.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>데이터베이스 서버에 대한 연결 유효성 검사

다음 스크립트를 추가하여 Azure SQL Database 서버 또는 관리되는 인스턴스의 유효성을 검사합니다.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
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

### <a name="configure-the-virtual-network"></a>가상 네트워크 구성

다음 스크립트를 추가하여 Azure-SSIS 통합 런타임에 대한 가상 네트워크 사용 권한 및 설정을 자동으로 구성합니다.

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

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

리소스 그룹이 이미 있는 경우 스크립트에 이 코드를 복사하지 마세요. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기

다음 명령을 실행하여 데이터 팩터리를 만듭니다.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Integration Runtime 만들기

다음 명령을 실행하여 Azure에서 SSIS 패키지를 실행하는 Azure-SSIS 통합 런타임을 만듭니다.

SSISDB를 사용하지 않는 경우 `CatalogServerEndpoint`에서 및 `CatalogPricingTier` `CatalogAdminCredential` 매개 변수를 생략할 수 있습니다.

IP 방화벽 규칙/가상 네트워크 서비스 끝점이 있는 Azure SQL Database 서버를 사용하지 않거나 SSISDB를 호스트하기 위해 개인 끝점이 있는 관리되는 `VNetId` `Subnet` 인스턴스를 사용하지 않거나 온-프레미스 데이터에 액세스해야 하는 경우 해당 서버및 매개 변수를 생략하거나 빈 값을 전달할 수 있습니다. Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR을 구성하여 온-프레미스 데이터에 액세스하는 경우에도 생략할 수 있습니다. 그렇지 않으면 생략할 수 없으며 가상 네트워크 구성에서 유효한 값을 전달해야 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인을](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)참조하십시오.

관리인스턴스를 사용하여 SSISDB를 호스트하는 경우 `CatalogPricingTier` 매개 변수를 생략하거나 빈 값을 전달할 수 있습니다. 그렇지 않으면 생략할 수 없으며 Azure SQL Database에 대해 지원되는 가격 책정 계층 목록에서 유효한 값을 전달해야 합니다. 자세한 내용은 [SQL Database 리소스 제한을](../sql-database/sql-database-resource-limits.md)참조하십시오.

데이터 팩터리가 데이터베이스 서버에 연결하기 위해 관리되는 ID와 Azure AD 인증을 `CatalogAdminCredential` 사용하는 경우 매개 변수를 생략할 수 있습니다. 그러나 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 데이터 팩터리의 관리되는 ID를 추가해야 합니다. 자세한 내용은 [Azure-SSIS IR에 대한 Azure AD 인증 을 참조하세요.](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir) 그렇지 않으면 생략할 수 없으며 SQL 인증을 위해 서버 관리자 사용자 이름과 암호에서 형성된 유효한 개체를 전달해야 합니다.

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
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>통합 런타임 시작

다음 명령을 실행하여 Azure-SSIS Integration Runtime을 시작합니다.

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
> 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR이 가상 네트워크에 가입하는 데 20~30분이 걸릴 수 있습니다.
>
> SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 지정된 경우 가상 네트워크에 대한 권한 및 설정을 구성하고 Azure-SSIS IR을 가상 네트워크에 조인합니다.
> 
> Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 설치할 수 있는 다른 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

### <a name="full-script"></a>전체 스크립트

Azure-SSIS 통합 런타임을 만드는 전체 스크립트는 다음과 같습니다.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
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

### Configure a virtual network
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

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Azure 리소스 관리자 템플릿을 사용하여 통합 런타임 만들기

이 섹션에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure-SSIS 통합 런타임을 만듭니다. 다음은 샘플 연습입니다.

1. 다음 Azure Resource Manager 템플릿을 사용하여 JSON 파일을 만듭니다. 각도 대괄호(자리 표시자)의 값을 고유한 값으로 바꿉니다.

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

2. Azure 리소스 관리자 템플릿을 배포하려면 다음 예제와 같이 `New-AzResourceGroupDeployment` 명령을 실행합니다. 이 `ADFTutorialResourceGroup` 예제에서는 리소스 그룹의 이름입니다. `ADFTutorialARM.json`은 데이터 팩터리 및 Azure-SSIS IR에 대한 JSON 정의가 포함된 파일입니다.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    이 명령은 데이터 팩터리 및 Azure-SSIS IR을 생성하지만 IR을 시작하지는 않습니다.

3. Azure-SSIS IR을 시작하려면 `Start-AzDataFactoryV2IntegrationRuntime` 다음 명령을 실행합니다.

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다. 그러나 Azure-SSIS IR이 가상 네트워크에 가입하는 데 20~30분이 걸릴 수 있습니다.
>
> SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 또한 지정된 경우 가상 네트워크에 대한 권한 및 설정을 구성하고 Azure-SSIS IR을 가상 네트워크에 조인합니다.
> 
> Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 설치할 수 있는 다른 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)를 참조하세요.

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포

SSISDB를 사용하는 경우 SQL Server 데이터 도구(SSDT) 또는 SQL Server 관리 스튜디오(SSMS) 도구를 사용하여 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 이러한 도구는 다음과 같은 서버 엔드포인트를 통해 데이터베이스 서버에 연결됩니다. 

- Azure SQL Database 서버의 경우 서버 엔드포인트 형식은 `<server name>.database.windows.net`입니다.
- 프라이빗 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.<dns prefix>.database.windows.net`입니다.
- 퍼블릭 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.public.<dns prefix>.database.windows.net,3342`입니다. 

SSISDB를 사용하지 않는 경우 패키지를 파일 시스템, 파일 공유 또는 Azure 파일에 배포하고 `dtinstall`에서 , `dtutil`및 `dtexec` 명령줄 도구를 사용하여 Azure-SSIS IR에서 패키지를 실행할 수 있습니다. 자세한 내용은 [SSIS 패키지 배포](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)를 참조하세요. 

또한 두 경우 모두 Data Factory 파이프라인에서 SSIS 패키지 실행 활동을 사용하여 배포된 패키지를 Azure-SSIS IR에서 실행할 수도 있습니다. 자세한 내용은 [SSIS 패키지 실행을 Data Factory 첫 번째 클래스 활동으로 호출](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 설명서의 다른 Azure-SSIS IR 항목을 참조하십시오.

- [Azure-SSIS 통합 런타임.](concepts-integration-runtime.md#azure-ssis-integration-runtime) 이 문서에서는 Azure-SSIS IR을 포함하여 일반적으로 통합 런타임에 대한 정보를 제공합니다.
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR에 대한 정보를 검색하고 이해하는 방법을 보여 줍니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지, 시작 또는 삭제하는 방법을 보여 줍니다. 또한 노드를 더 추가하여 Azure-SSIS IR을 확장하는 방법도 보여 줍니다.
- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)