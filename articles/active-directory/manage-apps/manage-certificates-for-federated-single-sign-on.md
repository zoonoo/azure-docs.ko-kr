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
ms.openlocfilehash: ecadb499d140ccfc993820080cae0b749977fc61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824734"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리

이 문서에서는 일반적인 질문과 정보를 Azure Active Directory (Azure AD)를 설정 하기 위해 페더레이션된 single sign-on (SSO) 소프트웨어는 서비스 (SaaS) 응용 프로그램으로 만듭니다 인증서와 관련 된 다룹니다. Azure AD 앱 갤러리에서 또는 비갤러리 애플리케이션 템플릿을 사용하여 애플리케이션을 추가합니다. 페더레이션된 SSO 옵션을 사용하여 애플리케이션을 구성합니다.

이 문서를 통해 Azure AD SSO를 사용 하도록 구성 된 앱에만 관련이 [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 페더레이션 합니다.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>갤러리 및 비갤러리 애플리케이션에 대해 자동 생성된 인증서

갤러리에서 새 응용 프로그램을 추가 하 고는 SAML 기반 로그온 구성 (선택 하 여 **Single sign on** > **SAML** 응용 프로그램 개요 페이지에서), Azure AD에서 생성 된 3 년 동안 잘못 된 응용 프로그램에 대 한 인증서입니다. 보안 인증서로 활성 인증서를 다운로드 하려면 ( **.cer**) 파일을 해당 페이지로 돌아가서 (**SAML 기반 로그온**)에서 다운로드 링크를 선택 하 고는 **SAML 서명 인증서** 제목입니다. 원시 (이진) 인증서 또는 Base64 (기본 64로 인코딩된 텍스트) 인증서를 선택할 수 있습니다. 이 섹션에서는 갤러리 응용 프로그램에 대 한 페더레이션 메타 데이터 XML로 인증서를 다운로드 하려면 링크를 표시할 수도 있습니다 (프로그램 **.xml** 파일), 응용 프로그램의 요구 사항에 따라 합니다.

![SAML 활성 서명 인증서 다운로드 옵션](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

활성 또는 비활성 인증서를 선택 하 여 다운로드할 수도 있습니다는 **SAML 서명 인증서** 제목의 **편집** 표시 하는 아이콘 (연필)는 **SAML 서명 인증서** 페이지. 줄임표를 선택 ( **...** ) 원하는 다운로드를 선택한 후는 인증서 형식을 원하는 인증서 옆에 있는 합니다. 개인 정보 보호 향상 된 메일 (PEM) 형식으로 인증서를 다운로드 하려면 추가 옵션이 있습니다. 이 형식은 Base64와 동일 하지만 **.pem** 파일 이름 확장명을 인증서 형식으로 Windows에서 인식할 수 없습니다.

![SAML 서명 인증서 다운로드 옵션 (활성 및 비활성)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>페더레이션 인증서의 만료 날짜 사용자 지정 및 새 인증서 롤오버

기본적으로 Azure는 SAML single sign-on 구성 하는 동안 자동으로 생성 될 때 3 년 후에 만료 되도록 인증서를 구성 합니다. 저장 한 후 인증서의 날짜를 변경할 수 없으므로, 해야 합니다.

1. 원하는 날짜를 사용 하 여 새 인증서를 만듭니다.
2. 새 인증서를 저장 합니다.
3. 올바른 형식으로 새 인증서를 다운로드 합니다.
4. 응용 프로그램에 새 인증서를 업로드 합니다.
5. 활성 Azure Active Directory 포털에서 새 인증서를 확인 합니다.

다음 두 섹션에서는 이러한 단계를 수행 수 있습니다.

### <a name="create-a-new-certificate"></a>새 인증서 만들기

먼저 만들고 다른 만료 날짜를 사용 하 여 새 인증서를 저장 합니다.

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/)합니다. 합니다 **Azure Active Directory 관리 센터** 페이지가 나타납니다.

2. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 계정에 엔터프라이즈 응용 프로그램 목록이 표시 됩니다.

3. 영향을 받는 응용 프로그램을 선택 합니다. 응용 프로그램에 대 한 개요 페이지에 표시 됩니다.

4. 응용 프로그램 개요 페이지의 왼쪽된 창에서 선택 **Single sign on**합니다.

5. 경우는 **단일 로그온 방법 선택** 선택 페이지가 표시 되 면 **SAML**합니다.

6. 에 **구성에서 Single Sign-on SAML-미리 보기를 사용 하 여 설정** 페이지에서 **SAML 서명 인증서** 제목과 선택는 **편집** 아이콘 (연필). **SAML 서명 인증서** 상태를 표시 하는 페이지가 표시 됩니다 (**Active** 하거나 **비활성**), 만료 날짜 및 각 인증서의 지문 (해시 문자열)입니다.

7. 선택 **새 인증서**합니다. 새 행을 프로세스를 정확 하 게 3 년 현재 날짜 후에 만료 날짜가 기본적으로 인증서 목록 아래에 나타납니다. (변경 내용이 아직 저장 되지 않은, 만료 날짜를 계속 수정할 수 있습니다.)

8. 만료 날짜 열을 마우스로으로 새 인증서 만들기에서를 선택 합니다 **날짜 선택** 아이콘 (달력). 달력 컨트롤이 표시 새 행의 현재 만료 날짜의 월의 일 수를 표시 합니다.

9. 새 날짜를 설정 하 고 달력 컨트롤을 사용 합니다. 현재 날짜와 현재 날짜 이후 3 년 동안 까지의 모든 날짜를 설정할 수 있습니다.

10. **저장**을 선택합니다. 새 인증서의 상태를 사용 하 여 나타납니다 **비활성**, 만료 날짜를 선택 및 지문을 것입니다.

11. 선택 합니다 **X** 돌아가려면 합니다 **등록에서 Single Sign-on SAML-미리 보기를 사용 하 여 설정** 페이지.

### <a name="upload-and-activate-a-certificate"></a>업로드 하 고 인증서를 활성화 합니다.

그런 다음 올바른 형식으로 새 인증서를 다운로드, 응용 프로그램에 업로드 및 Azure Active Directory에서 활성화:

1. 추가 응용 프로그램의 SAML 로그온 구성 지침을 보려면:
   - 선택 하 여 **구성 가이드** 별도 브라우저 창이 나 탭에서 보려는 링크 또는
   - 하려고 합니다 **설정** 제목 및 선택 **단계별 지침을 보려면** 세로 막대의 합니다.

2. 지침에서 note 인증서 업로드에 대 한 필요한 인코딩 형식입니다.

3. 지침에 따라 합니다 [갤러리 및 비 갤러리 응용 프로그램에 대 한 인증서 자동 생성 된](#auto-generated-certificate-for-gallery-and-non-gallery-applications) 앞 섹션입니다. 이 단계는 업로드에 대 한 응용 프로그램에 필요한 인코딩 형식으로 인증서를 다운로드 합니다.

4. 새 인증서를 롤오버 하려는 경우로 다시 이동 합니다 **SAML 서명 인증서** 페이지를 하 고 새로 저장된 된 인증서 행에서 줄임표를 선택 ( **...** )을 선택 하 고 **인증서를 활성화**합니다. 새 인증서의 상태가으로 변경 **Active**, 및 이전에 활성 인증서의 상태 변경 사항을 **비활성**합니다.

5. 다음 SAML 서명 업로드할 수 있도록 이전에 표시 하는 응용 프로그램의 SAML 로그온 구성 지침 올바른 인코딩 형식으로 인증서를 계속 합니다.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>인증서 만료에 대 한 전자 메일 알림 주소를 추가 합니다.

Azure AD는 SAML 인증서가 만료 되기 전에 전자 메일 알림 60, 30 및 7 일을 보냅니다. 알림을 받으려면 둘 이상의 전자 메일 주소를 추가할 수 있습니다. 전자 메일 주소를 지정 하려는 알림을 전송할 수 있도록 합니다.

1. 에 **SAML 서명 인증서** 페이지로 이동 하는 **알림 전자 메일 주소** 제목입니다. 기본적으로이 머리글에는 응용 프로그램을 추가 하는 관리자의 전자 메일 주소만을 사용 합니다.

2. 아래 최종 전자 메일 주소, 인증서의 만료 경고를 수신 하 고 enter 키를 눌러 해야 할 전자 메일 주소를 입력 합니다.

3. 추가 하려는 각 전자 메일 주소에 대해 이전 단계를 반복 합니다.

4. 삭제 하려는 각 전자 메일 주소를 선택 합니다 **삭제** 전자 메일 주소 옆의 아이콘 (휴지통).

5. **저장**을 선택합니다.

aadnotification@microsoft.com으로부터 알림 이메일을 받습니다. 전자 메일 스팸 위치로 이동 하지 않으려면이 전자 메일 연락처를 추가 합니다.

## <a name="renew-a-certificate-that-will-soon-expire"></a>곧 만료되는 인증서 갱신

인증서가 만료 되려고 하는 것을 사용자에 게 상당한 가동 중지 시간 없이 발생 하는 프로시저를 사용 하 여 갱신할 수 있습니다. 만료 되는 인증서를 갱신 합니다.

1. 지침에 따라 합니다 [새 인증서를 만들려면](#create-a-new-certificate) 겹치는 날짜를 사용 하 여 기존 인증서를 사용 하 여 앞 섹션입니다. 날짜는 인증서 만료로 인해 가동 중지 시간을 제한 합니다.

2. 응용 프로그램 인증서를 자동으로 롤오버 수, 하는 경우 다음이 단계를 수행 하 여 새 인증서를 활성으로 설정 합니다.
   1. 로 다시 이동 합니다 **SAML 서명 인증서** 페이지입니다.
   2. 새로 저장된 된 인증서 행에서 줄임표를 선택 ( **...** )를 선택한 **인증서를 활성화**합니다.
   3. 다음 두 단계를 건너뜁니다.

3. 앱이 한 번에 하나의 인증서 처리할만 수, 하는 경우 다음 단계를 수행 하는 가동 중지 시간 간격을 선택 합니다. (그렇지 않은 경우 응용 프로그램 새 인증서를 자동으로 선택 하지 않습니다 하지만 둘 이상의 서명 인증서를 처리할 수 있는 경우 수행할 수 있습니다 다음 단계 언제 든 지.)

4. 이전 인증서가 만료 되기 전에의 지침에 따라 합니다 [업로드 하 고 인증서를 활성화](#upload-and-activate-a-certificate) 앞 섹션입니다.

5. 인증서를 올바르게 작동 하는지 확인 하려면 응용 프로그램에 로그인 합니다.

## <a name="related-articles"></a>관련 문서

* [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](../saas-apps/tutorial-list.md)
* [Azure Active Directory를 사용 하 여 응용 프로그램 관리](what-is-application-management.md)
* [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)
* [Azure Active Directory의 응용 프로그램에 SAML 기반 single sign on 디버그](../develop/howto-v1-debug-saml-sso-issues.md)
