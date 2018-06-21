---
title: Azure 스택 1805 업데이트 | Microsoft Docs
description: 알려진된 문제를 포함 하 여 Azure 스택 통합 시스템용 1805 업데이트의 새로운 란 무엇이 고 업데이트를 다운로드 하는 위치에 알아봅니다.
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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 80ed0d2353fc6ea3a515c0d05475c713920abe46
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295746"
---
# <a name="azure-stack-1805-update"></a>Azure 스택 1805 업데이트

*적용 대상: Azure 스택 시스템 통합*

이 문서는 향상 된 기능에 설명 하 고 알려진 문제가이 버전 및 업데이트를 다운로드 하는 위치에 대 한 1805 업데이트 패키지를 수정 합니다. 알려진된 문제는 직접 업데이트 프로세스와 관련 된 문제 및 문제 빌드와 (설치 후)으로 구분 됩니다.

> [!IMPORTANT]        
> 이 업데이트 패키지는 Azure 스택 통합 시스템에만. Azure 스택 개발 키트를이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조    
Azure 스택 1805 업데이트 빌드 번호는 **1.1805.1.47**합니다.  

> [!TIP]  
> Microsoft Azure 스택에 사용 중인 버전 스키마에는 업데이트는 고객의 의견에 따라.  새 스키마 1805,이 업데이트를 시작 합니다. 현재 클라우드 버전을 나타내는 더 잘 합니다.  
> 
> 버전 스키마는 이제 *Version.YearYearMonthMonth.MinorVersion.BuildNumber* 두 번째 및 세 번째 집합의 버전 및 릴리스 여기서 나타냅니다. 예를 들어 1805.1 나타냅니다는 *제조 하도록 릴리스* 1805 (RTM) 버전입니다.  


### <a name="new-features"></a>새로운 기능
이 업데이트에는 Azure 스택에 대 한 다음과 같은 개선 사항이 포함 됩니다.
<!-- 2297790 - IS, ASDK --> 
- **Azure 스택에 포함 되어 이제는 *Syslog* 클라이언트** 로 *미리 보기 기능*합니다. 이 클라이언트를 Syslog 서버 또는 보안 정보 및 이벤트 (SIEM) 관리 소프트웨어 외부 Azure 스택에 있는 Azure 스택 인프라와 관련 된 감사 및 보안 로그를 전달할 수 있습니다. 현재 Syslog 클라이언트는만 514 기본 포트를 통해 인증 되지 않은 UDP 연결을 지원합니다. 각 Syslog 메시지의 페이로드에 공통 이벤트 형식 (CEF) 지정 됩니다. 

  Syslog 클라이언트를 구성 하려면 사용 하 여는 **집합 SyslogServer** cmdlet은 권한 있는 끝점에 노출 합니다. 

  이 미리 보기에서는 다음 세 가지 경고를 볼 수 있습니다. 이러한 경고를 포함 하는 Azure 스택에서 나타나면 *설명* 및 *재구성* 지침. 
  - 해제 코드 무결성 제목:  
  - 감사 모드에서 코드 무결성 제목: 
  - 제목: 사용자 계정 생성

  이 기능은 미리 보기 상태인 동안 것 신뢰 해서는 시 프로덕션 환경에서 합니다.   




### <a name="fixed-issues"></a>해결된 문제

<!-- # - applicability -->
- 차단 문제 해결 [드롭다운 목록에서 새 지원 요청을 열](azure-stack-manage-portals.md#quick-access-to-help-and-support) 에서 관리자 포털 내에서. 이 옵션을 지금 의도 한 대로 작동 합니다. 

- **다양 한 수정 프로그램이** 성능, 안정성, 보안 및 Azure 스택에서 사용 되는 운영 체제에 대 한 합니다.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>시작하기 전에    

### <a name="prerequisites"></a>필수 조건
- Azure 스택 설치 [1804 업데이트](azure-stack-update-1804.md) Azure 스택 1805 업데이트를 적용 하기 전에.    
- 1805 업데이트의 설치를 시작 하기 전에 실행 [테스트 AzureStack](azure-stack-diagnostic-test.md) 발견 된 모든 작동 문제를 해결 하 고 Azure 스택의 상태를 확인할 수 있습니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결 합니다. 

### <a name="known-issues-with-the-update-process"></a>업데이트 프로세스의 알려진된 문제   
- 경고 제목 1805 업데이트를 설치 하는 동안 표시 될 수 있습니다 *오류 – FaultType UserAccounts.New에 대 한 템플릿이 누락 되었습니다.*  이러한 경고를 안전 하 게 무시할 수 있습니다. 이러한 경고 1805 업데이트가 완료 된 후 자동으로 닫힙니다.   

- <!-- 2489559 - IS --> 이 업데이트의 설치 하는 동안 가상 컴퓨터를 만들 하려고 하지 마십시오. 업데이트, seSe 관리 하는 방법에 대 한 자세한 내용은 [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md#plan-for-updates)합니다.


### <a name="post-update-steps"></a>업데이트 후 단계
1805 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 다음 기술 자료 문서를 보려면 뿐 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다.  
 - [KB 4340474-Azure 스택 핫픽스 1.1805.4.53](https://support.microsoft.com/en-us/help/4340474)합니다.


## <a name="known-issues-post-installation"></a>알려진된 문제 (설치 후)
이 빌드 버전에 대 한 설치 후 알려진된 문제는 다음과 같습니다.

### <a name="portal"></a>포털  
- <!-- 2551834 - IS, ASDK --> 선택 하는 경우 **개요** 관리자 또는 사용자의 정보를 포털의 저장소 계정에 대 한는 *Essentials* 창 표시 되지 않습니다.  Essentials 창 같은 계정에 대 한 정보가 표시 됩니다는 *리소스 그룹*, *위치*, 및 *구독 ID*합니다.  다른 옵션에 대 한 개요와 같은 액세스할 수 있는 *서비스* 및 *모니터링*에 옵션으로 *탐색기에에서 열려 있는* 또는 *저장소 계정 삭제* . 

  사용할 수 없는 정보를 보려면 사용 하 여는 [Get azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell cmdlet. 

- <!-- 2551834 - IS, ASDK --> 선택 하는 경우 **태그** 관리자 또는 사용자 포털의 저장소 계정에 대 한 정보를 로드 실패 하 고 표시 되지 않습니다.  

  사용할 수 없는 정보를 보려면 사용 하 여는 [Get AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell cmdlet.


- <!-- 2332636 - IS -->  Azure 스택 id 시스템 및이 버전의 Azure 스택을 업데이트에 대 한 AD FS를 사용 하는 경우 기본 공급자 구독의 기본 소유자는 기본 제공으로 다시 설정 **CloudAdmin** 사용자입니다.  
  해결 방법:이 업데이트를 설치한 후이 문제를 해결 하려면 사용에서 3 단계는 [트리거를 구성 하는 자동화 클레임 공급자 트러스트 Azure 스택의](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) 프로시저는 구독 소유자의 기본 공급자를 다시 설정 합니다.   

- <!-- TBD - IS ASDK --> 일부 관리 구독 유형은 사용할 수 있습니다.  Azure 스택이이 버전으로 업그레이드 하면 두 가지 구독 유형을 했던 [1804 버전에 도입](azure-stack-update-1804.md#new-features) 콘솔에 표시 되지 않습니다. 예상된 동작입니다. 사용할 수 없는 구독 유형은 *구독 계량*, 및 *소비 구독*합니다. 이 구독 유형은 1804 버전부터 새로운 Azure 스택 환경에 표시 됩니다 되지만 아직 사용할 준비가 되지 않습니다. 계속 사용 해야는 *기본 공급자* 구독 유형입니다.  

- <!-- 2403291 - IS ASDK --> 관리자 및 사용자 포털의 아래쪽에 가로 스크롤 막대를 사용 하 여가 없을 수도 있습니다. 포털의 상단에 있는 이동 경로 목록에서 표시할 블레이드 이름을 선택 하 여 포털에서 이전 블레이드로 이동 하려면 이동 경로 사용 하 여 남아 있는 가로 스크롤 막대에 액세스할 수 없는 경우.
  ![이동 경로 탐색](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> 관리자 포털에서 계산 또는 저장소 리소스를 보려면 하지 못할 수도 있습니다. 이 문제의 원인은 잘못 보고 될 성공으로를 업데이트 하는 업데이트를 설치 하는 동안 오류입니다. 이 문제가 발생 한 경우 Microsoft 고객 지원 서비스에 문의 하십시오.

- <!-- TBD - IS --> 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 톱니 바퀴형 아이콘을 선택 하 고 다음 선택 **기본 설정을 복원**합니다.

- <!-- TBD - IS ASDK --> 분리 된 리소스에서 사용자 구독 결과 삭제 합니다. 이 문제를 해결 먼저 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

- <!-- TBD - IS ASDK --> Azure 스택 포털을 사용 하 여 구독에 사용 권한을 볼 수 없습니다. 한 대 안으로 사용 권한을 확인 하려면 PowerShell을 사용 합니다.


### <a name="health-and-monitoring"></a>상태 및 모니터링
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

  두 가지 경고를 안전 하 게 무시할 수 및 시간에 따라 자동으로 닫히지 합니다.  

- <!-- 2368581 - IS. ASDK --> 메모리 부족 경고를 표시 하 고 테 넌 트 가상 컴퓨터와 함께 배포할 실패 하는 경우에 Azure 스택 연산자는 *패브릭 VM 만들기 오류*, Azure 스택 스탬프 사용 가능한 메모리가 부족 수도 있습니다. 사용 하 여는 [Azure 스택 Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 이해 하기 위해서는 작업에 대 한 사용 가능한 용량입니다. 


### <a name="compute"></a>컴퓨팅
- <!-- TBD - IS, ASDK --> 일부 F 시리즈 VM 크기는 가상 컴퓨터 배포에 대 한 가상 컴퓨터 크기를 선택할 때는 표시 되지 않습니다는 VM을 만들 때 크기 선택기의 일환으로 합니다. 다음 VM 크기 선택기에 표시 되지 않습니다: *F8s_v2*, *F16s_v2*, *F32s_v2*, 및 *F64s_v2*합니다.  
  문제를 해결 VM을 배포 하는 다음 방법 중 하나를 사용 합니다. 각 방법에 사용할 VM 크기를 지정 해야 합니다.

  - **Azure 리소스 관리자 템플릿:** 서식 파일을 사용 하면 설정는 *vmSize* 템플릿의 사용 하려는 VM 크기와 같아야 합니다. 사용 하는 VM을 배포 하려면 다음 항목이 사용 되는 예를 들어는 *F32s_v2* 크기:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** 사용할 수 있습니다는 [az vm 만들기](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 명령 및 VM 크기 비슷합니다 매개 변수로 지정 `--size "Standard_F32s_v2"`합니다.

  - **PowerShell:** Powershell을 사용할 수 있습니다 [새로 AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 비슷합니다 v M 크기를 지정 하는 매개 변수와 함께 `-VMSize "Standard_F32s_v2"`합니다.


- <!-- TBD - IS ASDK --> 크기 조정 설정을 가상 컴퓨터 크기 집합에 대 한 포털에서 사용할 수 없는 경우 한 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이 때문에 사용 해야 합니다는 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.

- <!-- TBD - IS --> 가용성으로 이동 하 여 포털에서 집합을 만들 때 **새로** > **계산** > **가용성 집합**를 만들 수 있습니다는 가용성 장애 도메인과 업데이트 도메인 1으로 설정합니다. 해결 방법으로, 새 가상 컴퓨터를 만들 때, 가용성 집합 내에서 또는 PowerShell, CLI를 사용 하 여 만듭니다 포털입니다.

- <!-- TBD - IS ASDK --> Azure 스택 사용자 포털에 가상 컴퓨터를 만들 때 포털 잘못 된 DS 시리즈 VM에 연결할 수 있는 데이터 디스크 수를 표시 합니다. DS 시리즈 Vm은 Azure 구성으로 많은 데이터 디스크를 수용할 수 있습니다.

- <!-- TBD - IS ASDK --> VM 이미지를 만들 실패 하면 VM 이미지 계산 블레이드로 삭제할 수 없는 실패 한 항목을 추가할 수 있습니다.

  이 문제를 해결 Hyper-v를 통해 만들 수 있는 dummy VHD로 새 VM 이미지 만들기 (NEW-VHD-경로 C:\dummy.vhd--SizeBytes 고정 1GB). 이 프로세스는 실패 한 항목을 삭제 하지 못하도록 보호 하는 문제를 수정 해야 합니다. 더미 이미지를 만든 후 15 분 수 성공적으로 삭제 합니다.

  그런 다음 이전에 실패 한 VM 이미지를 다시 다운로드 하려고 수 있습니다.

- <!-- TBD - IS ASDK --> VM 배포에 확장을 프로 비전 하지 않는 것이 걸리면, 사용자가 할당을 취소 하거나 VM을 삭제 하는 프로세스를 중지 하는 것이 아니라 프로 비전 제한 시간을 알려야 합니다.  

- <!-- 1662991 IS ASDK --> Linux VM 진단 Azure 스택에서 지원 되지 않습니다. VM 진단을 사용 하는 Linux VM을 배포 하는 경우 배포에 실패 합니다. 진단 설정을 통해 Linux VM 기본 메트릭을 사용 하도록 설정 하는 경우에 배포 실패 합니다.  


### <a name="networking"></a>네트워킹
- <!-- TBD - IS ASDK --> 관리자 또는 사용자 포털 중 하나에서 사용자 정의 경로 만들 수입니다. 사용 하 여 문제를 해결 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell)합니다.

- <!-- 1766332 - IS ASDK --> 아래 **네트워킹**누르면, **VPN 게이트웨이 만들기** VPN 연결을 설정 하려면 **정책 기반** VPN 형식으로 나열 됩니다. 이 옵션을 선택 하지 마십시오. 만 **경로 기반** 옵션은 Azure 스택에서 지원 합니다.

- <!-- 2388980 - IS ASDK --> VM을 만들고 공용 IP 주소와 연결 된 후에 해당 IP 주소에서 해당 VM을 분리할 수 없습니다. 작동 하도록 하는 연결 해제 나타나지만 이전에 할당 된 공용 IP 주소에 연결 되어 있는 원본 VM입니다.

  현재 만들면 새 Vm에 대 한 새 공용 IP 주소에만 사용 해야 합니다.

  새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고는 *VIP 교체*). 앞으로의 모든 새 아니라 원래 VM에 대 한 연결에서이 IP 주소 결과 통해 연결을 시도 합니다.

- <!-- 2292271 - IS ASDK --> 제안 및 연결 된 테 넌 트 구독 계획의 일부인 네트워크 리소스에 대 한 할당량 한도 늘릴 경우 해당 구독에 새 제한이 적용 되지 않습니다. 그러나 새 한도 할당량을 늘릴 후 생성 된 새 구독에 적용지 않습니다.

  이 문제를 해결 하려면 계획 이미 구독과 연결 된 경우 네트워크 할당량을 늘려야 하는 추가 기능 계획을 사용 합니다. 자세한 내용은 참조 하는 방법 [플랜을 통해 추가 기능을 사용할 수 있도록](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)합니다.

- <!-- 2304134 IS ASDK --> DNS 영역 리소스 또는 연결 된 경로 테이블 리소스가 있는 구독을 삭제할 수 없습니다. 구독을 삭제 하려면 먼저 테 넌 트 구독에서 DNS 영역 및 경로 테이블 리소스를 삭제 해야 합니다.


- <!-- 1902460 - IS ASDK --> 스택에서 단일 azure *로컬 네트워크 게이트웨이* 당 IP 주소입니다. 테 넌 트 구독 전체에서 그렇습니다. 동일한 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이 리소스를 만들려면 첫 번째 로컬 네트워크 게이트웨이 연결의 후속 만들기 시도가 차단 됩니다.

- <!-- 16309153 - IS ASDK --> DNS 서버 설정을 사용 하 여 만든 가상 네트워크에 *자동*로 변경 하는 사용자 지정 DNS 서버에 오류가 발생 합니다. 설정이 업데이트는 해당 Vnet의 Vm에 푸시되 지 않습니다.

- <!-- TBD - IS ASDK --> Azure 스택 VM을 배포한 후 VM 인스턴스를 추가 네트워크 인터페이스를 추가 하는 것을 지원 하지 않습니다. VM에서 둘 이상의 네트워크 인터페이스를 필요로 하는 경우 배포 시에 정의 되어야 합니다.

- <!-- 2096388 IS --> 네트워크 보안 그룹에 대 한 규칙을 업데이트 하는 관리 포털을 사용할 수 없습니다.

    응용 프로그램 서비스에 대 한 해결 방법: PowerShell 사용한 네트워크 보안 그룹 내에서 보안 규칙을 수정 컨트롤러 인스턴스를 원격 데스크톱을 해야 하는 경우.  다음은 하는 방법의 예 *허용*, 다음 구성을 사용 하 여 복원 *거부*:  

    - *허용:*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

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

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

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


### <a name="sql-and-mysql"></a>SQL 및 MySQL

- <!-- TBD - IS --> 항목을 만드는 서버에서 해당 호스트 SQL 또는 MySQL 리소스 공급자에만 사용할 수 있습니다. 리소스 공급자가 생성 되지 않은 호스트 서버에 만든 항목 일치 하지 않는 상태가 될 수 있습니다.  

- <!-- IS, ASDK --> 특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다는 **제품군** 또는 **계층** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다.


> [!NOTE]  
> <!-- TBD - IS --> Azure 스택 1805를 업데이트 한 후 이전에 배포 하는 SQL 및 MySQL 리소스 공급자를 사용 하도록 계속 수 있습니다.  새 릴리스는 사용할 수 있을 때 SQL 및 MySQL를 업데이트 하는 것이 좋습니다. Azure 스택과 마찬가지로 SQL 및 MySQL 리소스 공급자에 업데이트를 순서 대로 적용 합니다. 예를 들어 1803 버전을 사용 하는 경우 먼저 1804, 버전을 적용 하 고 1805로 업데이트 합니다.      
>   
> 1805 업데이트의 설치는 현재 사용자가 SQL 또는 MySQL 리소스 공급자를 사용 하는 영향을 주지 않습니다.
> 사용 리소스 공급자의 버전에 관계 없이 데이터베이스에 사용자가 데이터 연결, 아니며에 계속 액세스할 수 합니다.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> 사용자가 구독에 해당 첫 번째 Azure 기능을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.

- <!-- 2489178 - IS ASDK --> (작업자, 관리, 프런트 엔드 역할) 인프라를 확장 하기 위해 계산에 대 한 릴리스 정보에 설명 된 대로 PowerShell을 사용 해야 합니다.

- <!-- TBD - IS ASDK --> 앱 서비스에만 배포할 수는 *공급자 기본 구독* 이 이번에 있습니다. 새 배포 응용 프로그램 서비스는 향후 업데이트에서 *구독 계량* Azure 스택 1804에 도입 된입니다. 계량 사용 하기 위해 지원 되는 경우이 새 구독 형식에 기존의 모든 배포를 마이그레이션합니다.


### <a name="usage"></a>사용 현황  
- <!-- TBD - IS ASDK --> 사용 현황 공용 IP 주소 사용 계량 데이터를 보여 줍니다 동일한 *EventDateTime* 대신 각 레코드에 대 한 값은 *TimeDate* 레코드를 만들 때 보여 주는 스탬프입니다. 현재 공용 IP 주소 사용 정확한 계정 작업을 수행 하려면이 데이터를 사용할 수 없습니다.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>업데이트 다운로드
Azure 스택 1805 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.


## <a name="see-also"></a>참고 항목
- 권한 있는 끝점 (PEP)를 모니터링 하 고 업데이트를 다시 시작을 사용 하려면 참조 [업데이트 권한이 있는 끝점을 사용 하 여 Azure 스택의 모니터링](azure-stack-monitor-update.md)합니다.
- Azure 스택에서 업데이트 관리의 개요를 참조 하십시오. [Azure 스택 개요에서 업데이트를 관리](azure-stack-updates.md)합니다.
- Azure 스택을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 [Azure 스택에서 업데이트 적용](azure-stack-apply-updates.md)합니다.
