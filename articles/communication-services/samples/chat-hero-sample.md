---
title: 그룹 채팅 주인공 샘플
titleSuffix: An Azure Communication Services sample overview
description: 개발자가 샘플의 내부 작업에 대해 자세히 알아보고 수정하는 방법을 배울 수 있도록 Azure Communication Services를 사용하는 채팅 주인공 샘플의 개요입니다.
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b97b80927739d9a8658213a00b415c0bf321528b
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460563"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>그룹 채팅 주인공 샘플 시작

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

Azure Communication Services **그룹 채팅 주인공 샘플**은 Communication Services 채팅 웹 클라이언트 라이브러리를 사용하여 그룹 통화 환경을 만드는 방법을 보여줍니다.

이 샘플 빠른 시작에서는 샘플이 어떻게 작동하는지 살펴본 후 로컬 컴퓨터에서 샘플을 실행합니다. 그런 다음, 사용자 고유의 Azure Communication Services 리소스를 사용하여 Azure에 샘플을 배포합니다.

> [!IMPORTANT]
> [GitHub에서 샘플 다운로드](https://github.com/Azure/Communication/tree/master/samples/Group%20Chat%20Hero%20Sample/Web/Chat)

## <a name="overview"></a>개요

이 샘플에는 클라이언트 쪽 애플리케이션과 서버 쪽 애플리케이션이 모두 들어 있습니다. **클라이언트 쪽 애플리케이션**은 Microsoft의 Fluent UI 프레임워크를 사용하는 React/Redux 웹 애플리케이션입니다. 이 애플리케이션은 클라이언트 쪽 애플리케이션에서 Azure에 연결하는 데 도움이 되는 ASP.NET Core **서버 쪽 애플리케이션**으로 요청을 보냅니다. 

샘플은 다음과 같습니다.

:::image type="content" source="./media/chat/landing-page.png" alt-text="샘플 애플리케이션의 방문 페이지를 보여주는 스크린샷&quot;:::

&quot;채팅 시작" 단추를 누르면 웹 애플리케이션이 서버 쪽 애플리케이션에서 사용자 액세스 토큰을 가져옵니다. 가져온 토큰은 클라이언트 앱을 Azure Communication Services에 연결하는 데 사용됩니다. 토큰이 검색되면 채팅에서 자신을 나타낼 이름과 이모지를 지정하라는 메시지가 표시됩니다. 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="샘플 애플리케이션의 방문 페이지를 보여주는 스크린샷&quot;:::

&quot;채팅 시작":::

표시 이름 및 이모지 구성이 끝나면 채팅 세션에 참가할 수 있습니다. 이제 핵심 채팅 환경이 있는 기본 채팅 캔버스가 표시됩니다.

:::image type="content" source="./media/chat/main-app.png" alt-text="샘플 애플리케이션의 방문 페이지를 보여주는 스크린샷&quot;:::

&quot;채팅 시작":::

기본 채팅 화면의 구성 요소:

- **기본 채팅 영역**: 사용자가 메시지를 보내고 받을 수 있는 핵심 채팅 환경입니다. 메시지를 보내려면 입력 영역을 사용하고 Enter 키를 누릅니다(또는 보내기 단추를 사용). 받은 채팅 메시지는 정확한 이름 및 이모지를 사용하여 보낸 사람을 기준으로 분류됩니다. 채팅 영역에는 1) 사용자가 입력 중일 때 표시되는 입력 알림과 2) 메시지 발신 및 읽음 알림의 두 가지 알림 유형이 표시됩니다.
- **헤더**: 채팅 스레드의 제목, 참가자를 전환하고 사이드바를 설정하는 컨트롤, 채팅 세션을 종료하는 나가기 단추가 여기에 표시됩니다.
- **사이드바**: 헤더의 컨트롤을 사용하여 사이드바를 전환하면 참가자 및 설정 정보가 사이드바에 표시됩니다. 참가자 사이드바에는 채팅의 참가자 목록과 참가자를 채팅 세션에 초대하는 링크가 있습니다. 설정 사이드바를 통해 채팅 스레드 제목을 구성할 수 있습니다. 

아래에는 샘플을 설정하기 위한 필수 구성 요소 및 설정 단계가 자세히 설명되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Node.js(8.11.2 이상)](https://nodejs.org/en/download/)
- [Visual Studio(2017 이상)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1)(Visual Studio 인스턴스에 따라 32비트 또는 64비트 버전을 설치해야 함)
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication 리소스 만들기](../quickstarts/create-communication-resource.md)를 참조하세요. 이 빠른 시작에 대한 **연결 문자열** 리소스를 기록해야 합니다.

## <a name="locally-deploying-the-service--client-app"></a>서비스 및 클라이언트 앱을 로컬로 배포

단일 스레드 채팅 샘플은 기본적으로 클라이언트 애플리케이션과 서버 애플리케이션의 두 가지 "애플리케이션"으로 구성됩니다.

chat.csproj에서 Visual Studio를 열고 디버그 모드로 실행합니다. 그러면 채팅 프런트 엔드 서비스가 시작됩니다. 브라우저에서 서버 앱을 방문하면 로컬로 배포된 채팅 프런트 엔드 서비스로 트래픽이 리디렉션됩니다.

채팅 URL을 사용하여 여러 브라우저 세션을 열고 다중 사용자 채팅을 시뮬레이션하여 샘플을 로컬로 테스트할 수 있습니다.

## <a name="before-running-the-sample-for-the-first-time"></a>처음으로 샘플을 실행하기 전에

1. PowerShell, Windows 터미널, 명령 프롬프트 또는 그에 상응하는 인스턴스를 열고 샘플을 복제할 디렉터리로 이동합니다.
2. `git clone https://github.com/Azure/Communication.git`
3. Azure Portal에서 `Connection String`을 가져옵니다. 연결 문자열에 대한 자세한 내용은 [Azure Communication 리소스 만들기](../quickstarts/create-communication-resource.md)를 참조하세요.
4. `Connection String`을 가져온 후에는 Chat 폴더 아래에 있는 **Chat/appsettings.json** 파일에 연결 문자열을 추가합니다. `ResourceConnectionString` 변수에 연결 문자열을 입력합니다.
5. `./Chat/ClientApp/src/constants.tsx`의 ENVIRONMENT_URL을 리소스의 위치로 업데이트합니다. (예: https://<RESOURCE_NAME>.communication.azure.com)

### <a name="local-run"></a>로컬 실행

1. 채팅 폴더로 이동하여 Visual Studio에서 `Chat.csproj` 솔루션 열기
2. 프로젝트를 실행합니다. 이 브라우저는 localhost:5000에서 열립니다.

#### <a name="troubleshooting"></a>문제 해결

- 솔루션이 빌드되지 않으며 NPM 설치/빌드 중에 오류가 throw됩니다.

   C# 솔루션 정리/다시 빌드

## <a name="publish-the-sample-to-azure"></a>Azure에 샘플 게시

1. `Chat` 프로젝트를 마우스 오른쪽 단추로 클릭하고 [게시]를 선택합니다.
2. 새 게시 프로필을 만들고 Azure 구독을 선택합니다.
3. 게시하기 전에 `Edit App Service Settings`를 사용하여 연결 문자열을 추가하고, 키로 `ResourceConnectionString`을 입력하고, appsettings.json에서 복사한 연결 문자열을 값으로 제공합니다.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../quickstarts/create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"] 
>[GitHub에서 샘플 다운로드](https://github.com/Azure/Communication/tree/master/samples/Group%20Chat%20Hero%20Sample/Web/Chat)

자세한 내용은 다음 문서를 참조하세요.

- [채팅 개념](../concepts/chat/concepts.md)에 대한 자세한 정보
- [채팅 클라이언트 라이브러리](../concepts/chat/sdk-features.md) 숙지

## <a name="additional-reading"></a>추가 자료

- [Azure Communication GitHub](https://github.com/Azure/communication) - 공식 GitHub 페이지에서 더 많은 예제 및 정보 찾기
- [Redux](https://redux.js.org/) - 클라이언트 쪽 상태 관리
- [FluentUI](https://developer.microsoft.com/fluentui#/) - Microsoft 기반 UI 라이브러리
- [React](https://reactjs.org/) - 사용자 인터페이스 빌드용 라이브러리
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - 웹 애플리케이션 빌드용 프레임워크
