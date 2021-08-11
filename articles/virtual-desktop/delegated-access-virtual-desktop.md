---
title: Azure Virtual Desktop에서 위임된 액세스 - Azure
description: 예제를 포함하여 Azure Virtual Desktop 배포에서 관리 기능을 위임하는 방법입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 2367a2840e64956d3dfca41ce8ee5e31c9f354c7
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751994"
---
# <a name="delegated-access-in-azure-virtual-desktop"></a>Azure Virtual Desktop에서 위임된 액세스

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)를 참조하세요.

Azure Virtual Desktop에는 특정 사용자에게 역할을 할당하여 허용되는 액세스 양을 정의할 수 있는 위임된 액세스 모델이 있습니다. 역할 할당에는 보안 주체, 역할 정의, 범위의 세 가지 구성 요소가 있습니다. Azure Virtual Desktop 위임된 액세스 모델은 Azure RBAC 모델을 기반으로 합니다. 특정 역할 할당 및 해당 구성 요소에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어 개요](../role-based-access-control/built-in-roles.md)를 참조하세요.

Azure Virtual Desktop 위임된 액세스는 역할 할당의 각 요소에 대해 다음 값을 지원합니다.

* 보안 주체
    * 사용자
    * 사용자 그룹
    * 서비스 주체
* 역할 정의
    * 기본 제공 역할
    * 사용자 지정 역할
* 범위
    * 호스트 풀
    * 앱 그룹
    * 작업 영역

## <a name="powershell-cmdlets-for-role-assignments"></a>역할 할당을 위한 PowerShell cmdlet

시작하기 전에 Azure Virtual Desktop PowerShell 모듈이 아직 설정되지 않은 경우 [PowerShell 모듈 설정](powershell-module.md)의 지침에 따라 설정해야 합니다.

Azure Virtual Desktop은 사용자 또는 사용자 그룹에 앱 그룹을 게시하는 동안 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용합니다. 데스크톱 가상화 사용자 역할은 사용자 또는 사용자 그룹에 할당되며 범위는 앱 그룹입니다. 이 역할은 사용자에게 앱 그룹에 대한 특별한 데이터 액세스를 제공합니다.

다음 cmdlet을 실행하여 앱 그룹에 Azure Active Directory 사용자를 추가합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

다음 cmdlet을 실행하여 앱 그룹에 Azure Active Directory 사용자 그룹을 추가합니다.

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>다음 단계

각 역할이 사용할 수 있는 PowerShell cmdlet의 전체 목록은 [PowerShell 참조](/powershell/windows-virtual-desktop/overview)를 참조하세요.

Azure RBAC에서 지원되는 역할의 전체 목록은 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

Azure Virtual Desktop 환경을 설정하는 방법에 대한 지침은 [Azure Virtual Desktop 환경](environment-setup.md)을 참조하세요.
