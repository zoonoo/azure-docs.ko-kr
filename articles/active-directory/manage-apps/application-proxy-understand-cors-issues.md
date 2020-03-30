---
title: Azure AD 응용 프로그램 프록시 CORS 문제 이해 및 해결
description: Azure AD 응용 프로그램 프록시에서 CORS에 대한 이해와 CORS 문제를 식별하고 해결하는 방법을 제공합니다.
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
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025781"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Azure Active Directory 응용 프로그램 프록시 CORS 문제 이해 및 해결

[CORS(원본 간 리소스 공유)는](https://www.w3.org/TR/cors/) Azure Active Directory 응용 프로그램 프록시를 통해 게시하는 앱 및 API에 문제가 있을 수 있습니다. 이 문서에서는 Azure AD 응용 프로그램 프록시 CORS 문제 및 해결 에 대해 설명합니다.

브라우저 보안은 일반적으로 웹 페이지가 다른 도메인에 AJAX 요청을 하지 못하도록 합니다. 이러한 제한을 *동일한 원본 정책이라고*하며 악의적인 사이트가 다른 사이트에서 중요한 데이터를 읽지 못하도록 합니다. 그러나 다른 사이트에서 웹 API를 호출하도록 허용하려는 경우가 있습니다. CORS는 서버가 동일한 원본 정책을 완화하고 다른 요청을 거부하면서 일부 원본 간 요청을 허용하는 W3C 표준입니다.

## <a name="understand-and-identify-cors-issues"></a>CORS 문제 이해 및 식별

두 URL은 다음과 같이 동일한 구성표, 호스트 및[포트(RFC 6454)가](https://tools.ietf.org/html/rfc6454)있는 경우 동일한 출처를 갖습니다.

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

다음 URL의 출처는 이전 두 URL과 다릅니다.

-   http:\//contoso.net - 다른 도메인
-   http:\//contoso.com:9000/foo.html - 다른 포트
-   https:\//contoso.com/foo.html - 다른 체계
-   http:\//www.contoso.com/foo.html - 다른 하위 도메인

동일한 원본 정책은 앱이 올바른 액세스 제어 헤더를 사용하지 않는 한 다른 원본의 리소스에 액세스하지 못하도록 합니다. CORS 헤더가 없거나 올바르지 않은 경우 원본 간 요청이 실패합니다. 

브라우저 디버그 도구를 사용하여 CORS 문제를 식별할 수 있습니다.

1. 브라우저를 실행하고 웹 앱을 찾아봅봅봅습니다.
1. **F12를** 눌러 디버그 콘솔을 가져옵니다.
1. 트랜잭션을 재현하고 콘솔 메시지를 검토해 보십시오. CORS 위반으로 Origin에 대한 콘솔 오류가 발생합니다.

다음 스크린샷에서 **Try It** 단추를 선택하면 https:\//corswebclient-contoso.msappproxy.net 액세스 제어-허용-원본 헤더에서 찾을 수 없는 CORS 오류 메시지가 발생했습니다.

![CORS 문제](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>응용 프로그램 프록시를 통해 해결된 CORS 문제

다음 예제에서는 일반적인 Azure AD 응용 프로그램 프록시 CORS 시나리오를 보여 주며 있습니다. 내부 서버는 **CORSWebService** 웹 API 컨트롤러와 **CORSWebService를 호출하는 CORSWebClient를 호스팅합니다.** **CORSWebClient** **CORSWebClient에서 CORSWebService에** 대한 AJAX **요청이 있습니다.**

![온-프레미스 동일 원산지 요청](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 앱은 온-프레미스에서 호스팅할 때 작동하지만 Azure AD 응용 프로그램 프록시를 통해 게시할 때 로드되지 않거나 오류가 발생합니다. CORSWebClient 및 CORSWebService 앱을 응용 프로그램 프록시를 통해 다른 앱으로 별도로 게시한 경우 두 앱은 서로 다른 도메인에서 호스팅됩니다. CORSWebClient에서 CORSWebService로의 AJAX 요청은 원본 간 요청이며 실패합니다.

![응용 프로그램 프록시 CORS 요청](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>응용 프로그램 프록시 CORS 문제에 대한 솔루션

여러 가지 방법 중 하나로 앞의 CORS 문제를 해결할 수 있습니다.

### <a name="option-1-set-up-a-custom-domain"></a>옵션 1: 사용자 지정 도메인 설정

Azure AD 응용 프로그램 프록시 [사용자 지정 도메인을](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) 사용하여 앱 원본, 코드 또는 헤더를 변경하지 않고도 동일한 원본에서 게시할 수 있습니다. 

### <a name="option-2-publish-the-parent-directory"></a>옵션 2: 상위 디렉터리 게시

두 앱의 상위 디렉터리를 게시합니다. 이 솔루션은 웹 서버에 두 개의 앱만 있는 경우에 특히 효과적입니다. 각 앱을 별도로 게시하는 대신 공통 상위 디렉터리를 게시할 수 있으며, 이로 인해 동일한 원본이 생성됩니다.

다음 예제는 CORSWebClient 앱에 대한 포털 Azure AD 응용 프로그램 프록시 페이지를 보여 주습니다.  내부 **URL이** *contoso.com/CORSWebClient*설정되면 앱은 교차 원본이기 때문에 *contoso.com/CORSWebService* 디렉터리에 대한 성공적인 요청을 할 수 없습니다. 

![앱을 개별적으로 게시](./media/application-proxy-understand-cors-issues/image4.png)

대신 내부 **URL을** 설정하여 *CORSWebClient* 및 *CORSWebService* 디렉터리를 모두 포함하는 상위 디렉터리를 게시합니다.

![상위 디렉터리 게시](./media/application-proxy-understand-cors-issues/image5.png)

결과 앱 URL은 CORS 문제를 효과적으로 해결합니다.

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>옵션 3: HTTP 헤더 업데이트

원본 요청과 일치하도록 웹 서비스에 사용자 지정 HTTP 응답 헤더를 추가합니다. IIS(인터넷 정보 서비스)에서 실행되는 웹 사이트의 경우 IIS 관리자를 사용하여 헤더를 수정합니다.

![IIS 관리자에 사용자 지정 응답 헤더 추가](./media/application-proxy-understand-cors-issues/image6.png)

이 수정은 코드를 변경할 필요가 없습니다. Fiddler 추적에서 확인할 수 있습니다.

**헤더 추가 게시**\
HTTP/1.1 200 OK\
캐시 제어: 캐시 없음\
Pragma: 캐시 없음\
콘텐츠 유형: 텍스트/일반; charset =utf-8\
만료: -1\
다양성: 인코딩 수락\
서버: 마이크로소프트-IIS/8.5 마이크로소프트-HTTPAPI/2.0\
**액세스 제어 허용-원산지:\:https//corswebclient-contoso.msappproxy.net**\
X-AspNet 버전: 4.0.30319\
X 구동 바이: ASP.NET\
콘텐츠 길이: 17

### <a name="option-4-modify-the-app"></a>옵션 4: 앱 수정

적절한 값으로 액세스 제어-허용-원본 헤더를 추가하여 CORS를 지원하도록 앱을 변경할 수 있습니다. 헤더를 추가하는 방법은 앱의 코드 언어에 따라 다릅니다. 코드를 변경하는 것은 가장 많은 노력이 필요하기 때문에 권장되는 옵션이 가장 적습니다.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>옵션 5: 액세스 토큰의 수명 연장

앱이 인증할 *login.microsoftonline.com* 리디렉션되고 액세스 토큰이 만료되는 경우와 같이 일부 CORS 문제는 해결할 수 없습니다. 그러면 CORS 호출이 실패합니다. 이 시나리오의 해결 방법은 액세스 토큰의 수명을 연장하여 사용자의 세션 중에 만료되지 않도록 하는 것입니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Azure AD의 구성 가능한 토큰 수명을](../develop/active-directory-configurable-token-lifetimes.md)참조하십시오.

## <a name="see-also"></a>참조
- [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md) 
- [Azure AD 응용 프로그램 프록시 배포 계획](application-proxy-deployment-plan.md) 
- [Azure Active Directory 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대한 원격 액세스](application-proxy.md) 
