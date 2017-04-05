---
title: "보안 토큰 인증을 사용하여 Azure CDN 자산 보안 유지 | Microsoft Docs"
description: "토큰 인증을 사용하여 Azure CDN 자산에 대한 액세스 보안을 유지할 수 있습니다."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.lasthandoff: 03/29/2017


---


# <a name="securing-azure-cdn-assets-with-token-authentication"></a>보안 토큰 인증을 사용하여 Azure CDN 자산 보안 유지

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>개요

토큰 인증은 Azure CDN이 권한 없는 클라이언트에 자산을 제공하지 못하도록 할 수 있는 메커니즘입니다.  이는 일반적으로 다른 웹 사이트(종종 메시지 보드)에서 권한 없이 사용자의 자산을 사용하는 콘텐츠 "핫링크"를 방지하기 위해 수행되며,  콘텐츠 배달 비용에 영향을 줄 수 있습니다. CDN에서 이 기능을 사용하도록 설정하면 콘텐츠를 배달하기 전에 CDN 에지 POP에서 요청이 인증됩니다. 

## <a name="how-it-works"></a>작동 방법

토큰 인증은 요청에 요청자에 대한 인코딩된 정보가 포함된 토큰 값을 포함하도록 요구하여 요청이 신뢰할 수 있는 사이트에서 생성되었는지 확인합니다. 인코딩된 정보가 요구 사항을 충족하는 경우에만 요청자에게 콘텐츠가 제공되며,그렇지 않으면 요청이 거부됩니다. 아래의 매개 변수를 하나 이상 사용하여 요구 사항을 설정할 수 있습니다.

- 국가: 지정된 국가에서 시작된 요청을 허용하거나 거부합니다.  [올바른 국가 코드 목록](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL: 요청에 대한 지정된 자산 또는 경로만 허용합니다.  
- 호스트: 요청 헤더의 지정된 호스트를 사용하여 요청을 허용하거나 거부합니다.
- 참조 페이지: 요청의 지정된 참조 페이지를 허용하거나 거부합니다.
- IP 주소: 특정 IP 주소 또는 IP 서브넷에서 시작된 요청만 허용합니다.
- 프로토콜: 콘텐츠를 요청하는 데 사용된 프로토콜에 따라 요청을 허용하거나 거부합니다.
- 만료 시간: 링크가 제한된 기간 동안만 유효하도록 날짜 및 시간을 할당합니다.

각 매개 변수에 대한 자세한 구성 예제를 참조하세요.

## <a name="reference-architecture"></a>참조 아키텍처

웹앱과 함께 작동하도록 CDN에서 토큰 인증을 설정하는 참조 아키텍처는 아래를 참조하세요.

![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN 끝점의 토큰 유효성 검사 논리
    
이 차트에서는 CDN 끝점에 토큰 인증이 구성된 경우 Azure CDN 클라이언트에서 요청의 유효성을 검사하는 방법을 설명합니다.

![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>토큰 인증 설정

1. [Azure Portal](https://portal.azure.com)에서 CDN 프로필로 이동한 다음 **관리** 단추를 클릭하여 보조 포털을 시작합니다.

    ![CDN 프로필 블레이드 관리 단추](./media/cdn-rules-engine/cdn-manage-btn.png)

2. **HTTP Large**를 가리키고 플라이아웃에서 **토큰 인증**을 클릭합니다. 이 탭에서 암호화 키 및 암호화 매개 변수를 설정합니다.

    1. **기본 키**에 대한 고유한 암호화 키를 입력합니다.  **백업 키**에 대한 또 다른 고유한 암호화 키를 입력합니다.

        ![CDN 토큰 인증 설정 키](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. 암호화 도구를 사용하여 암호화 매개 변수를 설정합니다(만료 시간, 참조 페이지, 프로토콜, 클라이언트 IP를 기반으로 요청을 허용하거나 거부하며, 원하는 조합을 사용할 수 있음).

        ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec-expire: 지정된 기간 이후의 토큰 만료 시간을 할당합니다. 만료 시간이 지나면 제출된 요청이 거부됩니다. 이 매개 변수는 Unix 타임스탬프를 사용합니다(표준 Epoch인 1970년 1월 1일 00:00:00 GMT 이후의 초 단위. 온라인 도구를 사용하여 표준시와 Unix 시간 간의 변환을 제공할 수 있음).  예를 들어 2016년 12월 31일 12:00:00 GMT에 만료되도록 토큰을 설정하려면 아래와 같이 Unix 시간1483185600을 사용합니다.
    
        ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec-url-allow: 특정 자산 또는 경로에 맞게 토큰을 조정할 수 있습니다. 해당 URL이 특정 상대 경로로 시작하는 요청에 대한 액세스를 제한합니다. 각 경로를 쉼표로 구분하여 여러 경로를 입력할 수 있습니다. URL은 대/소문자를 구분합니다. 요구 사항에 따라 서로 다른 값을 설정하여 서로 다른 수준의 액세스를 제공할 수 있습니다. 다음은 몇 가지 시나리오입니다.
        
            URL이 http://www.mydomain.com/pictures/city/strasbourg.png인 경우 입력 값("")과 해당 액세스 수준을 확인합니다.

            1. 입력 값 "/": 모든 요청이 허용됩니다.
            2. 입력 값 "/pictures": 다음 요청이 모두 허용됩니다.
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. 입력 값 "/pictures/": /pictures/에 대한 요청만 허용됩니다.
            4. 입력 값 "/ pictures/city/strasbourg.png": 이 자산에 대한 요청만 허용됩니다.
    
        ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec-country-allow: 하나 이상의 지정된 국가에서 시작된 요청만 허용합니다. 다른 모든 국가에서 시작된 요청은 거부됩니다. 국가 코드를 사용하여 매개 변수를 설정하고 각 국가 코드를 쉼표로 구분합니다. 예를 들어 미국 및 프랑스에서의 액세스를 허용하려면 아래와 같이 US, FR을 열에 입력합니다.  
        
        ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec-country-deny: 하나 이상의 지정된 국가에서 시작된 요청을 거부합니다. 다른 모든 국가에서 시작된 요청은 허용됩니다. 국가 코드를 사용하여 매개 변수를 설정하고 각 국가 코드를 쉼표로 구분합니다. 예를 들어 미국 및 프랑스에서의 액세스를 거부하려면 US, FR을 열에 입력합니다.
    
        - ec-ref-allow: 지정된 참조 페이지의 요청만 허용합니다. 참조 페이지는 요청 중인 리소스에 연결된 웹 페이지의 URL을 식별합니다. 참조 페이지 매개 변수 값은 프로토콜을 포함하지 않아야 합니다. 호스트 이름 및/또는 해당 호스트 이름의 특정 경로를 입력할 수 있습니다. 또한 단일 매개 변수 내에 쉼표로 구분하여 여러 참조 페이지를 추가할 수 있습니다. 참조 페이지 값을 지정했지만 일부 브라우저 구성으로 인해 요청에서 참조 페이지 정보가 전송되지 않는 경우 이러한 요청은 기본적으로 거부됩니다. 매개 변수에 "Missing" 또는 빈 값을 할당하여 참조 페이지 정보가 누락된 이러한 요청을 허용할 수 있습니다. 또한 "*.consoto.com"을 사용하여 consoto.com의 모든 하위 도메인을 허용할 수 있습니다.  예를 들어 www.consoto.com, consoto2.com 아래의 모든 하위 도메인 및 참조 페이지가 비어 있거나 누락된 요청에 대한 액세스를 허용하려면 아래와 같이 값을 입력합니다.
        
        ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec-ref-deny: 지정된 참조 페이지의 요청을 거부합니다. 자세한 내용 및 예제는 "ec-ref-allow" 매개 변수를 참조하세요.
         
        - ec-proto-allow: 지정된 프로토콜(예: http 또는 https)의 요청만 허용합니다.
        
        ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec-proto-allow: 지정된 프로토콜(예: http 또는 https)의 요청을 거부합니다.
    
        - ec-clientip: 지정된 요청자의 IP 주소에 대한 액세스를 제한합니다. IPV4 및 IPV6 모두 지원됩니다. 단일 요청 IP 주소 또는 IP 서브넷을 지정할 수 있습니다.
            
        ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. 설명 도구를 사용하여 토큰을 테스트할 수 있습니다.

    4. 또한 요청이 거부된 경우 사용자에게 반환되는 응답의 형식을 사용자 지정할 수 있습니다. 기본적으로 403을 사용합니다.

3. 이제 **HTTP Large** 아래에서 **규칙 엔진** 탭을 클릭합니다. 이 탭을 사용하여 기능을 적용할 경로를 정의하고, 토큰 인증 기능을 사용하도록 설정하고, 기능과 관련된 추가 토큰 인증을 사용하도록 설정합니다.

    - 토큰 인증을 적용할 자산 또는 경로를 정의하려면 "IF" 열을 사용합니다. 
    - 토큰 인증을 사용하도록 설정하려면 기능 드롭다운 목록에서 "토큰 인증"을 클릭하여 추가합니다.
        
    ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. **규칙 엔진** 탭에 사용할 수 있는 몇 가지 추가 기능이 있습니다.
    
    - 토큰 인증 거부 코드: 요청이 거부되는 경우 사용자에게 반환할 응답 형식을 결정합니다. 여기서 설정한 규칙은 토큰 인증 탭의 거부 코드 설정을 재정의합니다.
    - 토큰 인증 무시: 토큰의 유효성을 검사하는 데 사용되는 URL의 대/소문자 구분 여부를 결정합니다.
    - 토큰 인증 매개 변수: 요청된 URL에 표시되는 토큰 인증 쿼리 문자열 매개 변수의 이름을 바꿉니다. 
        
    ![CDN 프로필 블레이드 관리 단추](./media/cdn-token-auth/cdn-rules-engine2.png)

5. 토큰 기반 인증 기능에 대한 토큰을 생성하는 응용 프로그램인 토큰을 사용자 지정할 수 있습니다. 소스 코드는 [GitHub](https://github.com/VerizonDigital/ectoken)에서 액세스할 수 있습니다.
사용 가능한 언어는 다음과 같습니다.
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN 기능 및 공급자 가격 책정

[CDN 개요](cdn-overview.md) 항목을 참조하세요.

