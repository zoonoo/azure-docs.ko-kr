---
title: Azure AD 조인 장치에서 로컬 관리자를 관리 하는 방법
description: Windows 디바이스의 로컬 관리자 그룹에 Azure 역할을 할당하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d3082e3dc45102bc8700c7d1285ef832d09712a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87419821"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Azure AD 조인 디바이스에서 로컬 관리자 그룹을 관리하는 방법

Windows 디바이스를 관리하려면 로컬 관리자 그룹의 구성원이 되어야 합니다. Azure AD(Azure Active Directory) 조인 프로세스의 일부로, Azure AD는 디바이스에서 이 그룹의 멤버 자격을 업데이트합니다. 비즈니스 요구 사항을 충족하도록 멤버 자격 업데이트를 사용자 지정할 수 있습니다. 멤버 자격 업데이트는 예를 들어 기술 지원팀 직원이 디바이스에 대해 관리자 권한이 필요한 작업을 수행할 수 있도록 하려는 경우에 유용합니다.

이 문서에서는 로컬 관리자 구성원 업데이트의 작동 방식 및 Azure AD 조인 중에이를 사용자 지정 하는 방법을 설명 합니다. 이 문서의 내용은 **하이브리드 AZURE AD 조인** 장치에는 적용 되지 않습니다.

## <a name="how-it-works"></a>작동 방법

Azure ad 조인을 사용 하 여 Azure AD에 Windows 장치를 연결 하는 경우 Azure AD는 장치의 로컬 관리자 그룹에 다음 보안 주체를 추가 합니다.

- Azure AD 글로벌 관리자 역할
- Azure AD 디바이스 관리자 역할 
- Azure AD 조인을 수행하는 사용자   

Azure AD 역할을 로컬 관리자 그룹에 추가하면 디바이스에서 아무것도 수정하지 않고 언제든지 Azure AD에서 디바이스를 관리할 수 있는 사용자를 업데이트할 수 있습니다. 현재 그룹을 관리자 역할에 할당할 수 없습니다.
또한 Azure AD는 PoLP(최소 권한의 원칙)를 지원하기 위해 로컬 관리자 그룹에 Azure AD 디바이스 관리자 역할을 추가합니다. 글로벌 관리자 외에도 사용자가 *오직* 디바이스를 관리하기 위한 디바이스 관리자 역할을 할당받도록 설정할 수 있습니다. 

## <a name="manage-the-global-administrators-role"></a>글로벌 관리자 역할 관리

글로벌 관리자 역할의 멤버 자격을 확인하고 업데이트하려면 다음을 참조하세요.

- [Azure Active Directory에서 관리자 역할의 모든 구성원 보기](../users-groups-roles/directory-manage-roles-portal.md)
- [Azure Active Directory에서 관리자 역할에 사용자 할당](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>디바이스 관리자 역할 관리 

Azure Portal에 있는 **디바이스** 페이지에서 디바이스 관리자 역할을 관리할 수 있습니다. **디바이스** 페이지를 엽니다.

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Azure Active Directory*를 검색하고 선택합니다.
1. **관리** 섹션에서 **디바이스**를 클릭합니다.
1. **디바이스** 페이지에서 **디바이스 설정**을 클릭합니다.

디바이스 관리자 역할을 수정하려면 **Azure AD 조인 디바이스의 추가 로컬 관리자**를 구성합니다.  

![추가 로컬 관리자](./media/assign-local-admin/10.png)

>[!NOTE]
> 이 옵션에는 Azure AD Premium 테넌트가 필요합니다. 

디바이스 관리자는 모든 Azure AD 조인 디바이스에 할당됩니다. 디바이스 관리자를 특정 디바이스 집합으로 범위를 지정할 수 없습니다. 디바이스 관리자 역할 업데이트가 반드시 영향을 받는 사용자에게 즉각적인 영향을 미치는 것은 아닙니다. 사용자가 이미 로그인 되어 있는 장치에서는 아래와 같은 작업을 *수행할 때 권한* 상승이 발생 합니다.

- 적절 한 권한으로 새 기본 새로 고침 토큰을 발급 하기 위해 Azure AD에 대해 최대 4 시간이 경과 되었습니다. 
- 사용자가 로그 아웃 했다가 다시 로그인 하 여 프로필을 새로 고칠 수 있습니다.

>[!NOTE]
> 위의 작업은 이전에 관련 장치에 로그인 하지 않은 사용자에 게는 적용 되지 않습니다. 이 경우 관리자 권한은 처음으로 장치에 로그인 한 후 즉시 적용 됩니다. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Azure AD 그룹을 사용 하 여 관리자 권한 관리 (미리 보기)

>[!NOTE]
> 이 기능은 현재 미리 보기로 제공됩니다.

Windows 10 2004 업데이트부터 Azure AD 그룹을 사용 하 여 [제한 된 그룹](/windows/client-management/mdm/policy-csp-restrictedgroups) MDM 정책을 사용 하 여 azure ad 가입 장치에 대 한 관리자 권한을 관리할 수 있습니다. 이 정책을 사용 하면 개별 사용자 또는 Azure AD 그룹을 Azure AD 조인 장치에 있는 로컬 관리자 그룹에 할당 하 여 여러 장치 그룹에 대 한 고유한 관리자를 구성 하는 세분성을 제공할 수 있습니다. 

현재, Intune에서이 정책을 관리 하는 UI가 없으므로 [사용자 지정 Oma-uri 설정을](/mem/intune/configuration/custom-settings-windows-10)사용 하 여 구성 해야 합니다. 이 정책에 대 한 몇 가지 고려 사항: 

- 정책을 통해 Azure AD 그룹을 추가 하려면 그룹 API를 실행 하 여 가져올 수 있는 그룹의 SID가 필요 합니다. SID는 그룹 API의 속성으로 정의 됩니다 `securityIdentifier` .
- 제한 된 그룹 정책을 적용 하면 멤버 목록에 없는 그룹의 현재 구성원이 제거 됩니다. 따라서이 정책을 새 구성원 또는 그룹으로 적용 하면 장치에서 장치 관리자 역할 및 전역 관리자 역할을 조인한 기존 관리자가 제거 됩니다. 기존 구성원을 제거 하지 않으려면 제한 된 그룹 정책에서 구성원 목록의 일부로 구성 해야 합니다. 
- 이 정책은 Windows 10 장치 (관리자, 사용자, 게스트, 고급 사용자, 원격 데스크톱 사용자 및 원격 관리 사용자)의 잘 알려진 다음 그룹에만 적용 됩니다. 
- 제한 된 그룹을 사용 하는 로컬 관리자 관리 정책은 하이브리드 Azure AD 조인 또는 Azure AD 등록 장치에 적용 되지 않습니다.
- Windows 10 2004 업데이트 이전에는 제한 된 그룹 정책이 있었지만, 장치의 로컬 관리자 그룹의 구성원으로 Azure AD 그룹을 지원 하지 않았습니다. 

## <a name="manage-regular-users"></a>일반 사용자 관리

기본적으로 Azure AD는 디바이스에서 관리자 그룹에 Azure AD 조인을 수행하는 사용자를 추가합니다. 일반 사용자가 로컬 관리자가 되지 않도록 하려면 다음 옵션을 사용할 수 있습니다.

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot은 조인을 수행하는 기본 사용자가 로컬 관리자가 되는 것을 방지하는 옵션을 제공합니다. 이 작업은 [Autopilot 프로필 만들기](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)를 통해 수행할 수 있습니다.
- [대량 등록](/intune/windows-bulk-enroll) - 대량 등록의 컨텍스트에서 수행되는 Azure AD 조인은 자동으로 생성된 사용자의 컨텍스트에서 발생합니다. 디바이스가 조인된 후에 로그인하는 사용자는 관리자 그룹에 추가되지 않습니다.   

## <a name="manually-elevate-a-user-on-a-device"></a>수동으로 디바이스에서 사용자 권한 상승 

Azure AD 조인 프로세스를 사용하는 것 외에, 수동으로 하나의 특정 디바이스에서 일반 사용자를 로컬 관리자로 권한을 상승시킬 수 있습니다. 이 단계를 사용하려면 로컬 관리자 그룹의 구성원이어야 합니다. 

**Windows 10 1709** 릴리스부터 **설정-> 계정-다른 사용자 >** 에서이 작업을 수행할 수 있습니다. **회사 또는 학교 사용자 추가**를 선택하고 **사용자 계정** 아래에 사용자의 UPN을 입력한 다음, **계정 유형**에서 ‘관리자’를 선택합니다.**  
 
또한 명령 프롬프트를 사용하여 사용자를 추가할 수 있습니다.

- 테넌트 사용자가 온-프레미스 Active Directory에서 동기화되면 `net localgroup administrators /add "Contoso\username"`을 사용합니다.
- 테넌트 사용자가 Azure AD에서 생성되면 `net localgroup administrators /add "AzureAD\UserUpn"`을 사용합니다.

## <a name="considerations"></a>고려 사항 

디바이스 관리자 역할에는 그룹을 할당할 수 없으며, 개별 사용자만 허용됩니다.

디바이스 관리자는 모든 Azure AD 조인 디바이스에 할당됩니다. 특정 디바이스 집합으로 범위를 지정할 수 없습니다.

디바이스 관리자 역할에서 사용자를 제거하는 경우 로그인되어 있는 한 디바이스에서 로컬 관리자 권한을 계속 가집니다. 새 주 새로 고침 토큰이 발급 되 면 다음에 로그인 하는 동안 권한이 해지 됩니다. 권한 상승을 유사한이 해지는 최대 4 시간까지 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 디바이스를 관리하는 방법에 대한 개요를 보려면 [Azure Portal을 사용하여 디바이스 관리](device-management-azure-portal.md)를 참조하세요.
- 장치 기반 조건부 액세스에 대해 자세히 알아보려면 [장치 기반 조건부 액세스 정책 Azure Active Directory 구성](../conditional-access/require-managed-devices.md)을 참조 하세요.
