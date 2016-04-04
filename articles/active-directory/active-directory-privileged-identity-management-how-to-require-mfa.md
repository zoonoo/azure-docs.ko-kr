<properties
   pageTitle="다단계 인증을 요구하는 방법 | Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management 확장을 사용하여 권한 있는 ID에 대해 MFA(Multi-Factor Authentication)를 요구하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: MFA를 요구하는 방법

모든 관리자에 대해 Multi-Factor Authentication을 요구하는 것이 좋습니다.

## Azure AD Privileged Identity Management에서 MFA 요구

PIM 관리자로 로그인하는 경우 권한 있는 계정에 MFA(Multi-Factor Authentication)가 필요하다는 경고가 표시됩니다. PIM 대시보드에서 보안 경고를 클릭하면 MFA가 필요한 관리자 계정 목록이 포함된 새 블레이드가 열립니다. 여러 역할을 선택하고 **수정** 단추를 클릭하여 MFA를 요구하거나, 개별 역할 옆에 있는 줄임표를 클릭한 다음 **수정** 단추를 클릭할 수 있습니다.

또한 대시보드의 역할 섹션에서 역할을 클릭한 다음 역할 블레이드에서 **설정**을 클릭하고 Multi-Factor Authentication에서 **사용**을 선택하여 해당 역할에 대해 MFA를 사용하도록 설정하면 특정 역할에 대한 MFA 요구 사항을 변경할 수 있습니다.

## Azure AD PIM에서 MFA의 유효성을 검사하는 방법

> [AZURE.IMPORTANT] Microsoft 계정(예: @outlook.com, @live.com 또는 @hotmail.com)은 현재 Azure MFA 등록에 사용하도록 지원되지 않으므로 높은 권한의 역할을 위한 임시 관리자로 허용되지 않습니다. 사용자가 Microsoft 계정을 사용하여 계속 워크로드를 관리해야 하는 경우 영구 관리자로 변환하세요.

사용자가 역할을 활성화할 때 MFA 유효성을 검사하는 두 가지 방법이 있습니다.

가장 간단한 방법은 권한 있는 역할을 활성화하는 사용자에 대해 Azure MFA를 사용하는 것입니다. 이렇게 하려면 먼저 해당 사용자에게 사용이 허가되었는지와 Azure MFA에 등록되었는지 확인합니다. 이 방법에 대한 자세한 내용은 [클라우드에서 Azure Multi-Factor Authentication 시작](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users)을 참조하세요. 이러한 사용자가 로그인할 때 MFA를 적용하도록 Azure AD를 구성하는 것이 좋지만 필수 사항은 아닙니다. MFA 검사가 Azure AD PIM 자체에서도 수행되기 때문입니다.

또는 사용자가 온-프레미스를 인증하는 경우 ID 공급자에게 MFA에 대한 책임을 지정할 수 있습니다. 예를 들어 Azure AD에 액세스하기 전에 스마트 카드 기반 인증을 요구하도록 AD 페더레이션 서비스를 구성한 경우 [Azure Multi-factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안 유지](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md)에 포함된 지침을 참조하여 Azure AD로 클레임을 보내도록 AD FS를 구성합니다. 사용자가 역할을 활성화하려고 하면 Azure AD PIM은 해당 클레임을 받을 때 사용자에 대해 MFA의 유효성이 이미 검사되었다는 데 동의합니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->