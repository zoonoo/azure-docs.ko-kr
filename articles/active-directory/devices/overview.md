---
title: Azure Active Directory의 디바이스 ID란?
description: 디바이스 ID 관리가 사용자 환경의 리소스에 액세스하는 디바이스를 관리하는 데 어떻게 도움이 되는지 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d38ca8bdf93ff201b3f5842f4cb0e8409dcd0c3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481665"
---
# <a name="what-is-a-device-identity"></a>디바이스 ID란?

모든 형태와 크기의 디바이스 및 BYOD(Bring Your Own Device) 개념이 확산됨에 따라 IT 전문가는 다음 두 가지 대립되는 목표에 직면하고 있습니다.

- 최종 사용자가 언제 어디서나 생산성을 높일 수 있도록 허용
- 조직의 자산 보호

이와 같은 자산을 보호하려면 IT 직원이 먼저 디바이스 ID를 관리해야 합니다. IT 직원은 Microsoft Intune 같은 도구를 통해 디바이스 ID를 기반으로 빌드하여 보안 및 규정 준수의 표준을 충족하는지 확인할 수 있습니다. Azure AD(Azure Active Directory)에서는 이 디바이스를 통해 어디에서든지 디바이스, 앱 및 서비스에 대해 Single Sign-On을 수행할 수 있습니다.

- 사용자가 필요한 조직 자산에 액세스할 수 있습니다. 
- IT 직원은 조직을 보호하는 데 필요한 컨트롤을 얻습니다.

디바이스 ID 관리는 [디바이스 기반 조건부 액세스](../conditional-access/require-managed-devices.md)에 대한 기반입니다. 디바이스 기반 조건부 액세스 정책을 사용할 경우 관리 디바이스를 통해서만 환경의 리소스에 액세스하도록 할 수 있습니다.

## <a name="getting-devices-in-azure-ad"></a>Azure AD에서 디바이스 가져오기

Azure AD에서 디바이스를 가져올 수 있는 여러 가지 옵션이 있습니다.

- **Azure AD 등록**
   - Azure AD 등록 상태의 디바이스는 일반적으로 개인 소유이거나 모바일 디바이스이며 개인 Microsoft 계정 또는 다른 로컬 계정으로 로그인됩니다.
      - 윈도우 10
      - iOS
      - Android
      - MacOS
- **Azure AD 조인**
   - Azure AD 조인 디바이스는 조직이 소유하며 조직에 속한 Azure AD 계정으로 로그인됩니다. 이 디바이스는 클라우드에만 존재합니다.
      - 윈도우 10 
- **하이브리드 Azure AD 조인**
   - 하이브리드 Azure AD 조인 디바이스는 조직이 소유하며 조직에 속한 Azure AD 계정으로 로그인됩니다. 이 디바이스는 클라우드와 다음 위치에 존재합니다. 
      - Windows 7, 8.1 또는 10
      - Windows Server 2008 이상

![Azure AD 디바이스 블레이드에 표시된 디바이스](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>디바이스 관리

Azure AD의 디바이스는 Microsoft Intune, System Center Configuration Manager, 그룹 정책(하이브리드 Azure AD 조인), MAM(모바일 애플리케이션 관리) 도구 또는 기타 타사 도구와 같은 MDM(모바일 디바이스 관리) 도구를 사용하여 관리할 수 있습니다.

## <a name="resource-access"></a>리소스 액세스

등록과 가입을 통해 사용자는 클라우드 리소스에 SSO(Seamless Sign-On)로 액세스할 수 있고 관리자는 해당 리소스에 조건부 액세스 정책을 적용할 수 있습니다. 

Azure AD 조인 디바이스 또는 하이브리드 Azure AD 조인 디바이스는 클라우드 리소스뿐 아니라 조직의 온-프레미스 리소스에 SSO를 이용할 수 있습니다. 자세한 내용은 [온-프레미스 리소스에 대한 SSO가 Azure AD 조인 디바이스에서 작동하는 방식](azuread-join-sso.md) 문서를 참조하세요.

## <a name="device-security"></a>디바이스 보안

- **Azure AD 등록 디바이스**는 최종 사용자가 관리하는 계정을 사용하며, 이 계정은 Microsoft 계정이거나 다음 중 하나 이상으로 보안이 지정된 다른 로컬 관리형 자격 증명입니다.
   - 암호
   - PIN
   - 패턴
   - Windows Hello
- **Azure AD 조인 디바이스 또는 하이브리드 Azure AD 조인 디바이스**는 다음 중 하나 이상으로 보안이 지정된 Azure AD의 조직 계정을 사용합니다.
   - 암호
   - 비즈니스용 Windows Hello

## <a name="provisioning"></a>프로비전

디바이스를 Azure AD로 가져오는 작업은 셀프 서비스 방식으로 수행하거나 관리자가 제어하는 프로비저닝 프로세스로 수행할 수 있습니다.

## <a name="summary"></a>요약

Azure AD의 디바이스 ID 관리를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Azure AD에서 디바이스 가져오기 및 관리 프로세스 간소화
- 사용자가 조직의 클라우드 기반 리소스에 대한 액세스를 편리하게 사용할 수 있도록 제공

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure AD 등록 디바이스](concept-azure-ad-register.md)에 대한 자세한 정보
- [Azure AD 조인 디바이스](concept-azure-ad-join.md)에 대한 자세한 정보
- [하이브리드 Azure AD 조인 디바이스](concept-azure-ad-join-hybrid.md)에 대한 자세한 정보
- Azure Portal에서 디바이스 ID를 관리하는 방법에 대한 개요를 보려면 [Azure Portal을 사용하여 디바이스 ID 관리](device-management-azure-portal.md)를 참조하세요.
- 디바이스 기반 조건부 액세스에 대한 자세한 내용은 [Azure Active Directory 디바이스 기반 조건부 액세스 정책 구성](../conditional-access/require-managed-devices.md)을 참조하세요.
