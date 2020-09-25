---
title: 그룹 통화 주인공 샘플
titleSuffix: An Azure Communication Services sample overview
description: 개발자가 샘플의 내부 작업에 대해 자세히 알아보는 방법을 배울 수 있도록 Azure Communication Services를 사용하는 통화 주인공 샘플의 개요입니다.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: caee5686695594604f49dcbade54342a9134abc0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945461"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>그룹 통화 주인공 샘플 시작

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

Azure Communication Services **그룹 통화 주인공 샘플**은 Communication Services 통화 웹 클라이언트 라이브러리를 사용하여 그룹 통화 환경을 만드는 방법을 보여줍니다.

이 샘플 빠른 시작에서는 샘플이 어떻게 작동하는지 살펴본 후 로컬 머신에서 샘플을 실행합니다. 그런 다음, 사용자 고유의 Azure Communication Services 리소스를 사용하여 Azure에 샘플을 배포합니다.

> [!IMPORTANT]
> [GitHub에서 샘플 다운로드](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>개요

이 샘플에는 클라이언트 쪽 애플리케이션과 서버 쪽 애플리케이션이 모두 들어 있습니다. **클라이언트 쪽 애플리케이션**은 Microsoft의 Fluent UI 프레임워크를 사용하는 React/Redux 웹 애플리케이션입니다. 이 애플리케이션은 클라이언트 쪽 애플리케이션에서 Azure에 연결하는 데 도움이 되는 ASP.NET Core **서버 쪽 애플리케이션**으로 요청을 보냅니다. 

샘플은 다음과 같습니다.

:::image type="content" source="./media/calling/landing-page.png" alt-text="샘플 애플리케이션의 방문 페이지를 보여 주는 스크린샷":::

"통화 시작" 단추를 누르면 웹 애플리케이션이 서버 쪽 애플리케이션에서 사용자 액세스 토큰을 가져옵니다. 가져온 토큰은 클라이언트 앱을 Azure Communication Services에 연결하는 데 사용됩니다. 토큰을 가져오면 사용할 카메라와 마이크를 지정하라는 메시지가 표시됩니다. 토글 컨트롤을 사용하여 디바이스를 사용하지 않거나 사용하도록 설정할 수 있습니다.

:::image type="content" source="./media/calling/pre-call.png" alt-text="샘플 애플리케이션의 통화 전 화면을 보여 주는 스크린샷":::

표시 이름 및 디바이스 구성이 끝나면 통화 세션에 참가할 수 있습니다. 이제 핵심 통화 환경이 있는 기본 통화 캔버스가 표시됩니다.

:::image type="content" source="./media/calling/main-app.png" alt-text="샘플 애플리케이션의 기본 화면을 보여 주는 스크린샷":::

기본 통화 화면의 구성 요소:

- **미디어 갤러리**: 참가자가 표시되는 기본 스테이지입니다. 참가자가 카메라를 사용하도록 설정한 경우 비디오 피드가 여기에 표시됩니다. 각 참가자별로 표시 이름 및 비디오 스트림을 보여 주는 개별 타일이 있습니다(있는 경우).
- **헤더**: 여기에는 설정 및 참가자 사이드바를 토글하고, 비디오 및 믹스를 설정/해제하고, 화면을 공유하고, 통화를 종료하는 기본 통화 컨트롤이 있습니다.
- **사이드바**: 헤더의 컨트롤을 사용하여 사이드바를 전환하면 참가자 및 설정 정보가 사이드바에 표시됩니다. 오른쪽 위 모서리에서 'X'를 사용하여 구성 요소를 해제할 수 있습니다. 참가자 사이드바에는 참가자 목록과 추가 사용자를 채팅에 초대하는 링크가 표시됩니다. 설정 사이드바에서는 마이크 및 카메라 설정을 구성할 수 있습니다.

아래에서 사전 요구 사항에 대한 추가 정보, 샘플을 설정하는 단계 및 다양한 구성 요소를 익히는 데 도움이 되는 단계별 자습서를 확인할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Node.js(12.18.4 이상)](https://nodejs.org/en/download/)
- [Visual Studio(2019 이상)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)(Visual Studio 인스턴스에 따라 32비트 또는 64비트 버전을 설치해야 합니다.)
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication 리소스 만들기](../quickstarts/create-communication-resource.md)를 참조하세요. 이 빠른 시작에 대한 **연결 문자열** 리소스를 기록해야 합니다.

## <a name="locally-deploy-the-service--client-applications"></a>클라이언트 애플리케이션 및 서비스를 로컬로 배포

그룹 통화 샘플은 기본적으로 ClientApp 및 Service.NET 앱이라는 두 가지 애플리케이션으로 구성됩니다.

로컬로 배포하려는 경우 두 애플리케이션을 모두 시작해야 합니다. 브라우저에서 서버 앱으로 이동하면 사용자 환경에 로컬로 배포된 ClientApp이 사용됩니다.

통화 URL을 사용하여 여러 브라우저 세션을 열고 다중 사용자 통화를 시뮬레이션하여 샘플을 로컬로 테스트할 수 있습니다.

### <a name="before-running-the-sample-for-the-first-time"></a>처음으로 샘플을 실행하기 전에

1. PowerShell, Windows 터미널, 명령 프롬프트 또는 그에 상응하는 인스턴스를 열고 샘플을 복제할 디렉터리로 이동합니다.
2. `git clone`
3. **Calling/ClientApp 폴더**로 이동하고 `npm run setup` 실행
   1. 오류 1이 발생할 경우 클라이언트에 권한을 부여하기 위해 이동해야 하는 URL의 출력을 살펴봅니다. URL은 `app.vssps.visualstudio.com/oauth2/authorize?clientid=...` 형식입니다. 브라우저에서 URL을 방문하고, 브라우저 창에서 명령을 복사하여 실행합니다.
   2. 이전 단계를 완료한 후 `npm run setup-vsts-auth` 명령을 다시 실행합니다.
4. Azure Portal에서 `Connection String`을 가져옵니다. 연결 문자열에 대한 자세한 내용은 [Azure 통신 리소스 만들기](../quickstarts/create-communication-resource.md)를 참조하세요.
5. 연결 문자열을 가져온 후 Service .NET 폴더 아래에 있는 **Calling/appsetting.json** 파일에 연결 문자열을 추가합니다. `ResourceConnectionString` 변수에 연결 문자열을 입력합니다.

### <a name="local-run"></a>로컬 실행

1. Calling 폴더로 이동
2. Visual Studio에서 `Calling.csproj` 솔루션 열기
2. `Calling` 프로젝트* 실행

*브라우저에 `localhost:5000`이 열립니다(노드가 클라이언트 앱을 배포하는 위치). Internet Explorer에서 앱이 지원되지 않습니다.

#### <a name="troubleshooting"></a>문제 해결

- 솔루션이 빌드되지 않으며 NPM 설치/빌드 중에 오류가 throw됩니다.

   프로젝트를 정리/다시 빌드해 보세요.

## <a name="publish-the-sample-to-azure"></a>Azure에 샘플 게시

1. `Calling` 프로젝트를 마우스 오른쪽 단추로 클릭하고 게시를 선택합니다.
2. 새 게시 프로필을 만들고 Azure 구독을 선택합니다.
3. 게시하기 전에 `Edit App Service Settings`를 사용하여 연결 문자열을 추가하고, 키로 `ResourceConnectionString`을 입력하고, appsettings.json에서 복사한 연결 문자열을 값으로 제공합니다.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../quickstarts/create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [통화 클라이언트 라이브러리 사용법](../quickstarts/voice-video-calling/calling-client-samples.md) 숙지
- [통화 클라이언트 라이브러리 기능](../quickstarts/voice-video-calling/calling-client-samples.md)에 대해 알아보기
- [통화 작동 방식](../concepts/voice-video-calling/about-call-types.md)에 대한 자세한 정보

## <a name="additional-reading"></a>추가 자료

- [Azure Communication 미리 보기](https://github.com/Azure/communication-preview) - 통화 웹 SDK에 대해 자세히 알아보기
- [Redux](https://redux.js.org/) - 클라이언트 쪽 상태 관리
- [FluentUI](https://developer.microsoft.com/fluentui#/) - Microsoft 기반 UI 라이브러리
- [React](https://reactjs.org/) - 사용자 인터페이스 빌드용 라이브러리
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - 웹 애플리케이션 빌드용 프레임워크
