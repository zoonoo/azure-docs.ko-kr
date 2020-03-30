---
title: MFA 또는 2FA 및 권한 있는 ID 관리 - Azure AD | 마이크로 소프트 문서
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022148"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>다단계 인증 및 권한 ID 관리

모든 관리자에 대해 Multi-Factor Authentication(MFA)을 요구하는 것이 좋습니다. 이렇게 하면 손상된 암호로 인한 공격의 위험이 줄어듭니다.

사용자가 로그인할 때 다단계 인증 챌린지를 완료하도록 요구할 수 있습니다. 또한 사용자가 Azure Active Directory(Azure AD) 권한 ID 관리(PIM)에서 역할을 활성화할 때 다단계 인증 챌린지를 완료하도록 요구할 수도 있습니다. 이렇게 하면 사용자가 로그인할 때 다단계 인증 챌린지를 완료하지 않은 경우 권한 있는 ID 관리에서 이 작업을 수행하라는 메시지가 표시됩니다.

> [!IMPORTANT]
> 현재 Azure 다단계 인증은 Microsoft 개인 계정(일반적으로 Skype, Xbox 또는 Outlook.com 같은 Microsoft 서비스에 로그인하는 데 사용되는 개인 계정)이 아닌 직장 또는 학교 계정에서만 작동합니다. 따라서 개인 계정을 사용하는 사람은 역할을 활성화하기 위해 다단계 인증을 사용할 수 없으므로 적격 관리자가 될 수 없습니다. 이 사용자들이 Microsoft 계정을 사용하여 계속 워크로드를 관리해야 하는 경우 영구 관리자로 승격하세요.

## <a name="how-pim-validates-mfa"></a>PIM이 MFA의 유효성을 검사하는 방법

사용자가 역할을 활성화할 때 다단계 인증의 유효성을 검사하는 데는 두 가지 옵션이 있습니다.

가장 간단한 옵션은 권한 있는 역할을 활성화하는 사용자에 대해 Azure 다단계 인증을 의존하는 것입니다. 이렇게 하려면 먼저 해당 사용자에게 필요한 경우 라이선스가 부여되고 Azure 다단계 인증에 등록되었는지 확인합니다. Azure 다단계 인증을 배포하는 방법에 대한 자세한 내용은 [클라우드 기반 Azure 다단계 인증 배포를](../authentication/howto-mfa-getstarted.md)참조하십시오. 사용자가 로그인할 때 이러한 사용자에 대해 다단계 인증을 적용하도록 Azure AD를 구성하는 것이 좋습니다. 이는 권한 있는 ID 관리 자체에 의해 다단계 인증 검사가 수행되기 때문입니다.

또는 사용자가 온-프레미스에서 인증하는 경우 ID 공급자가 다단계 인증을 담당하게 할 수 있습니다. 예를 들어 Azure AD에 액세스하기 전에 스마트 카드 기반 인증을 요구하도록 AD 페더레이션 서비스를 구성한 경우 [Azure Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안](../authentication/howto-mfa-adfs.md) 에 포함된 지침을 참조하여 Azure AD로 클레임을 보내도록 AD FS를 구성합니다. 사용자가 역할을 활성화하려고 하면 권한 있는 ID 관리는 적절한 클레임을 받으면 사용자에 대해 다단계 인증이 이미 검증되었음을 허용합니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [권한 있는 ID 관리에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
