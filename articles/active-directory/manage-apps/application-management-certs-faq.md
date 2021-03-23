---
title: Azure Active Directory 응용 프로그램 관리 인증서에 대 한 질문과 대답
description: IdP (Id 공급자)로 Azure Active Directory를 사용 하 여 앱에 대 한 인증서 관리에 대 한 FAQ (질문과 대답)에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803803"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory (Azure AD) 응용 프로그램 관리 인증서 faq (질문과 대답)

이 페이지에서는 IdP (Id 공급자)로 Azure Active Directory (Azure AD)를 사용 하 여 앱에 대 한 인증서를 관리 하는 방법에 대 한 질문과 대답을 제공 합니다.

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>만료 되는 SAML 서명 인증서 목록을 생성 하는 방법이 있나요?

[PowerShell 스크립트](app-management-powershell-samples.md)를 통해 CSV 파일의 디렉터리에서 지정 된 앱에 대해 만료 된 암호, 인증서 및 소유자의 모든 앱 등록을 내보낼 수 있습니다. 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>인증서 갱신 단계를 곧 만료 하는 시기에 대 한 정보는 어디에서 확인할 수 있나요?

단계는 [여기](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire)서 찾을 수 있습니다.

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Azure AD에서 발급 한 인증서의 만료 날짜를 사용자 지정 하려면 어떻게 해야 하나요?

기본적으로 Azure AD는 SAML Single Sign-On 구성 중에 자동으로 생성 되는 3 년 후에 만료 되도록 인증서를 구성 합니다. 인증서를 저장 한 후에는 인증서의 날짜를 변경할 수 없으므로 새 인증서를 만들어야 합니다. 이 작업을 수행 하는 방법에 대 한 단계는 [페더레이션 인증서의 만료 날짜를 사용자 지정 하 고 새 인증서로 롤오버](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)를 참조 하세요.

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>인증서 만료 알림을 자동화 하려면 어떻게 해야 하나요?

Azure AD는 SAML 인증서가 만료 되기 전에 60, 30 및 7 일의 전자 메일 알림을 보냅니다. 알림을 받을 전자 메일 주소를 둘 이상 추가할 수 있습니다. 

> [!NOTE]
> 알림 목록에 최대 5 개의 전자 메일 주소를 추가할 수 있습니다 (응용 프로그램을 추가한 관리자의 전자 메일 주소 포함). 더 많은 사용자에 게 알림이 필요한 경우 메일 그룹 메일을 사용 합니다. 

알림을 보낼 전자 메일을 지정 하려면 [인증서 만료에 대 한 전자 메일 알림 주소 추가](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)를 참조 하세요.

에서 받은 이러한 전자 메일 알림을 편집 하거나 사용자 지정 하는 옵션은 없습니다 `aadnotification@microsoft.com` . 그러나 [PowerShell 스크립트](app-management-powershell-samples.md)를 통해 암호 및 인증서가 만료 된 앱 등록을 내보낼 수 있습니다.

## <a name="who-can-update-the-certificates"></a>인증서를 업데이트할 수 있는 사람은 누구 인가요?

응용 프로그램 또는 전역 관리자 또는 응용 프로그램 관리자의 소유자는 Azure Portal UI, PowerShell 또는 Microsoft Graph를 통해 인증서를 업데이트할 수 있습니다.

## <a name="i-need-more-details-about-certificate-signing-options"></a>인증서 서명 옵션에 대 한 자세한 내용이 필요 합니다.

Azure AD에서 인증서 서명 옵션 및 인증서 서명 알고리즘을 설정할 수 있습니다. 자세히 알아보려면 [AZURE AD 앱에 대 한 고급 SAML 토큰 인증서 서명 옵션](certificate-signing-options.md)을 참조 하세요.

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Azure AD 응용 프로그램 프록시 응용 프로그램에 대 한 인증서를 바꾸어야 하며 추가 지침이 필요 합니다.

Azure AD 응용 프로그램 프록시 응용 프로그램에 대 한 인증서를 바꾸려면 [PowerShell 샘플-응용 프로그램 프록시 앱에서 인증서 바꾸기](scripts/powershell-get-custom-domain-replace-cert.md)를 참조 하세요.

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인에 대 한 인증서를 관리 어떻게 할까요??

사용자 지정 도메인을 사용하도록 온-프레미스 앱을 구성하려면 확인된 Azure Active Directory 사용자 지정 도메인, 사용자 지정 도메인에 대한 PFX 인증서 및 구성할 온-프레미스 앱이 필요합니다. 자세히 알아보려면 [Azure AD 응용 프로그램 프록시의 사용자 지정 도메인](application-proxy-configure-custom-domain.md)을 참조 하세요. 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>응용 프로그램 쪽에서 토큰 서명 인증서를 업데이트 해야 합니다. Azure AD 쪽에서 어디에서 얻을 수 있나요?

Saml x.509 인증서를 갱신할 수 있습니다. [Saml 서명 인증서](configure-saml-single-sign-on.md#saml-signing-certificate)를 참조 하세요.

## <a name="what-is-azure-ad-signing-key-rollover"></a>Azure AD 서명 키 롤오버 란?

자세한 내용은 [여기](../develop/active-directory-signing-key-rollover.md)에서 볼 수 있습니다. 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>응용 프로그램 토큰 암호화 인증서를 어떻게 할까요? 갱신 하 시겠습니까?

응용 프로그램 토큰 암호화 인증서를 갱신 하려면 [엔터프라이즈 응용 프로그램에 대 한 토큰 암호화 인증서를 갱신 하는 방법](howto-saml-token-encryption.md)을 참조 하세요. 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>응용 프로그램 토큰 서명 인증서를 어떻게 할까요? 갱신 하 시겠습니까?

응용 프로그램 토큰 서명 인증서를 갱신 하려면 [엔터프라이즈 응용 프로그램에 대 한 토큰 서명 인증서를 갱신 하는 방법](manage-certificates-for-federated-single-sign-on.md)을 참조 하세요.

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>내 페더레이션 인증서를 변경한 후 Azure AD를 업데이트 어떻게 할까요??

페더레이션 인증서를 변경한 후 Azure AD를 업데이트 하려면 [Microsoft 365 및 Azure Active Directory에 대 한 페더레이션 인증서 갱신](../hybrid/how-to-connect-fed-o365-certs.md)을 참조 하세요.
