---
title: B2B 초대 이메일의 요소 - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory B2B 협업 초대 이메일 템플릿
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407204"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Azure Active Directory B2B 협업 초대 이메일의 요소

초대 이메일은 온보드의 파트너를 Azure AD에서 B2B 협업 사용자로 불러오기 위한 중요한 구성 요소입니다. [B2B 공동 작업을 사용하여 누군가를 초대하는 이메일을 보낼 필요는 없지만](add-user-without-invite.md)이렇게 하면 초대를 수락할지 여부를 결정하는 데 필요한 모든 정보를 사용자에게 제공합니다. 또한 나중에 리소스로 돌아가야 할 때 항상 참조할 수 있는 링크를 제공합니다.

![B2B 초대 이메일을 보여 주는 스크린샷](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 이 새 전자 메일 템플릿은 여전히 모든 테넌트에 롤아웃되므로 일부 테넌트에서는 여전히 이전 디자인을 사용하고 있습니다. 2020년 5월 말까지 모든 테넌자의 초대장이 이 템플릿을 사용할 것입니다.

## <a name="explaining-the-email"></a>전자 메일 설명

전자 메일의 몇 가지 요소를 확인하여 이러한 기능을 최대한 활용하는 방법에 대해 살펴보겠습니다.

### <a name="subject"></a>제목

전자 메일의 제목은 다음 패턴을 따릅니다.

&lt;사용자&gt; 이름은 조직 내의 응용 프로그램에 액세스하도록 초대했습니다.

### <a name="from-address"></a>보낸 사람 주소

보낸 사람 주소에 대해서는 LinkedIn 유사 패턴을 사용합니다. 이 패턴은 전자 메일이 invites@microsoft.com에서 온 것이지만 초대가 다른 조직에서 온 것이라는 점을 분명히 해야 합니다. 형식은 다음과 같다: <invites@microsoft.com> &lt;테넌트 이름을&gt; <invites@microsoft.com>대신하는 Microsoft 초대장 또는 Microsoft 초대. 

### <a name="reply-to"></a>회신

회신 전자 메일은 사용 가능할 때 초대자의 전자 메일로 설정되므로 전자 메일에 회신하면 초대자에게 전자 메일이 다시 전송됩니다.

### <a name="phishing-warning"></a>피싱 경고

이메일은 사용자에게 피싱에 대한 간단한 경고로 시작하여 사용자가 예상하는 초대장만 수락해야 한다는 경고를 보도록 합니다. 초대하는 파트너가 미리 언급하여 초대에 놀라지 않도록 하는 것이 좋습니다.

![이메일에 피싱 경고 이미지](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>초대자에 대한 정보

이메일에는 초대자 및 초대를 보내는 조직에 대한 정보가 포함됩니다. 여기에는 보낸 사람의 이름과 전자 메일 주소뿐만 아니라 조직과 연결된 이름 및 기본 도메인이 포함됩니다. 이 모든 정보는 초대장이 초대를 수락하는 것에 대해 정보에 입각한 결정을 내리는 데 도움이 됩니다.

![이메일에 초대자의 정보 이미지](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>초대 메시지

초대자가 [게스트 사용자를 디렉터리, 그룹 또는 앱에 초대하거나](add-users-administrator.md) 초대 API를 사용할 때 [초대자의](customize-invitation-api.md)초대의 일부로 메시지를 포함하는 경우 이메일의 기본 섹션에 메시지가 강조 표시됩니다. 또한 초대자의 이름과 프로필 이미지가 설정된 경우 포함되어 있습니다. 메시지 자체는 텍스트 영역이므로 보안상의 이유로 HTML 태그를 처리하지 않습니다.

![이메일에 초대 메시지의 이미지](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>단추 수락 및 URL 리디렉션

이메일의 다음 섹션에는 초대를 수락한 후 초대받은 사람의 이동 위치에 대한 정보와 초대를 수행할 수 있는 버튼이 포함되어 있습니다.  앞으로 초대받는 사람은 항상 이 링크를 사용하여 리소스로 직접 돌아갈 수 있습니다.

![이메일에 수락 버튼과 리디렉션 URL의 이미지](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>바닥글 섹션

바닥글에는 전송중인 초대에 대한 자세한 정보가 포함되어 있습니다. 초대받는 사람은 항상 향후 초대를 차단할 수 있는 옵션이 있습니다. 조직에서 개인 [정보 취급 방침을 설정한](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)경우 명령문에 대한 링크가 여기에 표시됩니다.  그렇지 않으면 조직에서 개인 정보 취급 방침을 설정하지 않았음을 나타내는 메모입니다.

![이메일의 바닥글 섹션 이미지](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>언어를 결정하는 방법

초대 이메일에서 게스트 사용자에게 표시되는 언어는 다음 설정에 의해 결정됩니다. 이러한 설정은 우선 순위 순서대로 나열됩니다. 설정이 구성되지 않은 경우 목록의 다음 설정이 언어를 결정합니다.

- 초대 만들기 API를 사용하는 경우 [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) 개체의 **messageLanguage** 속성
-   게스트의 [사용자 개체](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)에 지정한 **preferredLanguage** 속성
-   게스트 사용자의 홈 테넌트의 속성에 설정된 **알림 언어**(Azure AD 테넌트만 해당)
-   리소스 테넌트의 속성에 설정된 **알림 언어**

이러한 설정을 하나도 구성하지 않은 경우 기본 언어는 영어(미국)입니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 공동 작업이란 무엇입니까?](what-is-b2b.md)
- [Azure Active Directory 관리자가 B2B 협업 사용자를 추가하는 방법은 무엇인가요?](add-users-administrator.md)
- [정보 작업자가 B2B 협업 사용자를 추가하는 방법은 무엇입니까?](add-users-information-worker.md)
- [B2B 협업 초대 상환](redemption-experience.md)
- [초대 없이 B2B 협업 사용자 추가](add-user-without-invite.md)
