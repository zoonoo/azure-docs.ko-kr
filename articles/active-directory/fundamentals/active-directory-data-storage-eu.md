---
title: 유럽 고객을 위한 ID 데이터 스토리지 - Azure AD
description: Azure Active Directory에서 유럽 고객의 ID 관련 데이터를 저장하는 위치에 대해 알아봅니다.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836940"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory에서 유럽 고객에 대한 ID 데이터 스토리지
ID 데이터는 Microsoft 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공하는 주소에 따라 지리적 위치에서 Azure AD에 의해 저장됩니다. ID 데이터를 저장하는 위치에 대한 정보는 Microsoft 보안 센터의 [데이터를 어디에 배치하나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션에서 확인할 수 있습니다.

유럽에 있는 주소를 제공한 고객의 경우 Azure AD는 유럽 데이터 센터 내에 대부분의 ID 데이터를 보관합니다. 이 문서에서는 Azure AD 서비스에 의해 유럽 외부에 저장되는 모든 데이터에 관해 설명합니다.

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD Multi-Factor Authentication

클라우드 기반 Azure AD Multi-Factor Authentication의 경우 사용자와 가장 가까운 데이터 센터에서 인증이 완료됩니다. Azure AD Multi-Factor Authentication의 데이터 센터는 북아메리카, 유럽, 아시아 태평양에 있습니다.

* 전화 통화를 사용하는 다단계 인증은 일반적으로 미국 데이터 센터에서 발생하고 글로벌 공급자에 의해 라우팅됩니다.
* SMS를 사용하는 다단계 인증은 글로벌 공급자에 의해 라우팅됩니다.
* EU 데이터 센터에서 발생하는 Microsoft Authenticator 앱 푸시 알림을 사용하는 다단계 인증 요청은 EU 데이터 센터에서 처리됩니다.
    * Apple 푸시 알림과 같은 디바이스 공급업체별 서비스는 유럽 외부에 있을 수 있습니다.
* EU 데이터 센터에서 발생하는 OATH 코드를 사용하는 다단계 인증 요청은 EU에서 유효성이 검사됩니다.

MFA 서버(Azure Multi-Factor Authentication 서버) 및 클라우드 기반 Azure AD MFA에서 수집하는 사용자 정보에 대한 자세한 내용은 [Azure Multi-Factor Authentication 사용자 데이터 컬렉션](../authentication/howto-mfa-reporting-datacollection.md)을 참조하세요.

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>엔터프라이즈 애플리케이션에 대한 암호 기반 Single Sign-On
 
고객이 (Azure AD 갤러리 또는 비 갤러리를 통해) 새 엔터프라이즈 애플리케이션을 만들고 암호 기반 SSO를 사용하도록 설정하면 애플리케이션 로그인 URL 및 사용자 지정 캡처 로그인 필드는 미국에 저장됩니다. 이 기능에 대한 자세한 내용은 [암호 기반 Single Sign-On 구성](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)을 참조하세요.

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure AD B2C(Azure Active Directory B2C)

Azure AD B2C 정책 구성 데이터 및 키 컨테이너는 미국 데이터 센터에 저장됩니다. 여기에는 사용자 개인 데이터가 포함되어 있지 않습니다. 정책 구성에 대한 자세한 내용은 [Azure Active Directory B2C: 기본 제공 정책](../../active-directory-b2c/user-flow-overview.md) 아티클을 참조하세요.

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure AD B2B(Azure Active Directory B2B) 
    
Azure AD B2B는 미국 데이터 센터에서 초대 및 리디렉션 URL 정보를 사용하여 초대를 저장합니다. 또한 B2B 초대 수신을 거부하는 사용자의 메일 주소도 미국 데이터 센터에 저장됩니다.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure AD DS(Azure Active Directory Domain Services)

Azure AD DS는 고객이 선택한 Azure Virtual Network와 동일한 위치에 사용자 데이터를 저장합니다. 따라서 네트워크가 유럽 밖에 있는 경우 데이터가 유럽 외부에 복제되고 저장됩니다.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013의 페더레이션
    
- AppID(애플리케이션 식별자) - Azure Active Directory 인증 시스템에서 생성하여 Exchange 조직을 식별하는 고유 번호입니다.
- 애플리케이션에 대해 승인된 페더레이션된 도메인 목록
- 애플리케이션의 토큰 서명 퍼블릭 키 

Microsoft Exchange 서버의 페더레이션에 대한 자세한 내용은 [페더레이션: Exchange 2013 도움말](/exchange/federation-exchange-2013-help) 문서를 참조하세요.


## <a name="other-considerations"></a>기타 고려 사항

Azure AD와 통합되는 모든 서비스 및 애플리케이션은 ID 데이터에 대한 액세스 권한이 있습니다. ID 데이터가 특정 서비스 및 애플리케이션에서 처리되는 방식과 회사의 데이터 스토리지 요구 사항을 준수하는지를 판단하는 데 사용하는 각 서비스 및 애플리케이션을 평가합니다.

Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 [데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
위에서 설명한 특징 및 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Multi-Factor Authentication이란?](../authentication/concept-mfa-howitworks.md)

- [Azure AD 셀프 서비스 암호 재설정](../authentication/concept-sspr-howitworks.md)

- [Azure Active Directory B2C란?](../../active-directory-b2c/overview.md)

- [Azure AD B2B 협업이란?](../external-identities/what-is-b2b.md)

- [Azure AD(Active Directory) Domain Services](../../active-directory-domain-services/overview.md)