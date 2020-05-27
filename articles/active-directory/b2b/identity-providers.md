---
title: 외부 ID용 ID 공급자 - Azure AD
description: Azure Active Directory B2B 협업 기능은 회사 애플리케이션에 대한 선택적 액세스를 위해 MFA(Multi-Factor Authentication)를 지원합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595712"
---
# <a name="identity-providers-for-external-identities"></a>외부 ID용 ID 공급자

‘ID 공급자’는 애플리케이션에 인증 서비스를 제공하는 동시에 ID 정보를 만들고 유지 관리합니다. 외부 사용자와 앱 및 리소스를 공유하는 경우 Azure AD는 공유를 위한 기본 ID 공급자입니다. 즉, 이미 Azure AD 또는 Microsoft 계정이 있는 외부 사용자를 초대하는 경우 사용자가 추가 구성 없이 자동으로 로그인할 수 있습니다.

그러나 사용자가 다양한 ID 공급자를 사용하여 로그인하도록 설정할 수 있습니다. 예를 들어 Google 및 Facebook을 포함하여 Azure AD에서 지원되는 소셜 ID 공급자와의 페더레이션을 설정할 수 있습니다. SAML 또는 WS-Fed 프로토콜을 지원하는 외부 ID 공급자와 페더레이션할 수도 있습니다. 외부 ID 공급자 페더레이션을 사용하면 기존 소셜 또는 엔터프라이즈 계정으로 앱에 로그인할 수 있는 기능을 외부 사용자에게 제공할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

Azure AD 외부 ID는 Google 및 Facebook과 페더레이션을 위해 미리 구성되어 있습니다. Azure AD 테넌트에서 이러한 ID 공급자를 설정하려면 각 ID 공급자에서 애플리케이션을 만들고 자격 증명을 구성합니다. 클라이언트 또는 앱 ID와 클라이언트 또는 앱 비밀을 가져올 수 있습니다. 그런 다음, Azure AD 테넌트에 추가할 수 있습니다.

Azure AD 테넌트에 ID 공급자를 추가하면 다음을 수행합니다.

- 외부 사용자를 조직의 앱 또는 리소스에 초대하는 경우 외부 사용자가 해당 ID 공급자와 함께 자신의 계정을 사용하여 로그인할 수 있습니다.
- 앱에 대해 [셀프 서비스 가입](self-service-sign-up-overview.md)을 사용하도록 설정하면 외부 사용자는 추가한 ID 공급자와 함께 자신의 계정을 사용하여 앱에 가입할 수 있습니다. 

초대를 사용하거나 앱에 가입하는 경우 외부 사용자는 소셜 ID 공급자를 사용하여 로그인하고 인증하는 옵션을 사용할 수 있습니다.

![Google 및 Facebook 옵션을 사용하여 로그인 화면을 보여 주는 스크린샷](media/identity-providers/sign-in-with-social-identity.png)

최적의 로그인 환경을 위해 가능한 경우 ID 공급자와 페더레이션하여 초대된 게스트가 앱에 액세스할 때 원활한 로그인 환경을 제공할 수 있도록 합니다.  

## <a name="next-steps"></a>다음 단계

애플리케이션에 로그인하기 위해 ID 공급자를 추가하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- 소셜 ID 공급자 목록에 [Google 추가](google-federation.md)
- 소셜 ID 공급자 목록에 [Facebook 추가](facebook-federation.md)
- ID 공급자가 SAML 2.0 또는 WS-Fed 프로토콜을 지원하는 모든 조직과의 [직접 페더레이션을 설정](direct-federation.md)합니다. 직접 페더레이션은 셀프 서비스 가입 사용자 흐름에 대한 옵션이 아닙니다.
