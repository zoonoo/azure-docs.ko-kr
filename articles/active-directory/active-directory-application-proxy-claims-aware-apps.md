---
title: "응용 프로그램 프록시에서 클레임 인식 앱으로 작업"
description: "Azure AD 응용 프로그램 프록시를 작동시키는 방법에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 164f9fada5565110d37c0f191bcdfac9ec8e48e8


---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>응용 프로그램 프록시에서 클레임 인식 앱으로 작업
클레임 인식 앱은 STS(보안 토큰 서비스)에 리디렉션을 수행하며, 이에 대한 반응으로 사용자를 응용 프로그램에 리디렉션하기 전에 토큰 대신 사용자에게 자격 증명을 요청합니다. 이러한 리디렉션에서 응용 프로그램 프록시를 사용하려면 다음 단계를 수행해야 합니다.

## <a name="prerequisites"></a>필수 조건
이 절차를 수행하기 전에 클레임 인식 앱이 리디렉션되는 STS가 온-프레미스 네트워크 외부에서 사용 가능한지 확인합니다.

## <a name="azure-classic-portal-configuration"></a>Azure 클래식 포털 구성
1. [응용 프로그램 프록시로 응용 프로그램 게시](active-directory-application-proxy-publish.md)에 설명된 지침에 따라 응용 프로그램을 게시합니다.
2. 응용 프로그램 목록에서 클레임 인식 앱을 선택하고 **구성**을 클릭합니다.
3. **통과**를 **사전 인증 메서드**로 선택한 경우 **HTTPS**를 **외부 URL** 구성표로 선택해야 합니다.
4. **Azure Active Directory**를 **사전 인증 메서드**로 선택했다면 **없음**을 **내부 인증 방법**으로 선택합니다.

## <a name="adfs-configuration"></a>ADFS 구성
1. ADFS 관리를 엽니다.
2. **신뢰 당사자 트러스트**로 이동하여 응용 프로그램 프록시로 게시하고자 하는 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.  
   ![신뢰 당사자 트러스트 앱 이름을 마우스 오른쪽 단추로 클릭 - 스크린샷](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. **끝점** 탭에 있는 **끝점 유형** 아래에서 **WS-Federation**을 선택합니다.
4. **신뢰할 수 있는 URL** 아래에 **외부 URL** 아래에 있는 응용 프로그램 프록시에 입력한 URL을 입력하고 **확인**을 클릭합니다.  
   ![끝점 추가 - 신뢰할 수 있는 URL 값 설정 - 스크린샷](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>참고 항목
* [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)
* [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
* [응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)
* [네이티브 클라이언트 앱을 사용하여 프록시 응용 프로그램과 상호 작용](active-directory-application-proxy-native-client.md)

최신 뉴스 및 업데이트는 [응용 프로그램 프록시 블로그](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Dec16_HO5-->


