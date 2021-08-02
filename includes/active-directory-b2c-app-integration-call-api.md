---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: fe128e7eee99d2b396c758979f0efff936ec6785
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073066"
---
인증 후에 사용자는 전달자 토큰 인증을 사용하여 보호된 웹 API를 호출하는 페이지로 이동합니다. 전달자 토큰은 Azure AD B2C에서 가져온 액세스 토큰입니다. 앱은 HTTPS 요청의 인증 헤더에 토큰을 전달합니다. 
    
```http
Authorization: Bearer <token>
```

액세스 토큰 범위가 웹 API 범위와 일치하지 않는 경우 인증 라이브러리는 올바른 범위의 새 액세스 토큰을 얻게 됩니다.
초