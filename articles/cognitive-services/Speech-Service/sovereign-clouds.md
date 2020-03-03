---
title: 소 버린 클라우드-음성 서비스
titleSuffix: Azure Cognitive Services
description: 소 버린 클라우드를 사용 하는 방법 알아보기
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228095"
---
# <a name="speech-services-with-sovereign-clouds"></a>소 버린 클라우드를 사용 하는 음성 서비스

## <a name="azure-government-united-states"></a>Azure Government (미국)

미국 연방, 주, 지방, 부족 정부 기관 및 해당 파트너만 선별된 미국 시민이 관리하는 작업으로 이 전용 인스턴스에 액세스할 수 있습니다.
- 지역: US Gov 버지니아
- SpeechSDK의 SR:*config FromHost ("wss://virginia.stt.speech.azure.us", "\<키\>");*
- SpeechSDK의 TTS: *config FromHost ("https[]()://virginia.tts.speech.azure.us", "\<키\>");*
- 인증 토큰: https[]()://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Custom Speech 포털: https://virginia.cris.azure.us/Home/CustomSpeech
- 사용 가능한 Sku: S0
- 지원되는 기능:
  - 음성 텍스트 변환
  - Custom Speech (음향/언어 적응)
  - 텍스트 음성 변환
  - 음성 변환기
- 지원되지 않는 기능
  - Custom Voice
  - 텍스트 음성 변환의 신경망
- 지원 되는 로캘: 다음 언어에 대 한 로캘이 지원 됩니다.
  - 아랍어 (ar-*)
  - 중국어 (zh-cn-*)
  - 영어 (en-*)
  - 프랑스어 (fr-fr-*)
  - 독일어 (de-*)
  - 힌디어
  - 한국어
  - 러시아어
  - 스페인어 (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure 중국

중국의 사용자가 고속 및 안정적인 로컬 네트워크 액세스 환경을 제공 하는 중국 모바일, 중국 통신, 중국 Unicom 및 기타 주요 캐리어 백본 네트워크에 직접 액세스할 수 있는 Azure 데이터 센터에 있습니다.
- 지역: 중국 동부 2 (상하이)
- SpeechSDK의 SR: *config FromHost ("wss://chinaeast2.stt.speech.azure.cn", "\<키\>");*
- SpeechSDK의 TTS: *config FromHost ("https[]()://chinaeast2.tts.speech.azure.cn", "\<키\>");*
- 인증 토큰: https[]()://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Custom Speech 포털: https://speech.azure.cn/CustomSpeech
- 사용 가능한 Sku: S0
- 지원되는 기능:
  - 음성 텍스트 변환
  - Custom Speech (음향/언어 적응)
  - 텍스트 음성 변환
  - 음성 변환기
- 지원되지 않는 기능
  - Custom Voice
  - 텍스트 음성 변환의 신경망
- 지원 되는 로캘: 다음 언어에 대 한 로캘이 지원 됩니다.
  - 아랍어 (ar-*)
  - 중국어 (zh-cn-*)
  - 영어 (en-*)
  - 프랑스어 (fr-fr-*)
  - 독일어 (de-*)
  - 힌디어
  - 한국어
  - 러시아어
  - 스페인어 (es-*)

