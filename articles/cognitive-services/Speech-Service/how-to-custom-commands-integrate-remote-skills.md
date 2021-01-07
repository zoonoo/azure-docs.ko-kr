---
title: '방법: 사용자 지정 명령 응용 프로그램을 원격 기술로 내보내기-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램을 기술로 내보내는 방법에 대해 알아봅니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025877"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>사용자 지정 명령 응용 프로그램을 원격 기술로 내보내기

이 문서에서는 사용자 지정 명령 응용 프로그램을 원격 기술로 내보내는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건
> [!div class="checklist"]
> * [Bot Framework 기술 이해](/azure/bot-service/skills-conceptual)
> * [기술 매니페스트 이해](https://aka.ms/speech/cc-skill-manifest)
> * [Bot Framework 봇에서 기술을 호출 하는 방법](/azure/bot-service/skills-about-skill-consumers)
> * 기존 사용자 지정 명령 응용 프로그램입니다. 사용자 지정 명령 응용 프로그램이 없는 경우- [빠른 시작: 사용자 지정 명령을 사용 하 여 음성 도우미 만들기](quickstart-custom-commands-application.md) 를 사용해 보세요.

## <a name="custom-commands-as-remote-skills"></a>원격 기술으로 서의 사용자 지정 명령
* 봇 프레임 워크 기술은 다시 사용할 수 있으며, 대화형 사용 사례를 포함 하는 대화형 기술 빌딩 블록을 사용 하면 몇 분 내에 봇에 광범위 한 기능을 추가할 수 있습니다. 이에 대 한 자세한 내용을 보려면 [봇 프레임 워크 기술](https://microsoft.github.io/botframework-solutions/overview/skills/)으로 이동 하세요.
* 사용자 지정 명령 응용 프로그램을 기술으로 내보낼 수 있습니다. 이 기술은 봇 Framework bot의 원격 기술 프로토콜을 통해 호출할 수 있습니다.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>원격 기술로 노출 되도록 응용 프로그램 구성

### <a name="application-level-settings"></a>응용 프로그램 수준 설정
1. 왼쪽 패널에서 **설정**  >  **원격 기술** 을 선택 합니다.
1. **원격 기술 사용** 설정 토글을 켜기로 설정 합니다.

### <a name="authentication-to-skills"></a>기술에 대 한 인증
1. 인증을 사용 하려면 사용자 지정 명령 응용 프로그램을 호출 하도록 구성할 Bot Framework 봇의 Microsoft 응용 프로그램 Id를 추가 합니다.
      > [!div class="mx-imgBorder"]
      > ![기술에 MSA id 추가](media/custom-commands/skill-add-msa-id.png)

1. 하나 이상의 항목이 목록에 추가 되어 있으면 응용 프로그램에서 인증을 사용 하도록 설정 하 고, 허용 된 봇만이 응용 프로그램을 호출할 수 있게 됩니다.
> [!TIP]
>  인증을 사용 하지 않도록 설정 하려면 허용 목록에서 모든 Microsoft 응용 프로그램 Id를 삭제 합니다. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>기술로 노출 되는 명령을 사용 하거나 사용 하지 않도록 설정

원격 기술을 통해 내보내려는 명령을 선택할 수 있는 옵션이 있습니다.

1. 기술 **에 대 한 명령을 노출 하려면 기술에 대 한 사용 명령** 아래에서 **새 명령 사용** 을 선택 합니다.
1. 드롭다운 목록에서 추가 하려는 명령을 선택 합니다.
1. **저장** 을 선택합니다.

### <a name="configure-triggering-utterances-for-commands"></a>명령에 대 한 길이 발언 트리거 구성
사용자 지정 명령은 길이 발언 트리거하는 기술을 생성 하기 위해 명령에 대해 구성 된 예제 문장을 사용 합니다. 이러한 **트리거 길이 발언** 은 **디스패처** 섹션 [**기술 매니페스트**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)를 생성 하는 데 사용 됩니다.

작성자는 기술에 대 한 트리거 길이 발언을 생성 하는 데 사용 되는 **예제 문장을** 제어 해야 할 수 있습니다.
1. 기본적으로 명령에서 트리거하는 모든 **예제** 는 매니페스트 파일에 포함 됩니다.
1. 한 가지 예를 명시적으로 제거 하려면 **기술에 사용할 수 있는 명령** 섹션의 명령에서 **편집** 아이콘을 선택 합니다.
    > [!div class="mx-imgBorder"]
    > ![기술에 대해 사용 하도록 설정 된 명령 편집](media/custom-commands/skill-edit-enabled-command.png)

1. 그런 다음 생략 하려는 문장 예제에서 **right click**  >  **사용 안 함 예제 문장** 을 마우스 오른쪽 단추로 클릭 합니다.
    > [!div class="mx-imgBorder"]
    > ![예 사용 안 함](media/custom-commands/skill-disable-example-sentences.png)

1. **저장** 을 선택합니다.
1. 이 창에 새 예를 추가할 수 없다는 것을 알 수 있습니다. 이 작업을 수행 해야 하는 경우 설정 섹션에서 종료로 이동 하 여 **명령** 아코디언에서 관련 명령을 선택 합니다. 이 시점에서 **예제 문장** 섹션에 새 항목을 추가할 수 있습니다. 이 변경 내용은 명령의 원격 기술 설정 값에 자동으로 반영 됩니다.

> [!IMPORTANT]
> 기존 예제 문장이 **문자열 > 카탈로그** 데이터 형식에 대 한 참조를 포함 하는 경우에는 해당 문장이 길이 발언의 기술 트리거 목록에서 자동으로 생략 됩니다. 

## <a name="download-skill-manifest"></a>기술 매니페스트 다운로드
1. 응용 프로그램을 **게시** 한 후 기술 매니페스트 파일을 다운로드할 수 있습니다.
1. 기술 매니페스트를 사용 하 여 사용자 지정 명령 기술에 대해를 호출 하도록 Bot Framework consumer bot을 구성 합니다.
> [!IMPORTANT]
> 기술 매니페스트를 다운로드 하려면 사용자 지정 명령 응용 프로그램을 **게시** 해야 합니다. </br>
> 또한 응용 프로그램을 **변경한** 경우 매니페스트 파일에 반영 하기 위해 최신 변경 내용이 반영 되도록 응용 프로그램을 다시 게시 해야 합니다.

> [!NOTE]
> 응용 프로그램 게시와 관련 된 문제가 발생 하 고 오류가 길이 발언를 트리거하는 기술로 전달 되는 경우 **기술에 대해 사용 하도록 설정 된 명령의** 구성을 다시 확인 하세요. 노출 된 각 명령에는 하나 이상의 유효한 트리거 utterance 있어야 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [클라이언트에서 명령 업데이트](./how-to-custom-commands-update-command-from-client.md)
