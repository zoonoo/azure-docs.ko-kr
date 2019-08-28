---
title: Azure Active Directory ID 보호에서 검색하는 취약성
description: Azure Active Directory ID 보호에서 검색하는 취약성에 대한 개요입니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335160"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory ID 보호에서 검색하는 취약성

취약점은 공격자에 의해 악용 될 수 있는 환경의 약점입니다. 관리자는 조직의 보안 상태를 개선 하기 위해 이러한 취약성을 해결 하는 것이 좋습니다.

![Id 보호에서 보고 된 취약점](./media/vulnerabilities/identity-protection-vulnerabilities.png)

다음 섹션에서는 ID 보호에서 보고하는 취약성에 대한 개요를 제공합니다.

## <a name="multi-factor-authentication-registration-not-configured"></a>Multi-factor Authentication 등록이 구성 되지 않음

이 취약점은 조직에서 Azure Multi-factor Authentication의 배포를 평가 하는 데 도움이 됩니다.

Azure Multi-factor Authentication은 사용자 인증에 대 한 두 번째 보안 계층을 제공 합니다. 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 데이터와 애플리케이션에 대한 액세스를 보호하는 데 도움이 됩니다. Azure Multi-factor Authentication은 다음과 같이 사용 하기 쉬운 확인 옵션을 제공 합니다.

* 전화 통화
* 문자 메시지
* 모바일 앱 알림
* OTP 확인 코드

사용자 로그인에 Azure Multi-Factor Authentication을 요구하는 것이 좋습니다. Multi-factor authentication은 Id 보호를 통해 사용할 수 있는 위험 기반 조건부 액세스 정책의 주요 역할을 수행 합니다.

자세한 내용은 [Azure Multi-Factor Authentication이란?](../authentication/multi-factor-authentication.md)을 참조하세요.

## <a name="unmanaged-cloud-apps"></a>관리되지 않는 클라우드 앱

이 취약점을 사용하면 조직에서 관리되지 않은 클라우드 앱을 식별할 수 있습니다.

IT 직원은 종종 조직의 모든 클라우드 응용 프로그램을 인식 하지 못합니다. 관리자가 회사 데이터에 대한 무단 액세스, 데이터 유출 가능성 및 기타 보안 위험에 대해 우려하는 원인을 쉽게 확인합니다.

Cloud Discovery를 배포 하 여 관리 되지 않는 클라우드 응용 프로그램을 검색 하 고 Azure Active Directory를 사용 하 여 이러한 응용 프로그램을 관리할 것을 권장

자세한 내용은 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)를 참조하세요.

## <a name="security-alerts-from-privileged-identity-management"></a>Privileged Identity Management의 보안 경고

이 취약점을 사용하여 조직에서 권한있는 ID에 대한 경고를 검색하고 해결할 수 있습니다.  

사용자가 권한이 필요한 작업을 수행할 수 있도록 조직에서는 Azure AD, Azure 또는 Office 365 리소스에서 기타 SaaS 앱에 대한 임시 또는 영구 액세스 권한을 사용자에게 부여해야 합니다. 이러한 권한이 있는 사용자는 조직의 공격에 대한 취약성을 증가시킵니다. 이 취약점을 사용하여 불필요한 액세스 권한이 있는 사용자를 식별하고 적절한 조치를 취해 이들이 미치는 위험을 줄이고 제거할 수 있습니다.

조직에서 Azure AD Privileged Identity Management를 사용 하 여 Azure AD에서 권한 있는 id를 관리, 제어 및 모니터링 하 고 Office 365 또는 Microsoft Intune와 같은 기타 Microsoft 온라인 서비스를 사용 하는 것이 좋습니다.

자세한 내용은 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)를 참조하세요.

## <a name="see-also"></a>참고자료

[Azure Active Directory ID 보호](../active-directory-identityprotection.md)
