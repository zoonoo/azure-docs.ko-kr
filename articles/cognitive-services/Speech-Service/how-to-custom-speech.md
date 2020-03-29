---
title: 사용자 지정 음성 - 음성 서비스 시작하기
titleSuffix: Azure Cognitive Services
description: 사용자 지정 음성은 응용 프로그램, 도구 및 제품에 대한 음성-텍스트 정확도를 평가하고 개선할 수 있는 온라인 도구 집합입니다. 시작하기 위해 필요한 것은 소수의 테스트 오디오 파일뿐입니다. 아래 링크를 따라 사용자 지정 음성-텍스트 환경 만들기를 시작합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: e30587ed4d09ba792516ebc7b16529a6ee5ac57c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918957"
---
# <a name="what-is-custom-speech"></a>사용자 지정 음성이란 무엇입니까?

[사용자 지정 음성은](https://aka.ms/customspeech) 응용 프로그램, 도구 및 제품에 대한 Microsoft의 음성-텍스트 정확도를 평가하고 개선할 수 있는 온라인 도구 집합입니다. 시작하기 위해 필요한 것은 소수의 테스트 오디오 파일뿐입니다. 아래 링크를 따라 사용자 지정 음성-텍스트 환경 만들기를 시작합니다.

## <a name="whats-in-custom-speech"></a>맞춤 스피치에는 무엇이 있습니까?

사용자 지정 음성으로 작업을 수행하려면 Azure 계정과 음성 서비스 구독이 필요합니다. 계정이 있으면 데이터를 준비하고, 모델을 교육 및 테스트하고, 인식 품질을 검사하고, 정확도를 평가하고, 궁극적으로 사용자 지정 음성-텍스트 모델을 배포하고 사용할 수 있습니다.

이 다이어그램은 사용자 [지정 음성 포털을](https://aka.ms/customspeech)구성하는 부분을 강조 표시됩니다. 각 단계에 대해 자세히 알아보려면 아래 링크를 사용하십시오.

![사용자 지정 음성 포털을 구성하는 다양한 구성 요소를 강조 표시됩니다.](./media/custom-speech/custom-speech-overview.png)

1. [프로젝트 구독 및 만들기](#set-up-your-azure-account) - Azure 계정을 만들고 음성 서비스에 가입합니다. 이 통합 구독을 통해 음성-텍스트, 텍스트 음성 변환, 음성 변환 및 [사용자 지정 음성 포털에](https://speech.microsoft.com/customspeech)액세스할 수 있습니다. 그런 다음 음성 서비스 구독을 사용하여 첫 번째 사용자 지정 음성 프로젝트를 만듭니다.

2. [테스트 데이터 업로드](how-to-custom-speech-test-data.md) - 테스트 데이터(오디오 파일)를 업로드하여 응용 프로그램, 도구 및 제품에 대한 Microsoft의 음성-텍스트 오퍼링을 평가합니다.

3. [인식 품질 검사](how-to-custom-speech-inspect-data.md) - [사용자 지정 음성 포털을](https://speech.microsoft.com/customspeech) 사용하여 업로드된 오디오를 재생하고 테스트 데이터의 음성 인식 품질을 검사합니다. 정량적 측정의 경우 [데이터 검사를](how-to-custom-speech-inspect-data.md)참조하십시오.

4. [정확도 평가](how-to-custom-speech-evaluate-data.md) - 음성-텍스트 모델의 정확도를 평가합니다. [사용자 지정 음성 포털은](https://speech.microsoft.com/customspeech) 추가 교육이 필요한지 확인하는 데 사용할 수 있는 Word *오류율을*제공합니다. 정확도가 만족스니요, 음성 서비스 API를 직접 사용할 수 있습니다. 정확도를 5% - 20%로 향상하려면 포털의 **교육** 탭을 사용하여 사람이 표시한 성적 증명서 및 관련 텍스트와 같은 추가 교육 데이터를 업로드할 수 있습니다.

5. [모델 학습](how-to-custom-speech-train-model.md) - 오디오 테스트 데이터와 함께 서면 성적 증명서(10-1,000시간) 및 관련 텍스트(<200MB)를 제공하여 음성-텍스트 모델의 정확도를 향상시킵니다. 이 데이터는 음성-텍스트 모델을 학습하는 데 도움이 됩니다. 교육 후 다시 테스트하고 결과가 만족스럽다면 모델을 배포할 수 있습니다.

6. [모델 배포](how-to-custom-speech-deploy-model.md) - 음성-텍스트 모델에 대한 사용자 지정 끝점을 만들고 응용 프로그램, 도구 또는 제품에 사용합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

[사용자 지정 음성 포털을](https://speech.microsoft.com/customspeech) 사용하여 사용자 지정 모델을 만들려면 음성 서비스 구독이 필요합니다. 다음 지침에 따라 표준 음성 서비스 구독 만들기: [음성 구독 만들기.](get-started.md#new-resource)

> [!NOTE]
> 표준(S0) 구독을 생성해야 하며 무료 평가판(F0) 구독은 지원되지 않습니다.

Azure 계정 및 음성 서비스 구독을 만든 후에는 사용자 지정 [음성 포털에](https://speech.microsoft.com/customspeech) 로그인하고 구독을 연결해야 합니다.

1. Azure 포털에서 음성 서비스 구독 키를 가져옵니다.
2. [사용자 지정 음성 포털에](https://aka.ms/custom-speech)로그인합니다.
3. 작업하는 데 필요한 구독을 선택하고 음성 프로젝트를 만듭니다.
4. 구독을 수정하려면 상단 탐색에 있는 **톱니바퀴** 아이콘을 사용합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점과 같은 콘텐츠는 [사용자 지정 음성 포털의](https://speech.microsoft.com/customspeech) **프로젝트로** 구성됩니다. 각 프로젝트는 도메인 및 국가/언어에 따라 다릅니다. 예를 들어 미국에서 영어를 사용하는 콜 센터용 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **음성-텍스트/사용자 지정 음성을**선택한 다음 **새 프로젝트를**클릭합니다. 마법사에서 제공한 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터,** **테스트,** **교육**및 **배포의**네 가지 탭이 표시됩니다. [다음 단계에](#next-steps) 제공된 링크를 사용하여 각 탭을 사용하는 방법을 알아봅니다.

> [!IMPORTANT]
> [사용자 지정 음성 포털이](https://aka.ms/custom-speech) 최근에 업데이트되었습니다! CRIS.ai 포털 또는 API를 사용하여 이전 데이터, 모델, 테스트 및 게시된 끝점을 만든 경우 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결해야 합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
