---
title: Azure 스택 1803 업데이트 | Microsoft Docs
description: Azure 스택 1803 업데이트에 포함 된 내용에 대 한 자세한 내용은 시스템, 알려진된 문제 및 업데이트를 다운로드 위치에 통합 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a74e77f84aa70519015a589cbc6e7478c0c41592
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318812"
---
# <a name="azure-stack-1803-update"></a>Azure 스택 1803 업데이트

*적용 대상: Azure 스택 시스템 통합*

이 문서는 향상 된 기능에 설명 하 고 알려진 문제가이 릴리스의 경우 및 업데이트를 다운로드 하는 위치에 대 한 1803 업데이트 패키지를 수정 합니다. 알려진된 문제는 직접 업데이트 프로세스와 관련 된 문제 및 문제 빌드와 (설치 후)으로 구분 됩니다.

> [!IMPORTANT]        
> 이 업데이트 패키지는 Azure 스택 통합 시스템에만. Azure 스택 개발 키트를이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조    
Azure 스택 1803 업데이트 빌드 번호는 **20180329.1**합니다.


## <a name="before-you-begin"></a>시작하기 전에    
> [!IMPORTANT]    
> 이 업데이트의 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md#plan-for-updates)합니다.


### <a name="prerequisites"></a>필수 조건
- Azure 스택 설치 [1802 업데이트](azure-stack-update-1802.md) Azure 스택 1803 업데이트를 적용 하기 전에.   

- 설치 **AzS 핫픽스 – 1.0.180312.1-빌드 20180222.2** Azure 스택 1803 업데이트를 적용 하기 전에. 이 핫픽스는 Windows Defender 업데이트 하 고 Azure 스택에 대 한 업데이트를 다운로드할 때 됩니다.

  에 대 한 일반적인 절차에 따라 핫픽스를 설치 하려면 [Azure 스택에 대 한 업데이트를 설치](azure-stack-apply-updates.md)합니다. 업데이트의 이름으로 표시 **AzS 핫픽스 – 1.0.180312.1**, 다음 파일을 포함 합니다. 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  이러한 파일을 저장소 계정 및 컨테이너에 업로드 한 후 관리 포털에서 업데이트 타일에서 설치를 실행 합니다. 
  
  Azure 스택 업데이트를와 달리이 업데이트를 설치 하는 Azure 스택의 버전을 변경 되지 않습니다. 이 업데이트가 설치를 확인 하려면 목록을 보려면 **설치 된 업데이트**합니다.



### <a name="new-features"></a>새로운 기능 
이 업데이트 Azure 스택에 대 한 다음과 같은 향상 된 기능 및 수정 프로그램을 포함합니다.

- **Azure 스택 암호 업데이트** -(계정 및 인증서). 암호를 관리 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택의 암호를 회전](azure-stack-rotate-secrets.md)합니다. 

- <!-- 1914853 --> **HTTPS로 자동 리디렉션** 관리자 및 사용자 포털에 액세스 HTTP를 사용 합니다. 이러한 향상에 따라 만들어진 [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) Azure 스택에 대 한 피드백 합니다. 

- <!-- 2202621  --> **마켓플레이스 액세스** – Azure 스택 Marketplace를 사용 하 여 이제 열 수 있습니다는 [+ 새로 만들기](https://ms.portal.azure.com/#create/hub) Azure 포털에서 작업을 수행 하는 동일한 방식으로 관리자 및 사용자 포털 내에서 옵션입니다.
 
- <!-- 2202621 --> **Azure 모니터** -Azure 스택에서 추가 [Azure 모니터](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) 관리자 및 사용자 포털에 있습니다. 메트릭 및 활동 로그에 대 한 새 탐색기 포함 됩니다. 이 Azure 모니터에서 외부 네트워크에 액세스 하려면 포트 **13012** 구성 방화벽에서에서 열려 있어야 합니다. Azure 스택에 필요한 포트에 대 한 자세한 내용은 참조 [스택 Azure 데이터 센터 통합-끝점 게시](azure-stack-integrate-endpoints.md)합니다.

   이 검토의 일환으로, 테이블 변경 아래 **더 많은 서비스**, *감사 로그* 으로 이제 나타납니다 *활동 로그*합니다. 기능은 이제 Azure 포털와 일치 합니다. 

- <!-- 1664791 --> **스파스 파일** -Azure 스택에 새 이미지를 추가 하거나 마켓플레이스 배포를 통해 이미지를 추가할 때 이미지가 스파스 파일에 변환 됩니다. Azure 스택 버전 1803 사용 하기 전에 추가 된 이미지를 변환할 수 없습니다. 대신,이 기능을 활용 하려면 해당 이미지를 다시 전송 해 마켓플레이스 배포를 사용 해야 합니다. 
 
   스파스 파일은 저장소 공간 사용을 줄이고 I/O를 향상 시키기 위해 하는 데 사용 하는 효율적인 파일 형식입니다.  자세한 내용은 참조 [Fsutil 스파스](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) Windows 서버에 대 한 합니다. 

### <a name="fixed-issues"></a>해결된 문제

- <!-- 1739988 --> 부하 분산 ILB (내부) 이제 제대로 처리 MAC 주소 백 엔드 Vm에 대 한 백 엔드 네트워크에 Linux 인스턴스를 사용 하는 경우 백 엔드 네트워크 패킷을 삭제 하는 ILB에 이르게 합니다. ILB 백 엔드 네트워크에 Windows 인스턴스와 제대로 작동합니다. 

- <!-- 1805496 --> Azure 보다 IKE 정책에 대 한 다른 설정을 사용 하 여 Azure 스택 인해 Azure 스택 간에 VPN 연결을 분리 되는 위치에 문제가 있습니다. SALifetime (시간) 및 SALiftetime (바이트)의 값을 Azure와 호환 였으며 Azure 설정에 맞게 1803에서 변경 되었습니다. 1803 이전의 SALifetime (초)에 대 한 값 1803 14400, 27,000 코드 변경 내용 이제 했습니다. 1803 전에 SALifetime (바이트)에 대 한 값이 819,200, 33,553,408 1803에서 코드 변경 내용입니다.

- <!-- 2209262 --> VPN 연결 포털;에서 이전에 볼 수 있었던 IP 문제 그러나 IP 전달 설정/해제 하거나 설정에 영향을 주지 않습니다. 기능 및 변경 아직 지원 하지 않는이 기능은 기본적으로 켜져 있습니다.  컨트롤 포털에서 제거 되었습니다. 

- <!-- 1766332 --> Azure 스택 옵션이 포털에 표시 되는 경우에 정책 기반 VPN 게이트웨이 지원 하지 않습니다.  포털에서는 옵션이 제거 되었습니다. 

- <!-- 1868283 --> Azure 스택 이제 동적 디스크를 사용 하 여 만든 가상 컴퓨터의 크기를 조정할 수 없습니다. 

- <!-- 1756324 --> 이제 가상 컴퓨터에 대 한 사용 현황 데이터는 시간별으로 분리 됩니다. 이 Azure와 일치 합니다. 

- <!--  2253274 --> 이 문제를 관리자 및 사용자 포털에서 vNet 서브넷에 대 한 설정 블레이드에서 로드 되지 않습니다. 이 문제를 해결 PowerShell을 사용 하 여 및 [Get AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet를 보고 하 여이 정보를 관리 합니다.

- 가상 컴퓨터, 메시지를 만들 때 *가격 책정을 표시할 수 없습니다* 더 이상 VM 크기에 대 한 크기를 선택할 때 나타납니다.

- 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한 다양 한 수정 합니다.


### <a name="changes"></a>변경 내용
- 새로 만든된 제품의 상태를 변경 하는 방법은 *개인* 를 *공용* 또는 *폐기 된* 변경 되었습니다. 자세한 내용은 참조 [제안을 만들](azure-stack-create-offer.md)합니다.


### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제    
<!-- 2328416 --> 1803 업데이트를 설치 하는 동안에 blob 서비스 및 blob 서비스를 사용 하는 내부 서비스의 가동 중지 시간이 있을 수 있습니다. 일부 가상 컴퓨터 작업이 포함 됩니다. 이 작동 중단 시간 수로 인해 실패할 테 넌 트의 작업 또는 경고 데이터를 액세스할 수 없는 서비스에서 합니다. 업데이트 설치를 완료 하는 경우이 문제는 자체 해결 합니다. 



### <a name="post-update-steps"></a>업데이트 후 단계
- 1803 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 다음 기술 자료 문서를 보려면 뿐 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다.

  - [KB 4341390-Azure 스택 핫픽스 1.0.180424.12](https://support.microsoft.com/en-us/help/4341390)합니다.

- 이 업데이트를 설치한 후 되도록 방화벽 구성을 검토 [필요한 포트가](azure-stack-integrate-endpoints.md) 열려 있습니다. 이 업데이트는 제공 하는 예를 들어 *Azure 모니터* 활동 로그에 감사 로그의 변경을 포함 합니다. 이러한 변경으로 인해 포트 13012는 이제 사용 및도 열려 있어야 합니다.  


### <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)
빌드에 대 한 설치 후 알려진된 문제는 다음과 같은 **20180323.2**합니다.

#### <a name="portal"></a>포털
- <!-- 2332636 - IS -->  Azure 스택 id 시스템 및이 버전의 Azure 스택을 업데이트에 대 한 AD FS를 사용 하는 경우 기본 공급자 구독의 기본 소유자는 기본 제공으로 다시 설정 **CloudAdmin** 사용자입니다.  
  해결 방법:이 업데이트를 설치한 후이 문제를 해결 하려면 사용에서 3 단계는 [트리거를 구성 하는 자동화 클레임 공급자 트러스트 Azure 스택의](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) 프로시저는 구독 소유자의 기본 공급자를 다시 설정 합니다.   

- 기능 [새 지원 요청을 드롭다운 목록에서 열려는](azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털 사용할 수 없습니다. 대신, 다음 링크를 사용 합니다.     
    - Azure 스택 통합된 시스템을 사용 하 여 https://aka.ms/newsupportrequest합니다.

- <!-- 2050709 --> 관리 포털에서 Blob 서비스, 테이블 서비스 또는 큐 서비스에 대 한 저장소 메트릭을 편집할 수 없으면입니다. 저장소를 이동 해야 하 고 다음 blob, 테이블 또는 큐 서비스 타일을 선택 하는 경우 해당 서비스에 대 한 메트릭 차트를 표시 하는 새 블레이드가 열립니다. 메트릭 차트 타일의 맨 위에서 다음 편집을 선택 하는 경우 차트 편집 블레이드 열리지만 메트릭을 편집 하는 옵션이 표시 되지 않습니다.

- 관리자 포털에서 계산 또는 저장소 리소스를 보려면 하지 못할 수도 있습니다. 이 문제의 원인은 잘못 보고 될 성공으로를 업데이트 하는 업데이트를 설치 하는 동안 오류입니다. 이 문제가 발생 한 경우 Microsoft 고객 지원 서비스에 문의 하십시오.

- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 톱니 바퀴형 아이콘을 선택 하 고 다음 선택 **기본 설정을 복원**합니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 업데이트 타일 업데이트에 대 한 정보를 표시 하는 실패 합니다. 이 문제를 해결 하려면 타일 새로 고침을 클릭 합니다.

- 관리 포털에 대 한 중요 한 알림이 표시 될 수도 있습니다는 *Microsoft.Update.Admin* 구성 요소입니다. 경고 이름, 설명 및으로 표시 하는 모든 업데이트 관리:  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

  이 경고는 무시 해도 됩니다. 


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고가 표시 될 수 있습니다는 *상태 컨트롤러* 다음 정보를 포함 하는 구성 요소:  

   #1 경고:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  경고 # 2:
   - 이름: 인프라 역할 비정상
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 시간에 따라 자동으로 종료 됩니다.  


#### <a name="marketplace"></a>Marketplace
- 사용자가 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 있으며, 계획 및 제안 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.



#### <a name="compute"></a>컴퓨팅
- 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- 가용성으로 이동 하 여 포털에서 집합을 만들 때 **새로** > **계산** > **가용성 집합**를 만들 수 있습니다는 가용성 장애 도메인과 업데이트 도메인 1으로 설정합니다. 해결 방법으로, 새 가상 컴퓨터를 만들 때, 가용성 집합 내에서 또는 PowerShell, CLI를 사용 하 여 만듭니다 포털입니다.

- Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

-  VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 --> Linux VM 진단 Azure 스택에서 지원 되지 않습니다. VM 진단을 사용 하는 Linux VM을 배포 하는 경우 배포에 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정 하는 경우에 배포 실패 합니다.  


#### <a name="networking"></a>네트워킹
- VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 연결 되어 있던 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.



- 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.

- Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.

- <!-- 2096388 --> 네트워크 보안 그룹에 대 한 규칙을 업데이트 하는 관리 포털을 사용할 수 없습니다. 

    응용 프로그램 서비스에 대 한 해결 방법: PowerShell 사용한 네트워크 보안 그룹 내에서 보안 규칙을 수정 컨트롤러 인스턴스를 원격 데스크톱을 해야 하는 경우.  다음은 하는 방법의 예 *허용*, 다음 구성을 사용 하 여 복원 *거부*:  
    
    - *허용:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *거부:*

        ```powershell
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```


#### <a name="sql-and-mysql"></a>SQL 및 MySQL
- 계속 하기 전에 중요 정보 검토 [시작 하기 전에](#before-you-begin) 앞부분의 이러한 릴리스 정보입니다.

- 사용자가 새 SQL 또는 MySQL 배포 데이터베이스를 만들 수 전에 최대 1 시간이 걸릴 수 있습니다.

- 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자가 생성 되지 않은 호스트 서버에 만든 항목 일치 하지 않는 상태가 될 수 있습니다.  

- <!-- IS, ASDK --> 특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다.

> [!NOTE]  
> Azure 스택 1803를 업데이트 한 후 이전에 배포 하는 SQL 및 MySQL 리소스 공급자를 사용 하도록 계속 수 있습니다.  새 릴리스는 사용할 수 있을 때 SQL 및 MySQL를 업데이트 하는 것이 좋습니다. Azure 스택과 마찬가지로 SQL 및 MySQL 리소스 공급자에 업데이트를 순서 대로 적용 합니다.  예를 들어 1711 버전을 사용 하는 경우 먼저 1712, 다음 1802, 버전을 적용 하 고 1803으로 업데이트 합니다.      
>   
> 1803 업데이트의 설치는 현재 사용자가 SQL 또는 MySQL 리소스 공급자를 사용 하는 영향을 주지 않습니다.
> 사용 리소스 공급자의 버전에 관계 없이 데이터베이스에 사용자가 데이터 연결, 아니며에 계속 액세스할 수 합니다.    



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


## <a name="download-the-update"></a>업데이트 다운로드
Azure 스택 1803 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.


## <a name="see-also"></a>참고 항목
- 권한 있는 끝점 (PEP)를 모니터링 하 고 업데이트를 다시 시작을 사용 하려면 참조 [업데이트 권한이 있는 끝점을 사용 하 여 Azure 스택의 모니터링](azure-stack-monitor-update.md)합니다.
- Azure 스택에서 업데이트 관리의 개요를 참조 하십시오. [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md)합니다.
- Azure 스택을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택에서 업데이트 적용](azure-stack-apply-updates.md)합니다.
