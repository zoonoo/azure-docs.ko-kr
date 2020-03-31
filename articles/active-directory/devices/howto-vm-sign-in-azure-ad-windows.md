---
title: Azure Active Directory(미리 보기)를 사용하여 Azure의 Windows 가상 컴퓨터에 로그인
description: Windows를 실행하는 Azure VM에 Azure AD 로그인
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050071"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Azure Active Directory 인증을 사용하여 Azure의 Windows 가상 컴퓨터에 로그인(미리 보기)

이제 조직은 **Windows Server 2019 데이터 센터 버전** 또는 Windows **10 1809** 이상을 실행하는 Azure 가상 시스템(VM)에 대한 Azure Active Directory(AD) 인증을 사용할 수 있습니다. Azure AD를 사용하여 VM에 인증하면 정책을 중앙에서 제어하고 적용할 수 있습니다. RBAC(Azure 역할 기반 액세스 제어) 및 Azure AD 조건부 액세스와 같은 도구를 사용하면 VM에 액세스할 수 있는 사람을 제어할 수 있습니다. 이 문서에서는 Azure AD 인증을 사용하도록 Windows Server 2019 VM을 만들고 구성하는 방법을 보여 주며 있습니다.

|     |
| --- |
| Azure Windows VM에 대한 Azure AD 로그인은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

Azure AD 인증을 사용하여 Azure의 Windows VM에 로그인하면 다음과 같은 많은 이점이 있습니다.

- 일반적으로 사용하는 것과 동일한 페더레이션 또는 관리되는 Azure AD 자격 증명을 활용합니다.
- 더 이상 로컬 관리자 계정을 관리할 필요가 없습니다.
- Azure RBAC를 사용하면 필요에 따라 VM에 대한 적절한 액세스 권한을 부여하고 더 이상 필요하지 않은 경우 제거할 수 있습니다.
- VM에 대한 액세스를 허용하기 전에 Azure AD 조건부 액세스는 다음과 같은 추가 요구 사항을 적용할 수 있습니다. 
   - Multi-Factor Authentication
   - 로그인 위험 확인
- VDI 배포의 일부인 Azure Windows VM의 Azure AD 조인을 자동화하고 확장합니다.

> [!NOTE]
> 이 기능을 활성화하면 Azure의 Windows VM이 Azure AD조가 됩니다. 온-프레미스 AD 또는 Azure AD DS와 같은 다른 도메인에 가입할 수 없습니다. 필요한 경우 확장을 제거하여 Azure AD 테넌트에서 VM연결을 끊어야 합니다.

## <a name="requirements"></a>요구 사항

### <a name="supported-azure-regions-and-windows-distributions"></a>지원되는 Azure 지역 및 Windows 배포판

다음 Windows 배포는 현재 이 기능을 미리 볼 때 지원됩니다.

- Windows Server 2019 Datacenter
- 윈도우 10 1809 이상

> [!IMPORTANT]
> Azure AD에 조인된 VM에 대한 원격 연결은 Azure AD가 조인된 Windows 10 PC또는 하이브리드 Azure AD가 VM과 **동일한** 디렉터리로 조인된 경우에만 허용됩니다. 

현재 이 기능의 미리 보기 기간 동안 다음과 같은 Azure 지역이 지원됩니다.

- 모든 Azure 전역 리전

> [!IMPORTANT]
> 이 미리 보기 기능을 사용하려면 지원되는 Windows 배포 및 지원되는 Azure 리전에서만 배포합니다. 이 기능은 현재 Azure 정부 또는 주권 클라우드에서 지원되지 않습니다.

### <a name="network-requirements"></a>네트워크 요구 사항

Azure의 Windows VM에 대한 Azure AD 인증을 사용하려면 VM 네트워크 구성에서 TCP 포트 443을 통해 다음 끝점에 대한 아웃바운드 액세스를 허용해야 합니다.

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure에서 Windows VM에 대한 Azure AD 로그인 사용

Azure에서 Windows VM에 대해 Azure AD 로그인을 사용하려면 먼저 Windows VM에 Azure AD 로그인 옵션을 사용하도록 설정한 다음 VM에 로그인할 권한이 있는 사용자에 대해 RBAC 역할 할당을 구성해야 합니다.
Windows VM에 대해 Azure AD 로그인을 사용하도록 설정할 수 있는 방법에는 여러 가지가 있습니다.

- Windows VM을 만들 때 Azure 포털 환경 사용
- Windows VM또는 기존 Windows **VM에 대해** Azure 클라우드 셸 환경을 사용

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure 포털을 사용하여 VM 환경을 만들어 Azure AD 로그인을 사용하도록 설정합니다.

Windows Server 2019 데이터 센터 또는 Windows 10 1809 및 이후 VM 이미지용 Azure AD 로그인을 활성화할 수 있습니다. 

Azure AD 로그온을 사용하여 Azure에서 Windows Server 2019 데이터 센터 VM을 만들려면 다음을 수행하십시오. 

1. [Azure 포털에](https://portal.azure.com)로그인하여 VM을 만들 수 있는 액세스 권한이 있는 계정으로 로그인하고 **+ 리소스 만들기를**선택합니다.
1. 마켓플레이스 검색 창에서 **Windows 서버를** 입력합니다.
   1. **Windows 서버를** 클릭하고 소프트웨어 계획 드롭다운 선택에서 **Windows Server 2019 데이터 센터를** 선택합니다.
   1. 을 **클릭합니다.**
1. "관리" 탭에서 Azure Active Directory 섹션에서 **AAD 자격 증명(미리 보기)으로 로그인하는** 옵션을 사용 **설정합니다.**
1. ID 섹션에서 **관리되는 ID가** **On으로**설정되어 있는지 확인합니다. 이 작업은 Azure AD 자격 증명으로 로그인을 활성화하면 자동으로 수행됩니다.
1. 가상 컴퓨터를 만드는 경험의 나머지 부분을 통해 이동합니다. 이 미리 보기 중에 VM에 대한 관리자 사용자 이름과 암호를 만들어야 합니다.

![Azure AD 자격 증명으로 로그인하면 VM이 생성됩니다.](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Azure AD 자격 증명을 사용하여 VM에 로그인하려면 먼저 아래 섹션 중 하나에 설명된 대로 VM에 대한 역할 할당을 구성해야 합니다.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure 클라우드 셸 환경을 사용하여 Azure AD 로그인을 활성화합니다.

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 계정에서 사용할 수 있도록 공용 Azure 도구가 Cloud Shell에 사전 설치 및 구성되어 있습니다. 간단하게 복사 단추를 선택하여 코드를 복사하고, Cloud Shell에 붙여넣고, Enter 키를 눌러 실행하면 됩니다. Cloud Shell을 여는 몇 가지 방법이 있습니다.

코드 블록의 오른쪽 위 모서리에서 사용을 선택합니다.
브라우저에서 Cloud Shell을 엽니다.
[Azure 포털의](https://portal.azure.com)오른쪽 위 모서리에 있는 메뉴에서 클라우드 셸 단추를 선택합니다.

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.31 이상을 실행해야 합니다. 버전을 찾으려면 az --version을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)문서를 참조하십시오.

1. [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만듭니다. 
1. 지원되는 리전에서 지원되는 배포를 사용하여 [az vm 이 만드는 VM을](/cli/azure/vm#az-vm-create) 만듭니다. 
1. Azure AD 로그인 VM 확장을 설치합니다. 

다음 예제에서는 Win2019Datacenter를 사용하는 myVM이라는 VM을 중남부 지역의 myResourceGroup이라는 리소스 그룹에 배포합니다. 다음 예제에서는 필요에 따라 고유한 리소스 그룹 및 VM 이름을 제공할 수 있습니다.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Azure AD 로그인 VM 확장을 설치하기 전에 가상 시스템에서 System 할당된 관리ID를 사용하도록 설정해야 합니다.

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다.

마지막으로 Azure AD 로그인 VM 확장을 설치하여 Windows VM용 Azure AD 로그인을 활성화합니다. VM 확장은 Azure 가상 머신에서 배포 후 구성 및 Automation 작업을 제공하는 작은 애플리케이션입니다. [az vm 확장](/cli/azure/vm/extension#az-vm-extension-set) 집합을 사용하여 myResourceGroup 리소스 그룹의 myVM이라는 VM에 AADLoginForWindows 확장을 설치합니다.

> [!NOTE]
> 기존 Windows 서버 2019 또는 Windows 10 1809 및 이후 VM에 AADLoginForWindows 확장을 설치하여 Azure AD 인증을 사용하도록 설정할 수 있습니다. AZ CLI의 예는 다음과 같습니다.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`Succeeded` 의 는 `provisioningState` 확장이 VM에 설치되면 표시됩니다.

## <a name="configure-role-assignments-for-the-vm"></a>VM에 대한 역할 할당 구성

VM을 만들었으니 VM에 로그인할 수 있는 사람을 결정하기 위해 Azure RBAC 정책을 구성해야 합니다. 두 개의 RBAC 역할을 사용하여 VM 로그인 권한을 부여합니다.

- **가상 컴퓨터 관리자 로그인**: 이 역할이 할당된 사용자는 관리자 권한이 있는 Azure 가상 컴퓨터에 로그인할 수 있습니다.
- **가상 머신 사용자 로그인**: 이 역할이 할당된 사용자는 일반 사용자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.

> [!NOTE]
> 사용자가 RDP를 통해 VM에 로그인할 수 있도록 하려면 가상 컴퓨터 관리자 로그인 또는 가상 컴퓨터 사용자 로그인 역할을 할당해야 합니다. VM에 할당된 소유자 또는 기여자 역할이 있는 Azure 사용자는 RDP를 통해 VM에 로그인할 수 있는 권한이 자동으로 없습니다. 이는 가상 컴퓨터를 제어하는 사용자 집합과 가상 시스템에 액세스할 수 있는 사용자 집합 간에 감사된 구분을 제공하기 위한 것입니다.

VM에 대한 역할 할당을 구성할 수 있는 방법에는 여러 가지가 있습니다.

- Azure AD 포털 환경 사용
- Azure 클라우드 셸 환경 사용

### <a name="using-azure-ad-portal-experience"></a>Azure AD 포털 환경 사용

Azure AD 지원 Windows Server 2019 데이터 센터 VM에 대한 역할 할당을 구성하려면 다음을 수행하십시오.

1. 특정 가상 컴퓨터 개요 페이지로 이동
1. 메뉴 옵션에서 **IAM(액세스 제어)을** 선택합니다.
1. 역할 **할당 추가를** 선택하여 역할 할당 추가 창을 엽니다. **Add**
1. **역할** 드롭다운 목록에서 가상 컴퓨터 관리자 **로그인** 또는 가상 **컴퓨터 사용자 로그인과**같은 역할을 선택합니다.
1. **선택** 필드에서 사용자, 그룹, 서비스 주체 또는 관리되는 ID를 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.
1. 역할을 할당하려면 **저장을**선택합니다.

몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

![VM에 액세스할 사용자에게 역할 할당](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Azure 클라우드 셸 환경 사용

다음 예제에서는 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용하여 현재 Azure 사용자의 VM에 대한 가상 머신 관리자 로그인 역할을 할당합니다. 활성 Azure 계정의 사용자 이름은 [az account show](/cli/azure/account#az-account-show)를 사용하여 가져옵니다. 또한 범위는 [az vm show](/cli/azure/vm#az-vm-show)를 사용하여 이전 단계에서 만든 VM으로 설정됩니다. 또한 리소스 그룹이나 구독 수준에서 범위를 할당할 수 있고 정상 RBAC 상속 사용 권한이 적용됩니다. 자세한 내용은 [역할 기반 액세스 제어를](../../virtual-machines/linux/login-using-aad.md)참조하십시오.

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD 도메인 및 로그온 사용자 이름 도메인이 일치하지 않는 경우 `--assignee-object-id` `--assignee`에 대한 사용자 이름뿐만 아니라 을 사용 하여 사용자 계정의 개체 ID를 지정해야 합니다. [az ad user list](/cli/azure/ad/user#az-ad-user-list)를 사용하여 사용자 계정의 개체 ID를 가져올 수 있습니다.

RBAC를 사용하여 Azure 구독 리소스에 대한 액세스를 관리하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [RBAC 및 Azure CLI를 사용하여 Azure 리소스에 대한 액세스 관리](/azure/role-based-access-control/role-assignments-cli)
- [RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 액세스 관리](/azure/role-based-access-control/role-assignments-portal)
- [RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 액세스를 관리합니다.](/azure/role-based-access-control/role-assignments-powershell)

## <a name="using-conditional-access"></a>조건부 액세스 사용

Azure AD 로그인으로 활성화된 Azure의 Windows VM에 대한 액세스를 승인하기 전에 다단계 인증 또는 사용자 로그인 위험 검사와 같은 조건부 액세스 정책을 적용할 수 있습니다. 조건부 액세스 정책을 적용하려면 클라우드 앱 또는 작업 할당 옵션에서 "Azure Windows VM 로그인" 앱을 선택한 다음 로그인 위험을 조건으로 사용하거나 권한 부여 액세스 제어로 다단계 인증을 요구해야 합니다. 

> [!NOTE]
> "Azure Windows VM 로그인" 앱에 대한 액세스를 요청하기 위한 권한 부여 액세스 제어로 "다단계 인증 필요"를 사용하는 경우 RDP 세션을 대상으로 시작하는 클라이언트의 일부로 다단계 인증 클레임을 제공해야 합니다. Azure의 VM입니다. Windows 10 클라이언트에서 이 작업을 달성하는 유일한 방법은 RDP 클라이언트에서 비즈니스 PIN 또는 생체 인식 인증에 Windows Hello를 사용하는 것입니다. 생체 인증에 대한 지원은 Windows 10 버전 1809의 RDP 클라이언트에 추가되었습니다. 비즈니스용 Windows Hello 인증을 사용하는 원격 데스크톱은 인증서 신뢰 모델을 사용하고 현재 키 신뢰 모델에 사용할 수 없는 배포에만 사용할 수 있습니다.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Windows VM에 Azure AD 자격 증명을 사용하여 로그인

> [!IMPORTANT]
> Azure AD에 조인된 VM에 대한 원격 연결은 Azure AD가 조인된 Windows 10 PC또는 하이브리드 Azure AD가 VM과 **동일한** 디렉터리로 조인된 경우에만 허용됩니다. 또한 Azure AD 자격 증명을 사용하는 RDP의 경우 사용자는 두 RBAC 역할 중 하나인 가상 컴퓨터 관리자 로그인 또는 가상 컴퓨터 사용자 로그인 중 하나에 속해야 합니다. 현재 Azure Bastion은 AADLoginForWindows 확장을 사용하여 Azure Active Directory 인증을 사용하여 로그인하는 데 사용할 수 없습니다. 직접 RDP만 지원됩니다.

Azure AD를 사용하여 Windows Server 2019 가상 컴퓨터에 로그인하려면 다음을 수행합니다. 

1. Azure AD 로그온으로 활성화된 가상 시스템의 개요 페이지로 이동합니다.
1. 가상 컴퓨터 블레이드에 연결을 열려면 **연결을** 선택합니다.
1. **RDP 파일 다운로드를**선택합니다.
1. 원격 데스크톱 연결 클라이언트를 실행하려면 **열기를** 선택합니다.
1. **연결을** 선택하여 Windows 로그온 대화 상자를 시작합니다.
1. Azure AD 자격 증명을 사용하여 로그온합니다.

이제 VM 사용자 또는 VM 관리자와 같이 할당된 역할 사용 권한이 있는 Windows Server 2019 Azure 가상 시스템에 로그인하게 됩니다. 

> [!NOTE]
> 을 저장할 수 있습니다. RdP 파일은 Azure 포털의 가상 시스템 개요 페이지로 이동하지 않고 연결 옵션을 사용하지 않고 가상 시스템에 대한 향후 원격 데스크톱 연결을 시작하기 위해 컴퓨터에 로컬로 표시됩니다.

## <a name="troubleshoot"></a>문제 해결

### <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

VM이 Azure AD 조인 프로세스를 완료하려면 AADLoginForWindows 확장이 성공적으로 설치되어야 합니다. VM 확장을 올바르게 설치하지 못한 경우 다음 단계를 수행합니다.

1. 로컬 관리자 계정을 사용하여 VM에 RDP를 검사하고 아래의 CommandExecuti'n.log를 검사합니다.  
   
   C:\WindowsAzure\로그\플러그인\마이크로소프트.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > 초기 오류 후 확장이 다시 시작되면 배포 오류가 있는 로그가 CommandExecution_YYYYMMDDHHMMSSSSS.log로 저장됩니다. "
1. VM에서 명령 프롬프트를 열고 Azure 호스트에서 실행 중인 IMDS(인스턴스 메타데이터 서비스) 끝점에 대해 이러한 쿼리를 확인합니다.

   | 실행 명령 | 예상 출력 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Azure VM에 대한 올바른 정보 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Azure 구독과 연결된 유효한 테넌트 ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | 이 VM에 할당된 관리되는 ID에 대해 Azure Active Directory에서 발급한 유효한 액세스 토큰 |

   > [!NOTE]
   > 액세스 토큰은 와 같은 [http://calebb.net/](http://calebb.net/)도구를 사용하여 디코딩할 수 있습니다. 액세스 토큰의 "appid"가 VM에 할당된 관리ID와 일치하는지 확인합니다.

1. 명령줄을 사용하여 VM에서 필요한 끝점에 액세스할 수 있는지 확인합니다.
   
   - 컬 https:\//login.microsoftonline.com/ -D –
   - 컬 https:\/`<TenantID>`/login.microsoftonline.com/ / -D –

   > [!NOTE]
   > Azure `<TenantID>` 구독과 연결된 Azure AD 테넌트 ID로 바꿉습니다.

   - 컬 https:\//enterpriseregistration.windows.net/ -D -
   - 컬 https:\//device.login.microsoftonline.com/ -D -
   - 컬 https:\//pas.windows.net/ -D -

1. 를 실행하여 `dsregcmd /status`장치 상태를 볼 수 있습니다. 목표는 장치 상태를 로 `AzureAdJoined : YES`표시하는 것입니다.

   > [!NOTE]
   > Azure AD 조인 활동은 사용자 장치 등록\관리 로그 아래의 이벤트 뷰어에서 캡처됩니다.

AADLoginForWindows 확장이 특정 오류 코드에서 실패하면 다음 단계를 수행할 수 있습니다.

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>문제 1: AADLoginForWindows 확장 터미널 오류 코드 '1007' 및 종료 코드와 함께 설치 하지 못합니다: -2145648574.

이 엑시트 코드는 확장이 Azure AD 테넌트 정보를 쿼리할 수 없기 때문에 DSREG_E_MSI_TENANTID_UNAVAILABLE 변환됩니다.

1. Azure VM이 인스턴스 메타데이터 서비스에서 TenantID를 검색할 수 있는지 확인합니다.

   - RDP를 로컬 관리자로 VM에 반환하고 VM의 높은 명령줄에서 이 명령을 실행하여 끝점이 유효한 테넌트 ID를 반환하는지 확인합니다.
      
      - 컬 -H 메타 데이터 :truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM 관리자는 AADLoginForWindows 확장을 설치하려고 시도하지만 관리되는 ID가 할당된 시스템에서 VM을 먼저 사용하도록 설정하지 않았습니다. VM의 ID 블레이드로 이동합니다. 시스템 지정 탭에서 상태가 켜지는지 확인합니다.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>문제 2: AADLoginForWindows 확장 종료 코드와 함께 설치 하지 못합니다: -2145648607

이 Exit 코드는 확장이 `https://enterpriseregistration.windows.net` 끝점에 도달할 수 없기 때문에 DSREG_AUTOJOIN_DISC_FAILED 변환됩니다.

1. 명령줄을 사용하여 VM에서 필요한 끝점에 액세스할 수 있는지 확인합니다.

   - 컬 https:\//login.microsoftonline.com/ -D –
   - 컬 https:\/`<TenantID>`/login.microsoftonline.com/ / -D –
   
   > [!NOTE]
   > Azure `<TenantID>` 구독과 연결된 Azure AD 테넌트 ID로 바꿉습니다. 테넌트 ID를 찾아야 하는 경우 계정 이름 위로 마우스를 가져가디렉터/테넌트 ID를 얻거나 Azure Portal에서 Azure Active Directory > 속성 > 디렉터리 ID를 선택할 수 있습니다.

   - 컬 https:\//enterpriseregistration.windows.net/ -D -
   - 컬 https:\//device.login.microsoftonline.com/ -D -
   - 컬 https:\//pas.windows.net/ -D -

1. "호스트를 `<URL>`확인할 수 없습니다"로 명령이 실패하면 이 명령을 실행하여 VM에서 사용 중인 DNS 서버를 확인합니다.
   
   `nslookup <URL>`

   > [!NOTE] 
   > "login.microsoftonline.com"과 같이 끝점에서 사용되는 정규화된 도메인 이름으로 바꿉니다. `<URL>`

1. 다음으로 공용 DNS 서버를 지정하면 명령이 성공할 수 있는지 확인합니다.

   `nslookup <URL> 208.67.222.222`

1. 필요한 경우 Azure VM이 속한 네트워크 보안 그룹에 할당된 DNS 서버를 변경합니다.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>문제 3: AADLoginForWindows 확장 종료 코드와 함께 설치 하지 못합니다: 51

종료 코드 51은 "이 확장은 VM의 운영 체제에서 지원되지 않음"으로 변환됩니다.

공개 미리 보기에서 AADLoginForWindows 확장은 Windows 서버 2019 또는 Windows 10(빌드 1809 이상)에만 설치하도록 되어 있습니다. Windows 버전이 지원되는지 확인합니다. Windows 빌드가 지원되지 않으면 VM 확장을 제거합니다.

### <a name="troubleshoot-sign-in-issues"></a>로그인 문제 해결

Azure AD 자격 증명을 사용하여 RDP를 시도할 때 몇 가지 일반적인 오류에는 할당된 RBAC 역할, 권한이 없는 클라이언트 또는 2FA 로그인 메서드가 필요하지 않습니다. 다음 정보를 사용하여 이러한 문제를 해결합니다.

를 실행하여 `dsregcmd /status`장치 및 SSO 상태를 볼 수 있습니다. 목표는 장치 상태를 표시하고 `AzureAdJoined : YES` `SSO State` 표시하는 `AzureAdPrt : YES`것입니다.

또한 Azure AD 계정을 사용하는 RDP 로그인은 AAD\운영 이벤트 로그 아래의 이벤트 뷰어에서 캡처됩니다.

#### <a name="rbac-role-not-assigned"></a>RBAC 역할 할당되지 않음

VM에 대한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시되는 경우: 

- 계정이 이 장치를 사용하지 못하도록 구성되어 있습니다. 자세한 내용은 시스템 관리자에게 문의하십시오.

![계정이 이 장치를 사용하지 못하도록 구성되어 있습니다.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

사용자에게 가상 컴퓨터 관리자 로그인 또는 가상 컴퓨터 사용자 로그인 역할을 부여하는 VM에 대한 RBAC 정책을 [구성했는지](../../virtual-machines/linux/login-using-aad.md) 확인합니다.
 
#### <a name="unauthorized-client"></a>승인되지 않은 클라이언트

VM에 대한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시되는 경우: 

- 자격 증명이 작동하지 않음

![자격 증명이 작동하지 않음](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

원격 데스크톱 연결을 시작하는 데 사용하는 Windows 10 PC가 Azure AD 조인된 PC이거나 VM이 조인된 동일한 Azure AD 디렉터리에 하이브리드 Azure AD가 조인된 PC인지 확인합니다. 장치 ID에 대한 자세한 내용은 [장치 ID입니다.](/azure/active-directory/devices/overview)

> [!NOTE]
> Windows 10 20H1은 Azure AD 등록 PC에 대한 지원을 추가하여 VM에 대한 원격 데스크톱 연결을 시작합니다. Windows 참가자 프로그램에 가입하여 Windows 10의 새로운 기능을 살펴보십시오.

또한 Azure AD 조인이 완료된 후 AADLoginForWindows 확장이 제거되지 않았는지 확인합니다.
 
#### <a name="mfa-sign-in-method-required"></a>MFA 로그인 방법 필요

VM에 대한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시되는 경우: 

- 사용하려는 로그인 메서드는 허용되지 않습니다. 다른 로그인 방법을 사용하거나 시스템 관리자에게 문의하십시오.

![사용하려는 로그인 메서드는 허용되지 않습니다.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

리소스에 액세스하기 전에 MFA(다단계 인증)가 필요한 조건부 액세스 정책을 구성한 경우 Windows 10 PC에서 강력한 기능을 사용하여 VM 에 대한 원격 데스크톱 연결을 시작해야 합니다. 이러한 인증 방법 윈도우 안녕하세요. 원격 데스크톱 연결에 대해 강력한 인증 방법을 사용하지 않으면 이전 오류가 표시됩니다.

비즈니스용 Windows Hello를 배포하지 않았고 현재 옵션이 아닌 경우 MFA가 필요한 클라우드 앱 목록에서 "Azure Windows VM 로그인" 앱을 제외하는 조건부 액세스 정책을 구성하여 MFA 요구 사항을 제외할 수 있습니다. 비즈니스용 Windows Hello에 대해 자세히 알아보려면 [비즈니스용 Windows Hello 개요를](/windows/security/identity-protection/hello-for-business/hello-identity-verification)참조하십시오.

> [!NOTE]
> RDP를 사용 하 여 비즈니스에 대 한 비즈니스 PIN 인증에 대 한 Windows 안녕하세요 에 의해 지원 되었습니다 10 여러 버전에 대 한, 그러나 RDP와 생체 인증에 대 한 지원 윈도우에 추가 되었다 10 버전 1809. RDP 중에 비즈니스용 Windows Hello를 사용하는 것은 인증서 신뢰 모델을 사용하고 현재 키 신뢰 모델에 사용할 수 없는 배포에만 사용할 수 있습니다.
 
## <a name="preview-feedback"></a>미리 보기 피드백

이 미리 보기 기능에 대한 피드백을 공유하거나 [Azure AD 피드백 포럼에서](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)이 기능을 사용하여 문제를 보고합니다.

## <a name="next-steps"></a>다음 단계

Azure Active Directory에 대한 자세한 내용은 [Azure Active Directory란?](/azure/active-directory/fundamentals/active-directory-whatis)을 참조하세요.
