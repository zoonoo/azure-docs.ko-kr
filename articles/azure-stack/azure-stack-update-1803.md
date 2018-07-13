---
title: Azure Stack 1803 업데이트 | Microsoft Docs
description: Azure Stack에 대 한 1803 업데이트의 새로운 기능 알아보기 시스템의 알려진된 문제 및 업데이트를 다운로드 하는 위치를 통합 합니다.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989574"
---
# <a name="azure-stack-1803-update"></a>Azure Stack 1803 업데이트

*적용 대상: Azure Stack 통합 시스템*

이 문서는 향상 된 기능에 설명 하 고이 릴리스 및 업데이트를 다운로드 하는 위치에 대 한 문제를 알려진 1803 업데이트 패키지의 수정. 알려진된 문제는 업데이트 프로세스에 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]        
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조    
Azure Stack 1803 업데이트 빌드 번호는 **20180329.1**합니다.


## <a name="before-you-begin"></a>시작하기 전에    
> [!IMPORTANT]    
> 이 업데이트를 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트를 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md#plan-for-updates)합니다.


### <a name="prerequisites"></a>필수 조건
- Azure Stack을 설치 [1802 업데이트](azure-stack-update-1802.md) Azure Stack 1803 업데이트를 적용 하기 전에 합니다.   

- 설치할 **AzS 핫픽스-빌드 20180222.2 1.0.180312.1-** Azure Stack 1803 업데이트를 적용 하기 전에 합니다. 이 핫픽스는 Windows Defender를 업데이트 하 고 Azure Stack에 대 한 업데이트를 다운로드할 때 사용할 수 있습니다.

  핫픽스를 설치 하려면 표준 절차를 따를 [Azure Stack에 대 한 업데이트를 설치](azure-stack-apply-updates.md)합니다. 업데이트의 이름으로 나타납니다 **AzS 핫픽스 – 1.0.180312.1**, 다음 파일 포함: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  이러한 파일을 저장소 계정 및 컨테이너에 업로드 한 후 관리 포털에서 타일 업데이트에서에서 설치를 실행 합니다. 
  
  Azure Stack에 대 한 업데이트를 달리이 업데이트 설치는 Azure Stack의 버전을 변경 되지 않습니다. 이 업데이트를 설치를 확인 하려면 목록을 보려면 **설치 된 업데이트**합니다.



### <a name="new-features"></a>새로운 기능 
이 업데이트는 Azure Stack에 대 한 다음과 같은 향상 된 기능 및 수정을 포함 합니다.

- **Azure Stack 암호 업데이트** -(계정 및 인증서). 암호를 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack에서 암호를 회전](azure-stack-rotate-secrets.md)합니다. 

- <!-- 1914853 --> **HTTPS로 자동 리디렉션** 관리자 및 사용자 포털에 액세스 HTTP를 사용 하면 됩니다. 이러한 향상 된이 기능에 따라 만들어진 [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) Azure Stack에 대 한 피드백. 

- <!-- 2202621  --> **Marketplace에 액세스할** -이제 Azure Stack Marketplace를 사용 하 여 열면 합니다 [+ 새로 만들기](https://ms.portal.azure.com/#create/hub) Azure 포털에서 수행한 동일한 방식으로 관리 하 고 사용자 포털 내에서 옵션입니다.
 
- <!-- 2202621 --> **Azure Monitor** -추가 하는 Azure Stack [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) 관리자 및 사용자 포털에 있습니다. 메트릭 및 활동 로그에 대 한 새 탐색기 포함 됩니다. 이 Azure Monitor에서 외부 네트워크에 액세스 하려면 포트 **13012** 방화벽 구성에서 열려 있어야 합니다. Azure Stack에 필요한 포트에 대 한 자세한 내용은 참조 하세요. [Azure Stack 데이터 센터 통합-끝점을 게시](azure-stack-integrate-endpoints.md)합니다.

   또한이 단계의 일부로 변경, 아래 **더 많은 서비스**, *감사 로그* 로 나타납니다 *활동 로그*합니다. 기능은 이제 Azure portal을 사용 하 여 일치 합니다. 

- <!-- 1664791 --> **스파스 파일** -Azure Stack에 새 이미지를 추가 하거나 마켓플레이스 배포를 통해 이미지를 추가할 때 이미지 스파스 파일로 변환 됩니다. Azure Stack 버전 1803 사용 하기 전에 추가 된 이미지를 변환할 수 없습니다. 대신,이 기능을 활용 하려면 이러한 이미지를 다시 제출 하려면 마켓플레이스 배포를 사용 해야 합니다. 
 
   스파스 파일은 저장소 공간 사용을 줄이고 I/O를 향상 시키기 위해 사용 되는 효율적인 파일 형식입니다.  자세한 내용은 [Fsutil 스파스](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) Windows Server에 대 한 합니다. 

### <a name="fixed-issues"></a>해결된 문제

- <!-- 1739988 --> 내부 부하 분산 (ILB) 이제 제대로 처리 MAC 주소 백 엔드 Vm에 대 한 백 엔드 네트워크에 Linux 인스턴스를 사용 하는 경우 백 엔드 네트워크 패킷을 삭제 하는 ILB에 이르게 합니다. ILB 백 엔드 네트워크에 Windows 인스턴스를 사용 하 여 제대로 작동합니다. 

- <!-- 1805496 --> Azure 보다 IKE 정책에 대 한 다른 설정을 사용 하 여 Azure Stack으로 인해 Azure Stack 간에 VPN 연결은 끊어질에 문제가 있습니다. SALifetime (시간) 및 SALiftetime (바이트)에 대 한 값을 Azure와 호환 했으며 Azure 설정과 일치 하도록 1803에서 변경 되었습니다. 1803 이전의 SALifetime (초)에 대 한 값 1803 14,400 및 27,000 이제 변경 했습니다. 1803 전에 SALifetime (바이트)에 대 한 값이 819,200 1803의 33,553,408 변경 합니다.

- <!-- 2209262 --> VPN 연결 포털;에서 이전에 표시 된 위치 IP 문제 그러나 IP 전달 설정/해제 하거나 사용에 영향을 주지 않습니다. 기능이 아직 지원 되지 않음이 변경 하는 기능 및 기본적으로 켜져 있습니다.  포털에서 컨트롤이 제거 되었습니다. 

- <!-- 1766332 --> Azure Stack 포털에는 옵션이 표시 되지만 정책 기반 VPN Gateway를 지원 하지 않습니다.  옵션을 포털에서 제거 되었습니다. 

- <!-- 1868283 --> Azure Stack에 이제 동적 디스크를 사용 하 여 만든 가상 머신의 크기를 조정 하지 않습니다. 

- <!-- 1756324 --> 이제 가상 머신에 대 한 사용 현황 데이터는 시간 간격 분리 됩니다. 이 Azure와 일치 합니다. 

- <!--  2253274 --> 문제가 있는 관리자 및 사용자 포털에서 vNet 서브넷에 대 한 설정 블레이드에서 로드 하지 못했습니다. 해결 방법으로 PowerShell을 사용 하며 [Get-azurermvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) 보고이 정보를 관리 하는 cmdlet입니다.

- 메시지를 가상 컴퓨터를 만들면 *가격 책정을 표시할 수 없습니다* 더 이상 VM 크기에 대 한 크기를 선택할 때 표시 됩니다.

- 성능, 안정성, 보안 및 Azure Stack에서 사용 되는 운영 체제에 대 한 다양 한 수정 합니다.


### <a name="changes"></a>변경 내용
- 새로 만든된 제품의 상태를 변경 하는 방법은 *사설* 하 *공용* 또는 *해제* 변경 되었습니다. 자세한 내용은 [제품을 만드는](azure-stack-create-offer.md)합니다.


### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제    
<!-- 2328416 --> 1803 업데이트를 설치 하는 동안에 blob service 및 blob service를 사용 하는 내부 서비스의 가동 중지 시간이 있을 수 있습니다. 이 일부 가상 머신 작업이 포함 됩니다. 가동 중지 시간이이 실패할 수 있습니다 테 넌 트의 작업 또는 경고 데이터를 액세스할 수 없는 서비스에서입니다. 업데이트 설치가 완료 되 면이 문제는 자체 확인 합니다. 



### <a name="post-update-steps"></a>업데이트 후 단계
- 1803의 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 다음 기술 자료 문서를 볼 뿐만 아니라 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다.

  - [KB 4344115-Azure Stack 핫픽스 1.0.180427.15](https://support.microsoft.com/help/4344115)합니다.

- 이 업데이트를 설치한 후 구성을 검토 하 고 방화벽 되도록 [필요한 포트가](azure-stack-integrate-endpoints.md) 열려 있습니다. 예를 들어,이 업데이트에 도입 *Azure Monitor* 활동 로그로 감사 로그의 변경을 포함 합니다. 이 변경으로 13012 포트는 이제 사용 및도 오픈 되어야 합니다.  


### <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)
다음은 빌드에 대 한 설치 후 알려진된 문제 **20180323.2**합니다.

#### <a name="portal"></a>포털
- <!-- 2332636 - IS -->  AD FS를 사용 하 여 Azure Stack의이 버전으로 업데이트 확인 하 고 Azure Stack id 시스템에 대 한 기본 제공 기본 공급자 구독의 기본 소유자 재설정 됩니다 **CloudAdmin** 사용자입니다.  
  해결 방법:이 업데이트를 설치한 후이 문제를 해결 하려면 사용에서 3 단계는 [트리거를 구성 하는 자동화 클레임 공급자 트러스트 Azure Stack에서](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) 기본 공급자 구독 소유자 다시 설정 하는 절차입니다.   

- 기능 [드롭다운 목록에서 새 지원 요청을 열려면](azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 내에서 포털을 사용할 수 없습니다. 다음 링크를 대신 사용 합니다.     
    - Azure Stack 통합된 시스템을 사용 하 여 https://aka.ms/newsupportrequest입니다.

- <!-- 2050709 --> 관리 포털에서 Blob service, Table service 또는 Queue 서비스에 대 한 저장소 메트릭을 편집할 수 없는 합니다. 저장소로 이동 하 고 blob, 테이블 또는 큐 서비스 타일을 선택 하는 경우 해당 서비스에 대 한 메트릭 차트를 표시 하는 새 블레이드가 열립니다. 메트릭 차트 타일의 맨 위에서 다음 편집을 선택 하는 경우 차트 편집 블레이드의 열리지만 메트릭을 편집 하는 옵션을 표시 하지 않습니다.

- 관리자 포털에서 계산 또는 저장소 리소스를 보려면 하지 못할 수도 있습니다. 이 문제의 원인은 잘못 보고 성공으로 업데이트 하면 업데이트를 설치 하는 동안 오류입니다. 이 문제가 발생 하는 경우 Microsoft 고객 지원 서비스에 문의 하십시오.

- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 기어 아이콘을 선택 하 고 선택한 **기본 설정 복원**합니다.

- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.

- 관리 포털의 대시보드에서 타일 업데이트는 업데이트에 대 한 정보를 표시 하려면 실패 합니다. 이 문제를 해결 하려면 타일을 새로 고치려면 클릭 합니다.

- 관리 포털에 대 한 중요 한 경고가 표시 될 수도 있습니다는 *Microsoft.Update.Admin* 구성 요소입니다. 경고 이름, 설명 및 수정으로 모두 표시 합니다.  
    - *오류-FaultType ResourceProviderTimeout에 대 한 템플릿이 누락 되었습니다.*

  이 경고는 무시 해도 됩니다. 


#### <a name="health-and-monitoring"></a>상태 및 모니터링
- <!-- 1264761 - IS ASDK -->  에 대 한 경고를 표시 될 수 있습니다 합니다 *상태 컨트롤러* 다음 세부 정보는 구성 요소:  

   경고 # 1:
   - 비정상 인프라 역할 이름:
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 하트 비트 검색 프로그램 사용할 수 없는 경우 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.  

  # 2를 경고 합니다.
   - 비정상 인프라 역할 이름:
   - 심각도: 경고
   - 구성 요소: 상태 컨트롤러
   - 설명: 상태 컨트롤러 오류 스캐너를 사용할 수 없습니다. 상태 보고서 및 메트릭에 영향을 줄 수 있습니다.

  두 가지 경고는 무시 해도 됩니다. 이러한 작업은 시간이 지남에 따라 자동으로 종료 됩니다.  


#### <a name="marketplace"></a>Marketplace
- 사용자 구독 없이 전체 마켓플레이스를 찾아볼 수 및 계획 및 제품은 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.



#### <a name="compute"></a>컴퓨팅
- 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- 가용성으로 이동 하 여 포털에서 집합을 만들면 **새로 만들기** > **계산** > **가용성 집합**를 만들 수 있습니다는 장애 도메인 및 업데이트 도메인 1을 사용 하 여 가용성 집합. 해결 방법으로, 새 가상 머신을 만들 때, 가용성 집합 내에서 또는 PowerShell, CLI를 사용 하 여 만들기 포털입니다.

- Azure Stack 사용자 포털에서 가상 컴퓨터를 만들 때 포털 잘못 된 D 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. 모든 지원 되는 D 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- VM 이미지를 만들려는 못하면 VM 이미지 계산 블레이드로 삭제할 수 없습니다는 실패 한 항목을 추가할 수 있습니다.

  해결 방법으로 Hyper-v를 통해 만들 수 있는 더미 VHD를 사용 하 여 새 VM 이미지 만들기 (새 VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하는 것을 금지 하는 문제를 수정 해야 합니다. 그런 다음 더미 이미지를 만든 후 15 분 삭제할 수 있습니다 했습니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드를 시도할 수 있습니다.

-  VM 배포에 확장을 프로 비전 너무 오래 걸리는 경우 사용자는 할당 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 대신 프로 비전 제한을 하도록 해야 합니다.  

- <!-- 1662991 --> Azure Stack의 Linux VM 진단을 지원 되지 않습니다. 를 사용 하도록 설정 하는 VM 진단을 사용 하 여 Linux VM을 배포 하는 경우 배포가 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정한 경우에 배포가 실패 합니다.  


#### <a name="networking"></a>네트워킹
- VM을 만들고 공용 IP 주소와 연결 된 후 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 연결 해제 하려면 나타나지만 이전에 할당된 된 공용 IP 주소 유지 원래 VM과 연결 됩니다.

  현재, 만든 새 Vm에 대 한 새 공용 IP 주소만 사용 해야 합니다.

  Azure Stack의 업데이트 관리 개요를 참조 하세요 *Azure Stack 개요에 대 한 업데이트 관리*합니다. Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. Azure Stack의 업데이트 적용합니다.



- Azure Stack 지원 단일 *로컬 네트워크 게이트웨이* IP 주소당 합니다. 모든 테 넌 트 구독에서 그렇습니다. 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만드는 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려는 시도가 차단 됩니다.

- DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*을 사용자 지정 DNS 서버 실패를 변경 합니다. 업데이트 된 설정은 해당 Vnet의 Vm에 푸시되 지 않습니다.

- Azure Stack VM을 배포한 후 추가 네트워크 인터페이스 VM 인스턴스를 추가 하는 것을 지원 하지 않습니다. VM이 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.

- <!-- 2096388 --> 네트워크 보안 그룹 규칙을 업데이트 하는 관리 포털을 사용할 수 없습니다. 

    App Service에 대 한 해결 방법: PowerShell 사용 하 여 네트워크 보안 그룹 내에서 보안 규칙을 수정할 컨트롤러 인스턴스에 원격 데스크톱 연결 하는 경우.  다음은 하는 방법의 예 *허용*, 다음 구성을 사용 하 여 복원 *거부*:  
    
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
- 계속 하기 전에 중요 정보를 검토 [시작 하기 전에](#before-you-begin) 앞부분의 이러한 릴리스 정보입니다.

- 사용자가 새 SQL 또는 MySQL 배포 데이터베이스를 만들 수 전에 최대 1 시간이 걸릴 수 있습니다.

- 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자에 의해 생성 되지 않은 호스트 서버에서 생성 하는 항목 일치 하지 않는 상태가 될 수 있습니다.  

- <!-- IS, ASDK --> 공간 및 마침표를 포함 하 여 특수 문자를 사용할 수 없습니다는 **제품군** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름입니다.

> [!NOTE]  
> Azure Stack 1803를 업데이트 한 후에 이전에 배포한 SQL 및 MySQL 리소스 공급자를 사용 하도록 계속 수 있습니다.  새 릴리스를 사용할 수 있을 때 SQL 및 MySQL를 업데이트 하는 것이 좋습니다. 예: Azure Stack에 SQL 및 MySQL 리소스 공급자 업데이트를 순차적으로 적용 됩니다.  예를 들어 버전 1711을 사용 하는 경우 먼저 1712, 다음 1802 버전을 적용 하 고 1803으로 업데이트 합니다.      
>   
> 1803 업데이트의 설치를 현재 사용자가 SQL 또는 MySQL 리소스 공급자 사용에 영향을 주지 않습니다.
> 사용할 리소스 공급자의 버전에 관계 없이 해당 데이터베이스의 사용자 데이터는 그대로 유지, 및 계속 액세스할 수 있습니다.    



#### <a name="app-service"></a>App Service
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.


#### <a name="usage"></a>사용 현황  
- 사용량 공용 IP 주소 사용 측정기 데이터를 보여 줍니다 동일 *EventDateTime* 대신 각 레코드에 대 한 값을 *TimeDate* 레코드를 만든 경우를 보여 주는 스탬프. 현재 공용 IP 주소 사용을 정확 하 게 설명 하는 데이 데이터를 사용할 수 없습니다.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub에서 Azure Stack 도구를 다운로드합니다.
- 사용 하는 경우는 *호출할 webrequest* Github에서 Azure Stack을 다운로드 하기 위한 PowerShell cmdlet 도구, 오류가 발생 합니다.     
    -  *호출 webrequest: 요청이 중단 되었습니다: SSL/TLS 보안 채널을 만들 수 없습니다.*     

  이 오류는 최근 GitHub 지원 사용 중단 Tlsv1 및 Tlsv1.1 암호화 표준 (PowerShell에 대 한 기본값)로 인해 발생합니다. 자세한 내용은 [취약 한 암호화 표준 제거 공지](https://githubengineering.com/crypto-removal-notice/)합니다.

  이 문제를 해결 하려면 추가 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` GitHub 리포지토리에서 다운로드 시 TLSv1.2를 사용 하도록 PowerShell 콘솔을 강제 적용 하는 스크립트의 맨 위로 이동 합니다.


## <a name="download-the-update"></a>업데이트 다운로드
Azure Stack 1803 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.


## <a name="see-also"></a>참고 항목
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.
- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.
