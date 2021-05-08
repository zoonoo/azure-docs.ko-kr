---
title: MSAL(Microsoft 인증 라이브러리)로 마이그레이션
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)과 ADAL(Azure AD 인증 라이브러리)의 차이점과 MSAL로 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 6517cdd7aafa7ae2fe351b349e62a66104469dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653782"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리)로 애플리케이션 마이그레이션

많은 개발자 들이 ADAL(Azure Active Directory 인증 라이브러리)을 사용하여 애플리케이션을 제작하고 배포했습니다. 이제 Azure AD 엔터티의 인증 및 권한 부여에 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다.

ADAL 대신 MSAL을 사용하면:

- 더욱 광범위한 ID 세트를 인증할 수 있습니다.
  - Azure AD ID
  - Microsoft 계정
  - Azure AD B2C를 통한 소셜 및 로컬 계정
- 사용자가 최상의 Single Sign-On 환경을 이용할 수 있습니다.
- 애플리케이션이 증분 동의를 설정할 수 있습니다.
- 조건부 액세스를 더 손쉽게 지원할 수 있습니다.
- 혁신을 통한 혜택을 누릴 수 있습니다. Microsoft가 이제 MSAL에 모든 개발 역량을 쏟고 있기 때문에 ADAL에는 새로운 기능이 구현되지 않을 것입니다.

**이제는 Microsoft ID 플랫폼과 함께 사용할 수 있는 인증 라이브러리로 MSAL을 권장합니다**.

## <a name="migration-guidance"></a>마이그레이션 지침

다음 문서가 MSAL로의 마이그레이션에 도움을 줄 수 있습니다.

- [MSAL.Android로 마이그레이션](migrate-android-adal-msal.md)
- [MSAL.iOS / macOS로 마이그레이션](migrate-objc-adal-msal.md)
- [MSAL Java로 마이그레이션](migrate-adal-msal-java.md)
- [MSAL.js로 마이그레이션](msal-compare-msal-js-and-adal-js.md)
- [MSAL.NET으로 마이그레이션](msal-net-migration.md)
- [MSAL Python으로 마이그레이션](migrate-python-adal-msal.md)
- [브로커를 사용하여 MSAL.NET으로 Xamarin 앱 마이그레이션](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

__Q: ADAL은 사용되지 않나요?__  
A: 예. 2020년 6월 30일부터 더 이상 ADAL에 새 기능을 추가하지 않습니다. 2022 년 6월 30일까지는 계속해서 ADAL에 중요 보안 픽스를 계속 추가할 예정입니다. 해당일 이후에도 ADAL을 사용하는 앱이 계속 작동하긴 할 것이지만 MSAL로의 업그레이드를 통해 최신 기능을 활용하면서 보안을 유지하길 권장합니다.

__Q: 기존의 ADAL 앱이 작동을 중지하나요?__  
A: 아니요. 기존의 앱은 수정 없이도 계속해서 작동할 것입니다. 해당 앱들을 2022년 6월 30일 이후에도 가지고 있을 계획이라면 보안을 위해 이것들을 MSAL로 업데이트하는 것이 좋지만 기존의 기능 유지에 MSAL로의 마이그레이션이 필수적인 것은 아닙니다.

__Q: ADAL을 사용하는 앱인지 알 수 있는 방법은 무엇인가요?__  
A: 해당 애플리케이션에 대한 소스 코드가 있는 경우 위의 마이그레이션 가이드를 참조해서 앱이 사용하는 라이브러리를 확인할 수 있으며 이를 MSAL로 마이그레이션하는 방법도 확인할 수 있습니다. ISV의 파트너인 경우라면 직접 이들에게 연락하여 MSAL로의 마이그레이션 경험에 대한 설명을 구합니다.

__Q: MSAL로의 이동에 투자해야 하는 이유는 무엇인가요?__  
A: MSAL에는 증분 동의, Single Sign-On, 토큰 캐시 관리를 포함해 ADAL에는 없는 새로운 기능이 들어 있습니다. 또한 ADAL과는 다르게 MSAL은 2022년 6월 30일 이후에도 계속해서 보안 패치를 받을 수 있습니다. [자세히 알아보기](msal-overview.md).

__Q: Microsoft는 자사 앱을 MSAL로 업데이트하나요?__  
예. Microsoft는 지원 종료의 최종 기한까지 애플리케이션을 MSAL로 마이그레이션하는 과정을 진행하여 MSAL의 지속적인 보안 및 기능 향상을 활용할 수 있도록 합니다.

__Q: 앱을 ADAL에서 MSAL로 이동할 때 도움이 되는 도구를 릴리스할 예정인가요?__  
A: 아니요. 라이브러리 간의 차이점으로 인해 MSAL 개선에 사용해야 할 리소스를 해당 도구의 개발과 유지에 전용 리소스로 사용해야 합니다. 그러나 애플리케이션에서 필요한 변경을 수행할 때 도움이 되는 이전의 마이그레이션 가이드 세트를 제공합니다.

__Q: MSAL은 AD FS에서 어떻게 작동하나요?__  
A: MSAL.NET은 AD FS 2019에 대한 인증을 위해 특정 시나리오를 지원합니다. 앱이 이전 버전의 AD FS에서 직접 토큰을 획득해야 하는 경우에는 ADAL에 남아 있어야 합니다. [자세히 알아보기](msal-net-adfs-support.md).

__Q: 애플리케이션을 마이그레이션할 때 필요한 도움은 어떻게 얻을 수 있나요?__  
A: 본 문서의 [마이그레이션 지침](#migration-guidance) 섹션을 참조하세요. 앱의 플랫폼에 대한 가이드를 읽은 후에도 추가적인 질문이 있는 경우 `[azure-ad-adal-deprecation]` 태그를 포함하여 [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html)에 게시하거나 라이브러리의 GitHub 리포지토리에서 이슈를 열 수 있습니다. 라이브러리 별 리포지토리로의 링크는 MSAL 개요 문서의 [언어 및 프레임워크](msal-overview.md#languages-and-frameworks) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 인증 라이브러리 및 Microsoft Graph API 사용을 위한 애플리케이션 업데이트](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Microsoft ID 플랫폼 개요](v2-overview.md)
- [MSAL 코드 샘플 검토](sample-v2-code.md)