---
title: 로그인 한 후 앱 페이지에 표시 되는 오류 메시지 | Microsoft Docs
description: Azure AD가 앱에서 오류 메시지를 반환 하는 경우 로그인을 사용 하 여 문제를 해결 하는 방법입니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ec1f510b028a2ffe2554bfcbd77bc439c4e79
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272956"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>사용자가 로그인 한 후 앱 페이지에 오류 메시지를 표시

이 시나리오에서는 Azure Active Directory (Azure AD) 사용자를 로그인 합니다. 하지만 응용 프로그램 오류 메시지를 표시 하 고 사용자 로그인 흐름을 완료 하도록 허용 하지 않습니다. 문제는 앱을 Azure AD에서 발급 하는 응답을 허용 하지 않습니다.

앱을 Azure AD의 응답을 수락 하지 않은 이유는 여러 가지 이유가 있습니다. 오류 메시지는 응답에서 빠진 부분은 무엇을 식별 명확 하 게 하지 하는 경우 다음을 시도 합니다.

-   Azure AD 갤러리 응용 프로그램을 사용 하는 경우의 단계를 수행 했는지 확인 하십시오 [Azure AD에서 SAML 기반 single sign 응용 프로그램을 디버깅 하는 방법을](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)합니다.

-   와 같은 도구를 사용 하 여 [Fiddler](https://www.telerik.com/fiddler) SAML 요청, 응답 및 토큰을 캡처할 수 있습니다.

-   앱 공급 업체에 대 한 SAML 응답을 전송 하 고 무엇이 요청 합니다.

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 응답에서 누락 된 특성

Azure AD 응답에서 전송 되는 Azure AD 구성의 특성을 추가 하려면 다음이 단계를 수행 합니다.

1. 엽니다는 [ **Azure portal** ](https://portal.azure.com/) 전역 관리자 또는 공동 관리자 로그인

2. 왼쪽의 탐색 창 맨 위에 있는 선택 **모든 서비스** 를 Azure AD 확장을 엽니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 앱 목록을 볼 수 있습니다.

   > [!NOTE]
   > 사용 하 여 원하는 앱이 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램 목록**. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. Single sign-on을 구성 하려는 응용 프로그램을 선택 합니다.

7. 앱 로드 되 면 선택 **Single sign on** 탐색 창에서.

8. 에 **사용자 특성** 섹션에서 **보기 및 기타 모든 사용자 특성 편집**합니다. 여기서 사용자가 로그인 할 때 SAML 토큰에서 앱으로 보낼 특성을 변경할 수 있습니다.

   특성을 추가하려면:

   1. **특성 추가**를 선택합니다. 입력를 **이름**를 선택 합니다 **값** 드롭 다운 목록에서.

   1.  **저장**을 선택합니다. 테이블에 새 특성이 표시 됩니다.

9. 구성을 저장합니다.

   사용자가 앱에 로그인 하는 다음에 Azure AD에서는 SAML 응답에 새 특성을 보냅니다.

## <a name="the-app-doesnt-identify-the-user"></a>앱 사용자를 식별 하지 않습니다.

앱에 로그인에 SAML 응답에는 역할과 같은 특성이 없기 때문에 실패 합니다. 다른 형식이 나 값에 대 한 앱을 예상 하기 때문에 실패 합니다 **NameID** 특성 (사용자 식별자)입니다.

사용 중인 경우 [Azure AD 자동 사용자 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 을 만들려면 유지 관리 및 앱에서 사용자를 제거, 사용자가 SaaS 앱에 프로 비전 되었는지 확인 합니다. 자세한 내용은 [사용자가 Azure AD 갤러리 응용 프로그램을 프로 비전 됨](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)합니다.

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Azure AD 앱 구성에 특성 추가

사용자 ID 값을 변경하려면 아래 단계를 수행합니다.

1. 엽니다는 [ **Azure portal** ](https://portal.azure.com/) 전역 관리자 또는 공동 관리자 로그인

2. 선택 **모든 서비스** Azure AD 확장을 열고 왼쪽의 탐색 창 맨 위에 있는 합니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 앱 목록을 볼 수 있습니다.

   > [!NOTE]
   > 사용 하 여 원하는 앱이 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램 목록**. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. Sso를 구성 하려는 앱을 선택 합니다.

7. 앱 로드 되 면 선택 **Single sign on** 탐색 창에서.

8. 아래 **사용자 특성**에서 사용자에 대 한 고유 식별자를 선택 합니다 **사용자 식별자** 드롭 다운 목록.

## <a name="change-the-nameid-format"></a>NameID 형식 변경

응용 프로그램에 대해 다른 형식이 필요로 하는 경우는 **NameID** (사용자 식별자) 특성을 참조 하십시오 [편집용 nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization.md#editing-nameid) NameID 형식을 변경 하려면.

Azure AD에 대 한 형식을 선택 합니다 **NameID** 선택한 값 또는 SAML AuthRequest에서 응용 프로그램에서 요청 하는 형식에 따라 특성 (사용자 식별자)입니다. 자세한 내용은의 "NameIDPolicy" 섹션을 참조 하세요 [Single sign-on SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)합니다.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>앱에서는 SAML 응답에 대 한 서로 다른 시그니처 메서드

Azure AD에서 서명한 SAML 토큰 부분을 변경 하려면 다음이 단계를 수행 합니다.

1. 엽니다는 [Azure portal](https://portal.azure.com/) 전역 관리자 또는 공동 관리자 로그인

2. 선택 **모든 서비스** Azure AD 확장을 열고 왼쪽의 탐색 창 맨 위에 있는 합니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 앱 목록을 볼 수 있습니다.

   > [!NOTE]
   > 사용 하 여 응용 프로그램을 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램 목록**. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. Single sign-on을 구성 하려는 응용 프로그램을 선택 합니다.

7. 응용 프로그램이 로드 되 면 선택 **Single sign on** 탐색 창에서.

8. 아래 **SAML 서명 인증서**를 선택 **고급 인증서 서명 설정 표시**합니다.

9. 선택 된 **서명 옵션** 에 이러한 옵션 중에서 필요한 앱.

   * **SAML 응답 서명**
   * **SAML 응답 및 어설션 서명**
   * **SAML 어설션 서명**

   사용자가 앱에 로그인 하는 다음에 Azure AD에는 선택한 SAML 응답의 일부로 서명 됩니다.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>앱에서는 sha-1 서명 알고리즘

기본적으로 Azure AD는 가장 높은 보안 알고리즘을 사용 하 여 SAML 토큰을 서명 합니다. 서명 알고리즘을 변경 하지 않는 것이 좋습니다 *SHA-1(secure* 앱에서 SHA-1(secure 필요한 경우가 아니면 합니다.

서명 알고리즘을 변경하려면 아래 단계를 수행합니다.

1. 엽니다는 [Azure portal](https://portal.azure.com/) 전역 관리자 또는 공동 관리자 로그인

2. 선택 **모든 서비스** Azure AD 확장을 열고 왼쪽의 탐색 창 맨 위에 있는 합니다.

3. 형식 **Azure Active Directory** 에서 필터 검색 상자를 선택 합니다 **Azure Active Directory**합니다.

4. 선택 **엔터프라이즈 응용 프로그램** Azure AD 탐색 창에서.

5. 선택 **모든 응용 프로그램** 응용 프로그램의 목록을 볼 수 있습니다.

   > [!NOTE]
   > 사용 하 여 응용 프로그램을 보이지 않으면 합니다 **필터** 맨 위에 있는 컨트롤을 **모든 응용 프로그램 목록**. 설정 된 **표시** "모든 응용 프로그램입니다." 옵션

6. Single sign-on을 구성 하려는 앱을 선택 합니다.

7. 앱 로드 되 면 선택 **Single sign on** 앱의 왼쪽 탐색 창에서.

8. 아래 **SAML 서명 인증서**를 선택 **고급 인증서 서명 설정 표시**합니다.

9. 선택 **SHA-1(secure** 으로 **서명 알고리즘**합니다.

   사용자가 앱에 로그인 하는 다음에 Azure AD는 sha-1 알고리즘을 사용 하 여 SAML 토큰을 서명 합니다.

## <a name="next-steps"></a>다음 단계
[Azure AD에서 SAML 기반 single sign 응용 프로그램을 디버깅 하는 방법을](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)합니다.
