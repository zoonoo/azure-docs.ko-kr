---
title: B2B 초대 전자 메일의 요소-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 협업 초대 이메일 템플릿
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03391ba5a82c128197c86ea6ed84389552fadb9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439845"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Azure Active Directory B2B 협업 초대 이메일의 요소

초대 이메일은 온보드의 파트너를 Azure AD에서 B2B 협업 사용자로 불러오기 위한 중요한 구성 요소입니다. [B2B 공동 작업을 사용 하 여 다른 사용자를 초대 하기 위해 전자 메일을 보낼 필요](add-user-without-invite.md)는 없지만, 이렇게 하면 초대를 수락할지 여부를 결정 하는 데 필요한 모든 정보가 사용자에 게 제공 됩니다. 또한 리소스에 반환 해야 하는 경우 나중에 언제 든 지 참조할 수 있는 링크를 제공 합니다.

![B2B 초대 이메일을 보여 주는 스크린샷](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> 이 새 전자 메일 템플릿은 여전히 모든 테 넌 트에 롤아웃 되 고 있으므로 일부 테 넌 트는 여전히 이전 디자인을 사용 하 고 있습니다. 2020 년 5 월 말까지 모든 테 넌 트의 초대는이 템플릿을 사용 합니다.

## <a name="explaining-the-email"></a>전자 메일 설명

전자 메일의 몇 가지 요소를 확인하여 이러한 기능을 최대한 활용하는 방법에 대해 살펴보겠습니다.

### <a name="subject"></a>주체

전자 메일의 제목은 다음 패턴을 따릅니다.

&lt;사용자 이름 &gt; 조직 내의 응용 프로그램에 액세스 하도록 초대 했습니다.

### <a name="from-address"></a>보낸 사람 주소

보낸 사람 주소에 대해서는 LinkedIn 유사 패턴을 사용합니다. 이 패턴은 전자 메일이에서 제공 되더라도 invites@microsoft.com 초대는 다른 조직에서 제공 된다는 것을 명확 하 게 확인 해야 합니다. 형식은  <invites@microsoft.com> tenantname 대신 Microsoft 초대 또는 microsoft 초대입니다 &lt; &gt;  <invites@microsoft.com> . 

### <a name="reply-to"></a>회신

회신 전자 메일은 사용 가능할 때 초대자의 전자 메일로 설정되므로 전자 메일에 회신하면 초대자에게 전자 메일이 다시 전송됩니다.

### <a name="phishing-warning"></a>피싱 경고

전자 메일은 사용자에 게 피싱에 대해 간단한 경고로 시작 하 여 예상 되는 초대를 수락 해야 한다는 경고를 보냅니다. 초대 하는 파트너에 게 사전에 언급 하 여 초대 하지 않을 지 확인 하는 것이 좋습니다.

![전자 메일의 피싱 경고 이미지](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>초대자에 대한 정보

전자 메일에는 초대를 보내는 초대자 및 조직에 대 한 정보가 포함 됩니다. 여기에는 조직과 연결 된 이름 및 주 도메인 뿐만 아니라 발신자의 이름과 전자 메일 주소도 포함 됩니다. 이 모든 정보를 통해 초대를 통해 초대를 수락 하는 것에 대 한 의사 결정을 내리는 데 도움이 됩니다.

![전자 메일의 초대자 정보 이미지](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>초대 메시지

초대자가 [게스트 사용자를 디렉터리, 그룹 또는 앱에 초대](add-users-administrator.md) 하거나 초대 [API를 사용할](customize-invitation-api.md)때 초대의 일부로 메시지를 포함 하는 경우 전자 메일의 주 섹션에 메시지가 강조 표시 됩니다. 초대자의 이름 및 프로필 이미지 (설정 된 경우)도 포함 되어 있습니다. 메시지 자체는 텍스트 영역 이므로 보안상의 이유로 HTML 태그를 처리 하지 않습니다.

![전자 메일의 초대 메시지 이미지](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>수락 단추 및 리디렉션 URL

전자 메일의 다음 섹션에는 초대를 수락 하 고 초대 하는 데 사용 되는 단추를 비롯 하 여 초대를 받는 위치에 대 한 정보가 포함 됩니다.  나중에 초대를 받은 사용자는 항상이 링크를 사용 하 여 리소스로 직접 돌아갈 수 있습니다.

![전자 메일의 수락 단추 및 리디렉션 URL 이미지](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>바닥글 섹션

바닥글에는 전송 중인 초대에 대 한 자세한 정보가 포함 됩니다. 초대를 받은 후에는 항상 초대를 차단할 수 있는 옵션이 있습니다. 조직에서 [개인 정보 취급 방침을 설정한](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)경우 문에 대 한 링크가 여기에 표시 됩니다.  그렇지 않으면 조직이 개인 정보 취급 방침을 설정 하지 않았음을 나타냅니다.

![전자 메일의 바닥글 섹션 이미지](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>조직 차단 (구독 취소)

조직의 초대에서 바닥글에는 **이후 초대를 차단**하는 옵션이 포함 되어 있습니다. 게스트 사용자는이 링크를 선택 하 여 조직에서 향후 초대를 차단할 수 있습니다. 이 작업은 또한에서 사용자의 구독 목록에 조직을 추가 합니다 [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>차단 된 조직 보기

게스트 사용자는 다음 단계에 따라 차단 된 조직을 보거나 내보낼 수 있습니다.

1. [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage)으로 이동합니다.
2. 전자 메일을 입력 하 고 일회용 암호 인증을 위한 로그인 단계를 따르세요.
3. 차단 된 조직을 보거나 복사 및 붙여넣기를 사용 하 여 이름을 내보냅니다.
   > [!NOTE]
   > 사용자를 다시 초대 하도록 차단한 조직을 허용 하려면 조직을 선택 하 고 **다음**을 선택 합니다.

## <a name="how-the-language-is-determined"></a>언어를 결정하는 방법

초대 이메일에서 게스트 사용자에게 표시되는 언어는 다음 설정에 의해 결정됩니다. 이러한 설정은 우선 순위 순서대로 나열됩니다. 설정이 구성되지 않은 경우 목록의 다음 설정이 언어를 결정합니다.

- 초대 만들기 API를 사용하는 경우 [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) 개체의 **messageLanguage** 속성
-   게스트의 [사용자 개체](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)에 지정한 **preferredLanguage** 속성
-   게스트 사용자의 홈 테넌트의 속성에 설정된 **알림 언어**(Azure AD 테넌트만 해당)
-   리소스 테넌트의 속성에 설정된 **알림 언어**

이러한 설정을 하나도 구성하지 않은 경우 기본 언어는 영어(미국)입니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 공동 작업 이란?](what-is-b2b.md)
- [Azure Active Directory 관리자가 B2B 협업 사용자를 추가하는 방법은 무엇인가요?](add-users-administrator.md)
- [정보 작업자가 B2B 협업 사용자를 추가하는 방법은 무엇입니까?](add-users-information-worker.md)
- [B2B 협업 초대 상환](redemption-experience.md)
- [초대 없이 B2B 협업 사용자 추가](add-user-without-invite.md)
