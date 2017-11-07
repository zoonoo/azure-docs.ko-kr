---
title: "Web API에서 사용자 지정 커넥터 만들기 - Azure Logic Apps | Microsoft Docs"
description: "Web API에서 사용자 지정 커넥터 빌드"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Web API에서 사용자 지정 커넥터 만들기

Azure Logic Apps, Microsoft Flow 또는 Microsoft PowerApps에서 사용할 수 있는 사용자 지정 커넥터를 만들려면 먼저 Azure Web Apps로 호스트하고, Azure Active Directory로 인증하고, Logic Apps, Flow 또는 PowerApp에서 커넥터로 등록할 수 있는 Web API를 만듭니다. 이 자습서는 ASP.NET Web API 앱을 빌드하여 이러한 작업을 수행하는 방법을 보여줍니다. 커넥터의 트리거 및 작업에 대한 코드를 구조화할 수 있는 패턴을 보려면 [논리 앱 워크플로에서 호출할 수 있는 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Visual Studio 2013 이상](https://www.visualstudio.com/vs/). 이 자습서는 Visual Studio 2015를 사용합니다.

* Web API용 코드. 아무 것도 없는 경우 [ASP.NET Web API 2 시작(C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) 자습서를 참조하세요.

* Azure 구독. 구독이 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/)으로 시작할 수 있습니다. 아니면 [종량제 구독](https://azure.microsoft.com/pricing/purchase-options/)에 등록하세요.

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Azure에 ASP.NET 웹앱 만들기 및 배포

이 자습서에서는 Visual C# ASP.NET 웹 응용 프로그램을 만듭니다. 

1. Visual Studio를 연 다음 **파일** > **새 프로젝트**를 선택합니다.

   1. **설치됨**을 확장하고 **템플릿** > **Visual C#** > **웹**으로 이동하여 **ASP.NET 웹 응용 프로그램**을 선택합니다.

   2. 앱에 대한 프로젝트 이름, 위치 및 솔루션 이름을 입력한 다음 **확인**을 선택합니다.

   예:

   ![Visual C# ASP.NET 웹 응용 프로그램 만들기](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. **새 ASP.NET 웹 응용 프로그램** 상자에서 **Web API** 템플릿을 선택합니다. 아직 선택하지 않은 경우 **클라우드의 호스트**를 선택합니다. **인증 변경**을 선택합니다.

   !["Web API" 템플릿, "클라우드의 호스트", "인증 변경"을 선택합니다.](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. **인증 없음**을 선택하고 **확인**을 선택합니다. 인증은 나중에 설정할 수 있습니다.

   !["인증 없음" 선택](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. **새 ASP.NET 웹 응용 프로그램** 상자가 나타나면 **확인**을 선택합니다. 

5. **App Service 만들기** 상자에서 테이블에 설명된 호스팅 설정을 검토하고 원하는 대로 변경한 다음 **만들기**를 선택합니다. 

   [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)은 Azure 구독에서 앱을 호스팅하는 데 사용하는 물리적 리소스 컬렉션을 나타냅니다. [App Service](../app-service/app-service-value-prop-what-is.md)에 대해 알아보세요.

   ![App Service 만들기](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | Azure 회사 또는 학교 계정 또는 개인 Microsoft 계정 | *your-user-account* | 사용자 계정을 선택합니다. | 
   | **웹앱 이름** | *custom-web-api-app-name* 또는 기본 이름 | 앱의 URL에 사용되는 Web API 앱의 이름(예: http://*web-api-app-name*)을 입력합니다. | 
   | **구독** | *Azure-subscription-name* | 사용할 Azure 구독을 선택합니다. | 
   | **리소스 그룹** | *Azure-resource-group-name* | 기존 Azure 리소스 그룹을 선택하거나 리소스 그룹이 아직 없으면 만듭니다. <p>**참고**: Azure 리소스 그룹은 Azure 구독에서 Azure 리소스를 구성합니다. | 
   | **App Service 계획** | *App-Service-plan-name* | 기존 App Service 계획을 선택하거나 아직 계획이 없으면 계획을 만듭니다. | 
   |||| 

   App Service 계획을 만드는 경우 다음 설정을 지정합니다.

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **위치**: | *deployment-region* | 앱을 배포할 지역을 선택합니다. | 
   | **크기** | *App-Service-plan-size* | 서비스 계획의 비용 및 컴퓨팅 리소스 용량을 결정하는 계획 크기를 선택합니다. | 
   |||| 

   앱에 필요한 다른 리소스를 설정하려면 **추가 Azure 서비스 탐색**을 선택합니다.

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **리소스 종류** | *Azure-resource-type* | 앱에 필요한 추가 리소스를 선택하고 설정합니다. | 
   |||| 

6. Visual Studio에서 프로젝트를 배포한 후 앱의 코드를 빌드합니다.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Web API를 설명하는 OpenAPI(Swagger) 파일 만들기

Web API 앱을 Logic Apps에 연결하려면 API 작업을 설명하는 [OpenAPI(이전의 Swagger) 파일](http://swagger.io/)이 필요합니다. [Swagger 온라인 편집기](http://editor.swagger.io/)를 사용하여 API에 대한 OpenAPI 정의를 직접 작성할 수 있지만 이 자습서에서는[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md)이라는 오픈 소스 도구를 사용합니다.

1. 아직 설치하지 않았다면 Visual Studio 프로젝트에 Swashbuckle Nuget 패키지를 설치하십시오.

   1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > 
   **패키지 관리자 콘솔**을 클릭합니다.

   2. **패키지 관리자 콘솔**에서 앱의 프로젝트 디렉터리가 아니면 이 디렉터리로 이동하여(`Set-Location "project-path"` 실행) 다음 PowerShell commandlet을 실행합니다. 
   
      `Install-Package Swashbuckle`

      예:

      ![패키지 관리자 콘솔, Swashbuckle 설치](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Swashbuckle을 설치한 후 앱을 실행하면 Swashbuckle이 다음 URL에 OpenAPI 파일을 생성합니다. 
   >
   > http://*{your-web-api-app-root-URL}*/swagger/docs/v1
   > 
   > Swashbuckle은 다음 URL에 사용자 인터페이스도 생성합니다. 
   > 
   > http://*{your-web-api-app-root-URL}*/swagger

3. 준비가 되면 Web API 앱을 Azure에 게시합니다. Visual Studio에서 게시하려면 솔루션 탐색기에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하여 **게시...**를 선택하고 지시를 따릅니다.

   > [!IMPORTANT]
   > 작업 ID가 중복되면 OpenAPI 문서가 유효하지 않게 됩니다. 샘플 C# 템플릿을 사용한 경우 템플릿이 *다음 작업 ID를 두 번 반복합니다*. `Values_Get` 
   > 
   > 이 문제를 해결하려면 인스턴스 하나를 `Value_Get`으로 변경하고 다시 게시합니다.

4. 다음 위치로 이동하여 OpenAPI 문서를 확보합니다. 

   http://*{your-web-api-app-root-URL}*/swagger/docs/v1

   이 자습서에서 [샘플 OpenAPI 문서](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json)를 다운로드 할 수도 있습니다. 
   문서를 사용하기 전에 "//"로 시작하는 주석을 제거해야 합니다.

5. 콘텐츠를 JSON 파일로 저장합니다. 브라우저에 따라 텍스트를 복사하여 빈 텍스트 파일에 붙여 넣어야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 커넥터에 대한 인증 설정](../logic-apps/custom-connector-azure-active-directory-authentication.md)











