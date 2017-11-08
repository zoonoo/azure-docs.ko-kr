---
title: "라이선스: Azure AD SSPR | Microsoft Docs"
description: "Azure AD 셀프 서비스 암호 재설정 라이선스 요구 사항"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 셀프 서비스 암호 재설정의 라이선스 요구 사항

Azure AD 암호 재설정의 기능을 작동하려면 **조직에서 할당된 하나 이상의 라이선스가 있어야 합니다**. 암호 재설정 환경에서 사용자별 라이선스를 적용하지 않습니다. Microsoft 라이선스 규약을 준수하려면 프리미엄 기능을 사용하는 모든 사용자에게 라이선스를 할당해야 합니다.

* **클라우드 전용 사용자** - Office 365(O365) 유료 SKU 또는 Azure AD Basic
* **클라우드** 및/또는 **온-프레미스 사용자** - Azure AD Premium P1 또는 P2, EMS(Enterprise Mobility + Security) 또는 SPE(Secure Productive Enterprise)

## <a name="licenses-required-for-password-writeback"></a>비밀번호 쓰기 저장에 필요한 라이선스

비밀번호 쓰기 저장을 사용하려면 다음 라이선스 중 하나를 테넌트에 할당해야 합니다.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!WARNING]
> 독립 실행형 Office 365 라이선스 계획은 **비밀번호 쓰기 저장을 지원하지 않습니다**. 따라서 이 기능을 작동시키기 위해 이전 계획 중 하나가 필요합니다.

다음 페이지에서는 라이선스 비용을 포함한 추가 정보를 찾을 수 있습니다.

* [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 기능 및 특성](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>그룹 또는 사용자 기반 라이선스 사용

이제 Azure AD는 관리자가 사용자 그룹에 대량으로 라이선스를 할당하지 않고 한 번에 하나씩 할당하는 그룹 기반 라이선스를 지원합니다. [라이선스 할당, 확인 및 문제 해결](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자가 해당 사용자에 대해 “사용 위치” 속성을 지정해야 합니다. 라이선스의 할당은 Azure Portal의 [사용자 > 프로필 > 설정]에서 수행할 수 있습니다. **그룹 라이선스 할당을 사용할 때 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다.**

## <a name="next-steps"></a>다음 단계

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](active-directory-passwords-best-practices.md)
* [암호 재설정 또는 변경](active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](active-directory-passwords-data.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](active-directory-passwords-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](active-directory-passwords-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](active-directory-passwords-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](active-directory-passwords-how-it-works.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)
