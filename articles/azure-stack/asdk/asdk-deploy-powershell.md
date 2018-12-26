---
title: Azure Stack PowerShell 배포 | Microsoft Docs
description: 이 문서에서는 PowerShell을 사용 하 여 명령줄에서를 ASDK를 설치 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 09/10/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c6b2387360973cd4e65b5a1e4ba483abf5ea9070
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716029"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>명령줄에서 ASDK 배포
ASDK는를 평가 하 여 Azure Stack 기능 및 서비스를 보여 줍니다. 배포할 수 있는 테스트 및 개발 환경. 하 고 실행 해야 환경의 하드웨어를 준비 하 고 (몇 시간 정도 걸릴 됩니다) 일부 스크립트를 실행 합니다. 그 후 서명할 수 있습니다 관리자 및 사용자 포털에 Azure Stack을 사용 하려면.

## <a name="prerequisites"></a>필수 조건 
개발 키트 호스트 컴퓨터를 준비 합니다. 에 하드웨어, 소프트웨어 및 네트워크를 계획 합니다. 개발 키트 (개발 키트 호스트)를 호스트 하는 컴퓨터 하드웨어, 소프트웨어 및 네트워크 요구 사항을 충족 해야 합니다. Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)를 사용할지도 선택 해야 합니다. 설치 프로세스를 원활 하 게 실행 되도록 배포를 시작 하기 전에 이러한 필수 구성이 요소를 준수 해야 합니다. 

ASDK를 배포 하기 전에 계획 된 개발 키트 호스트 컴퓨터의 하드웨어, 운영 체제, 계정 및 네트워크 구성의 ASDK를 설치 하기 위한 최소 요구 사항을 충족 해야 합니다.

**[ASDK 배포 계획 고려 사항 검토](asdk-deploy-considerations.md)**

> [!TIP]
> 사용할 수는 [Azure Stack 배포 요구 사항 확인 도구](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) 하드웨어 요구 사항을 모두 충족 하는지 확인 하려면 운영 체제를 설치한 후 합니다.

## <a name="download-and-extract-the-deployment-package"></a>다운로드 하 고 배포 패키지를 추출 합니다.
개발 키트 호스트 컴퓨터를 ASDK를 설치 하기 위한 기본 요구를 충족 하는지 확인에 한 후 다음 단계를 다운로드 하 여 ASDK 배포 패키지를 추출 됩니다. 배포 패키지는 부팅 가능한 운영 체제 및 Azure Stack 설치 파일을 포함 하는 가상 하드 드라이브는 Cloudbuilder.vhdx 파일을 포함 합니다.

개발 키트 호스트 또는 다른 컴퓨터에 배포 패키지를 다운로드할 수 있습니다. 다른 컴퓨터를 사용 하 여 개발 키트 호스트에 대 한 하드웨어 요구 사항을 줄일 수 있도록 추출된 배포 파일 60GB의 사용 가능한 디스크 공간을 차지 합니다.

**[다운로드 하 고 Azure Stack 개발 키트 (ASDK)를 추출 합니다.](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>개발 키트 호스트 컴퓨터를 준비 합니다.
ASDK 호스트 컴퓨터에 설치할 수 전에 환경을 준비 해야 하 고 시스템 VHD에서 부팅 하도록 합니다. 이 단계를 수행 하면 개발 키트 호스트 (가상 하드 드라이브를 부팅 가능한 운영 체제 및 Azure Stack 설치 파일을 포함 하는) Cloudbuilder.vhdx 부팅 됩니다.

CloudBuilder.vhdx에서 부팅 하도록 ASDK 호스트 컴퓨터를 구성 하려면 PowerShell을 사용 합니다. 이러한 명령은 ASDK 호스트 컴퓨터의 다운로드 및 추출 된 Azure Stack 가상 하드 디스크 (CloudBuilder.vhdx)에서 부팅 하도록 구성 합니다. 다음이 단계를 완료 한 후 ASDK 호스트 컴퓨터를 다시 시작 합니다.

컴퓨터를 구성 하려면 ASDK 호스트 CloudBuilder.vhdx에서 부팅 하도록 합니다.

  1. 관리자 권한으로 명령 프롬프트를 시작 합니다.
  2. `bcdedit /copy {current} /d "Azure Stack"` 실행
  3. 복사 (CTRL + C) CLSID 값 반환 등 필요한 {}' s입니다. 이 값 {CLSID} 라고 하 고 (CTRL + V 또는 마우스 오른쪽 단추로 클릭)에서 나머지 단계에서 붙여 넣을 수 해야 합니다.
  4. `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 실행 
  5. `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 실행 
  6. `bcdedit /set {CLSID} detecthal on` 실행 
  7. `bcdedit /default {CLSID}` 실행
  8. 부팅 설정을 확인 하려면 실행 `bcdedit`합니다. 
  9. CloudBuilder.vhdx 파일 (C:\CloudBuilder.vhdx) C:\ 드라이브의 루트에 옮겨졌고 개발 키트 호스트 컴퓨터를 다시 시작 하는지 확인 합니다. ASDK 호스트 컴퓨터를 다시 시작할 때에 ASDK 배포를 시작 하려면 CloudBuilder.vhdx 가상 컴퓨터 하드 드라이브에서 부팅 되어야 합니다. 

> [!IMPORTANT]
> 다시 시작 하기 전에 직접 물리적 컴퓨터 또는 KVM 액세스 개발 키트 호스트 컴퓨터에 있는지 확인 합니다. VM이 처음 시작 되 면 Windows Server 설치를 완료 하 라는 나타납니다. 개발 키트 호스트 컴퓨터에 로그온 할 때 사용한 동일한 관리자 자격 증명을 제공 합니다. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>PowerShell을 사용 하 여 개발 키트 호스트 준비 
개발 키트 후 호스트 컴퓨터 성공적으로 부팅 CloudBuilder.vhdx 이미지로 개발 키트 호스트 컴퓨터에 로그온 할 때 사용한 (및 Windows Server를 종료의 일부분으로 제공 하는 동일한 로컬 관리자 자격 증명으로 로그인 호스트 컴퓨터가 VHD에서 부팅 하는 경우 설정). 

> [!NOTE]
> 선택적으로 구성할 수도 있습니다 [Azure Stack 원격 분석 설정](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *전에* ASDK를 설치 합니다.

관리자 권한 PowerShell 콘솔을 열고 개발 키트 호스트에서 ASDK를 배포 하려면이 섹션의 명령을 실행 합니다.

> [!IMPORTANT] 
> ASDK 설치 네트워킹에 대 한 하나의 네트워크 인터페이스 카드 (NIC)를 지원합니다. 여러 Nic를 만든 경우는 하나만 사용 하도록 설정 (및 다른 모든 사용 하지 않는) 배포 스크립트를 실행 하기 전에 있는지 확인 합니다.

Azure AD 사용 하 여 Azure Stack을 배포할 수 있습니다 또는 Windows Server AD FS를 id 공급자로 합니다. Azure Stack에서 리소스 공급자 및 기타 응용 프로그램 모두를 사용 하 여 동일한 방식으로 작동 합니다.

> [!TIP]
> 설치 매개 변수 (InstallAzureStackPOC.ps1 선택적 매개 변수 및 아래 예제 참조)을 지정 하지 않으면 필수 매개 변수에 대 한 메시지가 표시 됩니다.

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure AD를 사용 하 여 Azure Stack 배포 
Azure Stack을 배포 하려면 **Azure AD를 id 공급자로 사용 하 여**, 직접적으로 또는 투명 프록시를 통해 인터넷에 연결 해야 합니다. 

Azure AD를 사용 하 여 개발 키트를 배포 하려면 다음 PowerShell 명령을 실행 합니다.

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

잠시 후 ASDK 설치에 Azure AD 자격 증명에 대 한 라는 메시지가 표시 됩니다. Azure AD 테 넌 트에 대 한 전역 관리자 자격 증명을 제공 해야 합니다. 

배포 후 Azure Active Directory 전역 관리자 권한이 필요 하지 않습니다. 그러나 일부 작업에는 전역 관리자 자격 증명을 필요할 수 있습니다. 예를 들어, 리소스 공급자 설치 관리자 스크립트 또는 권한 부여 요청을 새로운 기능. 일시적으로 계정의 전역 관리자 권한이 다시 설정 하거나의 소유자가 별도 전역 관리자 계정을 사용 합니다 *공급자 구독을 기본*입니다.

### <a name="deploy-azure-stack-using-ad-fs"></a>AD FS를 사용 하 여 Azure Stack 배포 
개발 키트 배포 **AD FS를 사용 하 여 id 공급자로**, (방금-UseADFS 매개 변수를 추가 해야 함) 다음 PowerShell 명령을 실행 합니다. 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

AD FS 배포는 기본 스탬프 디렉터리 서비스를 id 공급자로 사용 됩니다. 로그인 하는 데 기본 계정은 azurestackadmin@azurestack.local, 암호 PowerShell 설치 명령의 일부로 제공 되는 것으로 설정 됩니다.

배포 프로세스에는 시간 동안 시스템이 자동으로 다시 부팅 되 면 몇 시간 걸릴 수 있습니다. 배포에 성공 하면 PowerShell 콘솔에 표시 됩니다. **완료: '배포' 작업**합니다. 배포가 실패 하는 경우 다시 사용 하는 스크립트를 실행 해 볼 수 있습니다-다시 실행된 매개 변수입니다. 수도 있습니다 [ASDK 재배포](asdk-redeploy.md) 부터.

> [!IMPORTANT]
> ASDK 호스트 다시 부팅 한 후 배포 진행률을 모니터링 하려는 경우 AzureStack\AzureStackAdmin로 로그인 해야 합니다. 로컬 관리자로 호스트 컴퓨터를 다시 시작 (이며 azurestack.local 도메인에 가입) 후에 로그인 하는 경우에 배포 진행률을 볼 수 없습니다. 대신 실행 되는지 유효성을 검사 하려면 azurestack로 로그인, 배포를 다시 실행 되지 않습니다.


#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 배포 스크립트 예제
전체를 스크립팅할 수 있습니다 Azure AD 배포 합니다. 일부 선택적 매개 변수를 포함 하는 주석 처리 된 몇 가지 예는 다음과 같습니다.

Azure AD id와만 연결 된 경우 **하나의** Azure AD 디렉터리:
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

사용자 환경에 DHCP 사용이 없는 경우 (예제 사용량 제공) 위의 옵션 중 하나에 다음과 같은 추가 매개 변수를 포함 해야 합니다. 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 선택적 매개 변수
|매개 변수|필수/선택|설명|
|-----|-----|-----|
|AdminPassword|필수|개발 키트 배포의 일환으로 만든 모든 가상 컴퓨터에서 로컬 관리자 계정 및 다른 모든 사용자 계정을 설정 합니다. 이 암호에는 호스트에서 현재 로컬 관리자 암호와 일치 해야 합니다.|
|InfraAzureDirectoryTenantName|필수|테 넌 트 디렉터리를 설정합니다. AAD 계정이 여러 디렉터리를 관리할 수 있는 권한이 있는 특정 디렉터리를 지정 하려면이 매개 변수를 사용 합니다. AAD 디렉터리 테 넌 트 형식으로 전체 이름입니다. onmicrosoft.com 또는 Azure AD에 사용자 지정 도메인 이름을 확인 합니다.|
|TimeServer|필수|이 매개 변수를 사용 하 여 특정 시간 서버를 지정 합니다. 이 매개 변수는 유효한 시간 서버 IP 주소로 제공 되어야 합니다. 서버 이름은 지원 되지 않습니다.|
|InfraAzureDirectoryTenantAdminCredential|옵션|Azure Active Directory 사용자 이름 및 암호를 설정합니다. 이러한 Azure 자격 증명에는 조직 ID를 사용 해야 합니다.|
|InfraAzureEnvironment|옵션|이 Azure Stack 배포를 등록 하려는 Azure 환경을 선택 합니다. 옵션에는 공용 Azure, Azure 중국, Azure US Government 포함 됩니다.|
|DNSForwarder|옵션|DNS 서버는 Azure Stack 배포의 일부로 생성 됩니다. 스탬프 외부 이름을 확인 하기 위해 솔루션 내에서 컴퓨터를 허용 하려면 기존 인프라 DNS 서버를 제공 합니다. 타임 스탬프에서 DNS 서버는이 서버에 알 수 없는 이름 확인 요청을 전달합니다.|
|NatIPv4Address|DHCP NAT 지원에 필요한|MAS-BGPNAT01에 대 한 고정 IP 주소를 설정합니다. 인터넷에 액세스하기 위해 DHCP가 유효한 IP 주소를 할당할 수 없는 경우 이 매개 변수만 사용합니다.|
|NatIPv4Subnet|DHCP NAT 지원에 필요한|NAT 지원을 통한 DHCP에 사용 되는 IP 서브넷 접두사입니다. 인터넷에 액세스하기 위해 DHCP가 유효한 IP 주소를 할당할 수 없는 경우 이 매개 변수만 사용합니다.|
|PublicVlanId|옵션|VLAN ID를 설정합니다. 호스트와 MAS-BGPNAT01 실제 네트워크 (및 인터넷)에 액세스 하는 VLAN ID를 구성 해야 하는 경우에이 매개 변수를 사용 합니다. 예를 들어.\InstallAzureStackPOC.ps1-Verbose PublicVLan 305|
|다시 실행|옵션|이 플래그를 사용 하 여 배포를 다시 실행 합니다. 모든 이전 입력이 됩니다. 이전에 제공 된 데이터를 다시 입력에 몇 가지 고유한 값 생성 및 배포에 대 한 사용 하기 때문에 지원 되지 않습니다.|


## <a name="perform-post-deployment-configurations"></a>배포 후 구성을 수행합니다
ASDK는를 설치한 후는 몇 가지 권장 되는 사후 설치 검사 및 구성 변경 해야 합니다. 설치의 유효성을 검사 성공적으로 테스트 AzureStack cmdlet을 사용 하 여 설치한 및 Azure Stack PowerShell 및 GitHub 도구를 설치 합니다. 

또한 개발 키트 호스트에 대 한 암호에 평가 기간이 종료 되기 전에 만료 되지 않습니다는 되도록 암호 만료 정책 다시 설정 해야 합니다.

> [!NOTE]
> 선택적으로 구성할 수도 있습니다 [원격 분석 설정 Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *후* ASDK를 설치 합니다.

**[ASDK 배포 후 작업](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Azure를 사용 하 여 등록
수행할 수 있도록 Azure를 사용 하 여 Azure Stack 등록 해야 합니다 [Azure marketplace 항목 다운로드](asdk-marketplace-item.md) Azure Stack에 있습니다.

**[Azure를 사용 하 여 Azure Stack 등록](asdk-register.md)**

## <a name="next-steps"></a>다음 단계
축하합니다! 개발 키트 환경 둘 다를 사용 하 여 이러한 단계를 완료 해야 [관리자](https://adminportal.local.azurestack.external) 하 고 [사용자](https://portal.local.azurestack.external) 포털입니다. 

[ASDK 설치 후 구성 작업](asdk-post-deploy.md)

