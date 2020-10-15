---
title: Azure의 Postman FHIR 서버 - Azure API for FHIR
description: 이 자습서에서는 Postman을 사용하여 FHIR 서버에 액세스하는 데 필요한 단계를 안내합니다. Postman은 API에 액세스하는 애플리케이션을 디버깅하는 데 유용합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/07/2019
ms.openlocfilehash: f8b5e344fc963d466571e75ff16f17367dc32971
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844850"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Postman을 사용하여 Azure API for FHIR 액세스

클라이언트 애플리케이션은 [REST API](https://www.hl7.org/fhir/http.html)를 통해 FHIR API에 액세스합니다. 예를 들어 디버깅 목적으로 애플리케이션을 빌드할 때 FHIR 서버와 직접 상호 작용할 수도 있습니다. 이 자습서에서는 [Postman](https://www.getpostman.com/)을 사용하여 FHIR 서버에 액세스하는 데 필요한 단계를 안내합니다. Postman은 API에 액세스하는 애플리케이션을 빌드할 때 디버깅에 자주 사용되는 도구입니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure의 FHIR 엔드포인트. 관리되는 Azure API for FHIR 또는 오픈 소스 FHIR server for Azure를 사용하여 설정할 수 있습니다. [Azure Portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md) 또는 [Azure CLI](fhir-paas-cli-quickstart.md)를 사용하여 관리되는 Azure API for FHIR을 설정합니다.
- FHIR 서비스에 액세스하는 데 사용할 [클라이언트 애플리케이션](register-confidential-azure-ad-client-app.md)
- Postman 설치. [https://www.getpostman.com](https://www.getpostman.com)에서 가져올 수 있습니다.

## <a name="fhir-server-and-authentication-details"></a>FHIR 서버 및 인증 세부 정보

Postman을 사용하기 위해 다음 정보가 필요합니다.

- 사용자의 FHIR 서버 URL(예: `https://MYACCOUNT.azurehealthcareapis.com`)
- FHIR 서버에 대한 ID 공급자 `Authority`(예: `https://login.microsoftonline.com/{TENANT-ID}`)
- 구성된 `audience`. 이는 일반적으로 FHIR 서버의 URL입니다(예: `https://MYACCOUNT.azurehealthcareapis.com` 또는 `https://azurehealthcareapis.com`).
- FHIR 서비스에 액세스하는 데 사용할 [클라이언트 애플리케이션](register-confidential-azure-ad-client-app.md)의 `client_id`(또는 애플리케이션 ID).
- 클라이언트 애플리케이션의 `client_secret`(또는 애플리케이션 비밀).

마지막으로 `https://www.getpostman.com/oauth2/callback`이 클라이언트 애플리케이션에 대해 등록된 회신 URL인지 확인해야 합니다.

## <a name="connect-to-fhir-server"></a>FHIR 서버에 연결

Postman을 사용하여 `https://fhir-server-url/metadata`에 대한 `GET` 요청을 수행합니다.

![Postman 메타데이터 기능 문](media/tutorial-postman/postman-metadata.png)

Azure API for FHIR에 대한 메타데이터 URL은 `https://MYACCOUNT.azurehealthcareapis.com/metadata`입니다. 이 예제에서 FHIR 서버 URL은 `https://fhirdocsmsft.azurewebsites.net`이며, 서버의 기능 문은 `https://fhirdocsmsft.azurewebsites.net/metadata`에서 사용할 수 있습니다. 인증 없이 해당 엔드포인트에 액세스할 수 있어야 합니다.

제한된 리소스에 액세스하려고 하면 다음과 같이 “Authentication failed(인증 실패)” 응답을 받게 됩니다.

![인증 실패](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>액세스 토큰 가져오기

유효한 액세스 토큰을 얻으려면 “권한 부여”를 선택하고 “OAuth 2.0” 유형을 선택합니다.

![OAuth 2.0 설정](media/tutorial-postman/postman-select-oauth2.png)

“새 액세스 토큰 가져오기”를 누르면 다음과 같은 대화 상자가 표시됩니다.

![새 액세스 토큰 요청](media/tutorial-postman/postman-request-token.png)

다음과 같은 세부 정보가 필요합니다.

| 필드                 | 예제 값                                                                                                   | 주석                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| 토큰 이름            | MYTOKEN                                                                                                         | 선택한 이름          |
| 권한 부여 유형            | 인증 코드                                                                                              |                            |
| 콜백 URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| 인증 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience`는 Azure API for FHIR에 대한 `https://MYACCOUNT.azurehealthcareapis.com`입니다. |
| 액세스 토큰 URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| 클라이언트 ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | 애플리케이션 UI             |
| 클라이언트 암호         | `XXXXXXXX`                                                                                                        | 비밀 클라이언트 키          |
| 범위 | `<Leave Blank>` |
| 시스템 상태                 | `1234`                                                                                                            |                            |
| 클라이언트 인증 | 본문에서 클라이언트 자격 증명 보내기                                                                                 |                 

“토큰 요청”을 누르면 Azure Active Directory 인증 흐름이 안내되며 토큰이 Postman으로 반환됩니다. 문제가 발생하는 경우 Postman 콘솔(“보기->Postman 콘솔 표시” 메뉴 항목에서)을 엽니다.

반환된 토큰 화면에서 아래로 스크롤하여 “토큰 사용”을 누릅니다.

![토큰 사용](media/tutorial-postman/postman-use-token.png)

이제 토큰이 “액세스 토큰” 필드에 채워지고 “사용 가능한 토큰”에서 토큰을 선택할 수 있습니다. `Patient` 리소스 검색을 반복하기 위해 다시 “전송”하는 경우 상태 `200 OK`를 가져와야 합니다.

![200 정상](media/tutorial-postman/postman-200-OK.png)

이 경우 데이터베이스에 환자가 없고 검색이 비어 있습니다.

[https://jwt.ms](https://jwt.ms)와 같은 도구를 사용하여 액세스 토큰을 검사하면 다음과 같은 콘텐츠가 표시됩니다.

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

문제 해결 시 올바른 대상(`aud` 클레임)이 있는지 확인하는 것이 좋습니다. 토큰에 올바른 발급자(`iss` 클레임)가 있고 올바른 대상(`aud` 클레임)이 있지만 FHIR API에 액세스할 수 없는 경우에는 사용자 또는 서비스 주체(`oid` 클레임)가 FHIR 데이터 평면에 액세스할 수 없는 것일 수 있습니다. [Azure RBAC(Azure 역할 기반 액세스 제어)를 사용](configure-azure-rbac.md)하여 사용자에게 데이터 평면 역할을 할당하는 것이 좋습니다. 데이터 평면에 대한 외부의 보조 Azure Active Directory 테넌트를 사용하는 경우 [로컬 RBAC 할당을 구성](configure-local-rbac.md)해야 합니다.

또한 [Azure CLI를 사용하여 Azure API for FHIR에 대한 토큰을 가져올](get-healthcare-apis-access-token-cli.md) 수 있습니다. Azure CLI에서 얻은 토큰을 사용하는 경우 권한 부여 유형 “전달자 토큰”을 사용하여 토큰을 직접 붙여넣어야 합니다.

## <a name="inserting-a-patient"></a>환자 삽입

이제 유효한 액세스 토큰이 있습니다. 새 환자를 삽입할 수 있습니다. 메서드 “POST”로 전환하고 요청 본문에 다음 JSON 문서를 추가합니다.

[!code-json[](samples/sample-patient.json)]

“보내기”를 누르면 환자가 성공적으로 생성된 것을 알 수 있습니다.

![환자 생성됨](media/tutorial-postman/postman-patient-created.png)

환자 검색을 반복하면 이제 환자 레코드가 표시됩니다.

![환자 생성됨](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Postman을 사용하여 FHIR API에 액세스했습니다. 지원되는 기능 섹션에서 지원되는 API 기능을 읽어 보세요.
 
>[!div class="nextstepaction"]
>[지원되는 기능](fhir-features-supported.md)
