---
title: Azure AD에서 페더레이션 인증서 관리 | Microsoft Docs
description: 페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법 및 곧 만료되는 인증서를 갱신하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159032"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리

이 문서에서는 Azure Active Directory(Azure AD)가 SSO(페더레이션된 단일 사인온)를 서비스(SaaS) 응용 프로그램으로 소프트웨어에 설정하기 위해 만드는 인증서와 관련된 일반적인 질문과 정보를 다룹니다. Azure AD 앱 갤러리에서 또는 비갤러리 애플리케이션 템플릿을 사용하여 애플리케이션을 추가합니다. 페더레이션된 SSO 옵션을 사용하여 애플리케이션을 구성합니다.

이 문서는 [SAML(보안 어설션 태그 언어)](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 페더레이션을 통해 Azure AD SSO를 사용하도록 구성된 앱에만 관련이 있습니다.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>갤러리 및 비갤러리 애플리케이션에 대해 자동 생성된 인증서

갤러리에서 새 응용 프로그램을 추가하고 SAML 기반 사인온을 구성할 때(응용 프로그램 개요 페이지에서 **단일 사인온** > **SAML을** 선택함) Azure AD는 3년 동안 유효한 응용 프로그램에 대한 인증서를 생성합니다. 활성 인증서를 보안**인증서(.cer)** 파일로 다운로드하려면 해당**페이지(SAML 기반 사인온)로**돌아가서 **SAML 서명 인증서** 제목에서 다운로드 링크를 선택합니다. 원시(바이너리) 인증서 또는 Base64(기본 64인코딩된 텍스트) 인증서 중에서 선택할 수 있습니다. 갤러리 응용 프로그램의 경우 이 섹션에서는 응용 프로그램의 요구 사항에 따라 인증서를 페더레이션 메타데이터 **XML(.xml** 파일)으로 다운로드하는 링크가 표시될 수도 있습니다.

![SAML 활성 서명 인증서 다운로드 옵션](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

SAML 서명 인증서 페이지를 표시하는 **SAML 서명 인증서** 제목의 **편집** 아이콘(연필)을 선택하여 활성 또는 비활성 **인증서를** 다운로드할 수도 있습니다. 다운로드할 인증서 옆에 **...** 있는 타원(... ) 을 선택한 다음 원하는 인증서 형식을 선택합니다. 개인 정보 보호를 강화한 PEM(메일) 형식으로 인증서를 다운로드할 수 있는 추가 옵션이 있습니다. 이 형식은 Base64와 동일하지만 **.pem** 파일 이름 확장명이 있으며 Windows에서 인증서 형식으로 인식되지 않습니다.

![SAML 서명 인증서 다운로드 옵션(활성 및 비활성)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>페더레이션 인증서의 만료 날짜 사용자 지정 및 새 인증서 롤오버

기본적으로 Azure는 SAML 단일 사인온 구성 중에 인증서가 자동으로 생성되면 3년 후에 만료하도록 인증서를 구성합니다. 인증서를 저장한 후에는 인증서 날짜를 변경할 수 없으므로 다음을 수행해야 합니다.

1. 원하는 날짜로 새 인증서를 만듭니다.
1. 새 인증서를 저장합니다.
1. 올바른 형식으로 새 인증서를 다운로드합니다.
1. 응용 프로그램에 새 인증서를 업로드합니다.
1. Azure Active 디렉터리 포털에서 새 인증서를 활성화합니다.

다음 두 섹션에서는 이러한 단계를 수행하는 데 도움이 됩니다.

### <a name="create-a-new-certificate"></a>새 인증서 만들기

먼저 만료 날짜가 다른 새 인증서를 만들고 저장합니다.

1. Azure Active [디렉터리 포털에](https://aad.portal.azure.com/)로그인합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 계정의 엔터프라이즈 응용 프로그램 목록이 나타납니다.
1. 영향을 받는 응용 프로그램을 선택합니다. 응용 프로그램에 대한 개요 페이지가 나타납니다.
1. 응용 프로그램 개요 페이지의 왼쪽 창에서 **단일 사인온을**선택합니다.
1. 단일 **사인온 방법 선택 페이지가** 나타나면 **SAML**을 선택합니다.
1. **SAML을 사용 하 고 단일 사인온 설정 - 미리 보기** 페이지에서 **SAML 서명 인증서** 제목을 찾아 **편집** 아이콘(연필)을 선택합니다. **SAML 서명 인증서** 페이지가 나타나며 각 인증서의 상태(활성 또는 **비활성),** 만료 날짜 및 지문(해시 문자열)이 표시됩니다.**Active**
1. **새 인증서를**선택합니다. 만료 날짜가 현재 날짜이후 정확히 3년으로 기본값인 인증서 목록 아래에 새 행이 나타납니다. 변경 내용이 아직 저장되지 않았으므로 만료 날짜를 수정할 수 있습니다.
1. 새 인증서 행에서 만료 날짜 열 위로 마우스를 가져가고 **날짜 선택** 아이콘(달력)을 선택합니다. 새 행의 현재 만료 날짜의 월 일을 표시하는 달력 컨트롤이 나타납니다.
1. 캘린더 컨트롤을 사용하여 새 날짜를 설정합니다. 현재 날짜와 현재 날짜 이후 3년 사이의 날짜를 설정할 수 있습니다.
1. **저장**을 선택합니다. 이제 새 인증서가 **비활성**상태, 선택한 만료 날짜 및 지문과 함께 표시됩니다.
1. SamL - 미리 보기 페이지에서 **단일 사인온 설정으로** 돌아가려면 **X를** 선택합니다.

### <a name="upload-and-activate-a-certificate"></a>인증서 업로드 및 활성화

그런 다음 새 인증서를 올바른 형식으로 다운로드하여 응용 프로그램에 업로드한 다음 Azure Active Directory에서 활성화합니다.

1. 응용 프로그램의 추가 SAML 사인온 구성 지침을 다음 중 하나씩 확인합니다.

   - 별도의 브라우저 창 이나 탭에서 볼 **구성 가이드** 링크를 선택 하거나
   - **설정** 제목으로 이동하여 사이드바에서 볼 **단계별 지침 보기를** 선택합니다.

1. 지침에서 인증서 업로드에 필요한 인코딩 형식을 기록합니다.
1. [갤러리 및 비갤러리 응용 프로그램](#auto-generated-certificate-for-gallery-and-non-gallery-applications) 섹션에 대한 자동 생성 인증서의 지침을 따르십시오. 이 단계에서는 응용 프로그램에서 업로드하는 데 필요한 인코딩 형식으로 인증서를 다운로드합니다.
1. 새 인증서로 롤오버하려면 **SAML 서명 인증서** 페이지로 돌아가서 새로 저장된 인증서 행에서 타원(...**...**)을 선택하고 **인증서 활성화를**선택합니다. 새 인증서의 상태가 **활성으로**변경되고 이전에 활성 인증서가 **비활성**상태로 변경됩니다.
1. SAML 서명 인증서를 올바른 인코딩 형식으로 업로드할 수 있도록 이전에 표시한 응용 프로그램의 SAML 사인온 구성 지침을 계속 따르십시오.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>인증서 만료를 위한 이메일 알림 주소 추가

Azure AD는 SAML 인증서가 만료되기 60일, 30일 및 7일 전에 전자 메일 알림을 보냅니다. 알림을 받으려면 두 개 이상의 이메일 주소를 추가할 수 있습니다. 알림을 보낼 전자 메일 주소(es)를 지정하려면 다음 을 수행합니다.

1. **SAML 서명 인증서** 페이지에서 알림 **전자 메일 주소 제목으로 이동합니다.** 기본적으로 이 제목은 응용 프로그램을 추가한 관리자의 전자 메일 주소만 사용합니다.
1. 최종 전자 메일 주소 아래에 인증서 만료 알림을 받아야 하는 전자 메일 주소를 입력한 다음 Enter를 누릅니다.
1. 추가하려는 각 이메일 주소에 대해 이전 단계를 반복합니다.
1. 삭제하려는 각 이메일 주소에 대해 이메일 주소 옆에 있는 **삭제** 아이콘(가비지 캔)을 선택합니다.
1. **저장**을 선택합니다.

aadnotification@microsoft.com으로부터 알림 이메일을 받습니다. 스팸 위치로 전송되는 이메일을 방지하려면 이 이메일을 연락처에 추가합니다.

## <a name="renew-a-certificate-that-will-soon-expire"></a>곧 만료되는 인증서 갱신

인증서가 곧 만료될 경우 사용자에게 심각한 가동 중지 시간이 없는 프로시저를 사용하여 인증서를 갱신할 수 있습니다. 만료 인증서를 갱신하려면 다음을 수행하십시오.

1. 기존 [인증서와](#create-a-new-certificate) 겹치는 날짜를 사용하여 새 인증서 만들기 섹션의 지침을 따르십시오. 이 날짜는 인증서 만료로 인한 가동 중지 시간을 제한합니다.
1. 응용 프로그램이 인증서를 자동으로 롤오버할 수 있는 경우 다음 단계에 따라 새 인증서를 활성으로 설정합니다.
   1. **SAML 서명 인증서** 페이지로 돌아갑니다.
   1. 새로 저장된 인증서 행에서 타원 **(...**) 을 선택한 다음 **인증서 를 활성화합니다.**
   1. 다음 두 단계를 건너뜁니다.

1. 앱이 한 번에 하나의 인증서만 처리할 수 있는 경우 가동 중지 시간 간격을 선택하여 다음 단계를 수행합니다. 그렇지 않으면 응용 프로그램이 새 인증서를 자동으로 선택하지 않지만 두 개 이상의 서명 인증서를 처리할 수 있는 경우 언제든지 다음 단계를 수행할 수 있습니다.
1. 이전 인증서가 만료되기 전에 [업로드의](#upload-and-activate-a-certificate) 지침을 따르고 인증서 섹션을 더 일찍 활성화합니다.
1. 인증서가 제대로 작동하는지 확인하려면 응용 프로그램에 로그인합니다.

## <a name="related-articles"></a>관련 문서

- [SaaS 애플리케이션과 Azure Active Directory를 통합하는 방법에 대한 자습서](../saas-apps/tutorial-list.md)
- [Azure Active Directory의 애플리케이션 관리](what-is-application-management.md)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)
- [Azure Active Directory의 응용 프로그램에 SAML 기반 단일 사인온 디버그](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
