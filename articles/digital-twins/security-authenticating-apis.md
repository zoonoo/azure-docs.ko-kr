---
title: Azure Digital Twins API 인증 이해 | Microsoft Docs
description: Azure Digital Twins를 사용하여 API에 연결 및 인증
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016219"
---
# <a name="connect-and-authenticate-to-apis"></a>API에 연결 및 인증

Azure Digital Twins는 Azure AD(Azure Active Directory)를 사용하여 사용자를 인증하고 응용 프로그램을 보호합니다. Azure AD는 다양한 최신 아키텍처의 인증을 지원합니다. 모두 업계 표준 프로토콜인 OAuth 2.0 또는 OpenID Connect를 기반으로 합니다. 또한 개발자는 Azure AD를 사용하여 단일 테넌트 및 LOB(기간 업무) 애플리케이션을 빌드할 수 있습니다. Azure AD를 사용하여 다중 테넌트 애플리케이션을 개발할 수도 있습니다.

Azure AD 개요의 경우 단계별 가이드, 개념 및 빠른 시작은 [기본 페이지](https://docs.microsoft.com/azure/active-directory/fundamentals/index)를 방문하세요.

응용 프로그램 또는 서비스를 Azure AD와 통합하려면 개발자가 먼저 Azure AD에 응용 프로그램을 등록해야 합니다. 자세한 지침과 스크린샷은 [이 빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)을 참조하세요.

Azure AD에서 지원되는 [5가지 기본 애플리케이션 시나리오](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)는 다음과 같습니다.

* SPA(단일 페이지 애플리케이션): 사용자가 Azure AD로 보호된 단일 페이지 애플리케이션에 로그인해야 합니다.
* 웹 브라우저-웹 애플리케이션: 사용자가 Azure AD로 보호된 웹 애플리케이션에 로그인해야 합니다.
* 네이티브 애플리케이션-웹 API: 전화, 태블릿 또는 PC에서 실행되는 네이티브 애플리케이션이 사용자를 인증해야 Azure AD로 보호된 웹 API에서 리소스를 가져올 수 있습니다.
* 웹 응용 프로그램-웹 API: 웹 응용 프로그램이 Azure AD로 보호된 웹 API에서 리소스를 가져와야 합니다.
* 디먼 또는 서버 애플리케이션-웹 API: 웹 UI가 없는 서버 애플리케이션 또는 디먼 애플리케이션이 Azure AD로 보호된 웹 API에서 리소스를 가져와야 합니다.

Windows Azure 인증 라이브러리는 Active Directory 토큰을 획득하는 여러 가지 방법을 제공합니다. 라이브러리 및 코드 샘플에 대한 세부 사항은 [이 문서](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)를 참조하세요.

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>중간 계층 웹 API에서 Digital Twins 호출

개발자가 Digital Twins 솔루션을 설계할 때는 일반적으로 중간 계층 애플리케이션이나 API를 만듭니다. 그런 다음, 이 앱 또는 API가 Digital Twins API 다운스트림을 호출합니다. 사용자는 먼저 중간 계층 애플리케이션에 대해 인증한 다음, On-Behalf-Of 토큰 흐름은 다운스트림을 호출할 때 사용할 수 있습니다. On-Behalf-Of 흐름을 오케스트레이션하는 방법에 대한 지침은 [이 페이지](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)를 참조하세요. 또한 코드 샘플은 [이 페이지](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)에서 확인할 수 있습니다.


## <a name="test-with-the-postman-client"></a>Postman 클라이언트를 사용하여 테스트

Digital Twins API를 사용하여 가동하고 실행하기 위해 API 환경으로 Postman과 같은 클라이언트를 사용할 수 있습니다. Postman을 통해 복잡한 HTTP 요청을 신속하게 만들 수 있습니다. 다음 단계는 Postman UI 안에서 Digital Twins를 호출하는 데 필요한 Azure AD 토큰을 가져오는 방법을 보여 줍니다.


1. https://www.getpostman.com/으로 이동하여 앱을 다운로드합니다.
1. [이 빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)의 단계에 따라 Azure AD 애플리케이션을 만듭니다. 기존 등록을 다시 사용할 수 있습니다. 
1. **필수 사용 권한** 아래에서 "Azure Digital Twins"를 입력하고 **위임된 권한**을 선택합니다. 그런 다음, **사용 권한 부여**를 선택합니다.
1. 애플리케이션 매니페스트를 열고, **oauth2AllowImplicitFlow**를 true로 설정합니다.
1. [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback)에 대한 회신 URL을 구성합니다.
1. **권한 부여 탭**을 선택하고, **OAuth 2.0**을 선택한 다음, **새 액세스 토큰 가져오기**를 선택합니다.

    |**필드**  |**값** |
    |---------|---------|
    | 권한 부여 유형 | 암시적 |
    | 콜백 URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 인증 URL | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | 클라이언트 ID | 2단계에서 생성되거나 다른 용도로 사용된 Azure AD 앱에 대해 애플리케이션 ID를 사용합니다. |
    | 범위 | 비워 둡니다. |
    | 시스템 상태 | 비워 둡니다. |
    | 클라이언트 인증 | 기본 인증 헤더로 보냅니다. |

1. **토큰 요청**을 선택합니다.

    >[!NOTE]
    >"OAuth 2를 완료할 수 없습니다."라는 오류 메시지가 나타나면 다음과 같이 시도하세요.
    > * Postman을 닫은 후 다시 열고, 다시 시도합니다.
   
1. 아래로 스크롤하고 **토큰 사용**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 보안에 대한 자세한 내용은 [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md)를 참고하세요.
