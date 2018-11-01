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
ms.openlocfilehash: 414a52141aac05aac51bbb4fedf6ab67d98f2637
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092273"
---
# <a name="connect-and-authenticate-to-apis"></a>API에 연결 및 인증

Azure Digital Twins는 Azure AD(Azure Active Directory)를 사용하여 사용자를 인증하고 응용 프로그램을 보호합니다. Azure AD는 모두 업계 표준 프로토콜 OAuth 2.0 또는 OpenID Connect를 기반으로 하는 다양한 최신 아키텍처에 대한 인증을 지원합니다. 또한 Azure AD를 사용하면 개발자가 단일 테넌트 및 LOB(기간 업무) 애플리케이션을 모두 빌드할 뿐만 아니라 다중 테넌트 앱을 개발할 수 있습니다.

Azure AD 개요의 경우 단계별 가이드, 개념 및 빠른 시작은 [기본 페이지](https://docs.microsoft.com/azure/active-directory/fundamentals/index)를 방문하세요.

응용 프로그램 또는 서비스를 Azure AD와 통합하려면 개발자가 먼저 Azure AD에 응용 프로그램을 등록해야 합니다. 자세한 지침과 스크린샷은 [여기](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)에서 지침을 보세요.

다음은 Azure AD에서 지원되는 [다섯 가지 기본 응용 프로그램 시나리오](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)입니다.

* SPA(단일 페이지 응용 프로그램): 사용자가 Azure AD로 보호된 단일 페이지 응용 프로그램에 로그인해야 합니다.
* 웹 브라우저-웹 응용 프로그램: 사용자가 Azure AD로 보호된 웹 응용 프로그램에 로그인해야 합니다.
* 네이티브 응용 프로그램-웹 API: 전화, 태블릿 또는 PC에서 실행되는 네이티브 응용 프로그램이 사용자를 인증해야 Azure AD로 보호된 웹 API에서 리소스를 가져올 수 있습니다.
* 웹 응용 프로그램-웹 API: 웹 응용 프로그램이 Azure AD로 보호된 웹 API에서 리소스를 가져와야 합니다.
* 디먼 또는 서버 응용 프로그램-웹 API: 웹 사용자 인터페이스가 없는 서버 응용 프로그램 또는 디먼 응용 프로그램이 Azure AD로 보호된 웹 API에서 리소스를 가져와야 합니다.

Windows Azure 인증 라이브러리는 Active Directory 토큰을 획득하는 여러 가지 방법을 제공합니다. 코드 샘플뿐만 아니라 라이브러리에 대한 심층 분석은 [여기](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)에서 살펴보겠습니다.

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>중간 계층 웹 API에서 Digital Twins 호출

Digital Twins 솔루션을 설계할 때 개발자는 일반적으로 Digital Twins API 다운스트림을 호출하는 중간 계층 응용 프로그램 또는 API를 만들도록 선택합니다. 사용자는 먼저 중간 계층 응용 프로그램에 대해 인증한 다음, On-Behalf-Of 토큰 흐름은 다운스트림을 호출할 때 사용할 수 있습니다. On-Behalf-Of 흐름을 오케스트레이션하는 방법에 대한 자세한 지침은 [이 페이지](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)를 참조하세요. [여기](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)에서 코드 샘플을 볼 수도 있습니다.


## <a name="test-with-the-postman-client"></a>Postman 클라이언트를 사용하여 테스트

Digital Twins API를 사용하여 가동하고 실행하기 위해 API 환경으로 Postman과 같은 클라이언트를 사용할 수 있습니다. Postman을 통해 복잡한 HTTP 요청을 신속하게 만들 수 있습니다. 아래 지침에서는 Postman UI 내에서 Digital Twins를 호출하는 데 필요한 Azure AD 토큰을 가져오는 방법을 중점적으로 설명합니다.


1. https://www.getpostman.com/으로 이동하여 앱을 다운로드합니다.
1. [여기](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)의 단계에 따라 Azure Active Directory 응용 프로그램을 만듭니다.(또는 기존 등록을 다시 사용할 수 있습니다.) 
1. 필수 사용 권한 아래에서 "Azure Digital Twins"를 추가하고 위임된 권한을 선택합니다. 마무리하려면 사용 권한 부여를 클릭해야 합니다.
1. 응용 프로그램 매니페스트를 열고, oauth2AllowImplicitFlow를 true로 설정합니다.
1. [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback)에 대한 회신 URL을 구성합니다.
1. **권한 부여 탭**을 선택하고, **OAuth 2.0**을 클릭하고, **새 액세스 토큰 가져오기**를 선택합니다.

    |**필드**  |**값** |
    |---------|---------|
    | 권한 부여 유형 | 암시적 |
    | 콜백 URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 인증 URL | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | 클라이언트 ID | 1단계에서 생성되거나 다른 용도로 사용된 Azure AD 앱에 대해 응용 프로그램 ID를 사용합니다. |
    | 범위 | 비워 둡니다. |
    | 시스템 상태 | 비워 둡니다. |
    | 클라이언트 인증 | 기본 인증 헤더로 보내기 |

1. **토큰 요청**을 클릭합니다.

    >[!NOTE]
    >"OAuth 2를 완료할 수 없습니다."라는 오류 메시지가 나타나면 다음과 같이 시도하세요.
    > * Postman을 닫은 후 다시 열고, 다시 시도하세요.
   
1. 아래로 스크롤하여 **토큰 사용**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 보안에 대한 자세한 내용은 [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md)를 참고하세요.
