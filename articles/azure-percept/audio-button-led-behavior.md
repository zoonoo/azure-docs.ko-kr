---
title: Azure Percept 오디오 단추 및 LED 동작
description: Azure Percept Audio의 단추 및 LED 상태에 대해 자세히 알아보세요.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c7ee2289680bb50fabb8e6eb2a3ea0466bd58afb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663431"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept 오디오 단추 및 LED 동작

Azure Percept 오디오의 단추 및 LED 상태에 대 한 자세한 내용은 다음 지침을 참조 하세요.

## <a name="button-behavior"></a>단추 동작

단추를 사용 하 여 장치의 동작을 제어할 수 있습니다.

|단추 상태|  동작|
|------------|----------|
|음소거|  키를 누르면 mic 배열 음소거/음소거 해제 단추 이벤트는 눌렀다 놓았을 때 트리거됩니다.|
|PTT/포인트|   발견 상태 키워드를 무시 하 고 명령 수신 상태를 활성화 하려면 PTT를 누릅니다. 다시를 눌러 에이전트의 활성 대화 상자를 중지 하 고 발견 state 키워드로 되돌립니다.|

## <a name="led-behavior"></a>LED 동작

LED 표시기를 사용 하 여 장치가 있는 상태를 파악할 수 있습니다.

|LED|   LED 상태|  귀 SoM 상태|
|---|------------|----------------| 
|L02|   1x 흰색, 정적 설정 |전원 켜기 |
|L02|   1x 흰색, 0.5 Hz 깜박임|  인증 진행 중 |
|L01 & L02 & L03|   3, 파랑, 정적|     키워드가 검색 됨|
|L01 & L02 & L03|   LED 배열 깜박임, 20fps | 수신 또는 말하기|
|L01 & L02 & L03|   LED 배열 레이스, 20fps|    간주|
|L01 & L02 & L03|   3, 빨강, 고정 | 음소거|

## <a name="next-steps"></a>다음 단계

Azure Percept 오디오 장치에 대 한 문제 해결 팁은이 [가이드](./troubleshoot-audio-accessory-speech-module.md)를 참조 하세요.