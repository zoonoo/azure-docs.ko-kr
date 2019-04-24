---
title: Multi-factor authentication (MFA) 및 PIM-Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 MFA(Multi-Factor Authentication)의 유효성을 검사하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8831a838a403d58d8673c6400e0dda06c03dc69f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442678"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>MFA(Multi-Factor authentication) 및 PIM

모든 관리자에 대해 Multi-Factor Authentication(MFA)을 요구하는 것이 좋습니다. 이렇게 하면 손상된 암호로 인한 공격의 위험이 줄어듭니다.

사용자가 로그인할 때 MFA 챌린지를 완료하도록 요구할 수 있습니다. 사용자가 역할에서 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)을 활성화할 때 MFA 챌린지를 완료 하도록 요구할 수 있습니다. 이러한 방식으로 사용자가 로그인 시 MFA 챌린지를 완료하지 않은 경우 PIM에서 이 작업을 수행하도록 메시지를 표시합니다.

> [!IMPORTANT]
> 현재, Azure MFA는 회사 또는 학교 계정에서만 작동하며 Microsoft 계정 (일반적으로 Skype, Xbox, Outlook.com 등과 같은 Microsoft 서비스에 로그인하는 데 사용되는 개인 계정)에서는 작동하지 않습니다. 이 때문에 Microsoft 계정을 사용하는 사용자는 MFA를 사용하여 해당 역할을 활성화할 수 없기 때문에 적격 관리자가 될 수 없습니다. 이 사용자들이 Microsoft 계정을 사용하여 계속 워크로드를 관리해야 하는 경우 영구 관리자로 승격하세요.

## <a name="how-pim-validates-mfa"></a>PIM이 MFA의 유효성을 검사하는 방법

사용자가 역할을 활성화할 때 MFA 유효성을 검사하는 두 가지 방법이 있습니다.

가장 간단한 옵션은 권한 있는 역할을 활성화하는 사용자에 대해 Azure MFA를 사용하는 것입니다. 이렇게 하려면 먼저 해당 사용자에게 사용이 허가되었는지와 Azure MFA에 등록되었는지 확인합니다. Azure MFA를 배포하는 방법에 대한 자세한 내용은 [클라우드 기반 Azure Multi-Factor Authentication 배포](../authentication/howto-mfa-getstarted.md)를 참조하세요. 이러한 사용자가 로그인할 때 MFA를 적용하도록 Azure AD를 구성하는 것이 좋지만 필수 사항은 아닙니다. MFA 검사가 PIM 자체에서도 수행되기 때문입니다.

또는 사용자가 온-프레미스를 인증하는 경우 MFA에 대해 책임지는 ID 공급자를 사용할 수 있습니다. 예를 들어 Azure AD에 액세스하기 전에 스마트 카드 기반 인증을 요구하도록 AD 페더레이션 서비스를 구성한 경우 [Azure Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안](../authentication/howto-mfa-adfs.md) 에 포함된 지침을 참조하여 Azure AD로 클레임을 보내도록 AD FS를 구성합니다. 사용자가 역할을 활성화하려고 할 때 PIM은 해당 클레임을 받으면 사용자에 대해 MFA의 유효성이 이미 검사되었다는 것을 허용합니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [PIM에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
