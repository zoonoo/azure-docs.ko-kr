---
title: Azure Virtual Desktop(클래식)의 위임된 액세스 - Azure
description: 예제를 포함하여 Azure Virtual Desktop(클래식) 배포에서 관리 기능을 위임하는 방법.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9db8ad454ad38f24f32e05bf2f72d67ef7db1971
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111752048"
---
# <a name="delegated-access-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop(클래식)의 위임된 액세스

>[!IMPORTANT]
>이 내용은 Azure Resource Manager의 Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager의 Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../delegated-access-virtual-desktop.md)를 참조하세요.

Azure Virtual Desktop에는 특정 사용자에게 역할을 할당하여 허용되는 액세스 양을 정의할 수 있는 위임된 액세스 모델이 있습니다. 역할 할당에는 보안 주체, 역할 정의, 범위의 세 가지 구성 요소가 있습니다. Azure Virtual Desktop 위임된 액세스 모델은 Azure RBAC 모델을 기반으로 합니다. 특정 역할 할당 및 해당 구성 요소에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어 개요](../../role-based-access-control/built-in-roles.md)를 참조하세요.

Azure Virtual Desktop 위임된 액세스는 역할 할당의 각 요소에 대해 다음 값을 지원합니다.

* 보안 주체
    * 사용자
    * 서비스 주체
* 역할 정의
    * 기본 제공 역할
* 범위
    * 테넌트 그룹
    * 테넌트
    * 호스트 풀
    * 앱 그룹

## <a name="built-in-roles"></a>기본 제공 역할

Azure Virtual Desktop의 위임된 액세스에는 사용자 및 서비스 주체에 할당할 수 있는 몇 가지 기본 제공 역할 정의가 있습니다.

* RDS 소유자는 리소스에 대한 액세스를 포함하여 모든 항목을 관리할 수 있습니다.
* RDS 기여자는 모든 항목을 관리할 수 있지만 리소스에는 액세스할 수 없습니다.
* RDS 리더는 모든 항목을 볼 수 있지만 변경할 수는 없습니다.
* RDS 운영자는 진단 작업을 볼 수 있습니다.

## <a name="powershell-cmdlets-for-role-assignments"></a>역할 할당을 위한 PowerShell cmdlet

다음 cmdlet을 실행하여 역할 할당을 만들고, 확인하고, 제거할 수 있습니다.

* **Get-RdsRoleAssignment** 는 역할 할당 목록을 표시합니다.
* **New-RdsRoleAssignment** 는 새 역할 할당을 만듭니다.
* **Remove-RdsRoleAssignment** 는 역할 할당을 삭제합니다.

### <a name="accepted-parameters"></a>허용되는 매개 변수

다음 매개 변수를 사용하여 세 가지 기본 cmdlet을 수정할 수 있습니다.

* **AadTenantId**: 서비스 주체가 멤버인 Azure Active Directory 테넌트 ID를 지정합니다.
* **AppGroupName**: 원격 데스크톱 앱 그룹의 이름입니다.
* **Diagnostics**: 진단 범위를 나타냅니다. (**인프라** 또는 **테넌트** 매개 변수와 쌍을 이루어야 합니다.)
* **HostPoolName**: 원격 데스크톱 호스트 풀의 이름입니다.
* **Infrastructure**: 인프라 범위를 나타냅니다.
* **RoleDefinitionName**: 사용자, 그룹 또는 앱에 할당된 원격 데스크톱 서비스 역할 기반 액세스 제어 역할의 이름입니다. (예: 원격 데스크톱 서비스 소유자, 원격 데스크톱 서비스 리더 등)
* **ServerPrincipleName**: Azure Active Directory 애플리케이션의 이름입니다.
* **SignInName**: 사용자의 이메일 주소 또는 사용자 계정 이름입니다.
* **TenantName**: 원격 데스크톱 테넌트의 이름입니다.

## <a name="next-steps"></a>다음 단계

각 역할이 사용할 수 있는 PowerShell cmdlet의 전체 목록은 [PowerShell 참조](/powershell/windows-virtual-desktop/overview)를 참조하세요.

Azure Virtual Desktop 환경을 설정하는 방법에 대한 지침은 [Azure Virtual Desktop 환경](environment-setup-2019.md)을 참조하세요.
