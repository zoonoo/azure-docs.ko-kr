---
title: Azure Video Analyzer 액세스 정책
description: 이 문서에서는 Azure Video Analyzer가 액세스 정책에서 JWT 토큰을 사용하여 비디오를 보호하는 방법을 설명합니다.
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 3cf450249567d07bf6855d115a0e39640074eeb0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604197"
---
# <a name="access-policies"></a>액세스 정책

액세스 정책은 지정된 Video Analyzer 비디오 리소스에 대한 액세스 권한 및 기간을 정의합니다. 이러한 액세스 정책을 통해 타사(비 AAD 클라이언트) JWT 토큰을 사용하여 다음을 사용하도록 설정하는 클라이언트 API에 대한 권한 부여를 제공할 수 있으므로 제어 및 유연성이 향상됩니다. 

- 비디오 메타데이터에 대한 액세스 
- 비디오 스트리밍에 대한 액세스 

## <a name="access-policy-definition"></a>액세스 정책 정의

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> 키 유형은 하나만 필요합니다. 

### <a name="roles"></a>역할

현재 읽기 권한자 역할만 지원됩니다.

### <a name="issuer-matching-rules"></a>발급자 일치 규칙

여러 문제를 정책에 지정할 수 있으며 토큰에는 단일 발급자를 지정할 수 있습니다.  토큰 발급자가 정책에 지정된 발급자 중 하나인 경우 발급자가 일치합니다.

### <a name="audience-matching-rules"></a>대상 그룹 일치 규칙

대상 그룹 값이 비디오 리소스에 대해 ${System.Runtime.BaseResourceUrlPattern}인 경우 JWT 토큰에 제공된 대상은 기본 리소스 URL과 일치해야 합니다. 그렇지 않으면 토큰 대상 그룹이 액세스 정책의 대상 그룹과 일치해야 합니다.

### <a name="claims-matching-rules"></a>클레임 일치 규칙

액세스 정책 및 JWT 토큰에 여러 클레임을 지정할 수 있습니다.  유효성 검사를 통과하기 위해 토큰에 액세스 정책의 모든 클레임을 제공해야 하지만 JWT 토큰에는 액세스 정책에 나열되지 않은 추가 클레임이 있을 수 있습니다.

### <a name="keys"></a>구성

RSA 및 ECC 형식이라는 두 가지 유형의 키가 지원됩니다.

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg - 알고리즘  256, 384 또는 512가 될 수 있습니다. 
* kid - 키 ID
* n - 모듈러스
* e - 공용 지수 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg - 알고리즘  256, 384 또는 512가 될 수 있습니다.
* kid - 키 ID
* x - 좌표 값
* y - 좌표 값

### <a name="token-validation-process"></a>토큰 유효성 검사 프로세스

고객은 자체 JWT 토큰을 만들어야 하며 다음 방법을 사용하여 유효성을 검사합니다.

- 키 ID와 일치하는 정책 목록에서 유효성을 검사합니다.
  - 토큰 서명
  - 토큰 만료
  - 발급자
  - 사용자
  - 추가 클레임

### <a name="policy-audience-and-token-matching-examples"></a>정책 대상 그룹 및 토큰 일치 예제:

| **정책 대상 그룹**                      | 요청된 URL                         | 토큰 URL                            | 결과 |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| (모든 리터럴)                            | (임의)                                 | (일치)                              | 허용  |
| (모든 리터럴)                            | (임의)                                 | (일치하지 않음)                          | 거부   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | 거부   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | 허용  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | 거부   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | 거부   |

> [!NOTE]  
> Video Analyzer는 최대 20개의 정책을 지원합니다.  ${System.Runtime.BaseResourceUrlPattern}을 사용하면 하나의 액세스 정책 및 여러 토큰을 사용하여 특정 리소스에 보다 유연하게 액세스할 수 있습니다.  그러면 이러한 토큰을 통해 대상 그룹을 기반으로 하는 다양한 Video Analyzer 리소스에 액세스할 수 있습니다. 

## <a name="creating-an-access-policy"></a>액세스 정책 만들기

액세스 정책을 만드는 방법에는 다음 두 가지가 있습니다.

### <a name="in-the-azure-portal"></a>Azure Portal에서

1. Azure Portal에 로그인하여 Video Analyzer 계정이 있는 리소스 그룹으로 이동합니다.
2. Video Analyzer 리소스를 선택합니다.
3. Video Analyzer 아래에서 액세스 정책을 선택합니다.

   :::image type="content" source="./media/access-policies/access-policies-menu.png" alt-text="Azure Portal의 액세스 정책 메뉴":::
4. 새로 만들기를 클릭하고 다음을 입력합니다.

   - 액세스 정책 이름 - 임의의 이름입니다.
   - 발급자 - JWT 토큰 발급자와 일치해야 합니다. 
   - 대상 그룹 - JWT 토큰의 대상 그룹 -- ${System.Runtime.BaseResourceUrlPattern}이 기본값입니다. 
   - 키 유형 - kty 
   - 알고리즘 - alg
   - 키 ID - kid 
   - N / X 값 
   - E / Y 값 

   :::image type="content" source="./media/access-policies/access-policies-portal.png" alt-text="Azure Portal의 액세스 정책":::
5. `Save`을 클릭합니다.

### <a name="create-access-policy-via-api"></a>API를 통해 액세스 정책 만들기

ARM(Azure Resource Manager) API 참조 

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
