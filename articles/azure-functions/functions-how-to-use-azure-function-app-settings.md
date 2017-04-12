---
title: "Azure Functions 앱 설정 구성 | Microsoft Docs"
description: "Azure 함수 앱 설정을 구성하는 방법에 알아봅니다."
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b7334f68e4526b716210b28388afcd0ee6e48940
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-configure-azure-function-app-settings"></a>Azure 함수 앱 설정을 구성하는 방법
## <a name="settings-overview"></a>설정 개요
포털의 왼쪽 아래 모서리에 있는 **함수 앱 설정** 링크를 클릭하여 Azure 함수 앱 설정을 관리할 수 있습니다. Azure 함수 앱 설정은 앱의 모든 함수에 적용됩니다.

1. [Azure Portal](http://portal.azure.com) 로 이동하여 Azure 계정으로 로그인합니다.
2. 포털의 왼쪽 아래 모서리에 있는 **함수 앱 설정**을 클릭합니다. 그러면 선택할 수 있는 몇 가지 구성 옵션이 나타납니다. 

![Azure 함수 앱 설정](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="develop"></a>개발
### <a name="app-service-editor"></a>앱 서비스 편집기
App Service 편집기는 Json 구성 파일과 코드 파일을 둘 다 수정하는 데 사용할 수 있는 포털 내 고급 편집기입니다. 이 옵션을 선택하면 기본 편집기와 함께 별도의 브라우저 탭이 실행됩니다. 이를 통해 GitHub와 통합하고 코드를 실행 및 디버깅하며 함수 앱 설정을 수정할 수 있습니다.

![앱 서비스 편집기](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="application-settings"></a>응용 프로그램 설정
환경 변수, 프레임 워크 버전, 원격 디버깅, 앱 설정, 연결 문자열, 기본 문서 등을 관리합니다. 이러한 설정은 함수 앱에 적용됩니다. 

앱 설정을 구성하려면 **앱 설정 구성** 링크를 클릭합니다. 

![앱 설정 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="dev-console"></a>Dev 콘솔
Azure Functions 포털 내 콘솔로 DOS 스타일 명령을 실행할 수 있습니다. 일반적인 명령에는 배치 파일 및 스크립트 실행과 함께 디렉터리 및 파일의 생성 및 탐색이 포함됩니다. 

> [!NOTE]
> 스크립트를 업로드할 수 있습니다. 단, Azure 함수의 **고급 설정**에서 FTP 클라이언트를 먼저 구성해야 합니다.
> 
> 

포털 내 콘솔을 열려면 **개발자 콘솔 열기**를 클릭합니다.

![함수 앱 메모리 크기 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> ASCII 기술을 사용하는 콘솔에서 작업하면 근사할 것입니다.
> 
> 

## <a name="deploy"></a>배포
### <a name="continuous-integration"></a>연속 통합
함수 앱을 GitHub, Visual Studio Team Services 등과 통합할 수 있습니다.

1. **연속 통합 구성** 링크를 클릭합니다. 그러면 **배포** 창이 옵션과 함께 열립니다.
2. **배포** 창에서 **설치**를 클릭하면 하나의 옵션과 함께 **배포 소스** 창이 나타납니다. **소스 선택**을 클릭하여 사용 가능한 소스를 표시합니다. 
3. 사용할 수 있는 배포 소스인 Visual Studio Team Services, OneDrive, 로컬 Git 리포지토리, GitHub, Bitbucket, DropBox 또는 외부 리포지토리 중 하나를 클릭하여 선택합니다. 
   
    ![함수 앱 CI 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. 다양한 배포 소스에서 메시지를 표시하는 대로 자격 증명 및 정보를 입력합니다. 필요한 자격 증명 및 정보는 선택한 소스에 따라 약간 달라질 수 있습니다. 

CI를 설치하면 구성된 소스로 푸시한 연결된 코드가 자동으로 이 함수 앱에 배포됩니다.

### <a name="kudu"></a>Kudu
Kudu를 사용하면 함수 앱의 고급 관리 기능에 액세스할 수 있습니다.

Kudu를 열려면 **Kudu로 이동**을 클릭합니다. 그러면 Kudu 웹 관리자의 완전히 새로운 브라우저 창이 열립니다.

> [!NOTE]
> 또는 다음과 같이 함수의 URL에 "scm"을 삽입하여 **Kudu**를 열 수 있습니다. ```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

Kudu 웹 페이지에서 시스템 정보, 앱 설정, 환경 변수, HTTP 헤더, 서버 변수 등을 확인하고 관리할 수 있습니다.

![Kudu 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="manage-app-service-settings"></a>관리: 앱 서비스 설정
함수 앱을 다른 앱 서비스 인스턴스처럼 관리하세요. 이 옵션으로 앞에서 설명한 모든 설정과 더불어 몇 가지 추가 항목에 액세스할 수 있습니다.  

고급 설정을 열려면 **고급 설정** 링크를 클릭합니다. 

![앱 서비스 설정 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

각 앱 서비스 설정을 구성하는 방법에 대한 자세한 내용은 [Azure App Service 설정 구성](../app-service-web/web-sites-configure.md)을 참조하세요.

## <a name="manage-cors"></a>관리: CORS
일반적으로 보안상의 이유로 브라우저에서 Ajax 호출 등의 외부 소스에서 호스트(도메인)에 대한 호출은 허용되지 않습니다. 그렇지 않으면 악성 코드가 백 엔드로 전송되어 실행될 수 있습니다. 따라서 가장 안전한 경로는 사용자의 고유한 신뢰할 수 있는 몇 가지를 제외한 모든 코드 소스를 블랙리스트에 올리는 것입니다. CORS(원본 간 리소스 공유)를 구성하여 Azure Functions에서 호출을 수락하는 소스를 구성할 수 있습니다. CORS를 사용하면 Azure 함수 앱에서 함수를 호출할 수 있는 JavaScript의 소스인 도메인 목록을 만들 수 있습니다. 

1. CORS를 구성하려면 **CORS 구성** 링크를 클릭합니다. 
2. 허용 목록에 원하는 도메인을 입력합니다.

![함수 앱 CORS 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="manage-authenticationauthorization"></a>관리: 인증/권한 부여
HTTP 트리거를 사용하는 함수의 경우 호출을 인증해야 할 수 있습니다.

1. 인증을 구성하려면 **인증 구성** 링크를 클릭합니다.
2. **앱 서비스 인증** 단추를 **켜기**로 설정/해제합니다.

![함수 앱 CI 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

대부분의 인증 공급자는 요청 API 키/클라이언트 ID 및 비밀을 요구합니다. 단, Microsoft 계정 및 Facebook 옵션 모두 범위(특정 권한 부여 자격 증명)를 정할 수 있습니다. Active Directory에는 다양한 기본 또는 고급 구성 설정을 설정할 수 있습니다.

특정 인증 공급자를 구성하는 방법에 대한 자세한 내용은 [Azure App Service 인증 개요](../app-service/app-service-authentication-overview.md)를 참조하세요.

## <a name="manage-api-definition"></a>관리: API 정의
클라이언트가 HTTP에서 트리거한 함수를 더 쉽게 사용할 수 있습니다.

1. API를 설정하려면 **API 메타데이터 구성**을 클릭합니다. 
2. Swagger json 파일을 가리키는 URL을 입력합니다.

![함수 앱 API 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

Swagger를 사용하는 API 정의를 만드는 방법에 대한 자세한 내용은 [Azure에서 API 앱, ASP.NET 및 Swagger 시작](../app-service-api/app-service-api-dotnet-get-started.md)을 방문하세요.

## <a name="daily-usage-quota"></a>일일 사용 할당량

Azure Functions를 사용하면 일일 사용 할당량을 설정하여 플랫폼 사용량을 예측 가능하게 제한할 수 있습니다. 일일 사용 할당량에 도달하면 함수 앱이 중지합니다. 사용 할당량에 도달하여 중지한 함수 앱은 일일 사용 할당량을 설정할 때와 같은 컨텍스트에서 다시 활성화할 수 있습니다. 사용 할당량 단위는 청구 단위: GB-s(기가바이트-초)입니다.청구 모델에 대한 자세한 내용은 [Azure Functions 가격 책정 페이지](http://azure.microsoft.com/pricing/details/functions/)를 참조하세요. 

![함수 앱 메모리 크기 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-quota.png)

## <a name="next-steps"></a>다음 단계
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


