---
title: MFA 또는 2FA 및 Privileged Identity Management-Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 MFA(Multi-Factor Authentication)의 유효성을 검사하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74022148"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multi-factor authentication 및 Privileged Identity Management

모든 관리자에 대해 Multi-Factor Authentication(MFA)을 요구하는 것이 좋습니다. 이렇게 하면 손상된 암호로 인한 공격의 위험이 줄어듭니다.

사용자가 로그인 할 때 multi-factor authentication 챌린지를 완료 하도록 요구할 수 있습니다. 사용자가 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)에서 역할을 활성화할 때 multi-factor authentication 챌린지를 완료 하도록 요구할 수도 있습니다. 이러한 방식으로 사용자가 로그인 시 multi-factor authentication 챌린지를 완료 하지 않은 경우 Privileged Identity Management 하 여이를 수행 하 라는 메시지가 표시 됩니다.

> [!IMPORTANT]
> 현재 Azure Multi-Factor Authentication는 Microsoft 개인 계정 (일반적으로 Skype, Xbox 또는 Outlook.com와 같은 Microsoft 서비스에 로그인 하는 데 사용 되는 개인 계정)이 아닌 회사 또는 학교 계정 에서만 작동 합니다. 따라서 개인 계정을 사용 하는 사용자는 multi-factor authentication을 사용 하 여 해당 역할을 활성화할 수 없으므로 적격 관리자가 될 수 없습니다. 이 사용자들이 Microsoft 계정을 사용하여 계속 워크로드를 관리해야 하는 경우 영구 관리자로 승격하세요.

## <a name="how-pim-validates-mfa"></a>PIM이 MFA의 유효성을 검사하는 방법

사용자가 역할을 활성화 하는 경우 multi-factor authentication의 유효성을 검사 하는 두 가지 옵션이 있습니다.

가장 간단한 옵션은 권한 있는 역할을 활성화 하는 사용자에 게 Azure Multi-Factor Authentication를 사용 하는 것입니다. 이렇게 하려면 먼저 해당 사용자에 게 라이선스를 부여 하 고 필요한 경우 Azure Multi-Factor Authentication에 등록 했는지 확인 합니다. Azure Multi-Factor Authentication을 배포 하는 방법에 대 한 자세한 내용은 [클라우드 기반 azure Multi-Factor Authentication 배포](../authentication/howto-mfa-getstarted.md)를 참조 하세요. 사용자가 로그인 할 때 이러한 사용자에 대 한 multi-factor authentication을 적용 하도록 Azure AD를 구성 하는 것이 좋지만 필수는 아닙니다. 이는 Privileged Identity Management 자체에서 multi-factor authentication 검사를 수행 하기 때문입니다.

또는 사용자가 온-프레미스에서 인증 하는 경우 id 공급자가 multi-factor authentication을 담당할 수 있습니다. 예를 들어 Azure AD에 액세스하기 전에 스마트 카드 기반 인증을 요구하도록 AD 페더레이션 서비스를 구성한 경우 [Azure Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안](../authentication/howto-mfa-adfs.md) 에 포함된 지침을 참조하여 Azure AD로 클레임을 보내도록 AD FS를 구성합니다. 사용자가 역할을 활성화 하려고 하면 적절 한 클레임을 받은 후 사용자에 대해 multi-factor authentication의 유효성이 이미 검사 된 것으로 Privileged Identity Management.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
