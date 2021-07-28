---
title: Azure Percept Audio 단추 및 LED 동작
description: Azure Percept Audio의 단추 및 LED 상태에 대해 자세히 알아봅니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 169c3e7be60d5bf1efb7046aa92316a472fd4518
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930860"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept Audio 단추 및 LED 동작

Azure Percept Audio 디바이스의 단추 및 LED 상태에 대한 자세한 내용은 다음 지침을 참조하세요.

## <a name="button-behavior"></a>단추 동작

디바이스 동작을 제어하려면 단추를 사용합니다.

|단추 상태|동작|
|------------|----------|
|음소거|마이크 배열을 음소거/음소거 해제하려면 누릅니다. 단추 이벤트는 눌렀다 놓았을 때 트리거됩니다.|
|PTT/PTS|키워드 탐지 상태를 무시하고 명령 수신 상태를 활성화하려면 PTT를 누릅니다. 에이전트의 활성 대화 상자를 중지하고 키워드 탐지 상태로 돌아가려면 다시 누릅니다. 단추 이벤트는 눌렀다 놓았을 때 트리거됩니다. PTS는 에이전트가 듣거나 생각할 때가 아니라 말하는 동안 버튼을 눌렀을 때만 작동합니다.|

## <a name="led-behavior"></a>LED 동작

LED 표시기를 사용하여 디바이스의 상태를 파악할 수 있습니다.

|LED|LED 상태|Ear SoM 상태|
|---|------------|----------------|
|L02|1x 흰색, 정적 설정|전원 켜기 |
|L02|1x 흰색, 0.5Hz 깜박임|인증 진행 중 |
|L01 & L02 & L03|3x 파랑, 계속 켜져 있음|키워드를 기다리는 중|
|L01 & L02 & L03|LED 배열 깜박임, 20fps |수신 또는 말하기|
|L01 & L02 & L03|LED 배열 레이스, 20fps|생각|
|L01 & L02 & L03|3x 빨강, 계속 켜져 있음 |음소거|

## <a name="next-steps"></a>다음 단계

Azure Percept Audio 디바이스에 대한 문제 해결 팁은 이 [가이드](./troubleshoot-audio-accessory-speech-module.md)를 참조하세요.