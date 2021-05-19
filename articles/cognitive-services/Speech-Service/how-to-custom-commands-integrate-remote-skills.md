---
title: '방법: 사용자 지정 명령 애플리케이션을 원격 기술로 내보내기 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 애플리케이션을 기술로 내보내는 방법에 대해 알아봅니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025877"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>사용자 지정 명령 애플리케이션을 원격 기술로 내보내기

이 문서에서는 사용자 지정 명령 애플리케이션을 원격 기술로 내보내는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
> [!div class="checklist"]
> * [Bot Framework 기술 이해](/azure/bot-service/skills-conceptual)
> * [기술 메니페스트 이해](https://aka.ms/speech/cc-skill-manifest)
> * [Bot Framework 봇에서 기술을 호출하는 방법](/azure/bot-service/skills-about-skill-consumers)
> * 기존 사용자 지정 명령 애플리케이션. 사용자 지정 명령 애플리케이션이 없는 경우 [빠른 시작: 사용자 지정 명령을 사용하여 음성 도우미 만들기](quickstart-custom-commands-application.md)를 사용해 보세요.

## <a name="custom-commands-as-remote-skills"></a>사용자 지정 명령을 원격 기술로
* Bot Framework 기술은 대화형 사용 사례를 포함하는 재사용 가능한 대화 기술 구성 요소로, 몇 분 내에 봇에 광범위한 기능을 추가할 수 있습니다. 이에 대한 자세한 내용을 보려면 [Bot Framework 기술](https://microsoft.github.io/botframework-solutions/overview/skills/)로 이동합니다.
* 사용자 지정 명령 애플리케이션을 기술로 내보낼 수 있습니다. 이 기술은 Bot Framework 봇에서 원격 기술 프로토콜을 통해 호출할 수 있습니다.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>원격 기술로 노출되도록 애플리케이션 구성

### <a name="application-level-settings"></a>애플리케이션 수준 설정
1. 왼쪽 패널에서 **설정** > **원격 기술** 을 선택합니다.
1. **원격 기술 사용** 토글을 켜기로 설정합니다.

### <a name="authentication-to-skills"></a>기술에 대한 인증
1. 인증을 사용하려면 사용자 지정 명령 애플리케이션을 호출하도록 구성하려는 Bot Framework 봇의 Microsoft 애플리케이션 ID를 추가합니다.
      > [!div class="mx-imgBorder"]
      > ![기술에 MSA ID 추가](media/custom-commands/skill-add-msa-id.png)

1. 목록에 항목이 하나 이상 추가된 경우 애플리케이션에서 인증이 사용하도록 설정되고 허용된 봇만 애플리케이션을 호출할 수 있습니다.
> [!TIP]
>  인증을 사용하지 않도록 설정하려면 허용 목록에서 모든 Microsoft 애플리케이션 ID를 삭제합니다. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>기술로 노출되는 명령 사용/사용 안 함

원격 기술을 통해 내보낼 명령을 선택할 수 있습니다.

1. 기술을 통해 명령을 노출하려면 **기술에 대한 명령 사용** 에서 **새 명령 사용** 을 선택합니다.
1. 드롭다운에서 추가할 명령을 선택합니다.
1. **저장** 을 선택합니다.

### <a name="configure-triggering-utterances-for-commands"></a>명령에 대한 발화 트리거 구성
사용자 지정 명령은 발화를 트리거하는 기술을 생성하기 위해 명령에 대해 구성된 문장 예를 사용합니다. 이러한 **발화 트리거** 는 **디스패처** 섹션 [ **기술 매니페스트**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)를 생성하는 데 사용됩니다.

작성자는 기술에 대한 트리거 발화를 생성하는 데 사용되는 **문장 예** 를 제어할 수 있습니다.
1. 기본적으로 명령의 모든 **트리거 예** 가 매니페스트 파일에 포함됩니다.
1. 하나의 예를 명시적으로 제거하려면 **기술에 사용할 수 있는 명령** 섹션의 명령에서 **편집** 아이콘을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![기술에 사용할 수 있는 명령 편집](media/custom-commands/skill-edit-enabled-command.png)

1. 다음으로 생략하려는 예문에서 **마우스 오른쪽 단추 클릭** > **문장 예 사용 안 함** 을 클릭합니다.
    > [!div class="mx-imgBorder"]
    > ![문장 예 사용 안 함](media/custom-commands/skill-disable-example-sentences.png)

1. **저장** 을 선택합니다.
1. 이 창에서 새 예를 추가할 수 없다는 것을 알 수 있습니다. 추가하려면 설정 섹션을 종료하고 **명령** 아코디언에서 관련 명령을 선택합니다. 이 시점에서 **문장 예** 섹션에 새 항목을 추가할 수 있습니다. 이 변경 사항은 명령에 대한 원격 기술 설정 값에 자동으로 반영됩니다.

> [!IMPORTANT]
> 기존 문장 예에 **문자열 > 카탈로그** 데이터 형식에 대한 참조가 있는 경우 해당 문장은 발화를 트리거하는 기술 목록에서 자동으로 생략됩니다. 

## <a name="download-skill-manifest"></a>기술 매니페스트 다운로드
1. 애플리케이션을 **게시** 한 후 기술 매니페스트 파일을 다운로드할 수 있습니다.
1. 기술 매니페스트를 사용하여 사용자 지정 명령 스킬을 호출하도록 Bot Framework 소비자 봇을 구성합니다.
> [!IMPORTANT]
> 기술 매니페스트를 다운로드하려면 사용자 지정 명령 애플리케이션을 **게시** 해야 합니다. </br>
> 또한 애플리케이션을 **변경** 한 경우 최신 변경 사항이 매니페스트 파일에 반영되도록 애플리케이션을 다시 게시해야 합니다.

> [!NOTE]
> 애플리케이션을 게시하는 데 문제가 있고 오류가 발화를 트리거하는 기술로 연결되는 경우 **기술에 사용할 수 있는 명령** 에 대한 구성을 다시 확인하세요. 노출된 각 명령에는 하나 이상의 유효한 발화 트리거가 있어야 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [클라이언트에서 명령 업데이트](./how-to-custom-commands-update-command-from-client.md)
