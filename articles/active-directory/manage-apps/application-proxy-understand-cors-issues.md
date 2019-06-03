---
title: 파악 하 고 Azure AD 응용 프로그램 프록시 CORS 문제 해결
description: Azure AD 응용 프로그램 프록시를 식별 하 고 CORS 문제를 해결 하는 방법에 대 한 CORS 이해를 제공 합니다.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399339"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>파악 하 고 Azure Active Directory 응용 프로그램 프록시 CORS 문제 해결

[크로스-원본 자원 공유 (CORS)](http://www.w3.org/TR/cors/) 이 Azure Active Directory 응용 프로그램 프록시를 통해 게시할 앱 및 Api에 대 한 문제가 될 경우에 따라 수 있습니다. 이 문서에서는 Azure AD 응용 프로그램 프록시 CORS 문제와 해결 방법을 설명 합니다.

브라우저 보안은 일반적으로 AJAX 요청을 다른 도메인에서 웹 페이지를 방지 합니다. 이 제약 사항을 *동일 원본 정책(Same-Origin Policy)* 이라고 부르며, 악의적인 사이트가 다른 사이트의 민감한 데이터를 무차별적으로 읽는 것을 방지합니다. 그러나 하려는 경우가 있습니다 수 다른 사이트에 web API를 호출할 수 있습니다. CORS는 동일 원본 정책을 완화 하 고 다른 사용자를 거부 하는 동안 일부 원본 간 요청을 허용 하는 서버 수 있는 W3C 표준입니다.

## <a name="understand-and-identify-cors-issues"></a>이해 하 고 CORS 문제를 식별 합니다.

동일한 구성표, 호스트 및 포트에 있는 경우 두 개의 Url가 동일한 원본 ([RFC 6454](https://tools.ietf.org/html/rfc6454)), 같은:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

다음 Url에 이전 보다 다양 한 원본 2

-   http:\//contoso.net - Different domain
-   http:\//contoso.com:9000/foo.html - Different port
-   https:\//contoso.com/foo.html-다른 구성표
-   http:\//www.contoso.com/foo.html-다른 하위 도메인

동일 원본 정책 올바른 액세스 제어 헤더를 사용 하지 않는 경우 다른 원본에서 리소스에 액세스 하지 못하도록 앱을 방지 합니다. CORS 헤더가 없거나 잘못 된 경우 크로스-원본 요청 실패 합니다. 

브라우저 디버그 도구를 사용 하 여 CORS 문제를 식별할 수 있습니다.

1. 브라우저를 시작 하 고 웹 앱으로 이동 합니다.
1. 키를 눌러 **F12** 를 디버그 콘솔을 불러옵니다.
1. 트랜잭션 복제 및 콘솔 메시지를 검토 하려고 합니다. CORS 위반 원본에 대 한 콘솔 오류를 생성합니다.

다음 스크린 샷에서 선택 하는 **사용해 보세요** 발생 시킨 CORS 오류 메시지를 해당 https 단추:\//corswebclient-contoso.msappproxy.net 액세스-컨트롤-허용-원본 헤더에서 찾을 수 없습니다.

![CORS 문제](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 여 CORS 문제

다음 예제에서는 일반적인 Azure AD 응용 프로그램 프록시 CORS 시나리오입니다. 내부 서버 호스트를 **CORSWebService** web API 컨트롤러와 **CORSWebClient** 호출 하는 **CORSWebService**합니다. AJAX 요청이 **CORSWebClient** 하 **CORSWebService**합니다.

![온-프레미스 원본 동일한 요청](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 앱을 호스트 하는 경우 해당 로드 실패 또는 오류가 발생 하지만 온-프레미스에서 Azure AD 응용 프로그램 프록시를 통해 게시 될 때 작동 합니다. 응용 프로그램 프록시를 통해 다른 앱과 CORSWebClient 및 CORSWebService 앱에 별도로 게시 하는 경우 두 응용 프로그램이 다른 도메인에서 호스트 됩니다. CORSWebService CORSWebClient에서 AJAX 요청을 원본 간 요청을 이며 실패 합니다.

![응용 프로그램 프록시 CORS 요청](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>응용 프로그램 프록시 CORS 문제 해결

여러 가지 방법 중 하나로 이전 CORS 문제를 해결할 수 있습니다.

### <a name="option-1-set-up-a-custom-domain"></a>옵션 1: 사용자 지정 도메인 설정

Azure AD 응용 프로그램 프록시를 사용 하 여 [사용자 지정 도메인](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) 앱 원본, 코드 또는 헤더를 변경 하지 않고도 동일한 원본에서 게시 합니다. 

### <a name="option-2-publish-the-parent-directory"></a>옵션 2: 부모 디렉터리를 게시 합니다.

두 앱의 부모 디렉터리를 게시 합니다. 웹 서버에서 두 개의 앱이 있으면 특히 잘이 솔루션이 작동 합니다. 각 앱에 별도로 게시 하는 대신 동일한 원본에는 공통 부모 디렉터리를 게시할 수 있습니다.

다음 예제에서는 포털 CORSWebClient 앱에 대 한 Azure AD 응용 프로그램 프록시 페이지를 표시 합니다.  경우는 **내부 URL** 로 설정 된 *contoso.com/CORSWebClient*, 앱에 대 한 성공한 요청을 만들 수 없습니다는 *contoso.com/CORSWebService* 디렉터리 때문에 크로스-원본 일입니다. 

![앱을 개별적으로 게시](./media/application-proxy-understand-cors-issues/image4.png)

대신 설정 합니다 **내부 URL** 모두 포함 하는 부모 디렉터리에 게시 하는 *CORSWebClient* 및 *CORSWebService* 디렉터리:

![부모 디렉터리를 게시 합니다.](./media/application-proxy-understand-cors-issues/image5.png)

결과 앱 Url은 효과적으로 CORS 문제를 해결 합니다.

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>옵션 3: HTTP 헤더를 업데이트 합니다.

원본 요청을 일치 하도록 웹 서비스에서 사용자 지정 HTTP 응답 헤더를 추가 합니다. 웹 사이트의 인터넷 정보 서비스 (IIS)에서 실행 중인 경우 IIS 관리자를 사용 하 여 헤더를 수정 하려면:

![IIS 관리자에서 사용자 지정 응답 헤더를 추가 합니다.](./media/application-proxy-understand-cors-issues/image6.png)

이 수정에는 코드 변경 필요 하지 않습니다. Fiddler 추적에이 확인할 수 있습니다.

**Post는 헤더 추가**\
HTTP/1.1 200 OK\
Cache-control: no cache\
아니요-cache\ Pragma:
Content-Type: text/plain; charset=utf-8\
Expires: -1\
달라 집니다. Accept-Encoding\
서버:  Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
콘텐츠-길이: 17

### <a name="option-4-modify-the-app"></a>옵션 4: 앱 수정

적절 한 값을 사용 하 여 액세스 제어-허용-원본 헤더를 추가 하 여 CORS를 지원 하도록 앱을 변경할 수 있습니다. 헤더를 추가 하는 방법은 앱의 코드 언어에 따라 달라 집니다. 가장 많은 노력이 필요 하기 때문에 최소 권장 되는 옵션을는 코드를 변경 합니다.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>옵션 5: 액세스 토큰의 수명을 연장합니다

CORS 문제도 해결 앱 리디렉션됩니다 등 일 수 없습니다 *login.microsoftonline.com* 을 인증 하려면 액세스 토큰이 만료 되 고 있습니다. CORS를 호출 하는 실패 합니다. 이 시나리오에 대 한 해결 방법은 사용자의 세션 동안 만료 되지 않도록 방지 하기 위해 액세스 토큰의 수명을 연장 하는 것. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure AD에서 구성 가능한 토큰 수명](../develop/active-directory-configurable-token-lifetimes.md)합니다.

## <a name="see-also"></a>참고 항목
- [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 합니다.](application-proxy-add-on-premises-application.md) 
- [Azure AD 응용 프로그램 프록시 배포 계획](application-proxy-deployment-plan.md) 
- [Azure Active Directory 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대 한 원격 액세스](application-proxy.md) 
