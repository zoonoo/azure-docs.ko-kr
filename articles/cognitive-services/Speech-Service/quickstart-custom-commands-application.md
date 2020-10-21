---
title: '빠른 시작: 사용자 지정 명령을 사용 하 여 음성 도우미 만들기'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Speech Studio를 사용 하 여 기본 사용자 지정 명령 응용 프로그램을 만들고 테스트 합니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d89f9330947d1c5d0146b8531f265d86d0fd5160
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329154"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>사용자 지정 명령을 사용하여 음성 도우미 만들기

이 빠른 시작에서는 Speech Studio를 사용 하 여 기본 사용자 지정 명령 응용 프로그램을 만들고 테스트 합니다. 이 응용 프로그램은 Windows 클라이언트 앱 에서도 액세스할 수 있습니다. **사용자 지정 명령을** 사용 하면 음성 우선 상호 작용 환경에 최적화 된 풍부한 음성 명령 앱을 쉽게 빌드할 수 있습니다. 통합 된 제작 환경, 자동 호스팅 모델 및 비교적 낮은 복잡성을 제공 하므로 음성 명령 시나리오에 가장 적합 한 솔루션을 구축 하는 데 집중할 수 있습니다.

## <a name="region-availability"></a>지역 가용성
이번에는 사용자 지정 명령이 다음 지역에서 만든 음성 등록을 지원 합니다.
* 미국 서부
* 미국 서부2
* 미국 동부
* 미국 동부2
* 북유럽
* 서유럽
* 미국 중서부
* 인도 중부
* 동아시아
* 동남아시아

## <a name="prerequisites"></a>전제 조건

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">사용자 지정 명령을 지 원하는 지역에서 Azure Speech 리소스를 만듭니다.<span class="docon docon-navigate-external x-hidden-focus"></span></a> 지원 되는 지역 목록에 대해서는 위의 **지역 가용성** 섹션을 참조 하세요.
> * 샘플 [스마트 공간 Lite](https://aka.ms/speech/cc-quickstart) json 파일을 다운로드 합니다.
> * 최신 버전의 [Windows Voice Assistant 클라이언트](https://aka.ms/speech/va-samples-wvac)를 다운로드 합니다.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>사용자 지정 명령을 위한 Speech Studio로 이동

1. 웹 브라우저에서 [Speech Studio](https://speech.microsoft.com/)로 이동 합니다.
1. 자격 증명을 입력하여 포털에 로그인합니다.

   기본 보기는 음성 구독의 목록입니다.
   > [!NOTE]
   > 구독 선택 페이지가 표시 되지 않는 경우 위쪽 표시줄의 설정 메뉴에서 "음성 리소스"를 선택 하 여 탐색할 수 있습니다.

1. 음성 구독을 선택한 다음, **스튜디오로 이동**을 선택 합니다.
1. **사용자 지정 명령**을 선택 합니다.

   기본 보기는 선택한 구독 아래에 있는 사용자 지정 명령 응용 프로그램의 목록입니다.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>새 사용자 지정 명령 프로젝트로 기존 응용 프로그램 가져오기

1. **새 프로젝트** 를 선택 하 여 프로젝트를 만듭니다.

1. **이름** 상자에 프로젝트 이름 `Smart-Room-Lite` (또는 원하는 다른 항목)을 입력 합니다.
1. **언어** 목록에서 **영어 (미국)** 를 선택 합니다.
1. **찾아보기 파일** 을 선택 하 고 찾아보기 창에서 파일 **의SmartRoomLite.js** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![프로젝트 만들기](media/custom-commands/import-project.png)

1.  **LUIS authoring 리소스** 목록에서 제작 리소스를 선택 합니다. 유효한 제작 리소스가 없는 경우 create  **NEW LUIS authoring resource**를 선택 하 여 만듭니다.

    > [!div class="mx-imgBorder"]
    > ![리소스 그룹 만들기](media/custom-commands/create-new-luis-resource.png)
    
    
    1. **리소스 이름** 상자에 리소스의 이름을 입력 합니다.
    1. **리소스 그룹** 목록에서 리소스 그룹을 선택 합니다.
    1. **위치** 목록에서 위치를 선택 합니다.
    1. **가격 책정 계층** 목록에서 계층을 선택 합니다.
    
    
    > [!NOTE]
    > "리소스 그룹" 필드에 원하는 리소스 그룹 이름을 입력 하 여 리소스 그룹을 만들 수 있습니다. **만들기** 를 선택 하면 리소스 그룹이 생성 됩니다.


1. 그런 다음 **만들기** 를 선택 하 여 프로젝트를 만듭니다.
1. 프로젝트를 만든 후 프로젝트를 선택 합니다.
이제 새 사용자 지정 명령 응용 프로그램의 개요가 표시 됩니다.

## <a name="try-out-some-voice-commands"></a>몇 가지 음성 명령을 사용해 보세요.
1. 오른쪽 창 맨 위에서 **학습** 을 선택 합니다.
1. 교육이 완료 되 면 **테스트** 를 선택 하 고 다음 길이 발언를 시도 합니다.
    - Tv 켜기
    - 온도를 80도로 설정 합니다.
    - 꺼주세요
    - Tv
    - 오후 5 시에 대 한 경보 설정

## <a name="integrate-custom-commands-application-in-an-assistant"></a>길잡이에서 사용자 지정 명령 응용 프로그램 통합
Speech Studio 외부에서이 응용 프로그램에 액세스 하려면 먼저 응용 프로그램을 게시 해야 합니다. 응용 프로그램을 게시 하려면 예측 LUIS 리소스를 구성 해야 합니다.  

### <a name="update-prediction-luis-resource"></a>예측 LUIS 리소스 업데이트


1. 왼쪽 창에서 **설정** 을 선택 하 고 가운데 창에서  **LUIS resources** 를 선택 합니다.
1. 예측 리소스를 선택 하거나 **새 리소스 만들기**를 선택 하 여 하나를 만듭니다.
1. **저장**을 선택합니다.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS 리소스 설정](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> 제작 리소스는 매월 1000 예측 끝점 요청만 지원 하기 때문에 사용자 지정 명령 응용 프로그램을 게시 하기 전에 LUIS 예측 리소스를 설정 해야 mandatorily 됩니다.

### <a name="publish-the-application"></a>애플리케이션 게시

오른쪽 창 맨 위에서  **게시** 를 선택 합니다. 게시가 완료 되 면 새 창이 표시 됩니다. **응용 프로그램 ID** 및 **음성 리소스 키** 값을 적어둡니다. Speech Studio 외부에서 응용 프로그램에 액세스할 수 있으려면이 두 값이 필요 합니다.

또는 **설정**  >  **일반** 섹션을 선택 하 여 이러한 값을 가져올 수도 있습니다.

### <a name="access-application-from-client"></a>클라이언트에서 응용 프로그램에 액세스

이 문서의 범위에서 필수 구성 요소의 일부로 다운로드 한 Windows Voice Assistant 클라이언트를 사용 하 게 됩니다. 폴더의 압축을 풉니다.
1. **VoiceAssistantClient.exe**를 시작 합니다.
1. 새 게시 프로필을 만들고 **연결 프로필**에 대 한 값을 입력 합니다. **일반 설정** 섹션에서 값 **구독 키** (응용 프로그램을 게시할 때 저장 한 **음성 리소스 키** 값과 동일), **구독 키 지역** 및 **사용자 지정 명령 앱 ID**를 입력 합니다.
    > [!div class="mx-imgBorder"]
    > ![WVAC 프로필을 만들기 위한 일반 설정 섹션을 강조 표시 하는 스크린샷](media/custom-commands/create-profile.png)
1. **프로필 저장 및 적용을**선택 합니다.
1. 이제 음성/텍스트를 통해 다음 입력을 사용해 보세요.
    > [!div class="mx-imgBorder"]
    > ![WVAC 프로필 만들기](media/custom-commands/conversation.png)


> [!TIP]
> **활동 로그** 에서 항목을 클릭 하 여 사용자 지정 명령 서비스에서 전송 되는 원시 응답을 검사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 응용 프로그램을 사용 했습니다. 그런 다음, [방법 섹션](how-to-custom-commands-create-application-with-simple-commands.md)에서 사용자 지정 명령 응용 프로그램을 처음부터 디자인, 개발, 디버그, 테스트 및 통합 하는 방법을 알아봅니다.
