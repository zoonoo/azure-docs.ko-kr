---
title: API Management에서 클라이언트 인증서 인증을 사용하여 API 보호 - Azure API Management | Microsoft Docs
description: 클라이언트 인증서를 사용하여 API에 대한 액세스의 보안을 유지하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427315"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법

API Management에서는 클라이언트 인증서를 사용하여 API에 대한 액세스(예: API Management에 대한 클라이언트)를 보호하는 기능을 제공합니다. 들어오는 인증서의 유효성을 검사 하 고 정책 식을 사용 하 여 원하는 값에 대해 인증서 속성을 확인할 수 있습니다.

클라이언트 인증서 (예: 백 엔드에 API Management)을 사용 하 여 API의 백 엔드 서비스에 대 한 액세스를 보호 하는 방법에 대 한 내용은 [인증서 인증 클라이언트를 사용 하 여 백 엔드 서비스를 보호 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> 수신 및 소비 계층에서 클라이언트 인증서를 확인 하려면 먼저 "요청 클라이언트 인증서의" 아래와 같이 "사용자 지정 도메인" 블레이드에서 설정을 설정 해야 합니다.

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
> 확인 인증서 해지 목록 사용 하지 않도록 설정 `context.Request.Certificate.VerifyNoRevocation()` 대신 `context.Request.Certificate.Verify()`합니다.
> 클라이언트 인증서가 자체 서명 된 경우 루트 (또는 중간) CA 인증서 여야 합니다 [업로드](api-management-howto-ca-certificates.md) 에 대 한 API management `context.Request.Certificate.Verify()` 및 `context.Request.Certificate.VerifyNoRevocation()` 작동 하도록 합니다.

## <a name="checking-the-thumbprint"></a>지문 확인

클라이언트 인증서의 지문을 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 확인 인증서 해지 목록 사용 하지 않도록 설정 `context.Request.Certificate.VerifyNoRevocation()` 대신 `context.Request.Certificate.Verify()`합니다.
> 클라이언트 인증서가 자체 서명 된 경우 루트 (또는 중간) CA 인증서 여야 합니다 [업로드](api-management-howto-ca-certificates.md) 에 대 한 API management `context.Request.Certificate.Verify()` 및 `context.Request.Certificate.VerifyNoRevocation()` 작동 하도록 합니다.

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
> 확인 인증서 해지 목록 사용 하지 않도록 설정 `context.Request.Certificate.VerifyNoRevocation()` 대신 `context.Request.Certificate.Verify()`합니다.
> 클라이언트 인증서가 자체 서명 된 경우 루트 (또는 중간) CA 인증서 여야 합니다 [업로드](api-management-howto-ca-certificates.md) 에 대 한 API management `context.Request.Certificate.Verify()` 및 `context.Request.Certificate.VerifyNoRevocation()` 작동 하도록 합니다.

> [!TIP]
> 설명 하는 클라이언트 인증서 교착 상태 문제 [문서](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) 다양 한 방식에서으로 확인할 수 있습니다, 예: 요청을 고정 요청 결과 `403 Forbidden` 제한 시간, 종료 후 상태 코드 `context.Request.Certificate` 는 `null`합니다. 이 일반적으로 문제가 `POST` 고 `PUT` 60KB 약 이상인 콘텐츠 길이 사용 하 여 요청 합니다.
> 이 문제가 발생에서 하지 않으려면 아래와 같이 "사용자 지정 도메인" 블레이드에서 원하는 호스트 이름에 대 한 "Negotiate 클라이언트 인증서" 설정을 켭니다. 이 기능은 소비 계층에서 사용할 수 없습니다.

![클라이언트 인증서를 협상 합니다.](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>다음 단계

-   [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [인증서 업로드 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
