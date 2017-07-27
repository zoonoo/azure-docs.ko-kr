---
title: "Azure AD 앱 프록시 링크 및 URL 변환 | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시 커넥터에 대한 기본 사항을 제공합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d716476deb81f6627cf03401f78ab399ae940e68
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017

---

# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 게시된 앱에 대해 하드 코드된 링크 리디렉션

Azure AD 응용 프로그램 프록시는 원격 사용자가 자신의 장치에서 온-프레미스 앱을 사용할 수 있도록 합니다. 그러나 일부 앱은 온-프레미스 대상 그룹을 염두에 두고 개발되었습니다. 일반적으로 로컬 링크는 HTML에 포함되어 앱이 원격으로 사용되는 경우 올바르게 작동하지 않는다는 의미입니다. 여러 온-프레미스 응용 프로그램이 서로를 가리키고 사용자가 각 앱에 대한 외부 URL을 입력하는 대신 링크를 사용하여 앱 간을 이동하려는 경우에 가장 일반적입니다.

응용 프로그램 프록시의 링크 변환 기능은 이 문제를 해결하기 위해 개발되었습니다. 내부 끝점이나 포트를 직접 가리키는 앱을 사용하는 경우 이러한 내부 URL을 게시된 외부 응용 프로그램 프록시 URL에 매핑할 수 있습니다. 링크 변환을 사용하도록 설정하면 응용 프로그램 프록시에서 HTML, CSS를 검색하고 게시된 내부 링크에 대해 JavaScript 태그를 선택한 다음 변환하므로 사용자가 중단 없이 환경을 이용할 수 있습니다.

>[!NOTE]
>링크 변환 기능은 어떠한 이유로든 사용자 지정 도메인을 사용하여 앱에 대한 내부 및 외부 URL을 동일하게 지정할 수 없는 테넌트에만 사용됩니다. 이 기능을 사용하도록 설정하기 전에 [Azure AD 응용 프로그램 프록시의 사용자 지정 도메인](active-directory-application-proxy-custom-domains.md)이 작동하는지 확인하세요.

## <a name="how-link-translation-works"></a>링크 변환의 작동 방식

인증 후 프록시 서버가 사용자에게 응용 프로그램 데이터를 전달하면 응용 프로그램 프록시는 응용 프로그램에서 하드 코드된 링크를 검색하여 게시된 해당 외부 URL로 바꿉니다.

### <a name="which-links-are-affected"></a>영향을 받는 링크

링크 변환 기능은 앱 본문의 코드 태그에 있는 링크만 검색합니다. 응용 프로그램 프록시에는 헤더에서 쿠키 또는 URL을 변환하는 별도의 기능이 있습니다. 

온-프레미스 응용 프로그램에는 두 가지 일반적인 유형의 내부 링크가 있습니다.

- **상대 내부 링크** - `/claims/claims.html` 같은 로컬 파일 구조에서 공유 리소스를 가리킵니다. 이러한 링크는 응용 프로그램 프록시를 통해 게시되는 앱에서 자동으로 작동하며 링크 변환과 관계없이 계속 작동합니다. 
- **하드 코드된 내부 링크** - `http://expenses` 같은 다른 온-프레미스 앱이나 `http://expenses/logo.jpg` 같은 게시된 파일에 대한 링크입니다. 링크 변환 기능은 하드 코드된 내부 링크에서 작동하며 해당 링크를 원격 사용자가 통과해야 하는 외부 URL을 가리키도록 변경합니다.

### <a name="how-do-apps-link-to-each-other"></a>앱이 서로 연결되는 방식

링크 변환은 각 응용 프로그램에 대해 사용되므로 앱별 수준에서 사용자 환경을 제어할 수 있습니다. 변환할 앱의 *대상* 링크가 아니라 해당 앱의 *원본* 링크를 원하는 경우 앱에 대해 링크 변환을 설정합니다. 

예를 들어 응용 프로그램 프록시를 통해 게시된 세 개의 응용 프로그램 Benefits, Expenses 및 Travel이 있고 모두 서로 연결되어 있다고 가정합니다. 그리고 응용 프로그램 프록시를 통해 게시되지 않은 네 번째 Feedback 앱이 있습니다.

Benefits 앱에 대해 링크 변환을 사용하도록 설정하면 Expenses 및 Travel에 대한 링크는 해당 앱에 대한 외부 URL로 리디렉션되지만 Feedback에 대한 링크는 외부 URL이 없기 때문에 리디렉션되지 않습니다. Expenses 및 Travel에서 Benefits로 다시 링크는 작동하지 않는데, 이러한 두 앱에 대해서는 링크 변환이 설정되지 않았기 때문입니다.

![링크 변환을 설정한 경우 Benefits에서 다른 앱으로 링크](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>변환되지 않는 링크

성능 및 보안 향상을 위해 일부 링크는 변환되지 않습니다.

- 코드 태그 내부에 있지 않은 링크. 
- 다른 프로그램에서 연 내부 링크. 메일이나 인스턴트 메시지를 통해 전송되거나 다른 문서에 포함된 링크는 변환되지 않습니다. 사용자는 외부 URL로 이동됨을 알고 있어야 합니다.

이러한 두 시나리오 중 하나를 지원해야 하는 경우 동일한 외부 및 외부 URL을 사용하여 링크 변환을 수행할 필요가 없도록 할 수 있습니다.  

## <a name="enable-link-translation"></a>링크 변환을 사용하도록 설정

링크 변환 시작은 단추를 클릭하는 것만큼 간단합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **모든 응용 프로그램** > 관리할 앱 선택 > **응용 프로그램 프록시**로 이동합니다.
3. **Translate URLs in application body**(응용 프로그램 본문의 URL 변환)를 **예**로 설정합니다.

   ![[예]를 선택하여 응용 프로그램 본문의 URL 변환](./media/application-proxy-link-translation/select_yes.png)등 4가지 유형의 클러스터가 제공됩니다.
4. **저장**을 선택하여 변경 내용을 적용합니다.

이제 사용자가 이 응용 프로그램에 액세스하면 프록시는 테넌트에서 응용 프로그램 프록시를 통해 게시된 내부 URL을 자동으로 검사합니다.

## <a name="send-feedback"></a>피드백 보내기

이 기능이 모든 앱에서 작동하도록 하는 데 도움이 되도록 피드백을 보내 주세요. HTML 및 CSS에서 30개가 넘는 태그를 검색하고 지원할 JavaScript 케이스를 검토하고 있습니다. 생성된 링크 중 변환되지 않는 예가 있으면 [응용 프로그램 프록시 피드백](mailto:aadapfeedback@microsoft.com)으로 코드 조각을 보내 주세요. 

## <a name="next-steps"></a>다음 단계
- [동일한 내부 및 외부 URL을 사용하도록 Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 사용](active-directory-application-proxy-custom-domains.md)

