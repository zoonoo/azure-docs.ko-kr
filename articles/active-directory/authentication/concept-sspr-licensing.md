---
title: 라이선스 셀프 서비스 암호 재설정 - Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정 라이선스 요구 사항
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e4e32aef97d406fa47a2ccfe8cddb12b97ff088
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414990"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 셀프 서비스 암호 재설정의 라이선스 요구 사항

Azure AD(Azure Active Directory)는 다음과 같은 네 가지 버전으로 제공됩니다. 무료, 기본, 프리미엄 P1 및 프리미엄 P2. 변경, 재설정, 잠금 해제, 쓰기 저장 등 여러 Azure AD 버전에서 제공되는 여러 기능이 모여서 셀프 서비스 암호 재설정을 구성합니다. 이 문서에서는 차이점을 설명하려고 합니다. 각 Azure AD 버전에 포함된 기능에 대한 자세한 내용은 [Azure Active Directory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

## <a name="compare-editions-and-features"></a>버전 및 기능 비교

Azure AD 셀프 서비스 암호 재설정은 사용자 단위로 라이선스가 부여되며, 조직에서는 규정이 준수될 수 있도록 사용자에게 적절한 라이선스를 할당해야 합니다.

* 클라우드 사용자를 위한 셀프 서비스 암호 변경
   * 저는 **클라우드 전용 사용자**이며 암호를 알고 있습니다.
      * 암호를 **변경**하고 싶습니다.
   * 이 기능은 Azure AD의 모든 버전에 포함되어 있습니다.

* 클라우드 사용자를 위한 셀프 서비스 암호 재설정
   * 저는 **클라우드 전용 사용자**이며 암호가 기억 나지 않습니다.
      * 제가 알고 있는 다른 암호로 **변경**하고 싶습니다.
   * 이 기능은 Azure AD Basic, Premium P1/P2 또는 Microsoft 365 Business에 포함되어 있습니다.

* **온-프레미스 쓰기 저장**으로 셀프 서비스 암호 재설정/변경/잠금 해제
   * 저는 **하이브리드 사용자**이고, 저의 온-프레미스 Active Directory 사용자 계정은 Azure AD Connect를 사용하여 저의 Azure AD 계정과 동기화됩니다. 암호가 기억 나지 않아 또는 암호가 잠겨서 암호를 변경하고 싶습니다.
      * 암호를 변경하거나 알고 있는 암호로 재설정하거나 계정을 잠금 해제한 **후** 변경 내용을 온-프레미스 Active Directory와 동기화하고 싶습니다.
   * 이 기능은 Azure AD Premium P1/P2 또는 Microsoft 365 Business에 포함되어 있습니다.

> [!WARNING]
> 독립 실행형 Office 365 라이선스 플랜은 *"온-프레미스 쓰기 저장을 사용한 셀프 서비스 암호 재설정/변경/잠금 해제"를 지원하지 않습니다*. 따라서 이 기능이 작동하려면 Azure AD Premium P1, Premium P2 또는 Microsoft 365 Business가 포함된 플랜을 선택해야 합니다.
>

아래와 같은 페이지에서 라이선스와 요금에 대한 자세한 정보를 확인할 수 있습니다.

* [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 기능 및 특성](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business 서비스 설명](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>그룹 또는 사용자 기반 라이선스 사용

Azure AD가 이제 그룹 기반 라이선스를 지원합니다. 관리자는 라이선스를 한 번에 하나씩 할당하는 대신 사용자 그룹에 한꺼번에 라이선스를 할당할 수 있습니다. 자세한 내용은 [라이선스 할당, 확인 및 문제 해결](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)을 참조하세요.

일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자가 해당 사용자의 **사용 위치** 속성을 지정해야 합니다. 라이선스는 Azure Portal의 **사용자** > **프로필** > **설정**에서 할당할 수 있습니다. *그룹 라이선스 할당을 사용할 때 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다.*

## <a name="next-steps"></a>다음 단계

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](howto-sspr-deployment.md)
* [암호 재설정 또는 변경](../user-help/active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](../user-help/active-directory-passwords-reset-register.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](howto-sspr-authenticationdata.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](concept-sspr-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](howto-sspr-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](howto-sspr-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](concept-sspr-howitworks.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)
