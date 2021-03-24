---
title: Azure Percept Studio 내에서 음성 도우미 응용 프로그램 구성
description: Azure Percept Studio 내에서 음성 도우미 응용 프로그램 구성
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 8f22379049b74428787b738af832802081be7bf8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022894"
---
# <a name="managing-your-voice-assistant"></a>음성 도우미 관리

이 문서에서는 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)내에서 음성 도우미 응용 프로그램의 키워드 및 명령을 구성 하는 방법을 설명 합니다. 포털 대신 IoT Hub 내에서 키워드를 구성 하는 방법에 대 한 지침은이 [방법 문서](./how-to-configure-voice-assistant.md)를 참조 하세요.

음성 도우미 응용 프로그램을 아직 만들지 않은 경우 [Azure Percept Studio 및 Azure Percept Audio를 사용 하 여 코드 없는 음성 도우미 빌드](./tutorial-no-code-speech.md)를 참조 하세요.

## <a name="keyword-configuration"></a>키워드 구성

키워드는 음성 도우미를 활성화 하는 데 사용 되는 단어 또는 짧은 문구입니다. 예를 들어 "안녕하세요 Cortana"는 Cortana 길잡이에 대 한 키워드입니다. 음성 정품 인증을 사용 하면 사용자가 키워드를 말하는 방법으로 제품을 완벽 하 게 자유롭게 조작할 수 있습니다. 제품이 키워드를 지속적으로 수신 하는 동안 모든 오디오는 검색을 수행 하 여 사용자 데이터가 가능한 한 비공개로 유지 되도록 할 때까지 장치에서 로컬로 처리 됩니다.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>음성 도우미 데모 창에서 구성

1. 데모 페이지에서 **사용자 지정 키워드** 옆의 **변경** 을 클릭 합니다.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="접객 데모 창을 보여 주는 스크린샷":::

    데모 페이지가 열려 있지 않으면 장치 페이지로 이동 하 여 (아래 참조), 데모에 액세스 하려면 **작업** 아래에서 **음성 도우미 테스트** 를 클릭 합니다.

1. 사용 가능한 키워드 중 하나를 선택 하 고 **저장** 을 클릭 하 여 변경 내용을 적용 합니다.

1. 구성이 완료 되 고 음성 길잡이가 사용할 준비가 되 면 Azure Percept 오디오 장치의 세 LED 표시등이 밝은 파란색 (깜박이지 않음)으로 변경 됩니다.

### <a name="configuration-within-the-device-page"></a>장치 페이지 내 구성

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)의 개요 페이지에 있는 왼쪽 메뉴 창에서 **장치** 를 클릭 합니다.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="장치가 강조 표시 된 Azure Percept Studio 개요 페이지의 스크린샷":::

1. 음성 도우미 응용 프로그램이 배포 된 장치를 선택 합니다.

1. **음성** 탭을 엽니다.

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="음성 탭이 강조 표시 된 edge 장치 페이지의 스크린샷":::

1. **키워드** 옆의 **변경** 을 클릭 합니다.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="사용 가능한 음성 솔루션 작업의 스크린샷":::

1. 사용 가능한 키워드 중 하나를 선택 하 고 **저장** 을 클릭 하 여 변경 내용을 적용 합니다.

1. 구성이 완료 되 고 음성 길잡이가 사용할 준비가 되 면 Azure Percept 오디오 장치의 세 LED 표시등이 밝은 파란색 (깜박이지 않음)으로 변경 됩니다.

## <a name="create-a-custom-keyword"></a>사용자 지정 키워드 만들기

[Speech Studio](https://speech.microsoft.com/)를 사용 하 여 음성 도우미에 대 한 사용자 지정 키워드를 만들 수 있습니다. 기본 사용자 지정 키워드 모델을 학습 하는 데 최대 30 분이 걸립니다.

사용자 지정 키워드를 만드는 방법에 대 한 지침은 [Speech Studio 설명서](../cognitive-services/speech-service/custom-keyword-basics.md) 를 참조 하세요. 구성 된 새 키워드는 voice assistant 응용 프로그램에서 사용할 수 있도록 산타 Cruz 포털 프로젝트에서 사용할 수 있습니다.

## <a name="commands-configuration"></a>명령 구성

사용자 지정 명령을 사용 하면 음성 우선 상호 작용 환경에 최적화 된 풍부한 음성 명령 앱을 쉽게 빌드할 수 있습니다. 사용자 지정 명령은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합 합니다.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>음성 도우미 데모 창에서 구성

1. 데모 페이지에서 **사용자 지정 명령** 옆에 있는 **변경** 을 클릭 합니다. 데모 페이지가 열려 있지 않으면 장치 페이지로 이동 하 여 (아래 참조), 데모에 액세스 하려면 **작업** 아래에서 **음성 도우미 테스트** 를 클릭 합니다.

1. 사용 가능한 사용자 지정 명령 중 하나를 선택 하 고 **저장** 을 클릭 하 여 변경 내용을 적용 합니다.

### <a name="configuration-within-the-device-page"></a>장치 페이지 내 구성

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)의 개요 페이지에 있는 왼쪽 메뉴 창에서 **장치** 를 클릭 합니다.

1. 음성 도우미 응용 프로그램이 배포 된 장치를 선택 합니다.

1. **음성** 탭을 엽니다.

1. **명령** 옆의 **변경** 을 클릭 합니다.

1. 사용 가능한 사용자 지정 명령 중 하나를 선택 하 고 **저장** 을 클릭 하 여 변경 내용을 적용 합니다.

## <a name="create-custom-commands"></a>사용자 지정 명령 만들기

[Speech Studio](https://speech.microsoft.com/)를 사용 하 여 음성 길잡이가 실행할 사용자 지정 명령을 만들 수 있습니다.

사용자 지정 명령 만들기에 대 한 지침은 [Speech Studio 설명서](../cognitive-services/speech-service/quickstart-custom-commands-application.md) 를 참조 하세요. 구성 된 후에는 Azure Percept Studio에서 음성 도우미 응용 프로그램에 사용할 수 있는 새 명령이 제공 됩니다.

## <a name="next-steps"></a>다음 단계

음성 도우미 응용 프로그램을 빌드한 후에는 Azure Percept 진한 코드를 사용 [하지 않는 비전 솔루션](./tutorial-nocode-vision.md) 을 개발 해 보세요.