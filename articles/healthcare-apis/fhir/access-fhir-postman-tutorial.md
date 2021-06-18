---
title: Azure의 Postman FHIR 서버 - Azure API for FHIR
description: 이 자습서에서는 Postman을 사용하여 FHIR 서버에 액세스하는 데 필요한 단계를 안내합니다. Postman은 API에 액세스하는 애플리케이션을 디버깅하는 데 유용합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 72e5711ca813378e291d48bdaaa5803693d91482
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284026"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Postman을 사용하여 Azure API for FHIR 액세스

클라이언트 애플리케이션은 [REST API](https://www.hl7.org/fhir/http.html)를 통해 Azure API for FHIR에 액세스할 수 있습니다. 요청을 보내고, 응답을 확인하고, 애플리케이션을 빌드 중에 디버그하려면 선택한 API 테스트 도구를 사용합니다. 이 자습서에서는 [Postman](https://www.getpostman.com/)을 사용하여 FHIR 서버에 액세스하는 단계를 안내합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure의 FHIR 엔드포인트. 

  Azure API for FHIR(관리되는 서비스)를 배포하려면 [Azure Portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)또는 [Azure CLI](fhir-paas-cli-quickstart.md)사용할 수 있습니다.

- FHIR 서비스에 액세스하기 위한 등록된 [기밀 클라이언트 애플리케이션입니다.](register-confidential-azure-ad-client-app.md)
- 기밀 클라이언트 애플리케이션(예: "FHIR 데이터 기여자")에 FHIR 서비스에 액세스할 수 있는 권한을 부여했습니다. 자세한 내용은 [Azure RBAC for FHIR 구성을](./configure-azure-rbac.md)참조하세요.
- Postman 설치. 
    
  Postman에 대한 자세한 내용은 [Postman 시작을 참조하세요.](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>FHIR 서버 및 인증 세부 정보

Postman을 사용하려면 다음 인증 매개 변수가 필요합니다.

- FHIR 서버 URL(예: ) `https://MYACCOUNT.azurehealthcareapis.com`

- FHIR 서버에 대한 ID 공급자 `Authority`(예: `https://login.microsoftonline.com/{TENANT-ID}`)

- `audience`일반적으로 FHIR 서버의 URL인 구성된 입니다(예: `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` 또는 `https://azurehealthcareapis.com` ).

- `client_id`FHIR 서비스에 액세스하는 데 사용되는 [기밀 클라이언트 애플리케이션의](register-confidential-azure-ad-client-app.md) 또는 애플리케이션 ID입니다.

- `client_secret`기밀 클라이언트 애플리케이션의 또는 애플리케이션 비밀입니다.

마지막으로 `https://www.getpostman.com/oauth2/callback`이 클라이언트 애플리케이션에 대해 등록된 회신 URL인지 확인해야 합니다.

## <a name="connect-to-fhir-server"></a>FHIR 서버에 연결

Postman을 열고 **GET을** 선택하여 에 대한 요청을 `https://fhir-server-url/metadata` 만듭니다.

![Postman 메타데이터 기능 문](media/tutorial-postman/postman-metadata.png)

Azure API for FHIR에 대한 메타데이터 URL은 `https://MYACCOUNT.azurehealthcareapis.com/metadata`입니다. 

이 예제에서 FHIR 서버 URL은 `https://fhirdocsmsft.azurewebsites.net` 이고 서버의 capability 문은 에서 사용할 수 `https://fhirdocsmsft.azurewebsites.net/metadata` 있습니다. 이 엔드포인트는 인증 없이 액세스할 수 있습니다.

제한된 리소스에 액세스하려고 하면 "인증 실패" 응답이 발생합니다.

![인증 실패](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>액세스 토큰 가져오기
**Get New Access Token**(새 액세스 토큰 가져오기)을 선택합니다.

유효한 액세스 토큰을 가져오려면 **권한 부여를** 선택하고 **유형** 드롭다운 메뉴에서 **OAuth 2.0을** 선택합니다.

![OAuth 2.0 설정](media/tutorial-postman/postman-select-oauth2.png)

**Get New Access Token**(새 액세스 토큰 가져오기)을 선택합니다.

![새 액세스 토큰 요청](media/tutorial-postman/postman-request-token.png)

새 **액세스 토큰 얻기** 대화 상자에서 다음 세부 정보를 입력합니다.

| 필드                 | 예제 값                                                                                                   | 주석                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| 토큰 이름            | MYTOKEN                                                                                                         | 선택한 이름          |
| 권한 부여 유형            | 인증 코드                                                                                              |                            |
| 콜백 URL          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| 인증 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience`는 Azure API for FHIR에 대한 `https://MYACCOUNT.azurehealthcareapis.com`입니다. |
| 액세스 토큰 URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| 클라이언트 ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | 애플리케이션 UI             |
| 클라이언트 암호         | `XXXXXXXX`                                                                                                      | 비밀 클라이언트 키          |
| 범위 | `<Leave Blank>` | 범위는 사용되지 않습니다. 따라서 비워 두면 됩니다.  
| 시스템 상태                 | `1234`     | [상태는](https://learning.postman.com/docs/sending-requests/authorization/) 사이트 간 요청 위조를 방지하기 위한 불투명 값입니다. 선택 사항이며 '1234'와 같은 임의의 값을 사용할 수 있습니다.                           |
| 클라이언트 인증 | 본문에서 클라이언트 자격 증명 보내기                                                                                 |                 

Azure Active Directory 인증 흐름을 통해 안내할 **토큰 요청을** 선택하면 토큰이 Postman에 반환됩니다. 인증 실패가 발생하는 경우 자세한 내용은 Postman 콘솔을 참조하세요. **참고:** 리본에서 **보기** 를 선택한 다음 **Postman 콘솔 표시를** 선택합니다. Postman 콘솔의 바로 가기 키는 **Alt-Ctrl+C** 입니다.

Scroll down 반환된 토큰 화면을 본 다음, **토큰 사용을** 선택합니다.

![토큰 사용](media/tutorial-postman/postman-use-token.png)

새로 채워진 **토큰을** 보려면 액세스 토큰 필드를 참조합니다. **보내기를** 선택하여 리소스 검색을 반복하면 `Patient` **상태가** `200 OK` 반환됩니다. 반환된 상태는 `200 OK` 성공적인 HTTP 요청을 나타냅니다.

![200 정상](media/tutorial-postman/postman-200-OK.png)

환자 *검색* 예제에서는 데이터베이스에 검색 결과가 비어 있는 환자가 없습니다.

[jwt.ms](https://jwt.ms)같은 도구를 사용하여 액세스 토큰을 검사할 수 있습니다. 콘텐츠의 예는 다음과 같습니다.

```json
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

문제 해결 시 올바른 대상(`aud` 클레임)이 있는지 확인하는 것이 좋습니다. 토큰이 올바른 발급자( `iss` 클레임)의 토큰이고 올바른 대상 그룹( 클레임)을 가지고 `aud` 있지만 여전히 FHIR API에 액세스할 수 없는 경우 사용자 또는 서비스 주체( `oid` 클레임)가 FHIR 데이터 평면에 액세스할 수 없을 수 있습니다. [Azure RBAC(Azure 역할 기반 액세스 제어)를](configure-azure-rbac.md) 사용하여 사용자에게 데이터 평면 역할을 할당하는 것이 좋습니다. 데이터 평면에 외부 보조 Azure Active Directory 테넌트를 사용하는 경우 FHIR 할당에 [대한 로컬 RBAC를 구성해야](configure-local-rbac.md) 합니다.

Azure CLI [를 사용하여 Azure API for FHIR 토큰을](get-healthcare-apis-access-token-cli.md)얻을 수도 있습니다. Azure CLI 가져온 토큰을 사용하는 경우 권한 부여 유형 *Bearer 토큰 을* 사용해야 합니다. 토큰을 직접 붙여넣습니다.

## <a name="inserting-a-patient"></a>환자 삽입

유효한 액세스 토큰을 사용하면 이제 새 환자를 삽입할 수 있습니다. Postman에서 **게시를** 선택하여 메서드를 변경한 다음 요청 본문에 다음 JSON 문서를 추가합니다.

[!code-json[](../samples/sample-patient.json)]

**보내기를** 선택하여 환자가 성공적으로 만들어졌는지 확인합니다.

![환자가 성공적으로 생성되었음을 보여주는 스크린샷.](media/tutorial-postman/postman-patient-created.png)

환자 검색을 반복하면 이제 환자 레코드가 표시됩니다.

![환자 생성됨](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Postman을 사용하여 Azure API for FHIR 액세스했습니다. Azure API for FHIR 기능에 대한 자세한 내용은 다음을 참조하세요.
 
>[!div class="nextstepaction"]
>[지원되는 기능](fhir-features-supported.md)
