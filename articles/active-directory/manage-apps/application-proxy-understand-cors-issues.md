---
title: Azure AD 응용 프로그램 프록시 CORS 문제 이해 및 해결
description: Azure AD 응용 프로그램 프록시의 CORS 및 CORS 문제를 식별 하 고 해결 하는 방법에 대해 설명 합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8836295e9f54260c4e9ff6c1da333ef2a86d58fb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651858"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>CORS 문제 Azure Active Directory 응용 프로그램 프록시 이해 및 해결

[CORS (크로스-원본 자원 공유)](https://www.w3.org/TR/cors/) 는 Azure Active Directory 응용 프로그램 프록시을 통해 게시 하는 앱 및 api에 대 한 문제를 종종 일으킬 수 있습니다. 이 문서에서는 Azure AD 응용 프로그램 프록시 CORS 문제 및 해결 방법에 대해 설명 합니다.

브라우저 보안은 일반적으로 웹 페이지에서 다른 도메인에 대 한 AJAX 요청을 수행 하지 못하도록 합니다. 이러한 제한을 *동일한 원본 정책* 이라고 하며 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다. 그러나 경우에 따라 다른 사이트에서 web API를 호출 하도록 할 수도 있습니다. CORS는 서버에서 동일한 원본 정책을 완화 하 고 일부 원본 간 요청을 허용 하 여 다른 사용자를 거부할 수 있게 해 주는 W3C 표준입니다.

## <a name="understand-and-identify-cors-issues"></a>CORS 문제 이해 및 식별

동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 개의 url은 동일한 원본을 갖습니다.

-   http: \/ /contoso.com/foo.html
-   http: \/ /contoso.com/bar.html

다음 Url의 원본이 이전 두의 원본과 다릅니다.

-   http: \/ /contoso.net-다른 도메인
-   http: \/ /contoso.com:9000/foo.html-다른 포트
-   https: \/ /contoso.com/foo.html-다른 체계
-   http: \/ /www.contoso.com/foo.html-다른 하위 도메인

동일한 원본 정책은 올바른 액세스 제어 헤더를 사용 하지 않는 한 앱이 다른 원본에서 리소스에 액세스할 수 없도록 합니다. CORS 헤더가 없거나 잘못 된 경우 크로스-원본 요청이 실패 합니다. 

브라우저 디버그 도구를 사용 하 여 CORS 문제를 식별할 수 있습니다.

1. 브라우저를 시작 하 고 웹 앱으로 이동 합니다.
1. **F12** 키를 눌러 디버그 콘솔을 엽니다.
1. 트랜잭션을 재현 하 고 콘솔 메시지를 검토 하십시오. CORS 위반으로 인해 원본에 대 한 콘솔 오류가 생성 됩니다.

다음 스크린샷에는 **사용해 보기** 단추를 선택 하면 \/ corswebclient-contoso.msappproxy.net 헤더에서 https:/가 발견 되지 않았음을 나타내는 CORS 오류 메시지가 발생 했습니다.

![CORS 문제](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>응용 프로그램 프록시를 사용한 CORS 챌린지

다음 예제에서는 일반적인 Azure AD 응용 프로그램 프록시 CORS 시나리오를 보여 줍니다. 내부 서버는 **corswebservice** 웹 API 컨트롤러 및 **corswebservice** 를 호출 하는 **corswebservice** 를 호스팅합니다. **Corswebclient** 에서 **CORSWEBCLIENT** 로의 AJAX 요청이 있습니다.

![온-프레미스 동일한 원본 요청](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 앱은 온-프레미스에서 호스트 하는 경우 작동 하지만, Azure AD 응용 프로그램 프록시를 통해 게시 될 때 로드 되지 않거나 오류가 발생 합니다. 응용 프로그램 프록시를 통해 CORSWebClient 및 Corswebclient 앱을 다른 앱으로 개별적으로 게시 한 경우 두 앱은 서로 다른 도메인에서 호스팅됩니다. CORSWebClient에서 Corswebclient로의 AJAX 요청은 크로스-원본 요청 이며 실패 합니다.

![응용 프로그램 프록시 CORS 요청](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>응용 프로그램 프록시 CORS 문제에 대 한 솔루션

여러 가지 방법 중 하나로 이전 CORS 문제를 해결할 수 있습니다.

### <a name="option-1-set-up-a-custom-domain"></a>옵션 1: 사용자 지정 도메인 설정

앱 원본, 코드 또는 헤더를 변경 하지 않고도 Azure AD 응용 프로그램 프록시 [사용자 지정 도메인](./application-proxy-configure-custom-domain.md) 을 사용 하 여 동일한 원본에서 게시할 수 있습니다. 

### <a name="option-2-publish-the-parent-directory"></a>옵션 2: 부모 디렉터리 게시

두 앱의 부모 디렉터리를 게시 합니다. 이 솔루션은 웹 서버에 두 개의 앱만 있는 경우에 특히 효과적입니다. 각 앱을 별도로 게시 하는 대신 동일한 원본을 생성 하는 공통 부모 디렉터리를 게시할 수 있습니다.

다음 예에서는 CORSWebClient 앱에 대 한 포털 Azure AD 응용 프로그램 프록시 페이지를 보여 줍니다.  **내부 URL** 이 *contoso.com/CORSWebClient* 로 설정 된 경우 응용 프로그램은 교차 원본 이기 때문에 *contoso.com/CORSWebService* 디렉터리에 대 한 성공적인 요청을 수행할 수 없습니다. 

![개별적으로 앱 게시](./media/application-proxy-understand-cors-issues/image4.png)

대신 *Corswebclient* 및 *corswebclient* 디렉터리를 모두 포함 하는 부모 디렉터리를 게시 하도록 **내부 URL** 을 설정 합니다.

![부모 디렉터리 게시](./media/application-proxy-understand-cors-issues/image5.png)

결과 앱 Url은 CORS 문제를 효과적으로 해결 합니다.

- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebService
- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>옵션 3: HTTP 헤더 업데이트

웹 서비스에 사용자 지정 HTTP 응답 헤더를 추가 하 여 원본 요청과 일치 시킵니다. 인터넷 정보 서비스 (IIS)에서 실행 되는 웹 사이트의 경우에는 IIS 관리자를 사용 하 여 헤더를 수정 합니다.

![IIS 관리자에서 사용자 지정 응답 헤더 추가](./media/application-proxy-understand-cors-issues/image6.png)

이 수정 작업에는 코드 변경이 필요 하지 않습니다. Fiddler 추적에서 확인할 수 있습니다.

**헤더 추가 게시**\
HTTP/1.1 200 OK \
Cache-control: 캐시 안 함 \
Pragma: no cache \
Content-type: 텍스트/일반 형식입니다. charset = utf-8 \
만료:-1 \
Vary: Accept-Encoding \
서버: Microsoft-IIS/8.5 Microsoft-HTTPAPI.DLL/2.0 \
**액세스 제어-허용-원본: https \: //corswebclient-contoso.msappproxy.net**\
X-AspNet-버전: 4.0.30319 \
X-구동: ASP.NET \
콘텐츠-길이: 17

### <a name="option-4-modify-the-app"></a>옵션 4: 앱 수정

적절 한 값을 사용 하 여 액세스 제어-허용-원본 헤더를 추가 하 여 CORS를 지원 하도록 앱을 변경할 수 있습니다. 헤더를 추가 하는 방법은 앱의 코드 언어에 따라 달라 집니다. 코드를 변경 하는 것이 가장 좋습니다. 가장 좋은 옵션은 가장 필요 합니다.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>옵션 5: 액세스 토큰의 수명 연장

앱이 인증을 위해 *login.microsoftonline.com* 로 리디렉션되고 액세스 토큰이 만료 되는 경우와 같이 일부 CORS 문제는 해결할 수 없습니다. 그런 다음 CORS 호출이 실패 합니다. 이 시나리오에 대 한 해결 방법은 사용자 세션 중에 만료 되지 않도록 액세스 토큰의 수명을 연장 하는 것입니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [AZURE AD의 구성 가능한 토큰 수명](../develop/active-directory-configurable-token-lifetimes.md)을 참조 하세요.

## <a name="see-also"></a>참조
- [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md) 
- [Azure AD 애플리케이션 프록시 배포 계획](application-proxy-deployment-plan.md) 
- [Azure Active Directory 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 원격으로 액세스](application-proxy.md)