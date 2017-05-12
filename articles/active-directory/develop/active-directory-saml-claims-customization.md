---
title: "Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰에서 발급된 클레임 사용자 지정 | Microsoft Docs"
description: "Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰에서 발급된 클레임 사용자 지정
현재 Azure Active Directory에서는 SAML 2.0 프로토콜을 사용하여 Single Sign-On을 지원하는 150개 이상의 응용 프로그램을 포함하여 Azure AD 응용 프로그램 갤러리에서 사전 통합된 수천 개의 응용 프로그램을 지원합니다. 사용자가 SAML을 사용하여 Azure AD를 통해 응용 프로그램을 인증하면 Azure AD는 응용 프로그램에 토큰을 보냅니다(HTTP 302 리디렉션을 통해). 그런 다음 응용 프로그램이 토큰의 유효성을 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰에는 "클레임"이라고 알려진 사용자에 대한 정보가 포함되어 있습니다.

ID에서 "클레임"은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다. [SAML 토큰](http://en.wikipedia.org/wiki/SAML_2.0)에서 이 데이터는 일반적으로 SAML Attribute Statement에 포함되고 사용자의 고유 ID는 일반적으로 SAML Subject에 표시됩니다.

기본적으로 Azure AD는 Azure AD에 사용자의 사용자 이름 값과 함께 NameIdentifier 클레임이 포함된 SAML 토큰을 응용 프로그램에 발급합니다. 이 값이 사용자를 고유하게 식별할 수 있습니다. 또한 SAML 토큰에는 사용자의 메일 주소, 이름 및 성을 포함하는 추가 클레임이 포함됩니다.

SAML 토큰에서 응용 프로그램에 발급된 클레임을 보거나 편집하려면 Azure 클래식 포털에서 응용 프로그램 레코드를 열고 응용 프로그램 아래의 **특성** 탭을 선택합니다.

![특성 탭][1]

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.
* 응용 프로그램이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다. 
* Azure Active Directory에 저장된 사용자 이름(AKA 사용자 계정 이름) 이외의 NameIdentifier 클레임을 요구하는 방식으로 응용 프로그램이 배포되었습니다. 

원하는 기본 클레임 값을 편집할 수 있습니다. SAML 토큰 특성 테이블의 행 중 하나 위로 마우스를 가져가면 오른쪽에 나타나는 연필 모양의 아이콘을 선택합니다. 또한 **X** 아이콘을 사용하여 클레임(NameIdentifier 외 기타)을 제거할 수 있고 **사용자 특성 추가** 단추를 사용하여 새 클레임을 추가할 수 있습니다.

## <a name="editing-the-nameidentifier-claim"></a>NameIdentifier 클레임 편집
응용 프로그램이 다른 사용자 이름을 사용하여 배포된 경우의 문제를 해결하려면 NameIdentifier 클레임 옆에 있는 연필 아이콘을 클릭합니다. 이 작업을 수행하면 몇 가지 옵션이 있는 대화 상자가 제공됩니다.

![사용자 특성 편집][2]

**특성 값** 메뉴에서 **user.mail**을 선택하여 NameIdentifier 클레임이 디렉터리에서 사용자의 전자 메일 주소가 되도록 합니다. 또는 **user.onpremisessamaccountname**을 선택하여 온-프레미스 Azure AD에서 동기화된 사용자의 SAM 계정 이름으로 설정합니다.

특수한 ExtractMailPrefix() 함수를 사용하여 전자 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거할 수도 있습니다. 그러면 사용자 이름의 첫 부분만 전달됩니다(예: joe_smith@contoso.com 대신 "joe_smith").

![사용자 특성 편집][3]

## <a name="adding-claims"></a>클레임 추가
클레임을 추가할 때 특성 이름(SAML 사양에 따라 URI 패턴을 엄격히 따를 필요는 없음)을 지정할 수 있습니다. 디렉터리에 저장된 사용자 특성 중 원하는 것으로 값을 설정합니다.

![사용자 특성 추가][4]

예를 들어 조직에서 사용자가 속한 부서를 클레임(에: 영업)으로 보내야 합니다. 응용 프로그램에 필요한 클레임 값이 무엇이든 입력한 다음 **user.department**를 값으로 선택합니다.

지정된 사용자에 대해 선택한 특성에 대한 값이 저장되어 있지 않으면 해당 클레임이 토큰에서 발급되지 않습니다.

**참고:** **user.onpremisesecurityidentifier** 및 **user.onpremisesamaccountname**은 [Azure AD Connect 도구](../active-directory-aadconnect.md)를 사용하여 온-프레미스 Active Directory의 사용자 데이터를 동기화할 때만 지원됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](../active-directory-apps-index.md)
* [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](../active-directory-saas-custom-apps.md)
* [SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
