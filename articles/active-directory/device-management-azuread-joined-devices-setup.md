---
title: "Azure Active Directory 조인 장치 설정 | Microsoft Docs"
description: "Azure Active Directory 조인 장치를 설정하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>Azure Active Directory 조인 장치 설정

Azure AD(Active Directory)의 장치 관리를 통해 사용자가 보안 및 규정 준수 표준을 충족하는 장치의 리소스에 액세스할 수 있도록 합니다. 자세한 내용은 [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)를 참조하세요.

[Azure AD 조인 장치](device-management-introduction.md#azure-ad-joined-devices)를 사용하여 Azure AD의 제어를 받는 조직에서 소유하는 Windows 10 장치를 가져올 수 있습니다. 이러한 장치는 이미 [온-프레미스 AD에 조인](device-management-introduction.md#hybrid-azure-ad-joined-devices)되지 않아야 합니다.

이 항목에서는 Azure AD에 Windows 10 장치를 등록하는 방법에 대한 지침을 제공합니다. 

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

- Azure AD에 장치를 조인할 권한이 있습니다.

    ![연결됨](./media/device-management-azuread-joined-devices-setup/21.png)

- 아직 사용자당 장치의 최대 수를 초과하지 않았습니다. 

    ![연결됨](./media/device-management-azuread-joined-devices-setup/22.png)


자세한 내용은 [장치 설정 구성](device-management-azure-portal.md#configure-device-settings)을 참조하세요.



## <a name="what-you-should-know"></a>알아야 할 사항


- Windows에서는 Azure AD에 있는 조직의 디렉터리에 장치를 등록합니다.

- Multi-Factor Authentication 챌린지를 통과해야 할 수 있습니다. IT 관리자는 이러한 챌린지를 설정할 수 있습니다.

- Azure AD는 장치에 모바일 장치 관리 등록이 필요한지 여부를 확인합니다. 해당하는 경우 장치를 등록합니다.

- Windows에서는 자동 로그인 프로세스를 통해 관리되는 사용자를 데스크톱으로 리디렉션합니다.

- 페더레이션된 사용자는 Windows 로그인 페이지로 리디렉션되어 자격 증명을 입력해야 합니다.


## <a name="joining-a-device"></a>장치 조인

이 섹션에서는 Windows 10 장치를 Azure AD에 조인하는 단계를 제공합니다. Azure AD에 장치를 성공적으로 조인한 경우 **회사 또는 학교 액세스** 대화 상자에 해당 장치가 **\<Azure AD\>에 연결됨**으로 표시됩니다.

![연결됨](./media/device-management-azuread-joined-devices-setup/13.png)


**Windows 10 장치를 조인하려면**

1. **시작** 메뉴에서 **설정**을 클릭합니다.

    ![설정](./media/device-management-azuread-joined-devices-setup/01.png)

2. **계정**을 클릭합니다.

    ![계정](./media/device-management-azuread-joined-devices-setup/02.png)


3. **회사 또는 학교 액세스**를 클릭합니다.

    ![회사 또는 학교 액세스](./media/device-management-azuread-joined-devices-setup/03.png)

4. **회사 또는 학교 액세스** 대화 상자에서 **연결**을 클릭합니다.

    ![연결](./media/device-management-azuread-joined-devices-setup/04.png)


5. **회사 또는 학교 계정 설정** 대화 상자에서 **Azure Active Directory에 이 장치 가입**을 클릭합니다.

    ![연결](./media/device-management-azuread-joined-devices-setup/08.png)


6. **이제 로그인해 보겠습니다.** 대화 상자에서 계정 이름(예: someone@example.com)을 입력하고 **다음**을 클릭합니다.

    ![이제 로그인해 보겠습니다.](./media/device-management-azuread-joined-devices-setup/10.png)


6. **암호 입력** 대화 상자에서 암호를 입력한 다음 **로그인**을 클릭합니다.

    ![암호 입력](./media/device-management-azuread-joined-devices-setup/05.png)


7. **조직 확인** 대화 상자에서 **조인**을 클릭합니다.

    ![조직 확인](./media/device-management-azuread-joined-devices-setup/11.png)


8. **모든 설정을 완료했습니다.** 대화 상자에서 **완료**를 클릭합니다.

    ![모든 설정을 완료했습니다.](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>확인

장치가 Azure AD에 조인되었는지 여부를 확인하려면 장치에서 **회사 또는 학교 액세스** 대화 상자를 검토할 수 있습니다.

![연결됨](./media/device-management-azuread-joined-devices-setup/13.png)

또는 다음 명령을 실행할 수 있습니다. `dsregcmd /status`  
성공적으로 조인된 장치에서 **AzureAdJoined**는 **예**입니다.

![연결됨](./media/device-management-azuread-joined-devices-setup/14.png)

Azure AD 포털에서 장치 설정을 검토할 수도 있습니다.

![연결됨](./media/device-management-azuread-joined-devices-setup/15.png)

자세한 내용은 [장치 찾기](device-management-azure-portal.md#locate-devices)를 참조하세요.


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요. 

- [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)

- [Azure Portal을 사용하여 장치 관리](device-management-azure-portal.md)





