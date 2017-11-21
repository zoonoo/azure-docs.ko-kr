---
title: "보안 토큰 인증을 사용하여 Azure CDN 자산 보안 유지 | Microsoft Docs"
description: "토큰 인증을 사용하여 Azure CDN 자산에 대한 액세스 보안을 유지하는 방법을 알아봅니다."
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
ms.date: 11/03/2017
ms.author: mezha
ms.openlocfilehash: 700f4c49bbcda1eccbcc7eafc703e625697fa2b4
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>토큰 인증을 사용하여 Azure Content Delivery Network 자산 보안 유지

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>개요

토큰 인증은 Azure CDN(Content Delivery Network)이 권한 없는 클라이언트에 자산을 제공하지 못하도록 할 수 있는 메커니즘입니다. 토큰 인증은 일반적으로 다른 웹 사이트(종종 메시지 보드)에서 권한 없이 사용자의 자산을 사용하는 콘텐츠 "핫링크"를 방지하기 위해 수행되며, 핫링크는 콘텐츠 배달 비용에 영향을 줄 수 있습니다. CDN에서 토큰 인증을 사용하도록 설정하면 CDN에서 콘텐츠를 전송하기 전에 요청이 CDN 에지 POP에서 인증됩니다. 

## <a name="how-it-works"></a>작동 방법

토큰 인증은 요청에 요청자에 대한 인코딩된 정보가 포함된 토큰 값을 포함하도록 요구하여 요청이 신뢰할 수 있는 사이트에서 생성되었는지 확인합니다. 인코딩된 정보가 요구 사항을 충족하는 경우에만 요청자에게 콘텐츠가 제공되며, 그렇지 않으면 요청이 거부됩니다. 다음 매개 변수 중 하나 이상을 사용하여 요구 사항을 설정할 수 있습니다.

- 국가: [국가 코드](https://msdn.microsoft.com/library/mt761717.aspx)에 지정된 국가에서 발생한 요청을 허용하거나 거부합니다.
- URL: 지정된 자산 또는 경로와 일치하는 요청만 허용합니다.
- 호스트: 요청 헤더의 지정된 호스트를 사용하는 요청을 허용하거나 거부합니다.
- 참조 페이지: 지정된 참조 페이지의 요청을 허용하거나 거부합니다.
- IP 주소: 특정 IP 주소 또는 IP 서브넷에서 시작된 요청만 허용합니다.
- 프로토콜: 콘텐츠를 요청하는 데 사용된 프로토콜에 따라 요청을 허용하거나 거부합니다.
- 만료 시간: 링크가 제한된 기간 동안만 유효하도록 날짜 및 시간을 할당합니다.

자세한 내용은 [토큰 인증 설정](#setting-up-token-authentication)에서 각 매개 변수에 대한 자세한 구성 예를 참조하세요.

암호화된 토큰을 생성한 후에는 쿼리 문자열로 파일 URL 경로 끝에 추가합니다. 예: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

## <a name="reference-architecture"></a>참조 아키텍처

다음 워크플로 다이어그램은 CDN이 토큰 인증을 사용하여 웹앱 작업을 하는 방법을 설명합니다.

![CDN 토큰 인증 워크플로](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN 끝점의 토큰 유효성 검사 논리
    
다음 순서도에서는 CDN 끝점에 토큰 인증이 구성된 경우 Azure CDN 클라이언트에서 요청의 유효성을 검사하는 방법을 설명합니다.

![CDN 토큰 유효성 검사 논리](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>토큰 인증 설정

1. [Azure Portal](https://portal.azure.com)에서 CDN 프로필로 이동한 다음 **관리** 단추를 클릭하여 보조 포털을 시작합니다.

    ![CDN 프로필 관리 단추](./media/cdn-rules-engine/cdn-manage-btn.png)

2. **HTTP Large**를 가리키고 플라이아웃에서 **토큰 인증**을 클릭합니다. 그런 다음 암호화 키와 암호화 매개 변수를 다음과 같이 설정할 수 있습니다.

    1. **기본 키** 상자에 고유한 암호화 키를 입력하고 선택적으로 **백업 키** 상자에 백업 키를 입력합니다.

        ![CDN 토큰 인증 설정 키](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. 암호화 도구로 암호화 매개 변수를 설정합니다. 암호화 도구를 사용하면 만료 시간, 국가, 참조 페이지, 프로토콜 및 클라이언트 IP(모든 조합)를 기반으로 요청을 허용하거나 거부할 수 있습니다. 

        ![CDN 암호화 도구](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       **암호화 도구** 영역에서 다음 암호화 매개 변수 중 하나 이상에 대한 값을 입력합니다.  

       - **ec_expire**: 토큰이 만료되는 토큰 만료 시간을 할당합니다. 만료 시간이 지나면 제출된 요청이 거부됩니다. 이 매개 변수는 `1/1/1970 00:00:00 GMT` 표준 Epoch 이후의 초 수를 기반으로 하는 Unix 타임스탬프를 사용합니다. 온라인 도구를 사용하여 표준시와 Unix 시간 간을 변환할 수 있습니다. 예를 들어 토큰이 `12/31/2016 12:00:00 GMT`에 만료되도록 하려면 다음과 같이 Unix 타임스탬프 값 `1483185600`을 사용합니다. 
    
         ![CDN ec_expire 예제](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - **ec_url_allow**: 특정 자산 또는 경로에 맞게 토큰을 조정할 수 있습니다. 해당 URL이 특정 상대 경로로 시작하는 요청에 대한 액세스를 제한합니다. URL은 대/소문자를 구분합니다. 각 경로를 쉼표로 구분하여 여러 경로를 입력합니다. 요구 사항에 따라 서로 다른 값을 설정하여 서로 다른 수준의 액세스를 제공할 수 있습니다. 
        
         예를 들어 URL `http://www.mydomain.com/pictures/city/strasbourg.png`의 경우 다음 입력 값에 대한 요청이 허용됩니다.

         - 입력 값 `/`: 모든 요청이 허용됩니다.
         - 입력 값 `/pictures`: 다음 요청이 허용됩니다.
            - `http://www.mydomain.com/pictures.png`
            - `http://www.mydomain.com/pictures/city/strasbourg.png`
            - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
         - 입력 값 `/pictures/`: `/pictures/` 경로가 포함된 요청만 허용됩니다. 예: `http://www.mydomain.com/pictures/city/strasbourg.png`.
         - 입력 값 `/pictures/city/strasbourg.png`: 이 특정 경로 및 자산에 대한 요청만 허용됩니다.
    
       - **ec_country_allow**: 하나 이상의 지정된 국가에서 시작된 요청만 허용합니다. 다른 모든 국가에서 시작된 요청은 거부됩니다. 국가 코드를 사용하고 각각을 쉼표로 구분합니다. 예를 들어 미국 및 프랑스에서만 액세스를 허용하려면 아래와 같이 US, FR을 상자에 입력합니다.  
        
           ![CDN ec_country_allow 예제](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - **ec_country_deny**: 하나 이상의 지정된 국가에서 시작된 요청을 거부합니다. 다른 모든 국가에서 시작된 요청은 허용됩니다. 국가 코드를 사용하고 각각을 쉼표로 구분합니다. 예를 들어 미국 및 프랑스에서의 액세스를 거부하려면 US, FR을 상자에 입력합니다.
    
       - **ec_ref_allow**: 지정된 참조 페이지의 요청만 허용합니다. 참조 페이지는 요청 중인 리소스에 연결된 웹 페이지의 URL을 식별합니다. 참조 페이지 매개 변수 값의 프로토콜을 포함하지 않아야 합니다. 매개 변수 값에는 다음 유형의 입력이 허용됩니다.
           - 호스트 이름 또는 호스트 이름과 경로
           - 여러 참조 페이지. 여러 참조 페이지를 추가하려면 각 참조 페이지를 쉼표로 구분합니다. 참조 페이지 값을 지정했지만 브라우저 구성으로 인해 요청에서 참조 페이지 정보가 전송되지 않는 경우 이러한 요청은 기본적으로 거부됩니다. 
           - 참조 페이지 정보가 누락된 요청. 이러한 유형의 요청을 허용하려면 "없음" 텍스트를 입력하거나 빈 값을 입력합니다. 
           - 하위 도메인. 하위 도메인을 허용하려면 별표(\*)를 입력합니다. 예를 들어 `consoto.com`의 모든 하위 도메인을 허용하려면 `*.consoto.com`을 입력합니다. 
           
          다음 예제에서는 `www.consoto.com`의 요청, `consoto2.com`의 모든 하위 도메인 및 공백이나 누락된 참조 페이지의 요청에 대한 액세스를 허용하는 입력을 보여 줍니다.
        
          ![CDN ec_ref_allow 예제](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - **ec_ref_deny**: 지정된 참조 페이지의 요청을 거부합니다. 구현은 ec_ref_allow 매개 변수와 동일합니다.
         
       - **ec_proto_allow**: 지정된 프로토콜의 요청만 허용합니다. 예를 들어 HTTP 또는 HTTPS입니다.
            
       - **ec_proto_deny**: 지정된 프로토콜의 요청을 거부합니다. 예를 들어 HTTP 또는 HTTPS입니다.
    
       - **ec_clientip**: 지정된 요청자의 IP 주소에 대한 액세스를 제한합니다. IPV4 및 IPV6 모두 지원됩니다. 단일 요청 IP 주소 또는 IP 서브넷을 지정할 수 있습니다.
            
         ![CDN ec_clientip 예제](./media/cdn-token-auth/cdn-token-auth-clientip.png)

    3. 암호화 매개 변수 값 입력을 마친 후에는 **암호화할 키** 목록에서 암호화할 키 유형(기본 키와 백업 키를 모두 만든 경우)을 선택하고 **암호화 버전** 목록에서 암호화 버전을 선택하고 **암호화**를 클릭합니다.
        
    4. 필요에 따라 설명 도구를 사용하여 토큰을 테스트합니다. **암호를 해독할 토큰** 상자에 토큰 값을 붙여넣습니다. **암호를 해독할 키** 드롭다운 목록에서 암호를 해독할 암호화 키 유형을 선택한 다음 **암호 해독**을 클릭합니다.

    5. 필요에 따라 요청이 거부될 때 반환되는 응답 코드 유형을 사용자 지정할 수 있습니다. **응답 코드** 드롭다운 목록에서 코드를 선택하고 **저장**을 클릭합니다. **403** 응답 코드(사용할 수 없음)가 기본적으로 선택됩니다. 특정 응답 코드의 경우 **헤더 값** 상자에 오류 페이지의 URL을 입력할 수도 있습니다. 

    6. 암호화된 토큰을 생성한 후에는 쿼리 문자열로 URL 경로의 파일 끝에 추가합니다. 예: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

3. **HTTP Large** 아래에서 **규칙 엔진**을 클릭합니다. 규칙 엔진을 사용하여 기능을 적용할 경로를 정의하고, 토큰 인증 기능을 사용하도록 설정하고, 기능과 관련된 추가 토큰 인증을 사용하도록 설정합니다. 자세한 내용은 [규칙 엔진 참조](cdn-rules-engine-reference.md)를 참조하세요.

    1. 기존 규칙을 선택하거나 새 규칙을 만들어 토큰 인증을 적용할 자산 또는 경로를 정의합니다. 
    2. 규칙에서 토큰 인증을 사용하도록 설정하려면 **기능** 드롭다운 목록에서 **[토큰 인증](cdn-rules-engine-reference-features.md#token-auth)**을 선택한 다음 **사용**을 선택합니다. 규칙을 업데이트하는 경우 **업데이트**를 클릭하고 규칙을 만드는 경우 **추가**를 클릭합니다.
        
    ![CDN 규칙 엔진 토큰 인증 사용 예제](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. 규칙 엔진에서 추가 토큰 인증 관련 기능을 사용하도록 설정할 수도 있습니다. 다음 기능 중 하나를 사용하도록 설정하려면 **기능** 드롭다운 목록에서 해당 기능을 선택한 다음 **사용**을 선택합니다.
    
    - **[토큰 인증 거부 코드](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: 요청이 거부되는 경우 사용자에게 반환할 응답 형식을 결정합니다. 여기서 설정된 규칙은 토큰 기반 인증 페이지의 **사용자 지정 거부 처리** 섹션에 설정된 응답 코드를 재정의합니다.
    - **[토큰 인증 URL 대/소문자 무시](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: 토큰 유효성을 검사하는 데 사용되는 URL의 대/소문자 구분 여부를 결정합니다.
    - **[토큰 인증 매개 변수](cdn-rules-engine-reference-features.md#token-auth-parameter)**: 요청된 URL에 표시되는 토큰 인증 쿼리 문자열 매개 변수의 이름을 바꿉니다. 
        
    ![CDN 규칙 엔진 토큰 인증 설정 예제](./media/cdn-token-auth/cdn-rules-engine2.png)

5. [GitHub](https://github.com/VerizonDigital/ectoken)의 소스 코드에 액세스하여 토큰을 사용자 지정할 수 있습니다.
사용 가능한 언어는 다음과 같습니다.
    
- C
- C#
- PHP
- Perl
- Java
- Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN 기능 및 공급자 가격 책정

자세한 내용은 [CDN 개요](cdn-overview.md)를 참조하세요.
