---
title: "OAuth&2;.0을 사용한 Azure AD 서비스 간 인증 | Microsoft Docs"
description: "이 문서는 OAuth&2;.0 클라이언트 자격 증명 부여 흐름을 사용하여 서비스 간 인증을 구현하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 9904ec79782b1790ecb21f31b078fea9936c8380
ms.openlocfilehash: c5164ad1f0fef17a86295db5e4caee21a9a19d33


---
# <a name="service-to-service-calls-using-client-credentials"></a>클라이언트 자격 증명을 사용하여 서비스를 호출하는 서비스
OAuth 2.0 클라이언트 자격 증명 부여 흐름은 사용자를 가장하는 대신 다른 웹 서비스를 호출할 때 웹 서비스( **기밀 클라이언트**)가 자체 자격 증명을 사용하여 인증하도록 허용합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다.

## <a name="client-credentials-grant-flow-diagram"></a>클라이언트 자격 증명 부여 흐름 다이어그램
다음 다이어그램에서는 Azure Active Directory(Azure AD)에서 클라이언트 자격 증명 부여 흐름이 작동하는 방법에 대해 설명합니다.

![OAuth&2;.0 클라이언트 자격 증명 부여 흐름](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. 클라이언트 응용 프로그램은 Azure AD 토큰 발급 끝점 인증하고 액세스 토큰을 요청합니다.
2. Azure AD 토큰 발급 끝점이 액세스 토큰을 발급합니다.
3. 보안 리소스에 인증하는 데 액세스 토큰이 사용됩니다.
4. 보안 리소스의 데이터는 웹 응용 프로그램에 반환됩니다.

## <a name="register-the-services-in-azure-ad"></a>Azure AD에서 서비스 등록
Azure AD(Azure Active Directory)에서 호출 서비스와 수신 서비스를 등록합니다. 자세한 지침은 [Azure Active Directory와 응용 프로그램 통합](active-directory-integrating-applications.md)을 참조하세요.

## <a name="request-an-access-token"></a>액세스 토큰 요청
액세스 토큰을 요청하려면 테넌트별 Azure AD 끝점에 HTTP POST를 사용합니다.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>서비스 간 액세스 토큰 요청
서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| response_type |필수 |요청된 응답 유형을 지정합니다. 클라이언트 자격 증명 부여 흐름에서 값은 **client_credentials**이어야 합니다. |
| client_id |필수 |호출 웹 서비스의 Azure AD 클라이언트 ID를 지정합니다. Azure 관리 포털에서 호출 응용 프로그램의 클라이언트 ID를 찾으려면, **Active Directory**을 클릭하고, 디렉터리를 클릭하고, 응용 프로그램을 클릭한 후 **구성**을 클릭합니다. |
| client_secret |필수 |Azure AD에서 호출 웹 서비스에 등록된 키를 입력합니다. Azure 관리 포털에서 키를 생성하려면, **Active Directory**을 클릭하고, 디렉터리를 클릭하고, 응용 프로그램을 클릭한 후 **구성**을 클릭합니다. |
| resource |필수 |수신 웹 서비스의 앱 ID URI를 입력합니다. Azure 관리 포털에서 앱 ID URI을 찾으려면, **Active Directory**을 클릭하고, 디렉터리를 클릭하고, 응용 프로그램을 클릭한 후 **구성**을 클릭합니다. |

## <a name="example"></a>예
다음 HTTP POST는 https://service.contoso.com/ 웹 서비스용 액세스 토큰을 요청합니다. `client_id` 은(는) 액세스 토큰을 요청하는 웹 서비스를 식별합니다.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>서비스 간 액세스 토큰 응답
성공 응답에는 다음 매개 변수가 있는 JSON OAuth 2.0 응답이 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| access_token |요청된 액세스 토큰입니다. 호출 웹 서비스는 이 토큰을 사용하여 수신 웹 서비스에 인증할 수 있습니다. |
| access_type |토큰 유형 값을 나타냅니다. Azure AD는 **전달자**유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| expires_on |액세스 토큰이 만료되는 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. 이 값은 캐시된 토큰의 수명을 결정하는 데 사용됩니다. |
| resource |수신 웹 서비스의 앱 ID URI입니다. |

## <a name="example"></a>예
다음 예제는 웹 서비스에 액세스 토큰 요청에 대한 성공 응답을 보여줍니다.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>참고 항목
* [Azure AD의 OAuth 2.0](active-directory-protocols-oauth-code.md)


<!--HONumber=Feb17_HO2-->


