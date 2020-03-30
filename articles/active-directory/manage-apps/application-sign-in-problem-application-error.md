---
title: 로그인 한 후 앱 페이지에 오류 메시지가 나타납니다 | 마이크로 소프트 문서
description: 앱에서 오류 메시지를 반환할 때 Azure AD 로그인 문제를 해결하는 방법
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185487"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>앱 페이지에 사용자가 에 서명한 후 오류 메시지가 표시됩니다.

이 시나리오에서는 Azure Active Directory(Azure AD)가 사용자를 에 서명합니다. 그러나 응용 프로그램에 오류 메시지가 표시되고 사용자가 로그인 흐름을 완료하지 못하게 합니다. 문제는 앱이 Azure AD가 발행한 응답을 수락하지 않았다는 것입니다.

앱이 Azure AD의 응답을 수락하지 않은 데는 여러 가지 이유가 있을 수 있습니다. 오류 메시지가 응답에서 누락된 내용을 명확하게 식별하지 못하는 경우 다음을 시도해 보십시오.

-   앱이 Azure AD 갤러리인 경우 [Azure AD의 응용 프로그램에 SAML 기반 단일 사인온을 디버깅하는 방법의](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)단계를 따랐는지 확인합니다.

-   [Fiddler와](https://www.telerik.com/fiddler) 같은 도구를 사용하여 SAML 요청, 응답 및 토큰을 캡처합니다.

-   SAML 응답을 앱 공급업체에 보내고 누락된 내용을 물어봅니다.

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 응답에서 특성이 누락되었습니다.

Azure AD 응답에서 전송될 Azure AD 구성에 특성을 추가하려면 다음 단계를 따르십시오.

1. Azure [**포털을**](https://portal.azure.com/) 열고 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽 탐색 창 상단에서 **모든 서비스를** 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을**사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. 단일 사인온에 대해 구성할 응용 프로그램을 선택합니다.

7. 앱이 로드된 후 탐색 창에서 **단일 사인온을** 선택합니다.

8. 사용자 **특성** 섹션에서 보기를 선택하고 **다른 모든 사용자 특성을 편집합니다.** 여기서 사용자가 로그인할 때 SAML 토큰에서 앱에 보낼 속성을 변경할 수 있습니다.

   특성을 추가하려면:

   1. **특성 추가**를 선택합니다. **이름을**입력하고 드롭다운 목록에서 **값을** 선택합니다.

   1.  **저장**을 선택합니다. 테이블에 새 특성이 표시됩니다.

9. 구성을 저장합니다.

   사용자가 다음에 앱에 로그인할 때 Azure AD는 SAML 응답에서 새 특성을 보냅니다.

## <a name="the-app-doesnt-identify-the-user"></a>앱에서 사용자를 식별하지 않습니다.

SAML 응답에 역할과 같은 특성이 없기 때문에 앱에 로그인하지 못합니다. 또는 앱이 **NameID(사용자** 식별자) 특성에 대해 다른 형식이나 값을 기대하기 때문에 실패합니다.

[Azure AD 자동화된 사용자 프로비저닝을](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 사용하여 앱에서 사용자를 생성, 유지 관리 및 제거하는 경우 사용자가 SaaS 앱에 프로비전되었는지 확인합니다. 자세한 내용은 [Azure AD 갤러리 응용 프로그램에 프로비전중인 사용자 없음을](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)참조하십시오.

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Azure AD 앱 구성에 특성 추가

사용자 ID 값을 변경하려면 아래 단계를 수행합니다.

1. Azure [**포털을**](https://portal.azure.com/) 열고 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽 탐색 창 상단에 있는 **모든 서비스를** 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을**사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. SSO에 대해 구성할 앱을 선택합니다.

7. 앱이 로드된 후 탐색 창에서 **단일 사인온을** 선택합니다.

8. **사용자 특성에서** **사용자 식별자** 드롭다운 목록에서 사용자에 대한 고유 식별자를 선택합니다.

## <a name="change-the-nameid-format"></a>NameID 형식 변경

응용 프로그램에서 **NameID(사용자** 식별자) 특성에 대한 다른 형식을 예상하는 경우 [nameID 편집을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) 참조하여 NameID 형식을 변경합니다.

Azure AD는 선택한 값 또는 SAML AuthRequest에서 앱에서 요청한 형식을 기반으로 **NameID** 특성(사용자 식별자)의 형식을 선택합니다. 자세한 내용은 [단일 사인온 SAML 프로토콜의](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)"NameIDPolicy" 섹션을 참조하십시오.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>앱은 SAML 응답에 대해 다른 서명 방법을 기대합니다.

Azure AD에서 SAML 토큰의 디지털 서명부분을 변경하려면 다음 단계를 따르세요.

1. Azure [포털을](https://portal.azure.com/) 열고 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽 탐색 창 상단에 있는 **모든 서비스를** 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 응용 프로그램이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을**사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. 단일 사인온에 대해 구성할 응용 프로그램을 선택합니다.

7. 응용 프로그램이 로드된 후 탐색 창에서 **Single sign-on을** 선택합니다.

8. **SAML 서명 인증서에서** **고급 인증서 서명 설정 표시를**선택합니다.

9. 다음 옵션 중에서 앱이 기대하는 **서명** 옵션을 선택합니다.

   * **SAML 응답 에 서명**
   * **SAML 응답 및 어설션 서명**
   * **SAML 어설션 서명**

   사용자가 다음에 앱에 로그인할 때 Azure AD는 선택한 SAML 응답의 일부에 서명합니다.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>응용 프로그램은 SHA-1 서명 알고리즘을 기대

기본적으로 Azure AD는 가장 안전한 알고리즘을 사용하여 SAML 토큰에 서명합니다. 앱에 SHA-1이 필요하지 않은 한 서명 알고리즘을 *SHA-1로* 변경하지 않는 것이 좋습니다.

서명 알고리즘을 변경하려면 아래 단계를 수행합니다.

1. Azure [포털을](https://portal.azure.com/) 열고 글로벌 관리자 또는 공동 관리자로 로그인합니다.

2. 왼쪽 탐색 창 상단에 있는 **모든 서비스를** 선택하여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory를** 입력한 다음 **Azure Active Directory**를 선택합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다.

5. **모든 응용 프로그램을** 선택하여 응용 프로그램 목록을 봅니다.

   > [!NOTE]
   > 원하는 응용 프로그램이 표시되지 않으면 모든 응용 프로그램 목록 맨 위에 있는 **필터** **컨트롤을**사용합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정합니다.

6. 단일 사인온에 대해 구성할 앱을 선택합니다.

7. 앱이 로드된 후 앱 왼쪽의 탐색 창에서 **Single sign-on을** 선택합니다.

8. **SAML 서명 인증서에서** **고급 인증서 서명 설정 표시를**선택합니다.

9. **서명 알고리즘으로** **SHA-1을** 선택합니다.

   사용자가 다음에 앱에 로그인할 때 Azure AD는 SHA-1 알고리즘을 사용하여 SAML 토큰에 서명합니다.

## <a name="next-steps"></a>다음 단계
[Azure AD의 응용 프로그램에 SAML 기반 단일 사인온을 디버깅하는 방법.](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
