---
title: 사용자 지정 음성-음성 서비스를 사용 하 여 합성 향상
titleSuffix: Azure Cognitive Services
description: 사용자 지정 음성은 브랜드에 대해 인식할 수 있는 일종의 음성을 만들 수 있는 온라인 도구 집합입니다. 시작 하는 데 사용 되는 모든 작업은 몇 가지 오디오 파일 및 관련 된 음성입니다. 사용자 지정 음성-텍스트 환경 만들기를 시작 하려면 아래 링크를 따르세요.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 272367d5311952b45c73febe0e05b1ec2d225261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056689"
---
# <a name="get-started-with-custom-voice"></a>Custom Voice 시작

[사용자 지정 음성](https://aka.ms/customvoice) 은 브랜드에 대해 인식할 수 있는 일종의 음성을 만들 수 있는 온라인 도구 집합입니다. 시작 하는 데 사용 되는 모든 작업은 몇 가지 오디오 파일 및 관련 된 음성입니다. 사용자 지정 텍스트 음성 변환 환경 만들기를 시작 하려면 아래 링크를 따르세요.

## <a name="whats-in-custom-voice"></a>사용자 지정 음성의 기능

사용자 지정 음성으로 시작 하기 전에 Azure 계정 및 음성 서비스 구독이 필요 합니다. 계정을 만든 후에는 데이터를 준비 하 고, 모델을 학습 및 테스트 하 고, 음성 품질을 평가 하 고 궁극적으로 사용자 지정 음성 모델을 배포할 수 있습니다.

아래 다이어그램은 [사용자 지정 음성 포털](https://aka.ms/customvoice)을 사용 하 여 사용자 지정 음성 모델을 만드는 단계를 강조 표시 합니다. 자세한 내용은 링크를 사용 하십시오.

![사용자 지정 음성 아키텍처 다이어그램](media/custom-voice/custom-voice-diagram.png)

1. [프로젝트 구독 및 만들기](#set-up-your-azure-account) -Azure 계정을 만들고 음성 서비스 구독을 만듭니다. 이 통합 구독은 음성 텍스트, 텍스트 음성 변환, 음성 번역 및 사용자 지정 음성 포털에 대 한 액세스를 제공 합니다. 그런 다음, 음성 서비스 구독을 사용 하 여 첫 번째 사용자 지정 음성 프로젝트를 만듭니다.

2. [데이터 업로드](how-to-custom-voice-create-voice.md#upload-your-datasets) -사용자 지정 음성 포털 또는 사용자 지정 음성 API를 사용 하 여 데이터 (오디오 및 텍스트)를 업로드 합니다. 포털에서 발음 점수와 신호 대 노이즈 비율을 조사 하 고 평가할 수 있습니다. 자세한 내용은 [사용자 지정 음성을 위한 데이터를 준비 하는 방법](how-to-custom-voice-prepare-data.md)을 참조 하세요.

3. [모델 학습](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – 데이터를 사용 하 여 사용자 지정 텍스트 음성 변환 음성 모델을 만듭니다. 모델을 다른 언어로 학습 시킬 수 있습니다. 학습 후 모델을 테스트 하 고 결과에 만족 하는 경우 모델을 배포할 수 있습니다.

4. [모델 배포](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) -텍스트 음성 변환 음성 모델에 대 한 사용자 지정 끝점을 만들어 제품, 도구 및 응용 프로그램의 음성 합성에 사용 합니다.

## <a name="custom-neural-voices"></a>사용자 지정 신경망

신경망 사용자 지정 기능은 현재 공개 미리 보기로 제공 되며 선택 된 고객으로 제한 됩니다. 이 [응용 프로그램 양식을](https://go.microsoft.com/fwlink/?linkid=2108737) 작성 하 여 시작 하세요.

> [!NOTE]
> Microsoft는 책임이 있는 AI 디자인을 위한 노력으로 개인 및 사회의 권리를 보호 하 고 사용자 컴퓨터의 투명 한 상호 작용을 촉진 합니다. 따라서 사용자 지정 신경망은 일반적으로 모든 고객에 게 제공 되지 않습니다. 응용 프로그램을 검토 하 고 윤리 원칙에 따라이를 사용 하도록 커밋한 후에만 기술에 대 한 액세스 권한을 얻을 수 있습니다. [응용 프로그램 제어 프로세스](https://aka.ms/custom-neural-gating-overview)에 대해 자세히 알아보세요.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

Custom Speech 포털을 사용 하 여 사용자 지정 모델을 만들려면 먼저 음성 서비스 구독이 필요 합니다. Azure에서 음성 서비스 구독을 만들려면 다음 지침을 따르세요. Azure 계정이 없는 경우 새 계정을 등록할 수 있습니다.  

Azure 계정과 음성 서비스 구독을 만든 후에는 사용자 지정 음성 포털에 로그인 하 여 구독을 연결 해야 합니다.

1. Azure Portal에서 음성 서비스 구독 키를 가져옵니다.
2. [사용자 지정 음성 포털](https://aka.ms/custom-voice)에 로그인 합니다.
3. 구독을 선택 하 고 음성 프로젝트를 만듭니다.
4. 다른 음성 구독으로 전환 하려면 위쪽 탐색에 있는 코그 아이콘을 사용 합니다.

> [!NOTE]
> 서비스를 사용 하려면 먼저 Azure에서 F0 또는 S0 키를 만들어야 합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점과 같은 콘텐츠는 사용자 지정 음성 포털에서 **프로젝트로** 구성 됩니다. 각 프로젝트는 국가/언어 및 만들려는 음성의 성별에만 적용 됩니다. 예를 들어 미국 (en-us)에서 영어를 사용 하는 콜 센터의 채팅 봇에 대해 여성 용 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **텍스트 음성 변환/사용자 지정 음성** 탭을 선택한 다음 **새 프로젝트**를 클릭 합니다. 마법사에서 제공 하는 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터**, **학습**, **테스트**및 **배포**의 네 가지 탭이 표시 됩니다. [다음 단계](#next-steps) 에서 제공 된 링크를 사용 하 여 각 탭을 사용 하는 방법을 알아보세요.

> [!IMPORTANT]
> [사용자 지정 음성 포털이](https://aka.ms/custom-voice) 최근에 업데이트 되었습니다. CRIS.ai 포털에서 또는 Api를 사용 하 여 이전 데이터, 모델, 테스트 및 게시 된 끝점을 만든 경우 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 음성 데이터 준비](how-to-custom-voice-prepare-data.md)
- [사용자 지정 음성 만들기](how-to-custom-voice-create-voice.md)
- [가이드: 음성 샘플 기록](record-custom-voice-samples.md)
