---
title: API 관리에서 클라이언트 인증서 인증을 사용하는 보안 API
titleSuffix: Azure API Management
description: 클라이언트 인증서를 사용하여 API에 대한 액세스의 보안을 유지하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713140"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법

API Management에서는 클라이언트 인증서를 사용하여 API에 대한 액세스(예: API Management에 대한 클라이언트)를 보호하는 기능을 제공합니다. 들어오는 인증서의 유효성을 검사하고 정책 식을 사용하여 원하는 값에 대해 인증서 속성을 확인할 수 있습니다.

클라이언트 인증서를 사용하여 API의 백 엔드 서비스에 대한 액세스 보안(예: API 관리에서 백 엔드까지)에 대한 액세스 보안에 대한 자세한 내용은 [클라이언트 인증서를 사용하여 백 엔드 서비스를 보호하는 방법을](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) 참조하세요.

> [!IMPORTANT]
> 개발자, 기본, 표준 또는 프리미엄 계층에서 HTTP/2를 통해 클라이언트 인증서를 수신하고 확인하려면 아래와 같이 "사용자 지정 도메인" 블레이드에서 "클라이언트 인증서 협상" 설정을 설정해야 합니다.

![클라이언트 인증서 협상](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 소비 계층에서 클라이언트 인증서를 받고 확인하려면 아래와 같이 "사용자 지정 도메인" 블레이드에서 "클라이언트 인증서 요청" 설정을 설정해야 합니다.

![클라이언트 인증서 요청](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>발급자 및 주체 확인

클라이언트 인증서의 발급자 및 주체를 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 인증서 해지 목록 사용을 `context.Request.Certificate.VerifyNoRevocation()` 사용하지 `context.Request.Certificate.Verify()`않도록 설정하려면 을 사용합니다.
> 클라이언트 인증서가 자체 서명된 경우 루트(또는 중간) CA 인증서를 API `context.Request.Certificate.Verify()` Management에 `context.Request.Certificate.VerifyNoRevocation()` [업로드하고](api-management-howto-ca-certificates.md) 작동하려면 업로드해야 합니다.

## <a name="checking-the-thumbprint"></a>지문 확인

클라이언트 인증서의 지문을 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 인증서 해지 목록 사용을 `context.Request.Certificate.VerifyNoRevocation()` 사용하지 `context.Request.Certificate.Verify()`않도록 설정하려면 을 사용합니다.
> 클라이언트 인증서가 자체 서명된 경우 루트(또는 중간) CA 인증서를 API `context.Request.Certificate.Verify()` Management에 `context.Request.Certificate.VerifyNoRevocation()` [업로드하고](api-management-howto-ca-certificates.md) 작동하려면 업로드해야 합니다.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management에 업로드된 인증서에 대해 지문 확인

다음 예제에서는 API Management에 업로드된 인증서에 대해 클라이언트 인증서의 지문을 확인하는 방법을 보여 줍니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 인증서 해지 목록 사용을 `context.Request.Certificate.VerifyNoRevocation()` 사용하지 `context.Request.Certificate.Verify()`않도록 설정하려면 을 사용합니다.
> 클라이언트 인증서가 자체 서명된 경우 루트(또는 중간) CA 인증서를 API `context.Request.Certificate.Verify()` Management에 `context.Request.Certificate.VerifyNoRevocation()` [업로드하고](api-management-howto-ca-certificates.md) 작동하려면 업로드해야 합니다.

> [!TIP]
> 이 [문서에](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) 설명된 클라이언트 인증서 교착 상태 문제는 여러 가지 방법으로 나타날 `403 Forbidden` 수 있습니다(예: `context.Request.Certificate` `null`요청 고정, 요청은 시간 지정 후 상태 코드로 생성됩니다. 이 문제는 `POST` 일반적으로 `PUT` 약 60KB 이상의 콘텐츠 길이가 있는 요청과 영향을 줍니다.
> 아래와 같이 "사용자 지정 도메인" 블레이드에서 원하는 호스트 이름에 대해 "클라이언트 인증서 협상" 설정을 켜지 않도록 합니다. 이 기능은 소비 계층에서 사용할 수 없습니다.

![클라이언트 인증서 협상](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>다음 단계

-   [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [인증서 업로드 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
