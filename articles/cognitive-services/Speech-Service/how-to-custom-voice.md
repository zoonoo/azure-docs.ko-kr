---
title: 음성 서비스-사용자 지정 음성이란? 
titlesuffix: Azure Cognitive Services
description: 사용자 지정 음성은 브랜드에 대한 인식할 수 있는 한 종류의 음성을 만들 수 있는 온라인 도구 집합입니다. 시작 하기 위해 필요한 것은 적은 수의 오디오 파일 및 관련 기록뿐입니다. 사용자 지정 텍스트 음성 변환 환경 만들기를 시작 하려면 아래 링크를 따르세요.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 8cf9dc6cbfc96448462aac3a64807f8beb6036ad
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156951"
---
# <a name="get-started-with-custom-voice"></a>사용자 지정 음성 시작

사용자 지정 음성은 브랜드에 대한 인식할 수 있는 한 종류의 음성을 만들 수 있는 온라인 도구 집합입니다. 시작 하기 위해 필요한 것은 적은 수의 오디오 파일 및 관련 기록뿐입니다. 사용자 지정 텍스트 음성 변환 환경 만들기를 시작 하려면 아래 링크를 따르세요.

## <a name="whats-in-custom-voice"></a>사용자 지정 음성이란?

사용자 지정 음성을 시작 하기 전에, Azure 계정 및 Speech Services 구독이 필요 합니다. 계정을 만든 경우 데이터 준비, 모델 학습 및 테스트, 음성 품질 평가 및 궁극적으로 사용자 지정 음성 모델을 배포할 수 있습니다.

아래 다이어그램에는 사용자 지정 음성 포털을 사용하여 사용자 지정 음성 모델을 만드는 단계를 강조 표시 합니다. 자세한 내용을 보려면 링크를 사용 합니다.

![사용자 지정 음성 아키텍처 다이어그램](media/custom-voice/custom-voice-diagram.png)

1.  [구독 및 프로젝트 생성](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#set-up-your-azure-account) -Azure 계정을 만들고 음성 서비스 구독을 만듭니다. 이 통합된 구독은 음성-텍스트, 텍스트 음성 변환, 음성 번역 및 사용자 지정 음성 포털에 액세스할 수 있습니다. 그런 다음, 음성 서비스 구독을 사용하여 첫 번째 사용자 지정 음성 프로젝트를 생성합니다.

2.  [데이터 업로드](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#upload-your-datasets) -사용자 지정 음성 포털 또는 사용자 지정 음성 API를 사용하여 데이터 (오디오 및 텍스트)를 업로드 합니다. 포털에서, 발음 점수 및 신호 대 노이즈 비율을 평가하고 조사할 수 있습니다. 자세한 내용은 [사용자 지정 음성에 대한 데이터를 준비 하는 방법](how-to-custom-voice-prepare-data.md)을 확인합니다.

3.  [모델 학습](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#build-your-voice-font) – 데이터를 사용하여 사용자 지정 음성 모델을 만듭니다. 다른 언어에서 모델을 학습 시킬 수 있습니다. 학습 후 모델을 테스트하고 결과에 만족 하는 경우 모델을 배포할 수 있습니다.

4.  [모델 배포](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#create-and-use-a-custom-endpoint) -텍스트 음성 변환 모델에 대한 사용자 지정 끝점을 만들고 제품, 도구 및 응용 프로그램에서 음성 합성에 끝점을 사용합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

사용자 지정 음성 포털을 사용하여 사용자 지정 모델을 만들기 전에 음성 서비스 구독이 필요 합니다. Azure에서 음성 서비스 구독을 만들려면 다음 지침을 따릅니다. Azure 계정이 없으면 신규 가입할 수 있습니다.  

Azure 계정 및 음성 서비스 구독을 만든 후에 사용자 지정 음성 포털에 로그인하고 구독을 연결해야 합니다.

1. Azure portal에서 음성 서비스 구독 키를 가져옵니다.
2. [사용자 지정 음성 포털](https://aka.ms/custom-voice)에 로그인 합니다.
3. 구독을 선택하고 음성 프로젝트를 만듭니다.
4. 다른 음성 구독으로 전환하려는 경우 위쪽 탐색 모음에 있는 톱니바퀴 아이콘을 사용합니다.

> [!NOTE]
> 사용자 지정 음성 서비스는 30 일 무료 평가판 키를 지원하지 않습니다. 서비스를 사용 하려면 먼저 Azure에서 만든 F0 또는 S0 키가 있어야 합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점과 같은 콘텐츠는 사용자 지정 음성 포털에서 **프로젝트**로 구성 됩니다. 각 프로젝트는 국가/언어와 만들려는 음성의 성별로 지정됩니다. 예를 들어 미국 (EN-US)에서 영어를 사용하는 콜 센터의 채트 봇을 위한 여성 음성에 대한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **텍스트 음성 변환/사용자 지정 음성** 탭을 선택한 다음, **새 프로젝트**를 클릭합니다. 프로젝트를 만드는 마법사에서 제공한 지침을 따릅니다. 프로젝트를 만든 후에 네 개의 탭이 표시 됩니다: **데이터**, **학습**, **테스트** 및 **배포**. 각 탭을 사용하는 방법에 대해서는 [다음 단계](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#next-steps)에서 제공되는 링크를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 음성 데이터 준비](how-to-custom-voice.md)
- [사용자 지정 음성 만들기](how-to-custom-voice-create-voice.md)
- [가이드: 음성 샘플 기록](record-custom-voice-samples.md)
