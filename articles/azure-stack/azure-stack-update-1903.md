---
title: Azure Stack 1903 업데이트 | Microsoft Docs
description: Azure Stack 통합 시스템의 경우 새로운 기능을 포함 하 여 1903 업데이트에 대 한 알려진된 문제 및 업데이트를 다운로드 하는 위치를 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 2a2e289423eda53d610b2346193f6ee8a30b9c48
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917688"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 업데이트

*적용 대상 Azure Stack 통합 시스템*

이 문서에서는 1903 업데이트 패키지의 내용을 설명 합니다. 업데이트는 향상 된 기능, 수정 및 Azure Stack의이 버전에 대 한 새로운 기능을 포함합니다. 또한이 문서는이 릴리스의 알려진된 문제를 설명 하 고 업데이트를 다운로드 하려면 링크를 포함 합니다. 알려진된 문제는 업데이트 프로세스를 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]  
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure Stack 1903 업데이트 빌드 번호는 **1.1903.0.35**합니다.

> [!IMPORTANT]
> 1903 페이로드는 ASDK 릴리스에 포함 되지 않습니다.

## <a name="hotfixes"></a>핫픽스

Azure Stack에는 핫픽스를 정기적으로 해제합니다. 설치 해야 합니다 [최신 Azure Stack 핫픽스](#azure-stack-hotfixes) 1902 1903로 Azure Stack을 업데이트 하기 전에 대 한 합니다.

Azure Stack 핫픽스 Azure Stack 통합 시스템에 적용할 수만 있습니다. ASDK에서 핫픽스를 설치 하지 마십시오.

> [!TIP]  
> 다음을 구독할 *RSS* 또는 *Atom* Azure Stack 핫픽스를 피드 합니다.
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 핫픽스

- **1902**: [4481548 – Azure Stack 1.1902.2.73 핫픽스 (kb)](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>향상 된 기능

- 1903 업데이트 페이로드를 포함 하지 않는 기본 운영 체제가 호스트 하는 Azure Stack Azure Stack의 구성 요소에 대 한 업데이트를 포함 합니다. 따라서 특정 업데이트를 범위를 지정할 수 있습니다. 결과적으로, 필요한 걸리는 시간 1903 업데이트가 완료에 대 한 작은 (약 16 시간 동안 하지만 정확한 시간이 달라질 수 있습니다). 런타임에서이 감소 1903 업데이트와 관련 되며 후속 업데이트는 서로 다른 런타임 나타내므로 운영 체제에 대 한 업데이트를 포함할 수 있습니다. 이후 업데이트는 업데이트에 포함 된 페이로드에 따라 완료 하는 데 걸리는 예상된 시간에 유사한 지침을 제공 합니다.

- 네트워킹 변경 하지 못하는 버그가 수정 되었습니다 합니다 **유휴 시간 제한 (분)** 의 값을 **공용 IP 주소** 적용에서. 이전에이 값으로 변경 된 무시 수행한 모든 변경 내용은 관계 없이 값은 기본적으로 4 분 있도록. 이 설정은 연결 유지 메시지를 보내는 클라이언트에 의존 하지 않고 TCP 연결을 유지 하는 시간 (분) 열을 제어 합니다. 참고가이 버그에만 영향을 인스턴스 수준 공용 Ip, load balancer에 할당 된 공용 Ip입니다.

- 중단 없이 업데이트 적용 되도록의 일반적인 문제 자동 업데이트를 비롯 한 여러 업데이트 엔진의 안정성 향상 되었습니다.

- 검색 및 디스크 공간 부족 조건의 업데이트 관리 향상 되었습니다.

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> 설치 합니다 [최신 Azure Stack 핫픽스](#azure-stack-hotfixes) 1903를 업데이트 하기 전에 1902 (있는 경우)에 대 한 합니다.

- 최신 버전을 사용 해야 합니다 [Azure Stack는 capacity planner](https://aka.ms/azstackcapacityplanner) 계획 및 크기 조정 작업을 수행 합니다. 최신 버그 수정 포함 및 릴리스되는 새로운 기능을 제공 각 Azure Stack 업데이트 합니다.

- 이 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 하려면 다음 매개 변수를 사용 하 여 모든 경고 및 오류를 포함 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결:

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- Azure Stack을 System Center Operations Manager에서 관리 하는 경우 업데이트 해야 합니다 [Microsoft Azure Stack 용 관리 팩](https://www.microsoft.com/download/details.aspx?id=55184) 1903 적용 하기 전에 1.0.3.11 버전으로 합니다.

- Azure Stack 업데이트에 대 한 패키지 형식에서 변경 되었습니다 **.bin/.exe/.xml** 하 **.zip/.xml** 1902 릴리스부터 합니다. 연결 된 Azure Stack 배율 단위를 사용 하 여 고객에 게 표시 합니다 **업데이트 사용 가능** 포털에서 메시지입니다. 연결 되지 않은 고객 수 이제 단순히 다운로드 하 고 해당.xml을 사용 하 여.zip 파일을 가져옵니다.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제

- 실행할 때 [테스트 AzureStack](azure-stack-diagnostic-test.md), 베이스 보드 관리 컨트롤러 (BMC)에서 경고 메시지가 표시 됩니다. 이 경고를 안전 하 게 무시할 수 있습니다.

- <!-- 2468613 - IS --> 이 업데이트를 설치 하는 동안 경고 제목으로 표시 될 수 있습니다 `Error – Template for FaultType UserAccounts.New is missing.` 이러한 경고를 안전 하 게 무시할 수 있습니다. 이 업데이트의 설치가 완료 된 후 경고를 자동으로 닫습니다.

## <a name="post-update-steps"></a>업데이트 후 단계

- 이 업데이트의 설치가 끝나면 모든 적용 가능한 핫픽스를 설치 합니다. 자세한 내용은 [핫픽스](#hotfixes), 뿐만 아니라 우리 [서비스 정책](azure-stack-servicing-policy.md)합니다.  

- 암호화 미사용 데이터를 검색 하 고 Azure Stack 배포 외부에서 안전 하 게 저장 합니다. 수행 합니다 [키를 검색 하는 방법에 대 한 지침](azure-stack-security-bitlocker.md)합니다.

## <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)

이 빌드 버전에 대 한 설치 후 알려진된 문제는 다음과 같습니다.

### <a name="portal"></a>포털

<!-- 2930820 - IS ASDK --> 
- 관리자와 사용자 포털에서 "Docker"를 검색 하는 경우 항목을 올바르게 반환 됩니다. Azure Stack에서 사용할 수 없는 합니다. 만들 하려고 하면 오류가 표시 된 블레이드가 표시 됩니다. 

<!-- 2931230 – IS  ASDK --> 
- 사용자 구독에서 해당 계획을 제거 하는 경우에는 추가 요금제는 사용자 구독에 추가 되는 계획을 삭제할 수 없습니다. 계획에는 추가 요금제를 참조 하는 구독도 삭제 될 때까지 상태로 유지 됩니다. 

<!-- TBD - IS ASDK --> 
- 1804 버전을 사용 하 여 도입 된 관리 구독 유형에 사용할 수 없습니다. 구독 유형은 **구독을 계량**, 및 **소비 구독**합니다. 이러한 구독 유형에 1804 버전부터 새 Azure Stack 환경에서 표시 되지만 아직 사용할 준비가 완료 됩니다. 계속 사용 해야 합니다 **기본 공급자** 구독 유형입니다.

<!-- 3557860 - IS ASDK --> 
- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.

<!-- 1663805 - IS ASDK --> 
- Azure Stack 포털을 사용 하 여 구독에 사용 권한을 확인할 수 없습니다. 대 안으로 사용 하 여 [사용 권한을 확인 하려면 PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)합니다.

<!-- Daniel 3/28 -->
- 사용자 포털에서 저장소 계정 내의 blob을 이동할를 열려고 **액세스 정책** 탐색 트리에서 후속 창을 로드 하지 못했습니다.

<!-- Daniel 3/28 -->
- 사용자 포털을 사용 하 여 blob을 업로드 하려고 할 때에 **OAuth(preview)** 옵션 작업이 오류 메시지와 함께 실패 합니다. 이 문제를 해결 하려면 사용 하 여 blob을 업로드 합니다 **SAS** 옵션입니다.

- Azure Stack 포털에 로그인 한 경우에 공용 Azure portal에 대 한 알림을 볼 수 있습니다. Azure Stack에 현재 적용 되지 않으므로 이러한 알림을 안전 하 게 무시할 수 있습니다 (예를 들어 "새 업데이트 1-다음과 같은 업데이트가 제공 됩니다. Azure portal 2019 년 4 월 업데이트 ").

<!-- ### Health and monitoring -->

### <a name="compute"></a>컴퓨팅

- 새 Windows 가상 머신 (VM)를 만들 때 다음 오류가 표시 될 수 있습니다.

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   오류는 VM에서 부팅 진단을 사용 하도록 설정 해도 부트 진단 저장소 계정을 삭제 하는 경우에 발생 합니다. 이 문제를 해결 하려면 이전에 사용한 동일한 이름 사용 하 여 저장소 계정을 다시 만듭니다.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Virtual Machine Scale Set 만들기 환경 배포 옵션으로 7.2 CentOS 기반을 제공합니다. 모두 배포에 대 한 다른 운영 체제를 선택 하거나 다운로드 하 여 marketplace에서 배포 하기 전에 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 하 여 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 연산자입니다.  

<!-- TBD - IS ASDK --> 
- 업데이트 된 1903를 적용 한 후, Managed Disks를 사용 하 여 Vm을 배포 하는 경우 다음과 같은 문제가 발생할 수 있습니다.

   - Managed Disks를 사용 하 여 VM 배포 1808 업데이트 하기 전에 구독을 만든 경우 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면 각 구독에 대해 다음이 단계를 수행 합니다.
      1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 선택 **리소스 공급자**을 선택한 후 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
      2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **Azure Stack – Managed Disk** 나열 됩니다.
   - 다중 테 넌 트 환경에 구성한 게스트 디렉터리와 연결 된 구독에서 Vm을 배포할 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면에서 다음이 단계를 수행 [이 문서에서는](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) 각 게스트 디렉터리를 다시 구성 합니다.

- 사용 하도록 설정 하는 SSH 인증을 사용 하 여 만든 Ubuntu 18.04 VM는 SSH 키를 사용 하 여 로그인 할 수 없습니다. 이 문제를 해결 하려면 Linux 확장에 대 한 VM 액세스 프로 비전 한 후 SSH 키를 구현 하는 데 또는 암호 기반 인증을 사용 합니다.

- 하드웨어 수명 주기 호스트 (HLH) 없는 경우 1902 빌드 전에 그룹 정책 설정 해야 *컴퓨터 구성 설정 \ 보안 설정 \ 로컬 정책 \ 보안 옵션* 에 **lm NTLM –협상하는경우NTLMv2세션보안사용**. 1902 빌드 후 두어야 그대로 **정의 되지 않은** 로 설정 하거나 **NTLMv2 응답만 보내기** (값인 기본). 그렇지 않으면 PowerShell 원격 세션을 설정할 수 없습니다 하 고 받을 수 있습니다는 *액세스가 거부 되었습니다.* 오류:

   ```PowerShell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>네트워킹  

<!-- 3239127 - IS, ASDK -->
- Azure Stack 포털에 VM 인스턴스에 연결 된 네트워크 어댑터에 바인딩되어 있는 IP 구성에 대 한 고정 IP 주소를 변경 하면 나타납니다 없다는 경고 메시지 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    이 메시지를 안전 하 게 무시할 수 있습니다. IP 주소는 VM 인스턴스 다시 시작 하지 않습니다 하는 경우에 변경 됩니다.

<!-- 3632798 - IS, ASDK -->
- 인바운드 보안 규칙을 추가 하 고 선택 하는 경우 포털에서 **서비스 태그** 원본으로 여러 옵션에 표시 되는 **소스 태그가** Azure Stack에 대해 사용할 수 없는 목록입니다. Azure Stack에서 사용할 수 있는 유일한 옵션 아래와 같습니다.

  - **인터넷**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  다른 옵션으로 Azure Stack에서 소스 태그는 지원 되지 않습니다. 마찬가지로, 아웃 바운드 보안 규칙을 추가 하 고 선택 하는 경우 **서비스 태그** 옵션에 대 한 동일한 목록 대상으로 **소스 태그가** 표시 됩니다. 유효한 옵션은 동일 **소스 태그가**이전 목록에 설명 된 대로 합니다.

- 네트워크 보안 그룹 (Nsg) 동일한 방식으로 전체 Azure에서 Azure Stack에서 작동 하지 않습니다. Azure에서 하나의 NSG 규칙에서 여러 포트를 설정할 수 있습니다 (포털, PowerShell을 사용 하 여 및 Resource Manager 템플릿). 그러나 Azure Stack에서 설정할 수 없습니다 여러 포트에서 포털을 통해 NSG 규칙을 하나입니다. 이 문제를 해결 하려면 이러한 추가 규칙을 설정 하는 Resource Manager 템플릿 또는 PowerShell을 사용 합니다.

<!-- 3203799 - IS, ASDK -->
- Azure Stack 인스턴스 크기에 관계 없이 현재 4 개 이상의 네트워크 인터페이스 (Nic) 하 여 VM 인스턴스에 연결 하는 것을 지원 하지 않습니다.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- 구독에서 첫 번째 Azure Function을 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>업데이트 다운로드

Azure Stack 1903 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다. 

연결 된 시나리오 에서만 Azure Stack 배포 주기적으로 보안된 끝점을 확인 하 고 자동으로 클라우드를 위한 사용 가능한 업데이트가 있는 경우을 알립니다. 자세한 내용은 [Azure Stack에 대 한 업데이트 관리](azure-stack-updates.md#using-the-update-tile-to-manage-updates)합니다.

## <a name="next-steps"></a>다음 단계

- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.  
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.
- Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 새로운 서비스의 정책을 참조 하세요 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.  
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.  
