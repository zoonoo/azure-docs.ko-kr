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
ms.openlocfilehash: eff51c8568ce82c9d8d21bff7a2ba079c291679c
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007298"
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

사용자 지정 음성은 현재 표준 계층과 신경망을 모두 지원 합니다. 사용자 지정 신경망은 사용자가 더 적은 데이터를 요구 하는 동시에 고품질의 음성 모델을 빌드할 수 있도록 지원 하 고 AI를 강력한 방식으로 배포할 수 있는 방법을 제공 합니다 사용자 지정 신경망을 사용 하 여 보다 자연 스러운 대화형 인터페이스에 대해 보다 현실적인 음성을 개발 하 고 고객과 최종 사용자가 책임을 지는 최신 텍스트 음성 변환 기술을 활용 하는 것이 좋습니다. [사용자 지정 신경망에 대해 자세히 알아보세요](https://aka.ms/CNV-Transparency-Note). 

> [!NOTE]
> Microsoft는 책임이 있는 AI 디자인의 일환으로 사용자 지정 신경망 사용을 제한 했습니다. 응용 프로그램을 검토 하 고이를 담당 AI 원칙에 따라 사용 하도록 커밋한 후에만 기술에 대 한 액세스 권한을 얻을 수 있습니다. 액세스 제한 및 여기 [에 적용에서 정책](https://aka.ms/gating-overview) 에 대해 [](https://aka.ms/customneural)자세히 알아보세요. 표준 및 신경망 버전의 사용자 지정 음성에 대해 지원 되는 [언어](language-support.md#customization) 와 [지역은](regions.md#custom-voices) 다릅니다. 시작 하기 전에 세부 정보를 확인 합니다.  

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

Custom Speech 포털을 사용 하 여 사용자 지정 모델을 만들려면 먼저 음성 서비스 구독이 필요 합니다. Azure에서 음성 서비스 구독을 만들려면 다음 지침을 따르세요. Azure 계정이 없는 경우 새 계정을 등록할 수 있습니다.  

Azure 계정과 음성 서비스 구독을 만든 후에는 사용자 지정 음성 포털에 로그인 하 여 구독을 연결 해야 합니다.

1. Azure Portal에서 음성 서비스 구독 키를 가져옵니다.
2. [사용자 지정 음성 포털](https://aka.ms/custom-voice)에 로그인 합니다.
3. 구독을 선택 하 고 음성 프로젝트를 만듭니다.
4. 다른 음성 구독으로 전환 하려면 위쪽 탐색에 있는 코그 아이콘을 사용 합니다.

> [!NOTE]
> 서비스를 사용 하려면 Azure에서 F0 또는 S0 Speech service 키를 만들어야 합니다. 사용자 지정 신경망은 S0 계층만 지원 합니다. 

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점과 같은 콘텐츠는 사용자 지정 음성 포털에서 **프로젝트로** 구성 됩니다. 각 프로젝트는 국가/언어 및 만들려는 음성의 성별에만 적용 됩니다. 예를 들어 미국 (' en-us ')에서 영어를 사용 하는 콜 센터의 채팅 봇에 대해 여성 용 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **텍스트 음성 변환/사용자 지정 음성** 탭을 선택한 다음 **새 프로젝트** 를 클릭 합니다. 마법사에서 제공 하는 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터**, **학습**, **테스트** 및 **배포** 의 네 가지 탭이 표시 됩니다. [다음 단계](#next-steps) 에서 제공 된 링크를 사용 하 여 각 탭을 사용 하는 방법을 알아보세요.

> [!IMPORTANT]
> [사용자 지정 음성 포털이](https://aka.ms/custom-voice) 최근에 업데이트 되었습니다. CRIS.ai 포털에서 또는 Api를 사용 하 여 이전 데이터, 모델, 테스트 및 게시 된 끝점을 만든 경우 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결 해야 합니다.

## <a name="how-to-migrate-to-custom-neural-voice"></a>사용자 지정 신경망로 마이그레이션하는 방법

비 신경망 또는 표준 사용자 지정 음성을 사용 하는 경우 아래 단계에 따라 사용자 지정 신경망로 마이그레이션하는 것이 좋습니다. 사용자 지정 신경망로 이동 하면 보다 자연 스러운 대화형 인터페이스에 대해 보다 현실적인 음성을 개발 하 고 고객과 최종 사용자가 책임을 지는 최신 텍스트 음성 변환 기술을 활용할 수 있습니다. 

1. 액세스 제한 및 여기 [에 적용에서 정책](https://aka.ms/gating-overview) 에 대해 [](https://aka.ms/customneural)자세히 알아보세요. 사용자 지정 신경망 서비스에 대 한 액세스는 자격 조건에 따라 Microsoft의 유일한 판단을 받습니다. 고객은 응용 프로그램을 검토 한 후에만 기술에 액세스 하 고, [책임 AI 원칙](https://microsoft.com/ai/responsible-ai) 및 [준수 코드](https://aka.ms/custom-neural-code-of-conduct)와 연계 하 여 사용 하기 위해 최선을 다하고 있습니다. 
2. 응용 프로그램이 승인 되 면 "신경망" 학습 기능에 대 한 액세스 권한이 제공 됩니다. 응용 프로그램에서 제공 하는 것과 동일한 Azure 구독을 사용 하 여 [사용자 지정 음성 포털](https://speech.microsoft.com/customvoice) 에 로그인 해야 합니다. 
    > [!IMPORTANT]
    > 음성 인재을 보호 하 고 허가 되지 않은 레코딩 또는 음성 인재 승인 없이 음성 모델의 교육을 방지 하기 위해 고객은 자신의 동의를 제공 하는 음성 인재의 기록 된 문을 업로드 해야 합니다. 기록 스크립트를 준비할 때이 문장을 포함 했는지 확인 합니다. "I [first 및 last name] am은 내 음성 기록을 [회사 이름]에서 사용 하 여 내 음성의 가상 버전을 만들고 사용 하는 것을 인식 합니다."
    > 이 문장은 **음성 인재** 탭에 구두 동의 파일로 업로드 해야 합니다. 이는 학습 데이터 집합의 기록이 동의를 주는 동일한 사람에 의해 수행 되었는지 여부를 확인 하는 데 사용 됩니다.
3. 사용자 지정 신경망 모델을 만든 후에는 새 끝점에 음성 모델을 배포 합니다. 신경망 모델을 사용 하 여 새 사용자 지정 음성 끝점을 만들려면 **텍스트 음성 변환 > 사용자 지정 음성 > 배포** 로 이동 합니다. **모델 배포** 를 선택 하 고 사용자 지정 끝점에 대 한 **이름** 및 **설명** 을 입력 합니다. 그런 다음이 끝점과 연결 하려는 사용자 지정 신경망 모델을 선택 하 고 배포를 확인 합니다.  
4. 새 모델을 사용 하 여 새 끝점을 만든 경우 앱에서 코드를 업데이트 합니다. 

## <a name="next-steps"></a>다음 단계

- [사용자 지정 음성 데이터 준비](how-to-custom-voice-prepare-data.md)
- [사용자 지정 음성 만들기](how-to-custom-voice-create-voice.md)
- [가이드: 음성 샘플 기록](record-custom-voice-samples.md)
