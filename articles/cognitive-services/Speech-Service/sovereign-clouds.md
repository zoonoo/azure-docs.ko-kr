---
title: 주권 구름 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 주권 구름 을 사용하는 방법에 대해 알아보기
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228095"
---
# <a name="speech-services-with-sovereign-clouds"></a>주권 구름이 있는 음성 서비스

## <a name="azure-government-united-states"></a>Azure 정부 (미국)

미국 연방, 주, 지방 및 부족 정부 및 그 파트너만이 선별된 미국 시민이 제어하는 작업을 통해 이 전용 인스턴스에 액세스할 수 있습니다.
- 지역: 미국 정부 버지니아
- SR 에서 SpeechSDK:*구성. FromHost("wss://virginia.stt.speech.azure.us",\<"당신의\>키");*
- 음성의 TTS: *구성. FromHost("https://virginia.tts.speech.azure.us",[]()\<"키");\>*
- 인증 토큰:[]()https ://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- 사용자 지정 음성 포털:https://virginia.cris.azure.us/Home/CustomSpeech
- 사용 가능한 SUS: S0
- 지원되는 기능:
  - 음성 텍스트 변환
  - 사용자 지정 음성(음향/언어 적응)
  - 텍스트 음성 변환
  - 음성 번역기
- 지원되지 않는 기능
  - Custom Voice
  - 텍스트 음성 변환을 위한 신경 음성
- 지원되는 로캘: 다음 언어에 대한 로캘이 지원됩니다.
  - 아랍어(ar-*)
  - 중국어(zh-*)
  - 영어(en-*)
  - 프랑스어(fr-*)
  - 독일어(드*)
  - 힌디어
  - 한국어
  - 러시아어
  - 스페인어(es-*)

## <a name="microsoft-azure-china"></a>마이크로소프트 Azure 중국

중국에 위치한 Azure 데이터 센터는 중국 사용자가 빠르고 안정적인 로컬 네트워크 액세스 환경을 제공하기 위해 차이나 모바일, 차이나 텔레콤, 차이나 유니콤 및 기타 주요 이동통신사 백본 네트워크에 직접 액세스할 수 있습니다.
- 지역: 중국 동부 2 (상하이)
- SR 에서 SpeechSDK: *구성. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<"당신의\>키");*
- 음성의 TTS: *구성. FromHost("https://chinaeast2.tts.speech.azure.cn",[]()\<"키");\>*
- 인증 토큰:[]()https://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- 사용자 지정 음성 포털:https://speech.azure.cn/CustomSpeech
- 사용 가능한 SUS: S0
- 지원되는 기능:
  - 음성 텍스트 변환
  - 사용자 지정 음성(음향/언어 적응)
  - 텍스트 음성 변환
  - 음성 번역기
- 지원되지 않는 기능
  - Custom Voice
  - 텍스트 음성 변환을 위한 신경 음성
- 지원되는 로캘: 다음 언어에 대한 로캘이 지원됩니다.
  - 아랍어(ar-*)
  - 중국어(zh-*)
  - 영어(en-*)
  - 프랑스어(fr-*)
  - 독일어(드*)
  - 힌디어
  - 한국어
  - 러시아어
  - 스페인어(es-*)

