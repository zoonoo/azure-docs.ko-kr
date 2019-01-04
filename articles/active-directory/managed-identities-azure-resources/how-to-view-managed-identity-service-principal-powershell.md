---
title: PowerShell을 사용하여 관리 ID의 서비스 주체를 보는 방법
description: PowerShell을 사용하여 관리 ID의 서비스 주체를 보기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0ad3a52b837a5f79c9976c4c509e0a8516de1e7d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714117"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>PowerShell을 사용하여 관리 ID의 서비스 주체 보기

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 관리 ID의 서비스 주체를 보는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없는 경우 [체험 계정에 가입](https://azure.microsoft.com/free/)합니다.
- [가상 머신](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) 또는 [애플리케이션에서 시스템 할당 ID](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity)를 사용하도록 설정합니다.
- 로컬에서 PowerShell을 설치하고 사용하도록 선택하려는 경우 이 자습서를 진행하려면 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 
- PowerShell을 로컬로 실행하는 경우 다음이 필요합니다. 
    - `Login-AzureRmAccount`를 실행하여 Azure와 연결합니다.
    - [PowerShellGet 최신 버전](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)을 설치합니다.
    - `Install-Module -Name PowerShellGet -AllowPrerelease` 명령을 실행하여 `PowerShellGet` 모듈의 시험판 버전을 가져옵니다. 이 명령을 실행한 후 `AzureRM.ManagedServiceIdentity` 모듈을 설치하기 위해 현재 PowerShell 세션에서 `Exit`해야 할 수도 있습니다.
    - `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` 명령을 실행하여 `AzureRM.ManagedServiceIdentity` 모듈의 시험판 버전을 설치하고 이 문서의 사용자 할당 관리 ID 작업을 수행합니다.

## <a name="view-the-service-principal"></a>서비스 주체 보기

다음 명령은 시스템 할당 ID가 사용하도록 설정된 VM 또는 애플리케이션의 서비스 주체를 보는 방법을 보여 줍니다. `<VM or application name>`을 고유한 값으로 바꿉니다.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 Azure AD 서비스 주체를 보는 방법에 대한 자세한 내용은 [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal)을 참조하세요.


