---
title: 로그인한 후 애플리케이션 페이지의 오류 | Microsoft Docs
description: 애플리케이션 자체에서 오류를 내보내는 경우 Azure AD 로그인에서 발생한 문제를 해결하는 방법
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
ms.openlocfilehash: adfc96d2d7abf38c00f32a5d53615bb7c99c320e
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742378"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>로그인한 후 애플리케이션 페이지의 오류

이 시나리오에서는 Azure AD에 로그인한 사용자가 있지만 애플리케이션에서 사용자가 로그인 흐름을 성공적으로 완료할 수 없는 오류를 표시합니다. 이 시나리오에서 애플리케이션은 Azure AD에서 발생한 응답을 수락하지 않습니다.

애플리케이션이 Azure AD의 응답을 수락하지 않은 이유에는 몇 가지가 있습니다. 애플리케이션의 오류가 응답에서 누락된 내용을 명확하게 파악하지 못한 경우, 다음을 수행합니다.

-   애플리케이션이 Azure AD 갤러리인 경우 [Azure Active Directory의 애플리케이션에 SAML 기반 Single Sign-On을 디버깅하는 방법](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging) 문서에 있는 모든 단계를 수행했는지 확인합니다.

-   [Fiddler](https://www.telerik.com/fiddler)와 같은 도구를 사용하여 SAML 요청, SAML 응답 및 SAML 토큰을 캡처합니다.

-   애플리케이션 공급 업체와 함께 SAML 응답을 공유하여 누락된 내용을 파악합니다.

## <a name="missing-attributes-in-the-saml-response"></a>SAML 응답에서 누락된 특성

Azure AD 응답으로 보낼 Azure AD 구성의 특성을 추가하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5. **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8. **사용자 특성** 섹션에서 **다른 모든 사용자 특성 보기 및 편집**을 클릭하여 사용자가 로그인할 때 SAML 토큰을 통해 애플리케이션으로 보낼 특성을 편집합니다.

   특성을 추가하려면:

   * **특성 추가**를 클릭합니다. **이름**을 입력하고 드롭다운에서 **값**을 선택합니다.

   * **저장**을 클릭합니다. 테이블에 새 특성이 표시됩니다.

9. 구성을 저장합니다.

다음 번에 사용자가 애플리케이션에 로그인하면 Azure AD는 SAML 응답으로 새 특성을 보냅니다.

## <a name="the-application-doesnt-identify-the-user"></a>응용 프로그램 사용자를 식별 하지 않습니다.

SAML 응답이 역할과 같은 특성을 누락 하거나 응용 프로그램은 다른 형식이 나 값 EntityID 특성에 대 한 것 이기 때문에 응용 프로그램에 로그인 실패 됩니다.

사용 중인 경우 [Azure AD 자동 사용자 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 을 만들려면 유지 관리 및 응용 프로그램에서 사용자를 제거 합니다. 그런 다음 사용자가 SaaS 응용 프로그램을 제대로 준비 되었는지 확인 합니다. 자세한 내용은 참조 하세요. [사용자가 Azure AD 갤러리 응용 프로그램을 프로 비전 됨](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Azure AD 애플리케이션 구성에 특성을 추가합니다.

사용자 ID 값을 변경하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5. **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8. **사용자 특성**의 **사용자 식별자** 드롭다운에서 사용자의 고유한 식별자를 선택합니다.

## <a name="change-entityid-user-identifier-format"></a>EntityID(사용자 식별자) 형식 변경

애플리케이션이 EntityID 특성에 대해 다른 형식을 필요로 하는 경우입니다. 사용자 인증 후에 Azure AD에서 응답을 통해 애플리케이션으로 보내는 EntityID(사용자 식별자) 형식은 선택할 수 없습니다.

Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 애플리케이션이 요청한 형식을 기반으로 NameID 특성(사용자 식별자)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) 문서에서 확인할 수 있습니다.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>애플리케이션은 SAML 응답에 대해 다른 시그니처 메서드를 필요로 합니다.

Azure Active Directory에서 디지털 방식으로 로그인한 SAML 토큰을 변경하려고 합니다. 다음 단계를 수행하세요.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5. **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8. **SAML 서명 인증서** 섹션에서 **고급 인증서 서명 설정 표시**를 클릭합니다.

9. 애플리케이션에 필요한 적절한 **서명 옵션**을 선택합니다.

   * SAML 응답 서명

   * SAML 응답 및 어설션 서명

   * SAML 어설션 서명

다음 번에 사용자가 애플리케이션에 로그인하면 Azure AD는 선택한 SAML 응답의 일부를 서명합니다.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>애플리케이션에 필요한 SHA-1이라는 서명 알고리즘

기본적으로 Azure AD는 대부분의 보안 알고리즘을 사용하여 SAML 토큰을 서명합니다. 애플리케이션에서 필요한 경우가 아니면 SHA-1로 서명 알고리즘을 변경하는 것을 권장하지 않습니다.

서명 알고리즘을 변경하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5. **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8. **SAML 서명 인증서** 섹션에서 **고급 인증서 서명 설정 표시**를 클릭합니다.

9. **서명 알고리즘**에서 SHA-1을 선택합니다.

다음 번에 사용자가 애플리케이션에 로그인하면 Azure AD는 SHA-1 알고리즘을 사용하여 SAML 토큰을 서명합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그하는 방법](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
