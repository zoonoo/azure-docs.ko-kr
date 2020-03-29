---
title: 사용자 정의 음성으로 합성 개선 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 커스텀 보이스는 브랜드를 위해 인식할 수 있는 유일무이한 음성을 만들 수 있는 온라인 도구 세트입니다. 시작하기 위해 필요한 것은 소수의 오디오 파일과 관련 전사뿐입니다. 아래 링크를 따라 사용자 지정 음성-텍스트 환경 만들기를 시작합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: 786cf687e569af172eabfaa9068ae61ffd6b2988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221418"
---
# <a name="get-started-with-custom-voice"></a>Custom Voice 시작

[커스텀 보이스는](https://aka.ms/customvoice) 브랜드를 위해 인식할 수 있는 유일무이한 음성을 만들 수 있는 온라인 도구 세트입니다. 시작하기 위해 필요한 것은 소수의 오디오 파일과 관련 전사뿐입니다. 아래 링크를 따라 사용자 지정 텍스트 음성 변환 환경을 만듭니다.

## <a name="whats-in-custom-voice"></a>사용자 정의 음성에 무엇입니까?

사용자 지정 음성으로 시작하기 전에 Azure 계정과 음성 서비스 구독이 필요합니다. 계정을 만든 후에는 데이터를 준비하고, 모델을 학습 및 테스트하고, 음성 품질을 평가하고, 궁극적으로 사용자 지정 음성 모델을 배포할 수 있습니다.

아래 다이어그램은 사용자 지정 음성 [포털을](https://aka.ms/customvoice)사용하여 사용자 지정 음성 모델을 만드는 단계를 강조 합니다. 링크를 사용하여 자세히 알아보십시오.

![사용자 지정 음성 아키텍처 다이어그램](media/custom-voice/custom-voice-diagram.png)

1. [프로젝트를 구독하고 만들기](#set-up-your-azure-account) - Azure 계정을 만들고 음성 서비스 구독을 만듭니다. 이 통합 구독을 사용하면 음성-텍스트, 텍스트 음성 변환, 음성 변환 및 사용자 지정 음성 포털에 액세스할 수 있습니다. 그런 다음 음성 서비스 구독을 사용하여 첫 번째 사용자 지정 음성 프로젝트를 만듭니다.

2. [데이터 업로드](how-to-custom-voice-create-voice.md#upload-your-datasets) - 사용자 지정 음성 포털 또는 사용자 지정 음성 API를 사용하여 데이터(오디오 및 텍스트)를 업로드합니다. 포털에서 발음 점수와 신호 대 잡음 비율을 조사하고 평가할 수 있습니다. 자세한 내용은 [사용자 지정 음성에 대한 데이터를 준비하는 방법을](how-to-custom-voice-prepare-data.md)참조하십시오.

3. [모델 교육](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) - 데이터를 사용하여 사용자 지정 텍스트 음성 변환 모델을 만듭니다. 다른 언어로 모델을 학습할 수 있습니다. 교육 후 모델을 테스트하고 결과가 만족스럽다면 모델을 배포할 수 있습니다.

4. [모델 배포](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 텍스트 음성 변환 음성 모델에 대한 사용자 지정 끝점을 만들고 제품, 도구 및 응용 프로그램에서 음성 합성에 사용합니다.

## <a name="custom-neural-voices"></a>사용자 지정 신경 음성

신경음성 사용자 지정 기능은 현재 공개 미리 보기중이며 일부 고객으로 제한됩니다. 시작하려면 이 [신청서를](https://go.microsoft.com/fwlink/?linkid=2108737) 작성하십시오.

> [!NOTE]
> 책임감 있는 AI를 설계하려는 Microsoft의 노력의 일환으로 개인과 사회의 권리를 보호하고 투명한 인간과 컴퓨터 상호 작용을 촉진하는 것이 당사의 의도입니다. 이러한 이유로 사용자 지정 신경 음성은 모든 고객에게 일반적으로 제공되지 않습니다. 신청서를 검토하고 윤리 원칙에 따라 이 기술을 사용하기로 약속한 후에만 기술에 액세스할 수 있습니다. 우리의 응용 [프로그램 게이팅 프로세스에](https://aka.ms/custom-neural-gating-overview)대해 자세히 알아보십시오.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

사용자 지정 음성 포털을 사용하여 사용자 지정 모델을 만들려면 음성 서비스 구독이 필요합니다. Azure에서 음성 서비스 구독을 만들려면 다음 지침을 따릅니다. Azure 계정이 없는 경우 새 계정에 등록할 수 있습니다.  

Azure 계정 및 음성 서비스 구독을 만든 후에는 사용자 지정 음성 포털에 로그인하고 구독을 연결해야 합니다.

1. Azure 포털에서 음성 서비스 구독 키를 가져옵니다.
2. 사용자 지정 [음성 포털에](https://aka.ms/custom-voice)로그인합니다.
3. 구독을 선택하고 음성 프로젝트를 만듭니다.
4. 다른 음성 구독으로 전환하려면 상단 탐색에 있는 톱니바퀴 아이콘을 사용합니다.

> [!NOTE]
> 사용자 지정 음성 서비스는 30일 무료 평가판 키를 지원하지 않습니다. 서비스를 사용하려면 먼저 Azure에서 만든 F0 또는 S0 키가 있어야 합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점과 같은 콘텐츠는 사용자 지정 음성 포털의 **프로젝트로** 구성됩니다. 각 프로젝트는 국가/언어 및 만들려는 음성의 성별에 따라 다릅니다. 예를 들어 미국(en-US)에서 영어를 사용하는 콜 센터의 채팅 봇에 여성 음성 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **텍스트 음성 변환/사용자 지정 음성** 탭을 선택한 다음 새 **프로젝트를**클릭합니다. 마법사에서 제공한 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터,** **교육,** **테스트**및 **배포의**네 가지 탭이 표시됩니다. [다음 단계에](#next-steps) 제공된 링크를 사용하여 각 탭을 사용하는 방법을 알아봅니다.

> [!IMPORTANT]
> [사용자 지정 음성 포털이](https://aka.ms/custom-voice) 최근에 업데이트되었습니다! CRIS.ai 포털 또는 API를 사용하여 이전 데이터, 모델, 테스트 및 게시된 끝점을 만든 경우 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결해야 합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 음성 데이터 준비](how-to-custom-voice-prepare-data.md)
- [사용자 지정 음성 만들기](how-to-custom-voice-create-voice.md)
- [가이드: 음성 샘플 녹음](record-custom-voice-samples.md)
