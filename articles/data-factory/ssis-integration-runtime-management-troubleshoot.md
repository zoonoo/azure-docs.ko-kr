---
title: Azure Data Factory에서 SSIS Integration Runtime 관리 문제 해결 Microsoft Docs
description: 이 문서에서는 ssis Integration Runtime (SSIS IR)의 관리 문제에 대 한 문제 해결 지침을 제공 합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 17fe8d9ed02156b8fe9aafd7adca946531895883
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253026"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Azure Data Factory에서 SSIS Integration Runtime 관리 문제 해결

이 문서에서는 ssis Integration Runtime (SSIS IR)의 관리 문제에 대 한 문제 해결 가이드를 제공 합니다.

## <a name="overview"></a>개요

SSIS IR을 프로 비전 하거나 프로 비전 해제 하는 중에 문제가 발생 하면 ADF 포털에 오류 메시지가 있거나 PowerShell cmdlet에서 반환 됩니다. 오류는 항상 자세한 오류 메시지와 함께 오류 코드 형식입니다.

오류 코드가 InternalServerError 인 경우 서비스에 일시적인 문제가 있음을 의미 합니다. 나중에 작업을 다시 시도할 수 있습니다. 다시 시도 해도 도움이 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.

오류를 발생 시킬 수 있는 세 가지 주요 외부 종속성이 있습니다. 오류 코드가 InternalServerError가 아닌 경우 서버 또는 Managed Instance, 사용자 지정 설치 스크립트 및 Virtual Network 구성을 Azure SQL Database 합니다.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database 서버 또는 관리 되는 인스턴스 문제

Ssis 카탈로그 데이터베이스를 사용 하 여 SSIS IR을 프로 비전 하는 경우 Azure SQL Database 서버 또는 Managed Instance 필요 합니다. SSIS IR에서 Azure SQL Database 서버 또는 Managed Instance에 액세스할 수 있어야 합니다. Azure SQL Database 서버 또는 Managed Instance의 계정에는 SSISDB (SSIS 카탈로그 데이터베이스)를 만들 수 있는 권한이 있어야 합니다. 오류가 발생 하는 경우 자세한 SQL 예외 메시지와 함께 오류 코드는 ADF 포털에 표시 됩니다. 오류 코드 문제를 해결 하려면 아래 단계를 따르세요.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

새 SSIS IR을 프로 비전 하거나 IR을 실행 하는 동안이 문제가 발생할 수 있습니다.

IR 프로 비전 중에 오류가 표시 되 고 오류 메시지에서 세부 정보 SqlException 메시지를 가져올 수 있는 경우 다음과 같은 이유로 인해 발생할 수 있습니다.

* 네트워크 연결에 문제가 있습니다. SQL Server 또는 Managed Instance 호스트 이름에 액세스할 수 있는지 확인 하 고 SSIS IR이 서버에 액세스할 수 없도록 차단 하는 방화벽 또는 NSG가 없습니다.
* 로그인이 실패 하 고 SQL 인증을 사용 합니다. 이는 제공 된 계정이 SQL Server에 로그인 할 수 없음을 의미 합니다. 올바른 사용자 계정이 제공 되었는지 확인 합니다.
* 로그인 실패 및 AAD 인증 (관리 Id)이 사용 됩니다. AAD 그룹에 팩터리의 관리 되는 Id를 추가 하 고 관리 되는 Id에 카탈로그 데이터베이스 서버에 대 한 액세스 권한이 있는지 확인 합니다.
* 연결 제한 시간은 항상 보안 관련 구성 때문입니다. 새 VM을 만들고, IR이 VNet에 있는 경우 VM을 동일한 VNet VNet에 연결 하 고, SSMS를 설치 하 고 Azure SQL Database 서버 또는 Managed Instance 상태를 확인 하는 것이 좋습니다.

다른 문제는 SQL 예외 오류 메시지 세부 정보를 참조 하 고 오류 메시지에 표시 된 문제를 해결 하십시오. 문제가 계속 발생 하는 경우 Azure SQL Database 서버 또는 Managed Instance 지원 팀에 문의 하세요.

IR을 실행 하는 동안 오류가 표시 되는 경우 일부 네트워크 보안 그룹 또는 방화벽이 변경 될 수 있으며,이로 인해 SSIS IR 작업자 노드가 Azure SQL Database 서버에 액세스할 수 없거나 더 이상 Managed Instance. SSIS IR 작업자 노드의 차단을 해제 하 Azure SQL Database 서버 또는 Managed Instance에 액세스 합니다.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

오류 메시지는 "데이터베이스 ' SSISDB '의 크기 할당량에 도달 했습니다. 데이터를 분할 또는 삭제 하거나 인덱스를 삭제 하거나 설명서를 참조 하 여 가능한 해결 방법을 확인 하십시오. " 가능한 해결 방법은 다음과 같습니다.
* SSISDB의 크기 할당량을 늘립니다.
* SSISDB 구성을 변경 하 여 다음과 같은 크기를 줄입니다.
   * 보존 기간 및 프로젝트 버전 수를 줄입니다.
   * 로그의 보존 기간을 줄입니다.
   * 로그의 기본 수준 변경 등

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

이 오류는 Azure SQL Database 서버 또는 Managed Instance에 이미 다른 IR에서 만들고 사용 하는 SSISDB가 있음을 의미 합니다. 다른 Azure SQL Database 서버 또는 Managed Instance를 제공 하거나 기존 SSISDB를 삭제 하 고 새 IR을 다시 시작 해야 합니다.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

이 오류는 다음과 같은 이유로 인해 발생할 수 있습니다.

* SSIS IR에 대해 구성 된 사용자 계정에는 데이터베이스를 만들 수 있는 권한이 없습니다. 사용자에 게 데이터베이스를 만들 수 있는 권한을 부여할 수 있습니다.
* 실행 제한 시간, DB 작업 시간 제한 등의 데이터베이스 시간 제한을 만듭니다. 나중에 다시 시도 하 여 문제가 해결 되었는지 확인할 수 있습니다. 다시 시도가 작동 하지 않는 경우 Azure SQL Database 서버 또는 Managed Instance 지원 팀에 문의 하세요.

다른 문제는 SQL 예외 오류 메시지를 확인 하 고 오류 메시지에 언급 된 문제를 해결 하십시오. 문제가 여전히 발생 하는 경우 Azure SQL Database 서버 또는 Managed Instance 지원 팀에 문의 하세요.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

오류 메시지는 "잘못 된 개체 이름 ' catalog_properties '."와 유사 합니다 .이는 SSISDB 라는 데이터베이스가 이미 있지만 SSIS IR로 생성 되지 않았거나 마지막 SSIS IR 프로 비전에서 오류로 인해 데이터베이스가 잘못 된 상태에 있는 것을 의미 합니다. 이름이 SSISDB 인 기존 데이터베이스를 삭제 하거나 새 Azure SQL Database 서버를 구성 하거나 IR에 대해 Managed Instance 수 있습니다.

## <a name="custom-setup"></a>사용자 지정 설치

사용자 지정 설치 프로그램은 SSIS IR의 프로 비전 또는 재구성 중에 사용자 고유의 설정 단계를 추가 하기 위한 인터페이스를 제공 합니다. 자세한 내용은 [AZURE SSIS 통합 런타임에 대 한 설정 사용자 지정](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)을 참조 하세요.

컨테이너의 모든 파일이 SSIS IR 작업자 노드에 다운로드 되므로 컨테이너에 필요한 사용자 지정 설치 파일만 포함 해야 합니다. SSIS IR에서 스크립트를 실행 하기 전에 스크립트 실행 문제를 해결 하려면 로컬 컴퓨터에서 사용자 지정 설치 스크립트를 테스트 하는 것이 좋습니다.

사용자 지정 설치 스크립트를 다운로드 하 고 다시 설치 하기 위해 컨테이너에 다시 액세스 해야 하는 정기적인 업데이트로, SSIS IR이 정기적으로 실행 되는 동안에는 사용자 지정 설치 스크립트 컨테이너가 검사 됩니다. 컨테이너에 액세스할 수 있는지 여부와 기본 .cmd 파일이 있는지 여부를 확인 합니다.

사용자 지정 설치에 오류가 발생 하는 경우 코드 CustomSetupScriptFailure와 관련 된 오류가 표시 됩니다. 하위 오류 코드가 있는 오류 메시지를 확인 하십시오.  하위 오류 코드 문제를 해결 하려면 아래 단계를 따르세요.  

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

SSIS IR은 사용자 지정 설치를 위해 Azure blob 컨테이너에 액세스할 수 없음을 의미 합니다. 컨테이너의 SAS URI에 연결할 수 있고 만료 되지 않았는지 확인 합니다.

Ir이 실행 중 상태 이면 먼저 IR을 중지 하 고 새 사용자 지정 설치 컨테이너 SAS URI를 사용 하 여 IR을 다시 구성한 다음 IR을 다시 시작 합니다.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

즉, SSIS IR이 blob 컨테이너에서 사용자 지정 설치 스크립트 (기본 .cmd)를 찾을 수 없음을 의미 합니다. 기본 cmd.exe가 컨테이너에 있는지 확인 합니다 .이는 사용자 지정 설치 설치의 진입점입니다.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

즉, 사용자 지정 설치 스크립트 (기본 .cmd)의 실행이 실패 했음을 의미 합니다. 먼저 로컬 컴퓨터에서 스크립트를 사용해 보거나 blob 컨테이너에서 사용자 지정 설치 실행 로그를 확인할 수 있습니다.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

사용자 지정 설치 스크립트 시간 제한 실행을 의미 합니다. Blob 컨테이너에 필요한 사용자 지정 설치 파일만 포함 되어 있는지 확인 합니다. Blob 컨테이너에서 사용자 지정 설치 실행 로그를 확인할 수도 있습니다. 사용자 지정 설치에 대 한 최대 기간은 45 분 전에 설정 되며, 최대 기간은 컨테이너에서 모든 파일을 다운로드 하 여 SSIS IR에 설치 하는 시간을 포함 합니다. 더 긴 기간이 필요한 경우 지원 티켓을 발생 시킵니다.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

이는 blob 컨테이너에 사용자 지정 설치 실행 로그를 업로드 하지 못했음을 의미 합니다. SSIS IR에는 blob 컨테이너에 대 한 쓰기 권한이 없거나 일부 저장소나 네트워크 문제가 있기 때문입니다. 사용자 지정 설치가 완료 되 면이 오류는 SSIS 함수에 영향을 주지 않지만 로그가 누락 됩니다. 다른 오류로 인해 사용자 지정 설치에 실패 하 여 로그를 업로드 하지 못하는 경우 분석을 위해 로그를 업로드 하 고이 문제를 해결 한 후에 더 많은 지정 된 문제를 보고할 수 있도록이 오류가 먼저 보고 됩니다. 다시 시도한 후에도이 문제가 해결 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.

## <a name="virtual-network-configuration"></a>가상 네트워크 구성

SSIS IR을 VNet (Virtual Network)에 조인할 때 사용자 구독에서 VNet을 사용 합니다. 자세한 내용은 [AZURE SSIS integration runtime을 가상 네트워크에 가입](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)을 참조 하세요.

VNet 관련 문제가 있는 경우 아래와 같이 오류가 표시 됩니다.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

이는 변형 된 이유로 인해 발생할 수 있습니다. 하위 오류 코드 문제를 해결 하려면 아래 단계를 따르세요.

### <a name="forbidden"></a>사용할 수 없음

오류 메시지는 "subnetId가 현재 계정에 대해 사용 하도록 설정 되어 있지 않습니다. Microsoft Batch 리소스 공급자가 VNet의 동일한 구독에 등록 되어 있지 않습니다. "

Azure Batch VNet에 액세스할 수 없음을 의미 합니다. VNet의 동일한 구독에서 Microsoft Batch 리소스 공급자를 등록 합니다.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

오류 메시지는 "지정 된 VNet이 없거나 일괄 처리 서비스에서 액세스할 수 없습니다." 또는 "지정 된 서브넷 xxx가 없습니다."와 같습니다.

VNet이 없거나, Azure Batch 서비스에서 액세스할 수 없거나, 제공 된 서브넷이 존재 하지 않음을 의미 합니다. VNet 및 서브넷이 존재 하 고 Azure Batch 액세스할 수 있는지 확인 합니다.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

메시지는 "Vnet에서 Integration Runtime를 프로 비전 하지 못했습니다. DNS 서버 또는 NSG 설정이 구성 된 경우 DNS 서버에 액세스할 수 있고 NSG가 올바르게 구성 되어 있는지 확인 하십시오. "

사용자 지정 된 DNS 서버 또는 NSG 설정 구성이 있을 수 있으며,이로 인해 SSIS IR에 필요한 Azure 서버 이름을 확인할 수 없거나 액세스할 수 없습니다. 자세한 내용은 [SSIS IR VNet 구성](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 문서를 참조 하세요. 문제가 여전히 발생 하는 경우 Azure Data Factory 지원 팀에 문의 하세요.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR은 정기적으로 자동으로 업데이트 되 고, 업그레이드 하는 동안 새 Azure Batch 풀이 만들어지고 이전 Azure Batch 풀이 삭제 되며, 이전 풀의 VNet 관련 리소스가 삭제 되 고, 아래에 새 VNet 관련 리소스가 생성 됩니다. 가입은. 이 오류는 구독 또는 리소스 그룹 수준에서 삭제 잠금으로 인해 이전 풀의 VNet 관련 리소스를 삭제 하지 못했음을 의미 합니다. 삭제 잠금을 제거 하는 데 도움을 줍니다.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

이유 때문에 SSIS IR 프로 비전이 실패할 수 있습니다. 오류가 발생 하면 생성 된 모든 리소스가 삭제 됩니다. 그러나 구독 또는 리소스 그룹 수준에서 리소스 삭제 잠금이 있으므로 VNet 리소스를 삭제 하지 못했습니다. 삭제 잠금을 제거 하 고 IR을 다시 시작 합니다.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

SSIS IR을 중지할 때 VNet과 관련 된 모든 리소스가 삭제 되지만 구독 또는 리소스 그룹 수준에서 리소스 삭제 잠금이 있으므로 삭제 하지 못했습니다. 삭제 잠금을 제거 하 고 중지를 다시 시도 하십시오.

### <a name="nodeunavailable"></a>NodeUnavailable 수 없음

이 오류는 IR을 실행 하는 동안 발생 합니다. 즉, IR은 상태 이전에 상태를 유지 하 고 비정상 상태 이므로 항상 DNS 서버 또는 NSG 구성 변경으로 인해 SSIS IR이 종속 서비스에 연결할 수 없기 때문입니다. DNS 서버 또는 NSG 구성 문제를 해결 하는 데 도움이 됩니다. 자세한 내용은 [SSIS IR VNet 구성](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)을 참조 하세요. 문제가 여전히 발생 하는 경우 Azure Data Factory 지원 팀에 문의 하세요.
