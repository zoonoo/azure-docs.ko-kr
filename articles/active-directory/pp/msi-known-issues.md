---
title: "Azure Active Directory용 MSI(관리 서비스 ID)의 FAQ 및 알려진 문제"
description: "Azure Active Directory용 MSI(관리 서비스 ID)의 알려진 문제에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: b0b5b991b7d2baaa40ebedbd19247717e91aa027
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="faq-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Azure Active Directory용 MSI(관리 서비스 ID)의 FAQ 및 알려진 문제

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-msi-work-with-azure-cloud-services"></a>Azure Cloud Services와 함께 MSI를 사용할 수 있나요?

아니요, Azure Cloud Services에서 MSI를 지원하는 요금제는 없습니다.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI는 ADAL(Active Directory Authentication Library) 또는 MSAL(Microsoft Authentication Library)에서 작동하나요?

아니요, MSI는 ADAL 또는 MSAL과 아직 통합되지 않았습니다. MSI REST 끝점을 사용하여 MSI 토큰을 가져오는 방법에 대한 자세한 내용은 [토큰 획득을 위해 Azure VM MSI(관리 서비스 ID)를 사용하는 방법](msi-how-to-use-vm-msi-token.md)을 참조하세요.

### <a name="what-are-the-supported-linux-distributions"></a>지원되는 Linux 배포는 무엇입니까?

다음 Linux 배포판이 MSI를 지원합니다. 

- CoreOS Stable
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04

다른 Linux 배포판은 현재 지원되지 않으며 지원되지 않는 배포판에서는 확장이 실패할 수 있습니다.

확장은 CentOS 6.9에서 작동합니다. 그러나 6.9의 시스템 지원이 부족하기 때문에 확장은 크래시되거나 중지된 경우 자동으로 시작하지 않습니다. VM이 다시 시작하면 다시 시작하니다. 확장을 수동으로 다시 시작하려면 [MSI 확장을 다시 시작하는 방법](#how-do-you-restart-the-msi-extension) 참조

### <a name="how-do-you-restart-the-msi-extension"></a>MSI 확장을 다시 시작하는 방법
Windows 및 특정 버전의 Linux에서 확장이 중지한 경우 다음 cmdlet 사용하여 수동으로 다시 시작할 수 있습니다.

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

위치: 
- Windows에 대한 확장 이름 및 형식: ManagedIdentityExtensionForWindows
- Linux에 대한 확장 이름 및 형식: ManagedIdentityExtensionForLinux

### <a name="are-there-rbac-roles-for-user-assigned-identities"></a>사용자 할당 ID에 대한 RBAC 역할이 있나요?
예:
1. MSI 참가자: 

- 다음 작업을 수행할 수 있습니다: CRUD 사용자 할당 ID입니다. 
- 다음 작업을 수행할 수 없습니다: 리소스에 사용자 할당 ID를 할당합니다. (즉, VM에 ID 할당)

2. MSI 연산자: 

- 다음 작업을 수행할 수 있습니다: 리소스에 사용자 할당 ID를 할당합니다. (즉, VM에 ID 할당)
- 다음 작업을 수행할 수 없습니다: CRUD 사용자 할당 ID입니다.

참고: 기본 제공 참가자 역할은 위에서 설명한 작업을 모두 수행할 수 있습니다. 
- CRUD 사용자 할당 ID입니다.
- 리소스에 사용자 할당 ID를 할당합니다. (즉, VM에 ID 할당)


## <a name="known-issues"></a>알려진 문제

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>MSI 확장에 대한 스키마 내보내기를 시도하는 동안 발생하는 "Automation 스크립트" 오류

VM에서 관리되는 서비스 ID를 활성화하는 경우 VM 또는 해당 리소스 그룹에 "Automation 스크립트" 기능을 사용하려고 할 때 다음과 같은 오류가 표시됩니다.

![MSI 자동화 스크립트 내보내기 오류](~/articles/active-directory/media/msi-known-issues/automation-script-export-error.png)

현재 관리되는 서비스 ID VM 확장은 리소스 그룹 템플릿으로 해당 스키마를 내보내는 기능을 지원하지 않습니다. 결과적으로 생성된 템플릿은 리소스에서 관리되는 서비스 ID를 활성화하는 구성 매개 변수를 표시하지 않습니다. [템플릿을 사용하여 VM 관리되는 서비스 ID 구성](msi-qs-configure-template-windows-vm.md)의 예제를 수행하여 이러한 섹션을 수동으로 추가할 수 있습니다.

스키마 내보내기 기능을 MSI VM 확장에 사용할 수 있는 경우 [VM 확장을 포함하는 리소스 그룹 내보내기](~/articles/virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions)에 나열됩니다.

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Azure Portal에 구성 블레이드가 표시되지 않음

VM에 VM 구성 블레이드가 표시되지 않는 경우 해당 지역의 Portal에서 MSI가 아직 사용하도록 설정되지 않은 것입니다.  나중에 다시 확인하세요.  [PowerShell](msi-qs-configure-powershell-windows-vm.md) 또는 [Azure CLI](msi-qs-configure-cli-windows-vm.md)를 사용하여 VM에 대해 MSI를 사용하도록 설정할 수도 있습니다.

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>액세스 제어(IAM) 블레이드에서 가상 머신 액세스 권한을 할당할 수 없음

Azure Portal의 **액세스 제어(IAM)** > **권한 추가**에서 **다음에 대한 액세스 할당:**의 선택 항목으로 **Virtual Machine**이 표시되지 않는 경우 해당 지역의 Portal에서 관리 서비스 ID가 아직 사용하도록 설정되지 않은 것입니다. 나중에 다시 확인하세요.  MSI의 서비스 주체를 검색하여 역할 할당용으로 관리 서비스 ID를 선택할 수는 있습니다.  **선택** 필드에 VM 이름을 입력하면 서비스 주체가 검색 결과에 표시됩니다.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM이 리소스 그룹 또는 구독에서 이동한 후 시작되지 않음

실행 중인 상태의 VM을 이동하면 이동 중에도 VM은 계속 실행됩니다. 그러나 이동 후 중지했다가 다시 시작하면 VM이 시작되지 않습니다. 이 문제가 발생하는 이유는 VM이 MSI ID에 대한 참조를 업데이트하지 않고 이전 리소스 그룹의 ID를 계속 가리키기 때문입니다.

**해결 방법** 
 
MSI의 올바른 값을 가져올 수 있도록 VM에서 업데이트를 트리거합니다. VM 속성 변경을 수행하여 MSI ID에 대한 참조를 업데이트할 수 있습니다. 예를 들어 다음 명령을 사용하여 VM에 새 태그 값을 설정할 수 있습니다.

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
이 명령은 VM에 값이 1인 새 태그 "fixVM"을 설정합니다. 
 
이 속성을 설정하면 VM은 올바른 MSI 리소스 URI로 업데이트됩니다. 그리고 나면 VM을 시작할 수 있습니다. 
 
VM이 시작되면 다음 명령을 사용하여 태그를 제거할 수 있습니다.

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-msi-private-preview-feature"></a>사용자 할당 MSI*(비공개 미리 보기 기능)*의 알려진 문제

- 모든 사용자 할당 MSI를 제거하는 방법은 시스템 할당 MSI를 사용하도록 설정하는 것뿐입니다. 
- DNS 조회 오류로 인해 VM으로의 VM 확장 프로비저닝이 실패할 수 있습니다. VM을 다시 시작한 후에 다시 시도하세요. 
- '존재하지 않는' MSI를 추가하면 VM이 실패하게 됩니다. *참고: MSI가 존재하지 않으면 assign-identity에 실패하는 문제 해결이 롤아웃됩니다.*
- Azure Storage 자습서는 현재 미국 중부 EUAP에서만 제공됩니다. 
- 이름에서 특수 문자(예: 밑줄)를 사용하여 사용자 할당 MSI를 만들 수 없습니다.
- 두 번째 사용자 할당 ID를 추가할 때는 해당 ID용 토큰을 요청하는 데 clientID를 사용하지 못할 수 있습니다. 이 문제를 완화하려면 다음의 두 bash 명령을 사용하여 MSI VM 확장을 다시 시작합니다.
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Windows의 VMAgent는 현재 사용자 할당 MSI를 지원하지 않습니다. 
- VM에 사용자 할당 MSI는 있는데 시스템 할당 MSI는 없으면 Portal UI에는 MSI가 사용하도록 설정된 것으로 표시됩니다. 시스템 할당 MSI를 사용하도록 설정하려면 Azure Resource Manager 템플릿, Azure CLI 또는 SDK를 사용하세요.
