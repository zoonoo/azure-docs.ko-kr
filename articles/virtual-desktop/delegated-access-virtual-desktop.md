---
title: Windows 가상 데스크톱에서 위임 된 액세스-Azure
description: 예를 포함 하 여 Windows 가상 데스크톱 배포에서 관리 기능을 위임 하는 방법
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010058"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows Virtual Desktop에서 위임된 액세스

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)를 참조하세요.

Windows 가상 데스크톱에는 특정 사용자에 게 역할을 할당 하 여 허용 되는 액세스 권한을 정의할 수 있는 위임 된 액세스 모델이 있습니다. 역할 할당에는 보안 주체, 역할 정의 및 범위의 세 가지 구성 요소가 있습니다. Windows 가상 데스크톱 위임 된 액세스 모델은 Azure RBAC 모델을 기반으로 합니다. 특정 역할 할당 및 해당 구성 요소에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어 개요](../role-based-access-control/built-in-roles.md)를 참조 하세요.

Windows 가상 데스크톱 위임 된 액세스는 역할 할당의 각 요소에 대해 다음 값을 지원 합니다.

* 보안 주체
    * 사용자
    * 사용자 그룹
    * 서비스 주체
* 역할 정의
    * 기본 제공 역할
    * 사용자 지정 역할
* Scope
    * 호스트 풀
    * 앱 그룹
    * 작업 영역

## <a name="powershell-cmdlets-for-role-assignments"></a>역할 할당에 대 한 PowerShell cmdlet

시작 하기 전에 [powershell 모듈 설정](powershell-module.md) 의 지침에 따라 Windows 가상 데스크톱 powershell 모듈을 설정 하지 않은 경우 확인 합니다.

Windows 가상 데스크톱은 사용자 또는 사용자 그룹에 앱 그룹을 게시 하는 동안 Azure RBAC (역할 기반 액세스 제어)를 사용 합니다. 데스크톱 가상화 사용자 역할은 사용자 또는 사용자 그룹에 할당 되 고 범위는 앱 그룹입니다. 이 역할은 사용자에 게 앱 그룹에 대 한 특별 한 데이터 액세스를 제공 합니다.

앱 그룹에 Azure Active Directory 사용자를 추가 하려면 다음 cmdlet을 실행 합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

앱 그룹에 사용자 그룹 Azure Active Directory 추가 하려면 다음 cmdlet을 실행 합니다.

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>다음 단계

각 역할이 사용할 수 있는 PowerShell cmdlet의 전체 목록은 [powershell 참조](/powershell/windows-virtual-desktop/overview)를 참조 하세요.

Azure RBAC에서 지원 되는 역할의 전체 목록은 [azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조 하세요.

Windows 가상 데스크톱 환경을 설정 하는 방법에 대 한 지침은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.
