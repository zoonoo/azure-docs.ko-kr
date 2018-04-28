---
title: Microsoft Azure 스택 개발 키트 릴리스 정보 | Microsoft Docs
description: 향상 된 기능, 수정 및 Azure 스택 개발 키트에 대 한 알려진된 문제입니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3335fdd3a1bb20c378bf36307d742491de0e46ad
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure 스택 개발 키트 릴리스 정보
이러한 릴리스 정보에서는 향상 된 기능, 수정 및 Azure 스택 개발 키트의 알려진된 문제에 대 한 정보를 제공 합니다. 실행 중인 버전을 잘 모르는 경우 다음을 할 수 있습니다 [포털을 사용 하 여](.\.\azure-stack-updates.md#determine-the-current-version)합니다.

> 구독 하 여 ASDK는의 새로운 기능을 통해 최신 정보는 [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [피드](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)합니다.

## <a name="build-201803291"></a>빌드 20180329.1

### <a name="new-features-and-fixes"></a>새 기능 및 수정
새로운 기능 및 수정 사항에 대 한 통합 된 시스템 버전 1803 Azure 스택 개발 키트에 적용 하는 Azure 스택 해제 합니다. 참조는 [새로운 기능](.\.\azure-stack-update-1803.md#new-features) 및 [해결 된 문제](.\.\azure-stack-update-1803.md#fixed-issues) Azure 스택 1803의 섹션 자세한 내용은 릴리스 정보를 업데이트 합니다.  
> [!IMPORTANT]    
> 에 나열 된 항목 중 일부는 **새로운 기능** 및 **해결 된 문제** 섹션에서는 Azure 스택 통합 시스템에만 적용 됩니다.

### <a name="changes"></a>변경 내용
- 새로 만든된 제품의 상태를 변경 하는 방법은 *개인* 를 *공용* 또는 *폐기 된* 변경 되었습니다. 자세한 내용은 참조 [제안을 만들](.\.\azure-stack-create-offer.md)합니다. 


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- 기능 [새 지원 요청을 드롭다운 목록에서 열려는](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 개발 키트를 사용 하 여 https://aka.ms/azurestackforum합니다.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- 리소스 또는 리소스 그룹의 속성을 볼 때의 **이동** 단추가 비활성화 됩니다. 이 동작은 사용할 수 있습니다. 리소스 그룹 또는 구독 간에 리소스 또는 리소스 그룹을 이동 합니다. 현재 지원 되지 않습니다.
 
- 표시는 **활성화 필요** Azure 스택 개발 키트를 등록 해야 한다는 경고 성 알림입니다. 이 동작은 사용할 수 있습니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 업데이트 타일 업데이트에 대 한 정보를 표시 하는 실패 합니다. 이 문제를 해결 하려면 타일 새로 고침을 클릭 합니다.

-   관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 알림이 표시 될 수 있습니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 



#### <a name="marketplace"></a>Marketplace
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

-  VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>네트워킹
- 아래 **네트워킹**누르면, **연결** VPN 연결을 설정 하려면 **VNet 대 VNet** 가능한 연결 유형으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 현재만 **사이트 (IPsec)** 옵션은 지원 됩니다.

- VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.



- 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.



#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- 사용자가 새 SQL 또는 MySQL SKU에 데이터베이스를 만들 수 최대 1 시간이 걸릴 수 있습니다.

- 리소스 공급자 및 사용자 작업에 사용 하기 위해 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. 응용 프로그램 서비스를 포함 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

#### <a name="app-service"></a>App Service
- 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure 스택 도구를 다운로드합니다.
- 사용 하는 경우는 *호출 webrequest* Github에서 Azure 스택을 다운로드 하려면 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원의 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)으로 인해 발생합니다. 자세한 내용은 참조 [약한 암호화 표준 제거 통지](https://githubengineering.com/crypto-removal-notice/)합니다.

  이 문제를 해결 하려면 추가 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` TLSv1.2 GitHub 리포지토리에에서 다운로드할 때 사용할 PowerShell 콘솔을 강제 적용 하는 스크립트의 맨 위로 이동 합니다.






## <a name="build-201803021"></a>빌드 20180302.1

### <a name="new-features-and-fixes"></a>새 기능 및 수정
참조는 [새 기능 및 수정](.\.\azure-stack-update-1802.md#new-features-and-fixes) Azure 스택에 대 한 Azure 스택 1802 업데이트 릴리스 정보 섹션 시스템을 통합 합니다.

> [!IMPORTANT]    
> 에 나열 된 항목 중 일부는 **새 기능 및 수정** 섹션 통합 Azure 스택 시스템에만 적용 됩니다.


### <a name="known-issues"></a>알려진 문제
 
#### <a name="portal"></a>포털
- 기능 [새 지원 요청을 드롭다운 목록에서 열려는](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 개발 키트를 사용 하 여 https://aka.ms/azurestackforum합니다.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- 리소스 또는 리소스 그룹의 속성을 볼 때의 **이동** 단추가 비활성화 됩니다. 이 동작은 사용할 수 있습니다. 리소스 그룹 또는 구독 간에 리소스 또는 리소스 그룹을 이동 합니다. 현재 지원 되지 않습니다.
 
- 표시는 **활성화 필요** Azure 스택 개발 키트를 등록 해야 한다는 경고 성 알림입니다. 이 동작은 사용할 수 있습니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 업데이트 타일 업데이트에 대 한 정보를 표시 하는 실패 합니다. 이 문제를 해결 하려면 타일 새로 고침을 클릭 합니다.

-   관리 포털에서 Microsoft.Update.Admin 구성 요소에 대 한 중요 한 알림이 표시 될 수 있습니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

    이 경고는 무시 해도 됩니다. 

- 관리 포털 및 사용자 포털 모두에서 개요 블레이드에 이전 API 버전을 사용 하 여 만든 저장소 계정에 대 한 개요 블레이드를 선택 하면 로드에 실패 (예: 2015-06-15). 

  이 문제를 해결 PowerShell 실행을 사용 하는 **시작 ResourceSynchronization.ps1** 저장소 계정 세부 정보에 대 한 액세스를 복원 하는 스크립트입니다. [이 스크립트는 GitHub에서 사용할 수 있는]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)는 ASDK를 사용 하는 경우 개발 키트 호스트에서 서비스 관리자 자격 증명으로 실행 해야 합니다.  

- **서비스 상태** 블레이드 로드 되지 않습니다. 관리자 또는 사용자 포털에서 Azure 스택 서비스 상태 블레이드를 열 때 오류가 표시 되 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 않습니다 되지만 Azure 스택의 이후 버전에서 구현 됩니다.

#### <a name="health-and-monitoring"></a>상태 및 모니터링
Azure 스택 관리자 포털에서 이름으로 중요 한 알림이 표시 될 수 있습니다 **외부 인증서 만료 보류 중인**합니다.  이 경고는 무시 해도 됩니다 및 Azure 스택 개발 키트의 작업에 영향을 줍니다. 


#### <a name="marketplace"></a>Marketplace
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- Azure 스택 사용 하는 고정된 형식 Vhd만 지원 합니다. Azure 스택 마켓플레이스를 통해 제공 되는 일부 이미지 동적 Vhd를 사용 하지만 제거 된 것입니다. 가상 컴퓨터 (VM)에 연결 된 동적 디스크 크기 조정을 VM 실패 한 상태로 둡니다.

  이 문제를 해결 하려면 VM의 디스크를 저장소 계정에 VHD blob을 삭제 하지 않고 VM을 삭제 합니다. 동적 디스크에서 VHD를 고정 디스크로으로 변환 하 고 가상 컴퓨터를 다시 만듭니다.

- Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

-  VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>네트워킹
- 아래 **네트워킹**누르면, **연결** VPN 연결을 설정 하려면 **VNet 대 VNet** 가능한 연결 유형으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 현재만 **사이트 (IPsec)** 옵션은 지원 됩니다.

- VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.

-   그러나 포털에 IP 전달 기능을 표시할지, IP 전달을 사용 하도록 설정 해도 효과가 없습니다. 이 기능은 아직 지원 되지 않습니다.

- 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.
 
- Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.



#### <a name="sql-and-mysql"></a>SQL 및 MySQL 
- 사용자가 새 SQL 또는 MySQL SKU에 데이터베이스를 만들 수 최대 1 시간이 걸릴 수 있습니다.

- 리소스 공급자 및 사용자 작업에 사용 하기 위해 서버를 호스팅하는 데이터베이스 전용 이어야 합니다. 응용 프로그램 서비스를 포함 한 모든 다른 소비자에 의해 사용 되는 인스턴스를 사용할 수 없습니다.

#### <a name="app-service"></a>App Service
- 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure 스택 도구를 다운로드합니다.
- 사용 하는 경우는 *호출 webrequest* Github에서 Azure 스택을 다운로드 하려면 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원의 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)으로 인해 발생합니다. 자세한 내용은 참조 [약한 암호화 표준 제거 통지](https://githubengineering.com/crypto-removal-notice/)합니다.

  이 문제를 해결 하려면 추가 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` TLSv1.2 GitHub 리포지토리에에서 다운로드할 때 사용할 PowerShell 콘솔을 강제 적용 하는 스크립트의 맨 위로 이동 합니다.




## <a name="build-201801032"></a>빌드 20180103.2

### <a name="new-features-and-fixes"></a>새 기능 및 수정

- 참조는 [새 기능 및 수정](.\.\azure-stack-update-1712.md#new-features-and-fixes) Azure 스택에 대 한 Azure 스택 1712 업데이트 릴리스 정보 섹션 시스템을 통합 합니다.

    > [!IMPORTANT]
    > 에 나열 된 항목 중 일부는 **새 기능 및 수정** 섹션 통합 Azure 스택 시스템에만 적용 됩니다.

### <a name="known-issues"></a>알려진 문제
 
#### <a name="deployment"></a>배포
- IP 주소를 통해 시간 서버를 배포 하는 동안 지정 해야 합니다.

#### <a name="infrastructure-management"></a>인프라 관리
- 인프라 백업에 사용 하지 마십시오는 **인프라 백업** 블레이드입니다.
- 베이스 보드 관리 컨트롤러 (BMC) IP 주소와 모델의 배율 단위 노드는 중요 한 정보에 표시 되지 않습니다. Azure 스택 개발 키트에이 동작이 필요 합니다.

#### <a name="portal"></a>포털
- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 톱니 바퀴형 아이콘을 선택 하 고 다음 선택 **기본 설정을 복원**합니다.
- 리소스 그룹의 속성을 볼 때의 **이동** 단추가 비활성화 됩니다. 이 동작은 사용할 수 있습니다. 구독 간 리소스 그룹 이동 현재 지원 되지 않습니다.
-  모든 워크플로에 드롭 다운 목록에서 구독, 리소스 그룹 또는 위치 선택 되는 위치에 대 한 다음 문제 중 하나 이상이 발생할 수 있습니다.

   - 목록 맨 위에 있는 빈 행을 볼 수 있습니다. 여전히 예상 대로 항목을 선택할 수 있습니다.
   - 드롭다운 목록에 있는 항목의 목록이 긴 경우 수 항목 이름 중 하나를 볼 수 없습니다.
   - 구독이 여러 개인 사용자, 리소스 그룹 드롭 다운 목록 비어 있을 수 있습니다. 

   마지막 두 개의 문제를 해결 하려면 구독 또는 리소스 그룹 (경우 것)의 이름을 입력할 수 있습니다 또는 PowerShell을 대신 사용할 수 있습니다.

- 표시 됩니다는 **활성화 필요** Azure 스택 개발 키트를 등록 해야 한다는 경고 성 알림입니다. 이 동작은 사용할 수 있습니다.
- 경우는 **구성 요소** 에서 링크를 클릭 하면 **인프라 역할** 경고 결과 **개요** 블레이드를 로드 하려고 하 고 실패 합니다. 또한는 * * 개요 * * 블레이드 시간이 제한 되지 않습니다.
- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.
- Azure 스택 포털을 사용 하 여 구독에 대 한 사용 권한을 볼 수 없는 합니다. 이 문제를 해결 PowerShell을 사용 하 여 사용 권한을 확인할 수 있습니다.
- **서비스 상태** 블레이드 로드 되지 않습니다. 관리자 또는 사용자 포털에서 Azure 스택 서비스 상태 블레이드를 열 때 오류가 표시 되 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 않습니다 되지만 Azure 스택의 이후 버전에서 구현 됩니다.
#### <a name="marketplace"></a>Marketplace
- 이 릴리스의 호환성 문제로 인해 일부 마켓플레이스 항목을 제거 되 고 됩니다. 이러한 다시 활성화 됩니다 추가 유효성 검사 후 합니다.
- 사용자 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.
 
#### <a name="compute"></a>컴퓨팅
- 사용자에는 지역 중복 저장소를 사용 하 여 가상 컴퓨터를 만들려면 옵션이 제공 됩니다. 이 구성을 사용 하면 가상 컴퓨터 만들기 실패 합니다. 
- 가상 컴퓨터 가용성,의 장애 도메인 및 하나의 업데이트 도메인만 집합을 구성할 수 있습니다.
- 가상 컴퓨터 크기 집합을 만들려는 마켓플레이스 본 경험이 없는 경우 크기는 템플릿을 사용 하 여 집합을 만들 수 있습니다.
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

#### <a name="networking"></a>네트워킹
- 포털을 사용 하 여 공용 IP 주소와 부하 분산 장치를 만들 수 없습니다. 이 문제를 해결를 부하 분산 장치를 만드는 PowerShell을 사용할 수 있습니다.
- 네트워크 부하 분산 장치를 만들 때 네트워크 주소 변환 (NAT) 규칙을 만들어야 합니다. 이렇게 하지 않으면 부하 분산 장치를 만들면 NAT 규칙을 추가 하려고 할 때 오류가 받게 합니다.
- 아래 **네트워킹**누르면, **연결** VPN 연결을 설정 하려면 **VNet 대 VNet** 가능한 연결 유형으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 현재만 **사이트 (IPsec)** 옵션은 지원 됩니다.
- VM이 되어 해당 IP 주소와 연결 된 후 가상 컴퓨터 (VM)에서 공용 IP 주소를 분리할 수 없습니다. Disassociation 작동으로 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다. 새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다. 현재, 새 VM 만들기에 대 한 새 공용 IP 주소를만 사용 해야 합니다.
- Azure 스택 운영자를 배포, 삭제, Vnet 또는 네트워크 보안 그룹을 수정 못할 수 있습니다. 이 문제는 동일한 패키지의 후속 업데이트 시도에 주로 나타납니다. 이 현재 조사 중인 상태인 업데이트와 패키징 문제로 인해 발생 합니다.
- 부하 분산 ILB (내부) 백 엔드 Vm Linux 인스턴스를 사용 하는 경우 백 엔드 네트워크 패킷을 삭제에 대 한 MAC 주소를 잘못 처리 합니다.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 테 넌 트가 새 SQL 또는 MySQL SKU에 데이터베이스를 만들 수는 1 시간까지 걸릴 수 있으므로 합니다. 
- SQL 및 MySQL 리소스 공급자가 수행 되지 않은 서버 호스팅에 직접 항목의 생성은 지원 되지 않으며 일치 하지 않는 상태가 될 수 있습니다.

#### <a name="app-service"></a>App Service
- 사용자는 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.
 
#### <a name="usage"></a>사용 현황  
- 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.

#### <a name="identity"></a>ID

배포 된 환경에서 Azure Active Directory 페더레이션 서비스 (ADFS)는 **azurestack\azurestackadmin** 계정이 기본 공급자 구독 소유자가 더 이상. 에 로그인 하는 대신는 **관리 포털 / adminmanagement 끝점** 와 **azurestack\azurestackadmin**를 사용할 수 있습니다는 **azurestack\cloudadmin** 하므로 계정 관리 하 고 기본 공급자 구독을 사용 하 여 수 있습니다.

> [!IMPORTANT]
> 도 **azurestack\cloudadmin** 계정이 ADFS 배포 된 환경에서 기본 공급자 구독 소유자가, RDP에 사용 권한을 호스트도가 없습니다. 계속 사용 하는 **azurestack\azurestackadmin** 계정이 나 로컬 관리자 계정 로그인, 액세스 및 필요에 따라 호스트를 관리 합니다.


