---
title: Azure AD 앱 개발자를 위한 지원 및 도움말 옵션
description: Microsoft ID(Azure Active Directory 및 Microsoft 계정)와 통합되는 애플리케이션을 만들 때 개발과 관련된 질문 및 문제에 대한 지원 및 도움말을 얻는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 43990952f6cbe90c729ac2df421c682fe8d42b1b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517946"
---
# <a name="support-and-help-options-for-developers"></a>개발자를 위한 지원 및 도움말 옵션

Azure AD(Azure Active Directory), Microsoft ID 또는 Microsoft Graph API와 통합을 방금 시작한 경우 또는 애플리케이션에 새 기능을 구현 중인 경우 커뮤니티에서 도움을 얻거나 개발자로서 사용할 수 있는 지원 옵션을 파악해야 할 때가 있습니다. Microsoft id 플랫폼 솔루션을 개발할 때 도움을 받을 수 있는 위치에 대 한 제안 사항은 다음과 같습니다.

## <a name="create-an-azure-support-request"></a>Azure 지원 요청 만들기

<div class='icon is-large'>
    <img alt='Azure support' src='https://docs.microsoft.com/media/logos/logo_azure.svg'>
</div>

지금 클라우드를 시작하려는 개발자든 비즈니스에 중요한 전략적 애플리케이션을 배포하려는 대규모 조직이든 관계없이 가장 적합한 [Azure 지원 옵션 및 플랜 선택](https://azure.microsoft.com/support/plans)의 범위를 탐색합니다. Azure 고객은 Azure Portal에서 지원 요청을 만들고 관리할 수 있습니다.

- Azure 지원 계획이 이미 있는 경우 [여기에서 지원 요청을 여세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Azure 고객이 아닌 경우 [상용 지원](https://support.serviceshub.microsoft.com/supportforbusiness)을 통해 Microsoft 지원 요청을 열 수도 있습니다.

## <a name="post-a-question-to-microsoft-qa"></a>Microsoft Q&A에 질문 게시
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/common/question-mark-icon.png'>
</div>             

Microsoft 엔지니어, Azure Mvp (가장 귀중 한 전문가) 및 전문가 커뮤니티의 구성원 으로부터 직접 id 앱 개발 질문에 대 한 답변을 받을 수 있습니다.

[Microsoft Q&A](/answers/products/) 는 Azure에서 권장 하는 커뮤니티 지원 원본입니다.

Microsoft Q&A를 검색 하 여 문제에 대 한 답변을 찾을 수 없는 경우 새 질문을 제출 합니다. 고품질의 [질문](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html)을 할 때 다음 태그 중 하나를 사용 합니다.

| 구성 요소/영역| 태그  |
|------------|---------------------------|
| ADAL(Active Directory 인증 라이브러리)                              | [adal](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html)                |
| MSAL(Microsoft 인증 라이브러리)                                     | [msal](https://docs.microsoft.com/answers/topics/azure-ad-msal.html)                            |
| OWIN (Open Web Interface for .NET) 미들웨어                               | [[azure-active directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [Azure AD B2B/외부 Id](../external-identities/what-is-b2b.md) | [[azure-ad-b2b]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html)                     |
| [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html)                     |
| [Microsoft Graph API](https://developer.microsoft.com/graph/)               | [[azure-ad-그래프]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html)                 |
| 다른 모든 인증 및 권한 부여 영역                            | [[azure-active directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |

## <a name="create-a-github-issue"></a>GitHub 문제 만들기

<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/common/github.svg'>
</div>

MSAL (Microsoft 인증 라이브러리) 중 하나를 사용 하 여 도움을 받아야 하는 경우 GitHub의 리포지토리에서 문제를 여세요.

| MSAL 라이브러리 | GitHub 문제 URL|
| --- | --- |
| Android용 MSAL | https://github.com/AzureAD/microsoft-authentication-library-for-android/issues |
| MSAL 각도 | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| iOS 및 macOS용 MSAL| https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues |
| MSAL Java | https://github.com/AzureAD/microsoft-authentication-library-for-java/issues |
| MSAL.js | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
|MSAL.NET| https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues |
| MSAL 노드 | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |
| MSAL Python | https://github.com/AzureAD/microsoft-authentication-library-for-python/issues |
| MSAL 반응 | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues |

## <a name="submit-feedback-on-azure-feedback"></a>Azure 피드백에 대한 사용자 의견 제출

<div class='icon is-large'>
    <img alt='UserVoice' src='https://docs.microsoft.com/media/logos/logo-uservoice.svg'>
</div>

새 기능을 요청하려면 Azure 피드백에 게시하세요. Microsoft id 플랫폼을 개발 하는 응용 프로그램에 더 적합 하도록 아이디어를 공유 하세요.

| 서비스                       | Azure 피드백 URL |
|-------------------------------|---------------|
| Azure Active Directory | https://feedback.azure.com/forums/169401-azure-active-directory |
| Azure Active Directory 개발자 환경             | https://feedback.azure.com/forums/169401-azure-active-directory?category_id=164757 |
| Azure Active Directory-인증             | https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167256 |

## <a name="stay-informed-of-updates-and-new-releases"></a>업데이트 및 새 릴리스의 최신 정보 수신

<div class='icon is-large'>
    <img alt='Stay informed' src='https://docs.microsoft.com/media/common/i_blog.svg'>
</div>

- [Azure 업데이트](https://azure.microsoft.com/updates/?category=identity): 중요 한 제품 업데이트, 로드맵 및 공지에 대해 알아봅니다.

- [문서의 새로운 기능](https://docs.microsoft.com/azure/active-directory/develop/whats-new-docs): Microsoft id 플랫폼 설명서의 새로운 기능에 대해 알아봅니다.

- [Azure Active Directory Id 블로그](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity): Azure AD에 대 한 뉴스 및 정보를 가져옵니다.

- [기술 커뮤니티](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity/): 경험을 공유 하 고 전문가에 게 참여 하 고 학습 합니다.


