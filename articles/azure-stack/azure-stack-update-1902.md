---
title: Azure Stack 1902 업데이트 | Microsoft Docs
description: 알려진된 문제 및 업데이트를 다운로드 하는 where 1902 업데이트 새로운 기능을 포함 하 여 Azure Stack 통합 시스템에 대해 알아봅니다.
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/05/2019
ms.openlocfilehash: 2005cf4b1929dfe9e520f56308493db7d820226e
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361102"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 업데이트

*적용 대상 Azure Stack 통합 시스템*

이 문서에서는 1902 업데이트 패키지의 내용을 설명 합니다. 업데이트는 향상 된 기능, 수정 및 Azure Stack의이 버전에 대 한 새로운 기능을 포함합니다. 또한이 문서는이 릴리스의 알려진된 문제를 설명 하 고 업데이트를 다운로드 하려면 링크를 포함 합니다. 알려진된 문제는 업데이트 프로세스를 직접 관련 된 문제 및 문제 (설치 후) 빌드를 사용 하 여으로 구분 됩니다.

> [!IMPORTANT]  
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="build-reference"></a>빌드 참조

Azure Stack 1902 업데이트 빌드 번호는 **1.1902.0.69**합니다.

## <a name="hotfixes"></a>핫픽스

Azure Stack에는 핫픽스를 정기적으로 해제합니다. 설치 해야 합니다 [최신 Azure Stack 핫픽스](#azure-stack-hotfixes) 1901 1902로 Azure Stack을 업데이트 하기 전에 대 한 합니다.

Azure Stack 핫픽스 Azure Stack 통합 시스템에 적용할 수만 있습니다. ASDK에서 핫픽스를 설치 하지 마십시오.

> [!TIP]  
> 다음을 구독할 *RSS* 또는 *Atom* Azure Stack 핫픽스를 피드 합니다.
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 핫픽스

- **1809**: [4481548 – Azure Stack 1.1809.12.114 핫픽스 (kb)](https://support.microsoft.com/help/4481548/)
- **1811**: 현재 핫픽스 사용 가능 합니다.
- **1901**: [4495662 – Azure Stack 1.1901.3.105 핫픽스 (kb)](https://support.microsoft.com/help/4495662)
- **1902**: [4494719 – Azure Stack 1.1902.2.73 핫픽스 (kb)](https://support.microsoft.com/help/4494719)

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> 1902에서 직접 설치할 수 있습니다 합니다 [1.1901.0.95 또는 1.1901.0.99](azure-stack-update-1901.md#build-reference) 먼저 모든 1901 핫픽스를 설치 하지 않고 릴리스 합니다. 그러나 이전 설치한 경우 **1901.2.103** 핫픽스를 설치 해야 최신 [1901.3.105 핫픽스](https://support.microsoft.com/help/4495662) 1902 전에 합니다.

- 이 업데이트의 설치를 시작 하기 전에 실행할 [테스트 AzureStack](azure-stack-diagnostic-test.md) 에 Azure Stack의 상태를 확인 하 고 발견 된 작동 문제를 해결 하려면 다음 매개 변수를 사용 하 여 모든 경고 및 오류를 포함 합니다. 또한 활성 경고를 검토 하 고 작업을 필요로 하는 해결:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  경우는 `AzsControlPlane` 매개 변수는 포함 **테스트 AzureStack** 가 실행에서 다음 오류를 표시 됩니다는 **테스트 AzureStack** 출력: **실패 Azure Stack 제어 평면 Websites 요약**합니다. 이 특정 오류를 안전 하 게 무시할 수 있습니다.

- Azure Stack에서 System Center Operations Manager (SCOM)을 관리 되는 경우 업데이트 해야 합니다 [Microsoft Azure Stack 용 관리 팩](https://www.microsoft.com/download/details.aspx?id=55184) 1902 적용 하기 전에 1.0.3.11 버전으로 합니다.

- Azure Stack 업데이트에 대 한 패키지 형식에서 변경 되었습니다 **.bin/.exe/.xml** 하 **.zip/.xml** 1902 릴리스부터 합니다. 연결 된 Azure Stack 배율 단위를 사용 하 여 고객에 게 표시 합니다 **업데이트 사용 가능** 포털에서 메시지입니다. 연결 되지 않은 고객 수 이제 단순히 다운로드 하 고 해당.xml을 사용 하 여.zip 파일을 가져옵니다.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>향상 된 기능

- 1902 빌드 계획, 제안, 할당량 및 추가 기능 계획을 만들기 위한 Azure Stack 관리자 포털에서 새로운 사용자 인터페이스를 소개 합니다. 스크린샷을 포함 한 자세한 내용은 참조 하세요. [계획, 제품 및 할당량 만들기](azure-stack-create-plan.md)합니다.

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- "Running"를 "늘리는 저장소"에서 배율 단위 상태를 전환 하는 경우 추가 노드 작업 중 용량 확장의 안정성 향상 되었습니다.

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- 오프 라인 수집에 대 한 더 쉽게 관리 뿐만 아니라 패키지 무결성 및 보안 향상을 위해 Microsoft는.zip 파일에.exe 및.bin 파일에서 업데이트 패키지 형식의 변경 되었습니다. 때때로 업데이트 준비 상태를 일으킬 수 있는 압축 풀기 프로세스의 추가 안정성을 추가 하는 새 형식입니다. 동일한 패키지 형식으로 OEM에서 패키지 업데이트에 적용 됩니다.
- 환경을 개선 하기 위해 Azure Stack 연산자 AzureStack 테스트를 실행 하는 경우, 연산자 이제 사용할 수 있습니다 단순히, "테스트 AzureStack-그룹 UpdateReadiness"는 Include 문을 후 10 개의 추가 매개 변수를 전달 하는 대신 합니다.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- 을 개선 하기 위해 전체적인 안정성과 가용성의 핵심 인프라 서비스에서 업데이트 프로세스 중 업데이트 작업 계획의 일환으로 네이티브 업데이트 리소스 공급자는 감지 하 고 필요에 따라 자동 전역 재구성을 호출 합니다. 전역 수정 "복구" 워크플로 다음과 같습니다.

  - 최적이 아닌 상태가 되며 필요에 따라 복구 하려고 하는 인프라 가상 컴퓨터에 대 한 확인 합니다.
  - 컨트롤 계획의 일환으로 SQL 서비스 문제에 대 한 확인 하 고 필요에 따라 복구 하려고 합니다.
  - 네트워크 컨트롤러 (NC)의 일부로 소프트웨어 부하 분산 장치 (SLB) 서비스의 상태를 확인 하 고 필요에 따라 복구 하려고 합니다.
  - 네트워크 컨트롤러 (NC) 서비스의 상태를 확인 하 고 필요에 따라 복구 하려고 합니다.
  - 응급 복구 콘솔 서비스 (ERCS) service fabric 노드 상태를 확인 하 고 필요에 따라 복구 키를 누릅니다.
  - 인프라 역할의 상태를 확인 하 고 필요에 따라 복구 키를 누릅니다.
  - 일관 된 저장소 ACS (Azure) service fabric 노드 상태를 확인 하 고 필요에 따라 복구 키를 누릅니다.

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Azure의 향상 된 기능 스택 로그 컬렉션 안정성 및 성능을 개선 하는 진단 도구입니다. 네트워킹 및 id 서비스에 대 한 로깅을 추가 합니다. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- 비밀 회전 준비를 위한 테스트 AzureStack의 안정성 향상을 테스트 합니다.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- 고객의 Active Directory 환경과 통신 하는 경우 AD Graph 안정성을 높이기 위해 향상 된 기능

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Get AzureStackStampInformation에서 향상 된 하드웨어 인벤토리 컬렉션입니다.

- ERCS 인프라에서 실행 중인 작업의 안정성 향상을 위해 각 ERCS 인스턴스에 대 한 메모리를 8GB에서 12 g B로 증가 합니다. Azure Stack 통합 시스템 설치 하는 경우이 인해 12GB 증가 하는 전체.

> [!IMPORTANT]
> 를 패치 및 업데이트 프로세스를 최소한의 테 넌 트의 가동 중지 시간 발생 했는지 확인 하 여 Azure Stack 스탬프 12GB 이상에서 사용 가능한 공간에 있는지를 확인 합니다 **용량** 블레이드입니다. 에 반영 증가이 메모리를 볼 수 있습니다 합니다 **용량** 블레이드에서 업데이트의 설치를 완료 합니다.

## <a name="common-vulnerabilities-and-exposures"></a>일반적인 취약점 및 exposures

이 업데이트에는 다음 보안 업데이트를 설치합니다.  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

이러한 취약점에 대 한 자세한 내용은 위의 링크를 클릭 하거나 Microsoft 기술 자료 문서를 참조 하세요 [4487006](https://support.microsoft.com/en-us/help/4487006)합니다.

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

<!-- ### Health and monitoring -->

### <a name="compute"></a>컴퓨팅

- 새 Windows 가상 머신 (VM)를 만들 때 다음 오류가 표시 될 수 있습니다.

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   오류는 VM에서 부팅 진단을 사용 하도록 설정 해도 부트 진단 저장소 계정을 삭제 하는 경우에 발생 합니다. 이 문제를 해결 하려면 이전에 사용한 동일한 이름 사용 하 여 저장소 계정을 다시 만듭니다.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 가상 머신 확장 집합 (VMSS) 생성 환경에 대 한 배포 옵션으로 7.2 CentOS 기반을 제공합니다. 모두 배포에 대 한 다른 운영 체제를 선택 하거나 다운로드 하 여 marketplace에서 배포 하기 전에 다른 CentOS 이미지를 지정 하는 Azure Resource Manager 템플릿을 사용 하 여 해당 이미지를 Azure Stack에서 사용할 수 없는 때문에 연산자입니다.  

<!-- TBD - IS ASDK --> 
- 1902를 적용 한 후 업데이트를 Managed Disks를 사용 하 여 Vm을 배포 하는 경우 다음과 같은 문제가 발생할 수 있습니다.

   - Managed Disks를 사용 하 여 VM 배포 1808 업데이트 하기 전에 구독을 만든 경우 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면 각 구독에 대해 다음이 단계를 수행 합니다.
      1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 선택 **리소스 공급자**을 선택한 후 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
      2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **Azure Stack – Managed Disk** 나열 됩니다.
   - 다중 테 넌 트 환경에 구성한 게스트 디렉터리와 연결 된 구독에서 Vm을 배포할 내부 오류 메시지와 함께 실패할 수 있습니다. 오류를 해결 하려면에서 다음이 단계를 수행 [이 문서에서는](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) 각 게스트 디렉터리를 다시 구성 합니다.

- 사용 하도록 설정 하는 SSH 인증을 사용 하 여 만든 Ubuntu 18.04 VM은 SSH 키를 사용 하 여 로그인 할 수 없습니다. 이 문제를 해결 하려면 Linux 확장에 대 한 VM 액세스 프로 비전 한 후 SSH 키를 구현 하는 데 또는 암호 기반 인증을 사용 합니다.

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

### <a name="syslog"></a>syslog 

- Syslog 구성을 유지 되지 않습니다는 업데이트 주기를 통해 관련 구성 및 전달 되는 것을 중지 하려면 syslog 메시지 손실 syslog 클라이언트. 이 문제는 syslog 클라이언트 (1809)의 GA 이후 Azure Stack의 모든 버전에 적용 됩니다. 이 문제를 해결 하려면 syslog 클라이언트는 Azure Stack 업데이트를 적용 한 후 다시 구성 합니다.

## <a name="download-the-update"></a>업데이트 다운로드

Azure Stack 1902 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다. 

연결 된 시나리오 에서만 Azure Stack 배포 주기적으로 보안된 끝점을 확인 하 고 자동으로 클라우드를 위한 사용 가능한 업데이트가 있는 경우을 알립니다. 자세한 내용은 [Azure Stack에 대 한 업데이트 관리](azure-stack-updates.md#using-the-update-tile-to-manage-updates)합니다.

## <a name="next-steps"></a>다음 단계

- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.  
- Azure Stack을 사용 하 여 업데이트를 적용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.
- Azure Stack 통합 시스템 및 지원 되는 상태 시스템을 유지 하기 위해 해야 할 새로운 서비스의 정책을 참조 하세요 [Azure Stack 서비스 정책](azure-stack-servicing-policy.md)합니다.  
- 권한 있는 끝점 (PEP)를 사용 하 여 모니터링 하 고 업데이트를 다시 시작, 참조 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.  
