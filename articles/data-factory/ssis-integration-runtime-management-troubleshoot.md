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
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609628"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Azure Data Factory에서 SSIS Integration Runtime 관리 문제 해결

이 문서에서는 ssis IR이 라고도 하는 Azure IR (SQL Server Integration Services) Integration Runtime의 관리 문제에 대 한 문제 해결 지침을 제공 합니다.

## <a name="overview"></a>개요

SSIS IR을 프로 비전 하거나 프로 비전 해제 하는 동안 문제가 발생 하면 Microsoft Azure Data Factory 포털 또는 PowerShell cmdlet에서 반환 된 오류에 오류 메시지가 표시 됩니다. 오류는 항상 자세한 오류 메시지와 함께 오류 코드 형식으로 표시 됩니다.

오류 코드가 InternalServerError 인 경우 서비스에 일시적인 문제가 있으며 나중에 작업을 다시 시도해 야 합니다. 다시 시도 해도 도움이 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.

그렇지 않으면 세 가지 주요 외부 종속성으로 인해 오류가 발생할 수 있습니다. Azure SQL Database 서버 또는 관리 되는 인스턴스, 사용자 지정 설치 스크립트 및 가상 네트워크 구성입니다.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Azure SQL Database 서버 또는 관리 되는 인스턴스 문제

Ssis 카탈로그 데이터베이스를 사용 하 여 SSIS IR을 프로 비전 하는 경우에는 Azure SQL Database 서버 또는 관리 되는 인스턴스가 필요 합니다. SSIS IR은 Azure SQL Database 서버 또는 관리 되는 인스턴스에 액세스할 수 있어야 합니다. 또한 Azure SQL Database 서버 또는 관리 되는 인스턴스의 계정에는 SSISDB (SSIS 카탈로그 데이터베이스)를 만들 수 있는 권한이 있어야 합니다. 오류가 발생 하는 경우 자세한 SQL 예외 메시지가 포함 된 오류 코드가 Data Factory 포털에 표시 됩니다. 다음 목록의 정보를 사용 하 여 오류 코드 문제를 해결할 수 있습니다.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

새 SSIS IR을 프로 비전 하는 동안 또는 IR을 실행 하는 동안이 문제가 나타날 수 있습니다. IR을 프로 비전 하는 동안이 오류가 발생 하는 경우 오류 메시지에 다음과 같은 문제 중 하나를 나타내는 자세한 SqlException 메시지가 표시 될 수 있습니다.

* 네트워크 연결에 문제가 있습니다. SQL Server 또는 관리 되는 인스턴스 호스트 이름에 액세스할 수 있는지 확인 합니다. 또한 방화벽 또는 NSG (네트워크 보안 그룹)가 서버에 대 한 SSIS IR 액세스를 차단 하 고 있지 않은지 확인 합니다.
* SQL 인증을 수행 하는 동안 로그인 하지 못했습니다. 제공 된 계정이 SQL Server 데이터베이스에 로그인 할 수 없습니다. 올바른 사용자 계정을 제공 해야 합니다.
* Azure AD (Microsoft Azure Active Directory) 인증 중에 로그인 하지 못했습니다 (관리 id). AAD 그룹에 팩터리의 관리 id를 추가 하 고 관리 id에 카탈로그 데이터베이스 서버에 대 한 액세스 권한이 있는지 확인 합니다.
* 연결 시간이 초과 되었습니다. 이 오류는 항상 보안 관련 구성으로 인해 발생 합니다. 다음을 수행하는 것이 좋습니다.
  1. 새 VM을 만듭니다.
  1. IR이 가상 네트워크에 있는 경우 VM을 IR의 동일한 Microsoft Azure Virtual Network에 조인 합니다.
  1. SSMS를 설치 하 고 Azure SQL Database 서버 또는 관리 되는 인스턴스 상태를 확인 합니다.

다른 문제가 있는 경우 자세한 SQL 예외 오류 메시지에 표시 된 문제를 해결 하십시오. 문제가 여전히 발생 하는 경우 Azure SQL Database 서버 또는 관리 되는 인스턴스 지원 팀에 문의 하세요.

IR을 실행 하는 동안 오류가 발생 하는 경우 네트워크 보안 그룹 또는 방화벽 변경으로 인해 SSIS IR 작업자 노드가 Azure SQL Database 서버 또는 관리 되는 인스턴스에 액세스할 수 없게 될 수 있습니다. SSIS IR worker 노드가 Azure SQL Database 서버 또는 관리 되는 인스턴스에 액세스할 수 있도록 차단을 해제 합니다.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

이러한 종류의 오류 메시지는 다음과 같습니다. "' SSISDB ' 데이터베이스가 크기 할당량에 도달 했습니다. 데이터를 분할 또는 삭제 하거나 인덱스를 삭제 하거나 설명서를 참조 하 여 가능한 해결 방법을 확인 하십시오. " 

가능한 해결 방법은 다음과 같습니다.
* SSISDB의 할당량 크기를 늘립니다.
* SSISDB의 구성을 변경 하 여 크기를 줄입니다.
   * 보존 기간 및 프로젝트 버전 수를 줄입니다.
   * 로그의 보존 기간을 줄입니다.
   * 로그의 기본 수준 변경

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

이 오류는 Azure SQL Database 서버 또는 관리 되는 인스턴스에 이미 SSISDB가 있고 다른 IR에서 사용 되 고 있음을 의미 합니다. 다른 Azure SQL Database 서버 또는 관리 되는 인스턴스를 제공 하거나 기존 SSISDB를 삭제 하 고 새 IR을 다시 시작 해야 합니다.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

다음 이유 중 하나로 인해이 오류가 발생할 수 있습니다.

* SSIS IR에 대해 구성 된 사용자 계정에는 데이터베이스를 만들 수 있는 권한이 없습니다. 사용자에 게 데이터베이스를 만들 수 있는 권한을 부여할 수 있습니다.
* 실행 제한 시간 또는 DB 작업 시간 제한과 같은 데이터베이스를 만드는 동안 시간 초과가 발생 합니다. 작업은 나중에 다시 시도해 야 합니다. 다시 시도가 작동 하지 않는 경우 Azure SQL Database 서버 또는 Managed Instance 지원 팀에 문의 하세요.

다른 문제는 SQL 예외 오류 메시지를 확인 하 고 오류 세부 정보에 설명 된 문제를 해결 하십시오. 문제가 여전히 발생 하는 경우 Azure SQL Database 서버 또는 관리 되는 인스턴스 지원 팀에 문의 하세요.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

이러한 종류의 오류 메시지는 다음과 같습니다. "잘못 된 개체 이름 ' catalog_properties '." 이 경우 이름이 SSISDB 인 데이터베이스가 이미 있지만 SSIS IR을 통해 생성 되지 않았거나 마지막 SSIS IR 프로 비전에서 오류로 인해 데이터베이스가 잘못 된 상태에 있습니다. 이름이 SSISDB 인 기존 데이터베이스를 삭제 하거나 IR에 대 한 새 Azure SQL Database 서버 또는 관리 되는 인스턴스를 구성할 수 있습니다.

## <a name="custom-setup-issues"></a>사용자 지정 설치 문제

사용자 지정 설치 프로그램은 SSIS IR의 프로 비전 또는 재구성 중에 사용자 고유의 설정 단계를 추가 하기 위한 인터페이스를 제공 합니다. 자세한 내용은 [AZURE SSIS Integration Runtime 설정 사용자 지정](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)을 참조 하세요.

컨테이너에 필요한 사용자 지정 설치 파일만 포함 되어 있는지 확인 합니다. 컨테이너의 모든 파일이 SSIS IR 작업자 노드에 다운로드 됩니다. SSIS IR에서 스크립트를 실행 하기 전에 스크립트 실행 문제를 해결 하려면 로컬 컴퓨터에서 사용자 지정 설치 스크립트를 테스트 하는 것이 좋습니다.

SSIS IR이 정기적으로 업데이트 되기 때문에 IR을 실행 하는 동안 사용자 지정 설치 스크립트 컨테이너가 검사 됩니다. 이렇게 업데이트 하려면 컨테이너에 액세스 하 여 사용자 지정 설치 스크립트를 다운로드 하 고 다시 설치 해야 합니다. 또한이 프로세스는 컨테이너에 액세스할 수 있는지 여부와 기본 .cmd 파일이 있는지 여부를 확인 합니다.

사용자 지정 설치를 포함 하는 모든 오류에 대해 CustomSetupScriptBlobContainerInaccessible 또는 CustomSetupScriptNotFound와 같은 하위 코드를 포함 하는 CustomSetupScriptFailure 오류 코드가 표시 됩니다.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

이 오류는 SSIS IR이 사용자 지정 설치를 위해 Azure blob 컨테이너에 액세스할 수 없음을 의미 합니다. 컨테이너의 SAS URI에 연결할 수 있고 만료 되지 않았는지 확인 합니다.

IR을 실행 하는 경우 IR을 중지 하 고 새 사용자 지정 설치 컨테이너 SAS URI를 사용 하 여 ir을 다시 구성한 다음 IR을 다시 시작 합니다.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

이 오류는 SSIS IR이 blob 컨테이너에서 사용자 지정 설치 스크립트 (기본 .cmd)를 찾을 수 없음을 의미 합니다. 기본 cmd.exe가 컨테이너에 있는지 확인 합니다 .이는 사용자 지정 설치 설치의 진입점입니다.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

이 오류는 사용자 지정 설치 스크립트 (main. .cmd)의 실행이 실패 했음을 의미 합니다. 먼저 로컬 컴퓨터에서 스크립트를 사용해 보거나 blob 컨테이너에서 사용자 지정 설치 실행 로그를 확인 합니다.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

이 오류는 사용자 지정 설치 스크립트 실행 제한 시간을 나타냅니다. Blob 컨테이너에 필요한 사용자 지정 설치 파일만 포함 되어 있는지 확인 합니다. 또한 blob 컨테이너에서 사용자 지정 설치 실행 로그를 확인 해야 합니다. 사용자 지정 설치의 최대 기간은 시간 제한이 초과 되기 전 45 분이 고, 최대 기간은 컨테이너에서 모든 파일을 다운로드 하 여 SSIS IR에 설치 하는 시간을 포함 합니다. 시간이 더 필요한 경우 지원 티켓을 발생 시킵니다.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

이 오류는 사용자 지정 설치 실행 로그를 blob 컨테이너에 업로드 하려는 시도가 실패 했음을 의미 합니다. 이 문제는 SSIS IR이 blob 컨테이너에 대 한 쓰기 권한을가지고 있지 않거나 저장소 또는 네트워크 문제로 인해 발생 합니다. 사용자 지정 설치가 완료 되 면이 오류가 SSIS 함수에 영향을 주지 않지만 로그가 누락 됩니다. 다른 오류로 인해 사용자 지정 설치에 실패 하 고 로그가 업로드 되지 않은 경우 분석을 위해 로그를 업로드할 수 있도록 먼저이 오류를 보고 합니다. 또한이 문제를 해결 한 후에는 더 구체적인 문제를 보고 합니다. 다시 시도한 후에도이 문제가 해결 되지 않으면 Azure Data Factory 지원 팀에 문의 하세요.

## <a name="virtual-network-configuration"></a>가상 네트워크 구성

SSIS IR을 Azure Virtual Network에 조인할 때 SSIS IR은 사용자 구독에 속한 가상 네트워크를 사용 합니다. 자세한 내용은 [AZURE SSIS Integration Runtime를 가상 네트워크에 가입](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)을 참조 하세요.

Virtual Network 관련 문제가 있는 경우 다음 오류 중 하나가 표시 됩니다.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

이 오류는 다양 한 이유로 발생할 수 있습니다. 문제를 해결 하려면 [금지](#forbidden), [Invalidpropertyvalue](#invalidpropertyvalue)및 [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) 섹션을 참조 하세요.

### <a name="forbidden"></a>사용할 수 없음

이러한 종류의 오류는 다음과 유사할 수 있습니다. "SubnetId가 현재 계정에 대해 사용 하도록 설정 되어 있지 않습니다. Microsoft Batch 리소스 공급자가 VNet의 동일한 구독에 등록 되어 있지 않습니다. "

이러한 세부 정보는 Azure Batch 가상 네트워크에 액세스할 수 없음을 의미 합니다. Virtual Network와 동일한 구독에 있는 Microsoft Batch 리소스 공급자를 등록 합니다.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

이러한 종류의 오류는 다음 중 하 나와 유사할 수 있습니다. 

- "지정 된 VNet이 없거나 Batch 서비스에 액세스할 수 없습니다."
- "지정 된 서브넷 xxx가 없습니다."

이러한 오류는 가상 네트워크가 없거나, Azure Batch 서비스에서 액세스할 수 없거나, 제공 된 서브넷이 존재 하지 않음을 의미 합니다. 가상 네트워크 및 서브넷이 존재 하 고 Azure Batch 액세스할 수 있는지 확인 하십시오.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

이러한 종류의 오류 메시지는 다음과 같습니다. "VNet에서 Integration Runtime를 프로 비전 하지 못했습니다. DNS 서버 또는 NSG 설정이 구성 된 경우 DNS 서버에 액세스할 수 있고 NSG가 올바르게 구성 되어 있는지 확인 하십시오. "

이 경우에는 사용자 지정 된 DNS 서버 또는 NSG 설정 구성이 있을 수 있습니다. 그러면 SSIS IR에 필요한 Azure 서버 이름이 확인 되거나 액세스 되지 않습니다. 자세한 내용은 [SSIS IR Virtual Network 구성](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)을 참조 하세요. 문제가 여전히 발생 하는 경우 Azure Data Factory 지원 팀에 문의 하세요.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR은 정기적으로 자동으로 업데이트 됩니다. 업그레이드 하는 동안 새 Azure Batch 풀이 만들어지고 이전 Azure Batch 풀이 삭제 됩니다. 또한 이전 풀에 대 한 Virtual Network 관련 리소스가 삭제 되 고 새 Virtual Network 관련 리소스가 구독 하에 생성 됩니다. 이 오류는 구독 또는 리소스 그룹 수준에서 삭제 잠금으로 인해 이전 풀의 Virtual Network 관련 리소스를 삭제 하지 못했음을 의미 합니다. 고객은 삭제 잠금을 제어 하 고 설정 하므로이 경우 삭제 잠금을 제거 해야 합니다.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR 프로 비전이 실패 하면 생성 된 모든 리소스가 삭제 됩니다. 그러나 구독 또는 리소스 그룹 수준에서 리소스 삭제 잠금이 있는 경우 Virtual Network 리소스가 예상한 대로 삭제 되지 않습니다. 이 오류를 해결 하려면 삭제 잠금을 제거 하 고 IR을 다시 시작 합니다.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

SSIS IR을 중지 하면 Virtual Network 관련 된 모든 리소스가 삭제 됩니다. 그러나 구독 또는 리소스 그룹 수준에서 리소스 삭제 잠금이 있는 경우 삭제가 실패할 수 있습니다. 여기서는 고객이 삭제 잠금을 제어 하 고 설정 합니다. 따라서 삭제 잠금을 제거한 후 SSIS IR을 다시 중지 해야 합니다.

### <a name="nodeunavailable"></a>NodeUnavailable 수 없음

IR이 실행 중일 때이 오류가 발생 하며 IR이 비정상 상태임을 의미 합니다. 이 오류는 SSIS IR이 필요한 서비스에 연결 하지 못하도록 차단 하는 DNS 서버 또는 NSG 구성의 변경으로 인해 항상 발생 합니다. DNS 서버와 NSG의 구성은 고객에 의해 제어 되므로 고객은 최종 중단 문제를 해결 해야 합니다. 자세한 내용은 [SSIS IR Virtual Network 구성](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)을 참조 하세요. 문제가 여전히 발생 하는 경우 Azure Data Factory 지원 팀에 문의 하세요.
