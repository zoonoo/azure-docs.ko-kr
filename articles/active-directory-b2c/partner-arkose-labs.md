---
title: Arkose Labs를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 위험한 사용자 및 사기성 사용자를 식별 하도록 Arkose Labs를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46f117b13909c2d9624b88e9f5d9a62c4c646e51
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500295"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C을 사용 하 여 Arkose Labs 구성

이 샘플 자습서에서는 AD (Azure Active Directory) B2C 인증과 [Arkose Labs](https://www.arkoselabs.com/)를 통합 하는 방법에 대해 알아봅니다. Arkose Labs는 봇 공격, 계정 인수 공격 및 사기성 계정 입구에 대해 조직을 지원 합니다.  

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 다음이 필요 합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](tutorial-create-tenant.md) 입니다.

- [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) 계정.

## <a name="scenario-description"></a>시나리오 설명

Arkose Labs 통합에는 다음 구성 요소가 포함 됩니다.

- **Arkose Labs** -봇 및 기타 자동화 된 남용 으로부터 보호 하기 위한 사기 행위 및 남용 서비스입니다.

- **Azure AD B2C 등록 사용자 흐름** -Arkose Labs 서비스를 사용 하는 등록 환경입니다. 에서는 사용자 지정 HTML 및 JavaScript를 사용 하 고 API 커넥터를 사용 하 여 Arkose Labs 서비스와 통합 합니다.

- **Azure 함수** -api 커넥터 기능에서 작동 하는 api 끝점입니다. 이 API는 Arkose Labs 세션 토큰의 서버 쪽 유효성 검사를 수행 하는 작업을 담당 합니다.

다음 다이어그램에서는 Arkose Labs를 Azure AD B2C와 통합 하는 방법을 설명 합니다.

![Arkose Labs 아키텍처 다이어그램을 보여 주는 이미지](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| 단계  | 설명 |
|---|---|
|1     | 사용자가 등록 하 고 계정을 만듭니다. 사용자가 제출을 선택 하면 Arkose Labs 적용 챌린지가 표시 됩니다.         |
|2     |  사용자가 챌린지를 완료 한 후 Azure AD B2C는 상태를 Arkose Labs로 보내 토큰을 생성 합니다. |
|3     |  Arkose Labs는 토큰을 생성 하 고 Azure AD B2C 다시 보냅니다.   |
|4     |  Azure AD B2C는 중간 웹 API를 호출 하 여 등록 양식을 전달 합니다.      |
|5     |  중간 웹 API는 토큰 확인을 위해 Arkose Lab으로 등록 양식을 보냅니다.    |
|6     | Arkose Lab은 테스트를 처리 하 고 중간 웹 API로 확인 결과를 다시 보냅니다.|
|7     | 중간 웹 API는 챌린지의 성공 또는 실패 결과를 Azure AD B2C 보냅니다. |
|8     | 챌린지가 성공적으로 완료 되 면 등록 양식이 Azure AD B2C 전송 되 고 Azure AD B2C 인증을 완료 합니다.|

## <a name="onboard-with-arkose-labs"></a>Arkose Labs를 사용 하 여 등록

1. [Arkose](https://www.arkoselabs.com/book-a-demo/) 에 문의 하 여 계정을 만드세요.

2. 계정이 만들어지면로 이동 합니다. https://dashboard.arkoselabs.com/login  

3. 대시보드 내에서 사이트 설정으로 이동 하 여 공개 키와 개인 키를 찾습니다. 이 정보는 나중에 Azure AD B2C를 구성 하는 데 필요 합니다. 공용 및 개인 키의 값은 `ARKOSE_PUBLIC_KEY` `ARKOSE_PRIVATE_KEY` [샘플 코드](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)에서 및로 참조 됩니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>1 부-ArkoseSessionToken 사용자 지정 특성 만들기

사용자 지정 특성을 만들려면 다음 단계를 수행 합니다.  

1. **Azure Portal** 로 이동  >  **Azure AD B2C**

2. **사용자 특성** 선택

3. **추가** 를 선택합니다.

4. 특성 이름으로 **ArkoseSessionToken** 을 입력 합니다.

5. **만들기** 를 선택합니다.

[사용자 지정 특성](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-custom-attributes?pivots=b2c-user-flow)에 대해 자세히 알아보세요.

### <a name="part-2---create-a-user-flow"></a>2 부-사용자 흐름 만들기

사용자 흐름은 **등록** 및 **로그인** 또는 **등록** 에 사용할 수 있습니다. 등록 하는 동안에만 Arkose Labs 사용자 흐름이 표시 됩니다.

1. 사용자 흐름을 만드는 방법에 대 한 [지침](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) 을 참조 하세요. 기존 사용자 흐름을 사용 하는 경우 **권장 되는 (차세대 미리 보기)** 버전 유형 이어야 합니다.

2. 사용자 흐름 설정에서 **사용자 특성** 으로 이동 하 여 **ArkoseSessionToken** 클레임을 선택 합니다.

![이미지 사용자 지정 특성을 선택 하는 방법을 보여 줍니다.](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>3 부-사용자 지정 HTML, JavaScript 및 페이지 레이아웃 구성

제공 된 [HTML 스크립트로](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)이동 합니다. 파일에는 다음 세 가지 작업을 수행 하는 JavaScript 태그를 포함 하는 HTML 템플릿이 포함 되어 있습니다 `<script>` .

1. Arkose Labs 위젯을 렌더링 하 고 클라이언트 쪽 Arkose Labs 유효성 검사를 수행 하는 Arkose Labs 스크립트를 로드 합니다.

2. `extension_ArkoseSessionToken` `ArkoseSessionToken` 사용자에 게 표시 되는 UI에서 사용자 지정 특성에 해당 하는 입력 요소 및 레이블을 숨깁니다.

3. 사용자가 Arkose Labs 챌린지를 완료 하면 Arkose Labs에서 사용자의 응답을 확인 하 고 토큰을 생성 합니다. `arkoseCallback`사용자 지정 JavaScript의 콜백은 `extension_ArkoseSessionToken` 생성 된 토큰 값으로의 값을 설정 합니다. 이 값은 다음 섹션에 설명 된 대로 API 끝점에 전송 됩니다.

    Arkose Labs 클라이언트 쪽 유효성 검사에 대 한 자세한 내용은 [이 문서](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) 를 참조 하세요.

사용자 흐름에 사용자 지정 HTML 및 JavaScript를 사용 하려면 앞에서 설명한 단계를 따르세요.

1. [](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) `<ARKOSE_PUBLIC_KEY>` 클라이언트 쪽 유효성 검사를 위해 생성 된 값과 일치 하 고 계정에 대 한 Arkose Labs 스크립트를 로드 하는 데 사용 되는selfAsserted.html 파일을 수정 합니다.

2. CORS (크로스-원본 자원 공유) 사용 웹 끝점에서 HTML 페이지를 호스팅합니다. [Azure blob storage 계정을 만들고](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) CORS를 [구성](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)합니다.

  >[!NOTE]
  >사용자 고유의 사용자 지정 HTML이 있는 경우 요소를 복사 하 여 `<script>` html 페이지에 붙여 넣습니다.

3. 페이지 레이아웃을 구성 하려면 다음 단계를 따르세요.

   a. Azure Portal에서로 이동 **Azure AD B2C**

   b. **사용자 흐름** 으로 이동 하 여 사용자 흐름을 선택 합니다.

   다. **페이지 레이아웃** 선택

   d. **로컬 계정 등록 페이지 레이아웃** 선택

   e. **사용자 지정 페이지 콘텐츠 사용** 을 **예** 로 설정 합니다.

   f. 사용자 지정 HTML이 **사용자 지정 페이지 콘텐츠를 사용** 하는 URI를 붙여넣습니다.

   g. 소셜 Id 공급자를 사용 하는 경우 **소셜 계정 등록 페이지** 레이아웃에 대해 e 및 **f** **단계** 를 반복 합니다.

   ![페이지 레이아웃을 보여 주는 이미지](media/partner-arkose-labs/page-layouts.png)

4. 사용자 흐름에서 **속성** 으로 이동 하 여 JavaScript 적용 페이지 레이아웃 **사용** (미리 보기)을 선택 합니다. 자세히 알아보려면이 [문서](https://docs.microsoft.com/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-user-flow) 를 참조 하세요.

### <a name="part-4---create-and-deploy-your-api"></a>4 부-API 만들기 및 배포

Visual Studio Code에 대 한 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 을 설치 합니다.

>[!Note]
>이 섹션에서 설명 하는 단계에서는 Azure Function을 배포 하는 Visual Studio Code을 사용 하 고 있다고 가정 합니다. Azure Portal, 터미널 또는 명령 프롬프트를 사용 하거나 다른 코드 편집기를 사용 하 여 배포할 수도 있습니다.

#### <a name="run-the-api-locally"></a>로컬로 API 실행

1. 왼쪽 탐색 모음에서 Visual Studio code의 Azure 확장으로 이동 합니다. 로컬 Azure 기능을 나타내는 **로컬 프로젝트** 폴더를 선택 합니다.

2. **F5** 키를 누르거나 **디버그**  >  **디버깅 시작** 메뉴를 사용 하 여 디버거를 시작 하 고 Azure Functions 호스트에 연결 합니다. 이 명령은 Azure Function에서 만든 단일 디버그 구성을 자동으로 사용 합니다.

3. Azure 함수 확장은 로컬 개발을 위해 몇 개의 파일을 자동으로 생성 하 고, 종속성을 설치 하 고, 함수 핵심 도구 (없는 경우)를 설치 합니다. 이러한 도구는 디버깅 환경에 도움이 됩니다.

4. 함수 코어 도구의 출력은 Visual Studio Code **터미널** 패널에 표시 됩니다. 호스트가 시작 되 면 출력에 표시 된 로컬 URL을 **Alt + 클릭** 하 여 브라우저를 열고 함수를 실행 합니다. Azure Functions 탐색기에서 함수를 마우스 오른쪽 단추로 클릭 하 여 로컬로 호스팅된 함수의 url을 확인 합니다.

테스트 하는 동안 로컬 인스턴스를 다시 배포 하려면 1-4 단계를 반복 합니다.

#### <a name="add-environment-variables"></a>환경 변수 추가

이 샘플은 [HTTP 기본 인증](https://tools.ietf.org/html/rfc7617)을 사용 하 여 web API 끝점을 보호 합니다.

사용자 이름 및 암호는 리포지토리의 일부가 아니라 환경 변수로 저장 됩니다. 자세한 내용은 파일 [ 의local.settings.js](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=macos%2Ccsharp%2Cbash#local-settings-file) 를 참조 하세요.

1. 루트 폴더의 파일에 local.settings.js를 만듭니다.

2. 아래 코드를 복사 하 여 파일에 붙여 넣습니다.

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
**BASIC_AUTH_USERNAME** 및 **BASIC_AUTH_PASSWORD** 값은 Azure FUNCTION에 대 한 API 호출을 인증 하는 데 사용 되는 자격 증명이 됩니다. 원하는 값을 선택 합니다.

는 `<ARKOSE_PRIVATE_KEY>` Arkose Labs 서비스에서 생성 한 서버 쪽 암호입니다. [Arkose Labs 서버 쪽 유효성 검사 API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) 를 호출 하 여 프런트 엔드에서 생성 된의 값에 대 한 유효성을 검사 하는 데 사용 `ArkoseSessionToken` 됩니다.

는 `<B2C_EXTENSIONS_APP_ID>` 디렉터리에 사용자 지정 특성을 저장 하는 Azure AD B2C에서 사용 하는 앱의 응용 프로그램 ID입니다. 앱 등록로 이동 하 여 b2c를 검색 하 고 **개요** 창에서 응용 프로그램 (클라이언트) id를 복사 하 여이 응용 프로그램 id를 찾을 수 있습니다. 문자를 제거 `-` 합니다.

![앱 id로 검색을 보여 주는 이미지](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>웹에 응용 프로그램 배포

1. [이](https://docs.microsoft.com/azure/javascript/tutorial-vscode-serverless-node-04) 가이드에 설명 된 단계에 따라 Azure 함수를 클라우드에 배포 합니다. Azure Function의 끝점 웹 URL을 복사 합니다.

2. 배포 되 면 **업로드 설정** 옵션을 선택 합니다. 사용자 환경 변수를 App service의 [응용 프로그램 설정](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#application-settings-in-azure) 에 업로드 합니다. 이러한 응용 프로그램 설정은 [Azure Portal를 통해](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) 구성 하거나 관리할 수도 있습니다.

Azure Functions에 대 한 Visual Studio Code 개발에 대해 자세히 알아보려면 [이 문서](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code?tabs=csharp#republish-project-files) 를 참조 하세요.

#### <a name="configure-and-enable-the-api-connector"></a>API 커넥터 구성 및 사용

[API 커넥터를 만들고](https://docs.microsoft.com/azure/active-directory-b2c/add-api-connector) 사용자 흐름에 사용 하도록 설정 합니다. API 커넥터 구성은 다음과 같아야 합니다.

![Api 커넥터를 구성 하는 방법을 보여 주는 이미지](media/partner-arkose-labs/configure-api-connector.png)

- **끝점 url** -Azure function을 배포 하는 동안 이전에 복사한 함수 url입니다.

- **사용자 이름 및 암호** -이전에 환경 변수로 정의한 사용자 이름 및 암호입니다.

API 커넥터를 사용 하도록 설정 하려면 사용자 흐름에 대 한 **api 커넥터** 설정에서 사용자 단계를 **만들기 전에** 에서 호출할 api 커넥터를 선택 합니다. 그러면 사용자가 등록 흐름에서 **만들기** 를 선택할 때 API가 호출 됩니다. 이 API는 `ArkoseSessionToken` Arkose 위젯의 콜백에 의해 설정 된 값의 서버 쪽 유효성 검사를 수행 합니다 `arkoseCallback` .

![Api 커넥터 사용을 보여 주는 이미지](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 정책 아래에서 **사용자 흐름** 을 선택 합니다.

2. 이전에 만든 사용자 흐름을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   a. 응용 프로그램: 등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. 회신 URL: 리디렉션 URL을 선택 합니다.

   다. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 진행 하 고 계정을 만듭니다.

5. 로그아웃

6. 로그인 흐름을 진행 합니다.  

7. **계속** 을 선택한 후 Arkose Labs 퍼즐이 표시 됩니다.

## <a name="additional-resources"></a>추가 리소스

- Azure AD B2C 등록 사용자 흐름에 대 한 [샘플 코드](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
