---
title: "Azure Active Directory 등록 Windows 10 장치 설정 | Microsoft Docs"
description: "Azure Active Directory 등록 Windows 10 장치를 설정하는 방법을 알아봅니다."
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
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Azure Active Directory 등록 Windows 10 장치 설정

Azure AD(Active Directory)의 장치 관리를 통해 사용자가 보안 및 규정 준수 표준을 충족하는 장치의 리소스에 액세스할 수 있도록 합니다. 자세한 내용은 [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)를 참조하세요.

[Azure AD 등록 장치](device-management-introduction.md#azure-ad-registered-devices)에서 **BYOD(Bring Your Own Device)** 시나리오를 사용하도록 설정할 수 있습니다. 이 시나리오는 사용자가 개인 소유 장치를 사용하여 조직의 리소스에 액세스할 수 있도록 허용합니다.  

Azure AD에서 다음 항목에 Azure AD 등록 장치를 설정할 수 있습니다.

- 윈도우 10
- iOS
- Android
- macOS  

이 항목에서는 Azure AD에 Windows 10 장치를 등록하는 방법에 대한 지침을 제공합니다. 


## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인해야 합니다.

- 장치를 등록할 수 있는 사용 권한 

    ![등록](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- 아직 사용자당 장치의 최대 수를 초과하지 않았습니다. 

    ![등록](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

자세한 내용은 [장치 설정 구성](device-management-azure-portal.md#configure-device-settings)을 참조하세요.

## <a name="what-you-should-know"></a>알아야 할 사항

장치를 등록할 때 다음 항목을 적어둡니다.

- Windows에서는 Azure AD에 있는 조직의 디렉터리에 장치를 등록합니다.

- Multi-Factor Authentication 챌린지를 통과해야 할 수 있습니다. IT 관리자는 이러한 챌린지를 설정할 수 있습니다.

- Azure AD는 장치에 모바일 장치 관리 등록이 필요한지 여부를 확인합니다. 해당하는 경우 장치를 등록합니다.

- Windows에서는 자동 로그인 프로세스를 통해 관리되는 사용자를 데스크톱으로 리디렉션합니다.

- 페더레이션된 사용자는 Windows 로그인 페이지로 리디렉션되어 자격 증명을 입력해야 합니다.


## <a name="register-a-device"></a>장치 등록

Azure AD에서 Windows 10 장치를 등록하려면 다음 단계를 완료합니다. Azure AD에 장치를 성공적으로 등록한 경우 **회사 또는 학교 액세스** 페이지는 **회사 또는 학교 계정** 항목에서 표시합니다.

![등록](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


Windows 10 장치를 등록하려면:

1. **시작** 메뉴에서 **설정**을 클릭합니다.

    ![설정 선택](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. **계정**을 클릭합니다.

    ![계정 선택](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. **회사 또는 학교 액세스**를 클릭합니다.

    ![회사 또는 학교 액세스 선택](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. **회사 또는 학교 액세스** 페이지에서 **연결**을 클릭합니다.

    ![회사 또는 학교 액세스 페이지](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. **회사 또는 학교 계정 설정** 페이지에서 계정 이름(예: someone@example.com)을 입력하고 **다음**을 클릭합니다.

    ![회사 또는 학교 계정 설정 페이지](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. **암호 입력** 페이지에서 암호를 입력한 후 **다음**을 클릭합니다.

    ![암호 입력](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. **모든 설정을 완료했습니다.** 페이지에서 **완료**를 클릭합니다.

    ![모든 설정을 완료했습니다. 페이지](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>확인

장치가 Azure AD에 조인되었는지 여부를 확인하려면 장치에서 **회사 또는 학교 액세스** 페이지를 검토합니다.

![회사 또는 학교 계정 상태](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

또는 Azure AD 포털에서 장치 설정을 검토할 수 있습니다.

![Azure AD 등록 장치](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요. 

- [Azure Active Directory의 장치 관리 소개](device-management-introduction.md)

- [Azure Portal을 사용하여 장치 관리](device-management-azure-portal.md)





