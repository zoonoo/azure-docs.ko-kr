---
title: Custom Voice란? 음성 서비스
titlesuffix: Azure Cognitive Services
description: 사용자 지정 음성은 브랜드에 대 한 인식할 수 있는 한 종류 음성을 만들 수 있도록 온라인 도구 집합이 있습니다. 시작 하는 데 걸리는 모든 적은 수의 오디오 파일 및 연결 된 기록 됩니다. 사용자 지정 음성-텍스트 변환 환경을 만들기 시작하려면 아래 링크를 따르세요.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 016dcf32f2f846e43362f17bc9f4627113908352
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075141"
---
# <a name="get-started-with-custom-voice"></a>Custom Voice 시작

사용자 지정 음성은 브랜드에 대 한 인식할 수 있는 한 종류 음성을 만들 수 있도록 온라인 도구 집합이 있습니다. 시작 하는 데 걸리는 모든 적은 수의 오디오 파일 및 연결 된 기록 됩니다. 사용자 지정 텍스트 음성 변환 환경을 만들기를 시작 하려면 아래 링크를 따르세요.

## <a name="whats-in-custom-voice"></a>사용자 지정 음성에서 란?

사용자 지정 음성을 시작 하기 전에 Azure 계정 및 Speech Services 구독 해야 합니다. 계정을 만든 경우 데이터 준비, 학습 및 모델을 테스트할 음성 품질을 평가 하 수 궁극적으로 사용자 지정 음성 모델을 배포 합니다.

아래 다이어그램에는 사용자 지정 음성 포털을 사용 하 여 사용자 지정 음성 모델을 만드는 단계를 강조 표시 합니다. 자세한 내용을 보려면 링크를 사용 합니다.

![사용자 지정 음성 아키텍처 다이어그램](media/custom-voice/custom-voice-diagram.png)

1.  [프로젝트를 만들고 구독](#set-up-your-azure-account) -Azure 계정을 만들고 음성 서비스 구독을 만듭니다. 이 통합된 구독 음성-텍스트, 텍스트 음성 변환, 음성 번역 및 사용자 지정 음성 포털에 액세스할 수 있습니다. 그런 다음 첫 번째 사용자 지정 음성 프로젝트를 만들 음성 서비스 구독을 사용 합니다.

2.  [데이터 업로드](how-to-custom-voice-create-voice.md#upload-your-datasets) -업로드 데이터 (오디오 및 텍스트) 사용자 지정 음성 포털 또는 사용자 지정 음성 API를 사용 하 여 합니다. 포털에서 조사 하 고 발음 점수 및 신호 대 노이즈 비율이 평가할 수 있습니다. 자세한 내용은 [사용자 지정 음성에 대 한 데이터를 준비 하는 방법을](how-to-custom-voice-prepare-data.md)합니다.

3.  [모델 학습을](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – 데이터를 사용 하 여 사용자 지정 음성 모델을 만듭니다. 다른 언어에서 모델을 학습 시킬 수 있습니다. 학습 후 모델을 테스트 하 고 결과 만족 하는 경우에 모델을 배포할 수 있습니다.

4.  [모델을 배포할](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) -음성 모델에 대 한 사용자 지정 끝점을 만들고 제품, 도구 및 응용 프로그램에서 음성 합성에 사용 합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

사용자 지정 음성 포털을 사용 하 여 사용자 지정 모델을 만들기 전에 음성 서비스 구독이 필요 합니다. Azure에서 음성 서비스 구독을 만들려면 다음이 지침을 따릅니다. Azure 계정이 없으면 새 리소스 서명할 수 있습니다.  

Azure 계정 및 음성 서비스 구독을 만든 후에 사용자 지정 음성 포털에 로그인 하 고 구독을 연결 하는 것이 해야 합니다.

1. Azure portal에서 음성 서비스 구독 키를 가져옵니다.
2. 에 로그인 합니다 [사용자 지정 음성 포털](https://aka.ms/custom-voice)합니다.
3. 구독을 선택 하 고 음성 프로젝트를 만듭니다.
4. 다른 음성 구독으로 전환 하려는 경우 위쪽 탐색 모음에 있는 톱니바퀴 아이콘을 사용 합니다.

> [!NOTE]
> 사용자 지정 음성 서비스는 30 일 무료 평가판 키를 지원 하지 않습니다. F0 또는 서비스를 사용 하려면 먼저 Azure에서 만든 S0 키 있어야 합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점으로 구성 됩니다와 같은 콘텐츠 **프로젝트** 사용자 지정 음성 포털에서 합니다. 각 프로젝트는 특정 국가/언어를 만들려는 음성의 성별입니다. 예를 들어 미국 (EN-US)에서 영어를 사용 하는 콜 센터의 채트 봇 위한 여성 음성에 대 한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 선택 합니다 **Text-을-Speech/사용자 지정 음성** 탭을 클릭 한 다음 **새 프로젝트**합니다. 프로젝트를 만드는 마법사에서 제공한 지침을 따릅니다. 프로젝트를 만든 후에 네 개의 탭 표시 됩니다. **데이터**, **학습**합니다 **테스트**, 및 **배포**합니다. 에 나와 있는 링크를 사용 하 여 [다음 단계](#next-steps) 각 탭을 사용 하는 방법입니다.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 음성 데이터를 준비 합니다.](how-to-custom-voice.md)
- [사용자 지정 음성 만들기](how-to-custom-voice-create-voice.md)
- [가이드: 음성 샘플을 기록](record-custom-voice-samples.md)
