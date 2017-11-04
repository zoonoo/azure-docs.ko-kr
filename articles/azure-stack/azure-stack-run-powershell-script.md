---
title: "Azure 스택 개발 키트 배포 | Microsoft Docs"
description: "Azure 스택 개발 키트를 준비 하 고 배포 하는 PowerShell 스크립트를 실행 하는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Azure 스택 개발 키트를 배포 합니다.

*적용 대상: Azure 스택 개발 키트*

배포 하는 [Azure 스택 개발 키트](azure-stack-poc.md), 다음 단계를 완료 해야 합니다.

1. [배포 패키지를 다운로드](https://azure.microsoft.com/overview/azure-stack/try/?v=try) 는 Cloudbuilder.vhdx 얻으려고 합니다.
2. [cloudbuilder.vhdx 준비](#prepare-the-development-kit-host) 개발 키트를 설치 하려는 컴퓨터 (개발 키트 호스트)를 구성 하려면 asdk installer.ps1 스크립트를 실행 합니다. 이 단계를 개발 키트 호스트는 Cloudbuilder.vhdx 부팅 됩니다.
3. [개발 키트 배포](#deploy-the-development-kit) 개발 키트 호스트에 있습니다.

> [!NOTE]
> 최상의 결과 연결이 끊긴된 Azure 스택 환경 사용 하려는 경우에 것이 좋습니다 인터넷에 연결 되어 있는 동안 배포. 이런 방식으로 배포 시에는 Windows Server 2016 평가판을 활성화할 수 있습니다.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>다운로드 하 고 개발 키트를 압축 합니다.
1. 다운로드를 시작 하기 전에 컴퓨터가 다음 전제 조건을 충족 하는지 확인 합니다.

   * 컴퓨터 이상 60GB의 사용 가능한 디스크 공간에 있어야 합니다.
   * [.NET framework 4.6 (또는 이후 버전)](https://aka.ms/r6mkiy) 설치 해야 합니다.

2. [시작 페이지로 이동](https://azure.microsoft.com/overview/azure-stack/try/?v=try)자신의 세부 정보를 제공 하 고 클릭, **전송**합니다.
3. 아래 **소프트웨어 다운로드**, 클릭 **Azure 스택 개발 키트**합니다.
4. 다운로드 한 AzureStackDownloader.exe 파일을 실행 합니다.
5. 에 **Azure 스택 개발 키트 다운로더** 창에서 단계 1부터 5까지 수행 합니다.
6. 다운로드가 완료 되 면 클릭 **실행** 는 MicrosoftAzureStackPOC.exe 시작할 수 있습니다.
7. 사용권 계약 화면 및 자동 압축 풀기 마법사의 정보를 검토 한 다음 클릭 **다음**합니다.
8. 개인정보취급방침 화면 및 자동 압축 풀기 마법사의 정보를 검토 한 다음 클릭 **다음**합니다.
9. 추출할를 클릭 합니다. 파일에 대 한 대상을 선택 **다음**합니다.
   * 기본값은: <drive letter>:\<현재 폴더 > \Microsoft Azure 스택
10. 대상 위치 화면 및 자동 압축 풀기 마법사의 정보를 검토 한 다음 클릭 **추출** CloudBuilder.vhdx (~ 25 GB) 및 ThirdPartyLicenses.rtf 파일을 추출 하 합니다. 이 프로세스를 완료 하려면 다소 시간이 걸립니다.

> [!NOTE]
> 파일 압축을 푼 후에 컴퓨터에서 공간을 복구 exe 및 bin 파일을 삭제할 수 있습니다. 또는 이러한 파일을 다른 위치로 있습니다를 다시 배포 해야 하는 경우 파일을 다시 다운로드할 필요가 없습니다 이동할 수 있습니다.
> 
> 

## <a name="prepare-the-development-kit-host"></a>개발 키트 호스트 준비
1. 수 물리적으로 개발 키트 호스트에 연결 하거나 (예: KVM) 실제 콘솔 액세스 권한이 있는지 확인 합니다. 아래 13 단계에서 개발 키트 호스트를 다시 부팅 한 후 이러한 액세스를 권한이 있어야 합니다.
2. 개발 키트 호스트 충족 하는지 확인는 [최소 요구 사항을](azure-stack-deploy.md)합니다. 사용할 수는 [Azure 스택에 대 한 배포 검사](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) 요구 사항을 확인 합니다.
3. 개발 키트 호스트에 로컬 관리자로 로그인 합니다.
4. 복사 하거나 CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트로 이동 합니다.
5. 개발 키트 호스트에 c:\AzureStack_Installer 폴더로 (asdk installer.ps1) 개발 키트 설치 관리자 파일을 다운로드 하려면 다음 스크립트를 실행 합니다.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. 관리자 권한 PowerShell 콘솔을 열고 > C:\AzureStack_Installer\asdk-installer.ps1 스크립트 실행 > 클릭 **환경 준비**합니다.
7. 에 **선택 Cloudbuilder vhdx** 는 설치 관리자를 선택 합니다는 이전 단계에서 다운로드 한 cloudbuilder.vhdx 파일의 페이지입니다.
8. 선택 사항: 확인는 **드라이버 추가** 상자는 호스트에서 추가 드라이버를 포함 하는 폴더를 지정 합니다.
9. 에 **설정 (옵션)** 페이지 개발 키트 호스트에 대 한 로컬 관리자 계정을 제공 합니다. 이러한 자격 증명을 제공 하지 않으면, 아래 설치 프로세스 중 호스트에 대 한 KVM 액세스를 해야 합니다.
10. **설정 (옵션)** 페이지에서는 다음을 설정 하는 옵션 수 있습니다.
    - **Computername**:이 옵션은 개발 키트 호스트의 이름을 설정 합니다. 이름은은 FQDN 요구 사항을 준수 해야 하며 길이가 또는 15 자 여야 합니다. 기본값은 Windows에 의해 생성 된 임의의 컴퓨터 이름입니다.
    - **표준 시간대**: 개발 키트 호스트에 대 한 표준 시간대를 설정 합니다. 기본값은 (UTC-8:00) 태평양 표준시 (미국과 캐나다).
    - **고정 IP 구성을**: 고정 IP 주소를 사용 하도록 배포를 설정 합니다. 그렇지 않은 경우 설치 관리자는 cloudbuilder.vhx 다시 부팅, 네트워크 인터페이스 DHCP로 구성 됩니다.
11. **다음**을 누릅니다.
12. 이전 단계에서 고정 IP 구성을 선택한 경우 이제 해야 합니다.
    - 네트워크 어댑터를 선택 합니다. 클릭 하기 전에 어댑터에 연결할 수 있는지 확인 **다음**합니다.
    - 다음 사항을 확인는 **IP 주소**, **게이트웨이**, 및 **DNS** 값 올바른지, 그리고 클릭 **다음**합니다.
13. 클릭 **다음** 준비 프로세스를 시작 합니다.
14. 시점을 나타내는 준비 **Completed**, 클릭 **다음**합니다.
15. 클릭 **지금 다시 부팅** 에 cloudbuilder.vhdx로 부팅 하 고 배포 프로세스를 계속 합니다.

## <a name="deploy-the-development-kit"></a>개발 키트를 배포 합니다.
1. 개발 키트 호스트에 로컬 관리자로 로그인 합니다. 이전 단계에서 지정 된 자격 증명을 사용 합니다.

    > [!IMPORTANT]
    > Azure Active Directory 배포에 대 한 Azure 스택 직접 또는 투명 프록시를 통해 인터넷 액세스를 해야합니다. 배포에서 네트워킹에 대 한 정확히 한 개의 NIC를 지원 합니다. 여러 Nic를 설정한 경우를 하나만 사용 하도록 설정할 (및 다른 모든 항목을 사용할 수 없습니다) 다음 섹션에서 배포 스크립트를 실행 하기 전에 있는지 확인 합니다.
    
2. 관리자 권한 PowerShell 콘솔을 열고 > \AzureStack_Installer\asdk-installer.ps1 스크립트 (수도 있음는 Cloudbuilder.vhdx에서 다른 드라이브에 있음)를 실행 > 클릭 **설치**합니다.
3. 에 **형식** 상자 **Azure 클라우드** 또는 **ADFS**합니다.
    - **Azure 클라우드**: Azure Active Directory는 id 공급자입니다. AAD 계정에 전역 관리자 권한이 특정 디렉터리를 지정 하려면이 매개 변수를 사용 합니다. AAD 디렉터리 테 넌 트의 전체 이름입니다. 예를 들어. onmicrosoft.com 합니다. 
    - **ADFS**: 디렉터리 서비스는 기본 스탬프는 id 공급자로, 기본 계정이 로그인 하는 데 azurestackadmin@azurestack.local를 사용 하는 암호는 설정의 일부분으로 제공 된 것 이며 합니다.
4. 아래 **로컬 관리자 암호**에 **암호** 상자 (현재 구성 된 로컬 관리자 암호와 일치 해야 합니다)는 로컬 관리자 암호를 입력 한 다음 클릭**다음**합니다.
5. 네트워크 어댑터를 개발 키트를 사용 하 고 클릭 한 다음 선택 **다음**합니다.
6. DHCP 또는 BGPNAT01 가상 컴퓨터에 대 한 정적 네트워크 구성을 선택 합니다.
    - **DHCP** (기본값): 가상 컴퓨터는 DHCP 서버에서 IP 네트워크 구성을 가져옵니다.
    - **정적**: DHCP는 인터넷에 액세스할 수 있는 Azure 스택에 대 한 유효한 IP 주소를 할당할 수 없습니다 하는 경우에이 옵션을 사용 합니다. 고정 IP 주소는 서브넷 마스크 길이 (예를 들어 10.0.0.5/24)으로 지정 되어야 합니다.
7. 필요에 따라 다음 값을 설정 합니다.
    - **VLAN ID**: VLAN ID를 설정 합니다 호스트와 AzS BGPNAT01 VLAN ID 및 액세스 하는 실제 네트워크 (인터넷)를 구성 해야 하는 경우에이 옵션을 사용 합니다. 
    - **DNS 전달자**: DNS 서버가 Azure 스택 배포의 일부로 생성 됩니다. 스탬프 외부 이름을 확인 하기 위해 솔루션 내에 있는 컴퓨터를 허용 하려면 기존 DNS 서버 인프라를 제공 합니다. 스탬프에 DNS 서버는이 서버에 알 수 없는 이름 확인 요청을 전달합니다.
    - **시간 서버**:이 필수 필드 시간 서버를 설정 하 고 IP 주소 여야 합니다. 시간 서버 IP 주소를 확인 하려면 다음을 방문 [pool.ntp.org](http:\\pool.ntp.org) 또는 time.windows.com ping 합니다. 
8. **다음**을 누릅니다. 
9. 에 **네트워크 인터페이스 카드 속성 확인** 페이지에서 진행률 표시줄이 표시 됩니다. 
    - 표시 하는 경우 **업데이트를 다운로드할 수 없습니다**, 페이지의 지시를 따릅니다.
    - 표시 때 **Completed**, 클릭 **다음**합니다.
10. **요약** 페이지 **배포**합니다.
11. Azure Active Directory 배포를 사용 하는 경우 Azure Active Directory 전역 관리자 계정 자격 증명을 입력 하 라는 나타납니다.
12. 배포 프로세스는 시스템이 자동으로 재부팅 한 번 몇 시간 걸릴 수 있습니다.
   
   > [!IMPORTANT]
   > 배포 진행률을 모니터링 하려는 경우 azurestack\AzureStackAdmin로에 로그인 합니다. 로그인 되 면 로컬 관리자로 컴퓨터를 도메인에 가입 된 후에 배포 진행률을 볼 수 없습니다. 대신 실행 중인 유효성을 검사 하려면 azurestack\AzureStackAdmin로 로그인, 배포 다시 실행 하지 마십시오.
   > 
   > 
   
    배포에 성공 하면 PowerShell 콘솔에 표시 됩니다: **완료: 작업 '배포'**합니다.
   
배포가 실패 하는 경우에 동일한 관리자 권한 PowerShell 창에서 다음을 다시 실행 하는 PowerShell 스크립트를 사용할 수 있습니다.

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

이 스크립트는 성공 하는 마지막 단계에서 배포를 다시 시작 됩니다.

할 수 있습니다 또는 [재배포](azure-stack-redeploy.md) 처음부터 합니다.


## <a name="reset-the-password-expiration-to-180-days"></a>암호 만료를 180 일로 다시 설정

개발 키트 호스트에 대 한 암호가 너무 일찍 만료 되지 않도록 확인을 배포한 후 다음이 단계를 따르십시오.

Powershell에서 암호 만료 정책 변경 하려면:
1. Powershell 창에서 명령을; 실행 집합 ADDefaultDomainPasswordPolicy-MaxPasswordAge 180.00:00:00-Identity azurestack.local

변경 하려면 암호 만료 정책을 수동으로:
1. 개발 키트 호스트에서 엽니다 **그룹 정책 관리** 이동한 다음 **그룹 정책 관리** – **포리스트: azurestack.local** – **도메인** – **azurestack.local**합니다.
2. 마우스 오른쪽 단추로 클릭 **기본 도메인 정책** 클릭 **편집**합니다.
3. 그룹 정책 관리 편집기에서 이동 **컴퓨터 구성** – **정책** – **Windows 설정** – **보안 설정**– **계정 정책** – **암호 정책**합니다.
4. 오른쪽 창에서 두 번 클릭 **최대 암호 사용 기간**합니다.
5. 에 **최대 암호 사용 기간 속성** 변경 대화 상자는 **암호 만료** 클릭 한 다음 값을 180 **확인**합니다.


## <a name="next-steps"></a>다음 단계

[PowerShell 설치](azure-stack-powershell-configure-quickstart.md)

[Azure 스택 Azure 구독에 등록](azure-stack-register.md)

[Azure Stack에 연결](azure-stack-connect-azure-stack.md)

