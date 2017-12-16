---
title: "Azure 스택 개발 키트 배포 | Microsoft Docs"
description: "Azure 스택 개발 키트를 준비 하 고 배포 하는 PowerShell 스크립트를 실행 하는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 470a45aea253e1e238983527427b600117e413fe
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Azure 스택 개발 키트를 배포 합니다.

*적용 대상: Azure 스택 개발 키트*

배포 하는 [Azure 스택 개발 키트](azure-stack-poc.md), 다음 단계를 완료 해야 합니다.

1. [배포 패키지를 다운로드](https://azure.microsoft.com/overview/azure-stack/try/?v=try) 는 Cloudbuilder.vhdx 얻으려고 합니다.
2. 개발 키트를 설치 하려는 컴퓨터 (개발 키트 호스트)를 구성 하려면 cloudbuilder.vhdx를 준비 합니다. 이 단계를 개발 키트 호스트는 Cloudbuilder.vhdx 부팅 됩니다.
3. 개발 키트 개발 키트 호스트에 배포 합니다.

> [!NOTE]
> 최상의 결과 연결이 끊긴된 Azure 스택 환경 사용 하려는 경우에 것이 좋습니다 인터넷에 연결 되어 있는 동안 배포. 이런 방식으로 배포 시 개발 키트 설치에 포함 된 Windows Server 2016 평가판을 활성화할 수 있습니다.

## <a name="download-and-extract-the-development-kit"></a>다운로드 하 고 개발 키트를 압축 합니다.
1. 다운로드를 시작 하기 전에 컴퓨터가 다음 전제 조건을 충족 하는지 확인 합니다.

  - 컴퓨터에 적어도 60GB의 디스크 공간을 4 개의 개별와 동일한 논리 하드 드라이브에서 사용할 수 있는 뿐만 아니라 운영 체제 디스크에 있어야 합니다.
  - [.NET framework 4.6 (또는 이후 버전)](https://aka.ms/r6mkiy) 설치 해야 합니다.

2. [시작 페이지로 이동](https://azure.microsoft.com/overview/azure-stack/try/?v=try) 수 다운로드 Azure 스택 개발 키트, 프로그램 세부 정보를 제공 하 고 있는 클릭 **전송**합니다.
3. 다운로드 및 실행의 [Azure 스택 개발 키트용 배포 검사기](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) 필수 구성 요소 검사기 스크립트입니다. 이 독립 실행형 스크립트 Azure 스택 개발 키트에 대 한 설치 프로그램에서 수행 되는 필수 구성 요소 확인을 거칩니다. Azure 스택 개발 키트에 대 한 큰 패키지를 다운로드 하기 전에 하드웨어 및 소프트웨어 요구 사항을 충족 하는지 확인 하는 방법을 제공 합니다.
4. 아래 **소프트웨어 다운로드**, 클릭 **Azure 스택 개발 키트**합니다.

  > [!NOTE]
  > ASDK 다운로드 (AzureStackDevelopmentKit.exe)는 10GB approximiately 단독으로 합니다. 또한 Windows Server 2016 evaluation 버전 ISO 파일을 다운로드 하려는 경우 다운로드 크기는 약 17gb 증가 합니다. 만들고 ASDK 설치가 완료 된 후 Windows Server 2016 가상 컴퓨터 이미지를 Azure 스택 Marketplace로 추가 된 ISO 파일을 사용할 수 있습니다. 이 Windows Server 2016 evaluation 이미지는 ASDK에만 사용할 수과 ASDK 사용 조건에 따라 참고 합니다.

5. 다운로드가 완료 된 후 클릭 **실행** ASDK 자동 압축 풀기 (AzureStackDevelopmentKit.exe)를 시작할 수 있습니다.
6. 검토 하 고에서 표시 된 사용권 계약에 동의 **사용권 계약** 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **다음**합니다.
7. 에 표시 된 개인정보취급방침 정보를 검토는 **중요 알림** 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **다음**합니다.
8. Azure 스택 설치 파일에서 압축을 풀에 대 한 위치를 선택 하는 **대상 위치 선택** 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **다음**합니다. 기본 위치는 *현재 폴더*\Azure 스택 개발 키트입니다. 
9. 요약 대상 위치를 검토는 **추출 준비가** 을 클릭 한 다음 자동 압축 풀기 마법사의 페이지 **추출** CloudBuilder.vhdx (대략 25 GB)를 추출 하 고 ThirdPartyLicenses.rtf 파일입니다. 이 프로세스를 완료 하려면 다소 시간이 걸립니다.
10. 복사 하거나 ASDK 호스트 컴퓨터에서 CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트로 이동 합니다.

> [!NOTE]
> 파일을 추출한 후 삭제할 수 있습니다는 합니다. EXE 및 합니다. BIN 파일 하드 디스크 공간을 복구 합니다. 또는 ASDK 다시 배포 하는 경우 파일을 다시 다운로드 하지 않아도 되도록 이러한 파일을 백업할 수 있습니다.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>안내 방식 환경에서 사용 하 여 ASDK 배포
ASDK 다운로드 및 asdk installer.ps1 PowerShell 스크립트를 실행 하 여 제공 하는 그래픽 사용자 인터페이스 (GUI)를 사용 하 여 배포할 수 있습니다.

> [!NOTE]
> Azure 스택 개발 키트에 대 한 설치 관리자 사용자 인터페이스는 열린 sourced 스크립트 WCF 및 PowerShell에 기반 합니다.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>안내 방식된 사용자 경험을 사용 하 여 개발 키트 호스트 준비
호스트 컴퓨터에는 ASDK를 설치할 수 있습니다, 전에 ASDK 환경이 준비 되어야 합니다.
1. ASDK 호스트 컴퓨터에 로컬 관리자로 로그인 합니다.
2. CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트에 옮겨 졌음을 있는지 확인 합니다.
3. 개발 키트 설치 관리자 파일 (asdk installer.ps1)를 다운로드 하려면 다음 스크립트를 실행 하는 [Azure 스택 GitHub 도구 저장소](https://github.com/Azure/AzureStack-Tools) 에 **C:\AzureStack_Installer** 폴더에 사용자 개발 키트 호스트 컴퓨터의 경우:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 관리자 권한 PowerShell 콘솔에서 시작 된 **C:\AzureStack_Installer\asdk-installer.ps1** 클릭 및 스크립트에 **환경 준비**합니다.
5. 에 **선택 Cloudbuilder vhdx** 페이지는 설치 관리자를 선택 합니다는 **cloudbuilder.vhdx** 를 다운로드 하 고 이전 단계에서 추출 된 파일입니다. 또한, 필요에 따라 할 수 있습니다이 페이지에서 사용 하도록 설정 된 **드라이버를 추가** 개발 키트 호스트 컴퓨터에 추가 드라이버를 추가 해야 하는 경우 확인란 합니다.  
6. 에 **설정 (옵션)** 페이지 개발 키트 호스트 컴퓨터에 대 한 로컬 관리자 계정을 제공 합니다. 

  > [!IMPORTANT]
  > 이러한 자격 증명을 제공 하지 않으면, 해야 직접 또는 호스트에 대 한 KVM 액세스 개발 키트는 설정의 일부분으로 컴퓨터를 다시 시작 합니다.

7. 이러한 선택적 설정을 제공할 수 있습니다는 **설정 (옵션)** 페이지:
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


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>안내 방식 환경에서 사용 하 여 개발 키트를 배포 합니다.
ASDK 호스트 컴퓨터를 준비한 후 다음 단계를 사용 하 여 CloudBuilder.vhdx 이미지에는 ASDK은 배포할 수 있습니다. 
1. 호스트 컴퓨터 성공적으로 CloudBuilder.vhdx 이미지로 부팅 한 후 이전 단계에서 지정 된 관리자 자격 증명을 사용 하 여 로그인 합니다. 
2. 관리자 권한 PowerShell 콘솔을 열고 실행는 **\AzureStack_Installer\asdk-installer.ps1** 스크립트 (이 문서의 이제 CloudBuilder.vhdx 이미지에서 다른 드라이브에 있음). **Install**을 클릭합니다.
3. 에 **형식** 드롭다운 상자 **Azure 클라우드** 또는 **AD FS**합니다.
    - **Azure 클라우드**: 구성 Azure Active Directory (Azure AD) id 공급자로 합니다. 이 옵션을 사용 하려면 Azure AD의 전체 이름을 인터넷 연결 해야 합니다 디렉터리 테 넌 트의 형태로 *domainname*. onmicrosoft.com 또는 Azure AD에 대 한 사용자 지정 도메인 이름 및 전역 관리자 자격 증명을 확인 합니다.는 지정 된 디렉터리입니다. 
    - **AD FS**: 디렉터리 서비스를 id 공급자로 기본 스탬프입니다. 로그인 하는 데 기본 계정은 azurestackadmin@azurestack.local를 사용 하는 암호는 설정의 일부분으로 제공 된 것 이며 합니다.
4. 아래 **로컬 관리자 암호**에 **암호** 상자 (현재 구성 된 로컬 관리자 암호와 일치 해야 합니다)는 로컬 관리자 암호를 입력 한 다음 클릭**다음**합니다.
5. 네트워크 어댑터를 개발 키트를 사용 하 고 클릭 한 다음 선택 **다음**합니다.
6. DHCP 또는 BGPNAT01 가상 컴퓨터에 대 한 정적 네트워크 구성을 선택 합니다.
    - **DHCP** (기본값): 가상 컴퓨터는 DHCP 서버에서 IP 네트워크 구성을 가져옵니다.
    - **정적**: DHCP는 인터넷에 액세스할 수 있는 Azure 스택에 대 한 유효한 IP 주소를 할당할 수 없습니다 하는 경우에이 옵션을 사용 합니다. 고정 IP 주소 (예를 들어 10.0.0.5/24) CIDR 형식의 서브넷 마스크 길이가 지정 되어야 합니다.
7. 필요에 따라 다음 값을 설정 합니다.
    - **VLAN ID**: VLAN ID를 설정 합니다 호스트와 AzS BGPNAT01 VLAN ID 및 액세스 하는 실제 네트워크 (인터넷)를 구성 해야 하는 경우에이 옵션을 사용 합니다. 
    - **DNS 전달자**: DNS 서버가 Azure 스택 배포의 일부로 생성 됩니다. 스탬프 외부 이름을 확인 하기 위해 솔루션 내에 있는 컴퓨터를 허용 하려면 기존 DNS 서버 인프라를 제공 합니다. 스탬프에 DNS 서버는이 서버에 알 수 없는 이름 확인 요청을 전달합니다.
    - **시간 서버**:이 개발 키트에서 사용할 시간 서버를 설정 하는 필드에 필요 합니다. 이 매개 변수는 유효한 시간 서버 IP 주소로 제공 되어야 합니다. 서버 이름은 지원 되지 않습니다.
  
  > [!TIP]
  > 시간 서버 IP 주소를 확인 하려면 다음을 방문 [pool.ntp.org](http:\\pool.ntp.org) 또는 time.windows.com ping 합니다. 
  
8. **다음**을 누릅니다. 
9. 에 **네트워크 인터페이스 카드 속성 확인** 페이지에서 진행률 표시줄이 표시 됩니다. 
    - 표시 하는 경우 **업데이트를 다운로드할 수 없습니다**, 페이지의 지시를 따릅니다.
    - 표시 때 **Completed**, 클릭 **다음**합니다.
10. **요약** 페이지 **배포**합니다. 여기 개발 키트를 설치 하는 데 사용 됩니다 하는 PowerShell 설치 명령이 복사할 수 있습니다.
11. 배포는 Azure AD를 사용 하는 경우 설치 프로그램이 시작 된 후 몇 분이 Azure AD 전역 관리자 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다.
12. 배포 프로세스는 시스템이 자동으로 재부팅 한 번 몇 시간 걸릴 수 있습니다. 배포에 성공 하면 PowerShell 콘솔에 표시 됩니다: **완료: 작업 '배포'**합니다. 있습니다 수 배포가 실패 하는 경우 [재배포](azure-stack-redeploy.md) 처음부터 새로 만들거나 사용에서 다음 PowerShell 명령을, 동일한 관리자 권한 PowerShell 창에서 성공 하는 마지막 단계에서 배포를 다시 시작 하려면:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > 배포 진행률을 모니터링 하려는 경우 로그인 azurestack\AzureStackAdmin로 개발 키트 호스트를 설치 하는 동안 다시 시작할 때 합니다. 로그인 되 면 로컬 관리자로 컴퓨터를 도메인에 가입 된 후에 배포 진행률을 볼 수 없습니다. 대신 실행 중인 유효성을 검사 하려면 azurestack\AzureStackAdmin로 로그인, 배포 다시 실행 하지 마십시오.
   

## <a name="deploy-the-asdk-using-powershell"></a>PowerShell을 사용 하 여 ASDK 배포
이전 단계 이미 안내 사용자 경험을 사용 하 여 ASDK를 배포 하는 과정을 설명 합니다. 또한이 섹션의 단계에 따라 ASDK 개발 키트 호스트에 배포 하려면 PowerShell을 사용할 수 있습니다.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>CloudBuilder.vhdx에서 부팅 하도록 ASDK 호스트 컴퓨터를 구성 합니다.
이 명령은 경우 다운로드 하 고 추출 Azure 스택 가상 하드 디스크 (CloudBuilder.vhdx)에서 부팅 하도록 ASDK 호스트 컴퓨터를 구성 합니다. 다음이 단계를 완료 한 후 ASDK 호스트 컴퓨터를 다시 시작 합니다.

1. 관리자 권한으로 명령 프롬프트를 시작 합니다.
2. `bcdedit /copy {current} /d "Azure Stack"` 실행
3. 복사 (CTRL + C) CLSID 값 반환 필요한 {}를 포함 하 여 ' s입니다. 이 값은 {CLSID} 라고 하 고 나머지 단계에 (CTRL + V 또는 마우스 오른쪽 단추 클릭)에 붙여 넣이 필요 합니다.
4. `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 실행 
5. `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 실행 
6. `bcdedit /set {CLSID} detecthal on` 실행 
7. `bcdedit /default {CLSID}` 실행
8. 부팅 설정을 확인 하려면 실행 `bcdedit`합니다. 
9. CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트로 이동 되었는지와 개발 키트 호스트 컴퓨터를 다시 시작 되도록 합니다. ASDK 호스트를 다시 시작할 때 CloudBuilder.vhdx VM에서 부팅 하도록 기본 해야 것입니다. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>PowerShell을 사용 하 여 개발 키트 호스트 준비 
개발 키트 호스트 컴퓨터 성공적으로 CloudBuilder.vhdx 이미지로 부팅 한 후 관리자 권한 PowerShell 콘솔 열고 ASDK 개발 키트 호스트에 배포 하려면이 섹션의 명령을 실행할 수 있습니다.

> [!IMPORTANT] 
> ASDK 설치 네트워킹에 대 한 정확히 하나의 네트워크 인터페이스 카드 (NIC)를 지원합니다. 여러 Nic를 설정한 경우 하나만 사용 하도록 설정할 (올바르고 확인 조건은 다른 모든) 배포 스크립트를 실행 하기 전에.

Id 공급자로 Azure AD 또는 AD FS와 Azure 스택을 배포할 수 있습니다. Azure 스택, 리소스 공급자 및 다른 응용 프로그램에는 둘 다와 함께 동일한 방식으로 작동 합니다. Azure 스택에서 AD FS와 함께 지원 되는 기능에 대 한 자세한 내용은 참조는 [주요 기능 및 개념](.\azure-stack-key-features.md) 문서.

> [!TIP]
> 모든 설치 매개 변수 (InstallAzureStackPOC.ps1 선택적 매개 변수 및 아래 예제 참조)을 지정 하지 않으면 필수 매개 변수에 대해 라는 메시지가 표시 합니다.

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure AD를 사용 하 여 Azure 스택 배포 
Azure 스택을 배포 하려면 **id 공급자로 Azure AD를 사용 하 여**, 직접 또는 투명 프록시를 통해 인터넷에 연결 되어 있어야 합니다. Azure AD를 사용 하 여 개발 키트를 배포 하려면 다음 PowerShell 명령을 실행 합니다.

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  몇 분 정도 ASDK 설치에 Azure AD 자격 증명에 대 한 라는 메시지가 표시 됩니다. Azure AD 테 넌 트에 대 한 전역 관리자 자격 증명을 제공 해야 합니다. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Azure 스택 AD FS를 사용 하 여 배포 
개발 키트를 배포 하려면 **AD FS를 사용 하 여 id 공급자로**, (하기만-UseADFS 매개 변수를 추가 하면 됨) 다음 PowerShell 명령을 실행: 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

AD FS 배포는 기본 스탬프 디렉터리 서비스를 id 공급자로 사용 됩니다. 로그인 하는 데 기본 계정은 azurestackadmin@azurestack.local, 및 PowerShell 설치 명령의 일부로 제공 된 암호 설정 됩니다.

배포 프로세스에는 시간 동안 시스템이 자동으로 재부팅 한 번 몇 시간 걸릴 수 있습니다. 배포에 성공 하면 PowerShell 콘솔에 표시 됩니다: **완료: 작업 '배포'**합니다. 스크립트를 사용 하 여 다시 실행 해 수 배포가 실패 하는 경우 다시 실행된-매개 변수입니다. 할 수 있습니다 또는 [ASDK 재배포](.\azure-stack-redeploy.md) 처음부터 합니다.
> [!IMPORTANT]
> ASDK 호스트를 다시 부팅 한 후 배포 진행률을 모니터링 하려는 경우에 AzureStack\AzureStackAdmin로 로그인 해야 합니다. 로그인 되 면 로컬 관리자로 호스트 컴퓨터를 다시 시작 (고 후 azurestack.local 도메인에 가입)에 배포 진행률을 볼 수 없습니다. 대신 실행 중인 유효성을 검사 하려면 azurestack로 로그인, 배포 다시 실행 하지 마십시오.
>

#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 배포 스크립트 예제
전체를 스크립팅할 수 Azure AD 배포 합니다. 다음은 몇 가지 선택적 매개 변수를 포함 하는 주석 처리 된 몇 가지 예입니다.

Azure AD id만 연결 된 경우 **하나의** Azure AD 디렉터리:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Azure AD id와 연결 된 경우 **1 보다 큰** Azure AD 디렉터리:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

하는 경우 사용자 환경 **는 그렇지 않습니다** 가 DHCP 사용 (제공 된 예제 사용) 위의 옵션 중 하나에 다음과 같은 추가 매개 변수를 포함 해야 합니다. 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 선택적 매개 변수
|매개 변수|필수/선택|설명|
|-----|-----|-----|
|AdminPassword|필수|개발 키트 배포의 일부로 생성 되는 모든 가상 컴퓨터에서 로컬 관리자 계정 및 다른 모든 사용자 계정을 설정 합니다. 이 암호는 호스트에서 현재 로컬 관리자 암호와 일치 해야 합니다.|
|InfraAzureDirectoryTenantName|필수|테 넌 트 디렉터리를 설정합니다. AAD 계정의 사용 권한이 있는 여러 디렉터리를 관리 하는 특정 디렉터리를 지정 하려면이 매개 변수를 사용 합니다. 전체 형식의 AAD 디렉터리 테 넌 트의 이름입니다. onmicrosoft.com 또는 Azure AD에 사용자 지정 도메인 이름을 확인 합니다.|
|TimeServer|필수|이 매개 변수를 사용 하 여 특정 시간 서버를 지정 합니다. 이 매개 변수는 유효한 시간 서버 IP 주소로 제공 되어야 합니다. 서버 이름은 지원 되지 않습니다.|
|InfraAzureDirectoryTenantAdminCredential|옵션|Azure Active Directory 사용자 이름과 암호를 설정합니다. 이러한 Azure 자격 증명을 사용 하 여 조직 ID 해야 합니다.|
|InfraAzureEnvironment|옵션|이 Azure 스택 배포를 등록 하려는 Azure 환경을 선택 하십시오. 옵션에는 공용 Azure, Azure-중국 Azure-미국 정부 포함 됩니다.|
|DNSForwarder|옵션|DNS 서버는 Azure 스택 배포의 일부로 생성 됩니다. 스탬프 외부 이름을 확인 하기 위해 솔루션 내에 있는 컴퓨터를 허용 하려면 기존 DNS 서버 인프라를 제공 합니다. 스탬프에 DNS 서버는이 서버에 알 수 없는 이름 확인 요청을 전달합니다.|
|NatIPv4Address|DHCP NAT 지원에 필요한|MA BGPNAT01에 대 한 고정 IP 주소를 설정합니다. 인터넷에 액세스하기 위해 DHCP가 유효한 IP 주소를 할당할 수 없는 경우 이 매개 변수만 사용합니다.|
|NatIPv4Subnet|DHCP NAT 지원에 필요한|NAT 지원 통한 DHCP에 사용 되는 IP 서브넷 접두사입니다. 인터넷에 액세스하기 위해 DHCP가 유효한 IP 주소를 할당할 수 없는 경우 이 매개 변수만 사용합니다.|
|PublicVlanId|옵션|VLAN ID를 설정 합니다. 호스트와 MA BGPNAT01 VLAN ID 및 액세스 하는 실제 네트워크 (인터넷)를 구성 해야 하는 경우에이 매개 변수를 사용 합니다. 예를 들어.\InstallAzureStackPOC.ps1-305 PublicVLan를 자세한 정보|
|다시 실행|옵션|이 플래그를 사용 하 여 배포 다시 실행 합니다. 모든 이전 입력이 사용 됩니다. 이전에 제공 된 데이터를 다시 입력할 몇 가지 고유한 값 생성 되어 배포에 사용 하기 때문에 지원 되지 않습니다.|

## <a name="activate-the-administrator-and-tenant-portals"></a>관리자 및 테 넌 트 포털을 활성화 합니다.
를 Azure AD를 사용 하는 배포 후 Azure 스택 관리자 및 테 넌 트 포털 모두 활성화 해야 합니다. 이 활성화에 Azure 스택 포털 및 Azure 리소스 관리자 정확한 권한을 제공 (동의 페이지에 나열 된) 모든 사용자에 대 한 디렉터리의에 동의 합니다.

- 관리자 포털에 대 한 https://adminportal.local.azurestack.external/guest/signup, 정보에 따라 이동한 다음 클릭 **Accept**합니다. 를 수락한 후 디렉터리 테 넌 트 관리자가 아닌 서비스 관리자를 추가할 수 있습니다.

- 테 넌 트 포털에 대 한 https://portal.local.azurestack.external/guest/signup, 정보에 따라 이동한 다음 클릭 **Accept**합니다. 를 수락한 후 디렉터리의 사용자는 테 넌 트 포털에 로그인 수 있습니다. 

> [!NOTE] 
> 포털 활성화 되지 않으면 디렉터리 관리자만 로그인 한 포털을 사용 하 여 수 있습니다. 다른 사용자가 로그인 하는 경우는 관리자가 다른 사용자에 게 사용 권한을 부여 하지 알리는 오류가 표시 됩니다. 관리자에 등록 되어 있고 Azure 스택 디렉터리에 고유 하 게 속하지 않습니다, Azure 스택 디렉터리 활성화 URL에 추가 해야 합니다. Azure 스택 fabrikam.onmicrosoft.com 및 관리자 사용자에 등록 되어 있고이 예를 들어 admin@contoso.com, https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com 포털을 활성화 하려면로 이동 합니다. 

## <a name="reset-the-password-expiration-policy"></a>암호 만료 정책을 다시 설정 
개발 키트 호스트에 대 한 암호 평가 기간이 종료 되기 전에 만료 되지 않도록 확인을 하는 ASDK를 배포한 후 다음이 단계를 수행 합니다.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Powershell에서 암호 만료 정책 변경 하려면:
관리자 권한 Powershell 콘솔에서 명령을 실행 합니다.

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>변경 하려면 암호 만료 정책을 수동으로:
1. 개발 키트 호스트에서 엽니다 **그룹 정책 관리** 이동한 다음 **그룹 정책 관리** – **포리스트: azurestack.local** – **도메인** – **azurestack.local**합니다.
2. 마우스 오른쪽 단추로 클릭 **기본 도메인 정책** 클릭 **편집**합니다.
3. 그룹 정책 관리 편집기에서 이동 **컴퓨터 구성** – **정책** – **Windows 설정** – **보안 설정**– **계정 정책** – **암호 정책**합니다.
4. 오른쪽 창에서 두 번 클릭 **최대 암호 사용 기간**합니다.
5. 에 **최대 암호 사용 기간 속성** 변경 대화 상자는 **암호 만료** 클릭 한 다음 값을 180 **확인**합니다.


## <a name="next-steps"></a>다음 단계

[Azure Stack에 연결](azure-stack-connect-azure-stack.md)

[Azure 스택 환경에 대 한 PowerShell 설치](azure-stack-powershell-configure-quickstart.md)

[Azure 스택 Azure 구독에 등록](azure-stack-register.md)



