---
title: Azure에서 Custom Speech Service의 지원되는 로캘 및 언어 | Microsoft Docs
description: Cognitive Services에서 Custom Speech Service의 지원되는 언어에 대한 개요입니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 1f186681c7e46d2e47ed7eee55c8f61290c48fcb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987529"
---
# <a name="supported-locales-in-custom-speech-service"></a>Custom Speech Service에서 지원되는 로캘
현재 Custom Speech Service는 다음 로캘에서 모델의 사용자 지정을 지원합니다.

| 모델 형식 | 언어 지원 |
|----|-----|
| 음향 모델 | 미국 영어(en-US) |
| 언어 모델 | 미국 영어(en-US), 중국어(zh-CN) |

음향 모델 사용자 지정은 미국 영어로만 지원되지만 사용자 지정된 중국어 모델을 오프라인으로 테스트하기 위해 중국어 음향 데이터 가져오기가 지원됩니다.

작업을 수행하기 전에 적절한 로캘을 선택해야 합니다. 현재 로캘은 모든 데이터, 모델 및 배포 페이지의 표 제목에 표시됩니다. 로캘을 변경하려면 표 제목 아래에 있는 [로캘 변경] 단추를 클릭합니다. 그러면 로캘 확인 페이지로 이동합니다. [확인]을 클릭하여 표로 돌아갑니다.

다음 단계를 따라야 합니다.
* 인식 정확도를 향상시키기 위해 [사용자 지정 음향 모델을 만드는 방법](cognitive-services-custom-speech-create-acoustic-model.md)을 알아보세요.
* 인식 속도를 향상시키기 위해 [사용자 지정 언어 모델을 만드는 방법](cognitive-services-custom-speech-create-language-model.md)을 알아보세요.
* 데이터 준비를 위해 [기록 지침](cognitive-services-custom-speech-transcription-guidelines.md)을 따르세요.
