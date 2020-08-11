---
title: Custom Speech-Speech service 시작
titleSuffix: Azure Cognitive Services
description: Custom Speech는 응용 프로그램, 도구 및 제품에 대 한 음성-텍스트 정확도를 평가 하 고 개선할 수 있는 온라인 도구 집합입니다. 시작 하는 데 사용 되는 모든 작업은 몇 가지 테스트 오디오 파일입니다. 사용자 지정 음성-텍스트 환경 만들기를 시작 하려면 아래 링크를 따르세요.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: b9ac473c3864b4be06c978e7d6a3555bb4550d2a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056842"
---
# <a name="what-is-custom-speech"></a>Custom Speech 이란?

[Custom Speech](https://aka.ms/customspeech) 는 응용 프로그램, 도구 및 제품에 대 한 Microsoft의 음성-텍스트 정확도를 평가 하 고 향상 시킬 수 있는 온라인 도구 집합입니다. 시작 하는 데 사용 되는 모든 작업은 몇 가지 테스트 오디오 파일입니다. 사용자 지정 음성-텍스트 환경 만들기를 시작 하려면 아래 링크를 따르세요.

## <a name="whats-in-custom-speech"></a>Custom Speech은 무엇 인가요?

Custom Speech를 사용 하 여 모든 작업을 수행 하려면 Azure 계정 및 음성 서비스 구독이 필요 합니다. 계정을 받은 후에는 데이터를 준비 하 고, 모델을 학습 및 테스트 하 고, 인식 품질을 검사 하 고, 정확성을 평가 하 고, 궁극적으로 사용자 지정 음성-텍스트 모델을 배포 하 고 사용할 수 있습니다.

이 다이어그램은 [Custom Speech 포털](https://aka.ms/customspeech)을 구성 하는 부분을 강조 표시 합니다. 각 단계에 대 한 자세한 내용을 보려면 아래 링크를 사용 하세요.

![Custom Speech 포털을 구성 하는 다양 한 구성 요소를 강조 표시 합니다.](./media/custom-speech/custom-speech-overview.png)

1. [프로젝트 구독 및 만들기](#set-up-your-azure-account) -Azure 계정을 만들고 음성 서비스를 구독 합니다. 이 통합 구독은 음성 텍스트, 텍스트 음성 변환, 음성 번역 및 [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 대 한 액세스를 제공 합니다. 그런 다음 음성 서비스 구독을 사용 하 여 첫 번째 Custom Speech 프로젝트를 만듭니다.

2. [테스트 데이터](how-to-custom-speech-test-data.md) 업로드-테스트 데이터 (오디오 파일)를 업로드 하 여 응용 프로그램, 도구 및 제품에 대 한 Microsoft의 음성 텍스트 제품을 평가 합니다.

3. [인식 품질 검사](how-to-custom-speech-inspect-data.md) - [Custom Speech 포털](https://speech.microsoft.com/customspeech) 을 사용 하 여 업로드 된 오디오를 재생 하 고 테스트 데이터의 음성 인식 품질을 검사 합니다. 양적 측정에 대해서는 [데이터 검사](how-to-custom-speech-inspect-data.md)를 참조 하세요.

4. [정확도 평가](how-to-custom-speech-evaluate-data.md) -음성 텍스트 모델의 정확도를 평가 합니다. [Custom Speech 포털](https://speech.microsoft.com/customspeech) 은 추가 교육이 필요한 지 여부를 결정 하는 데 사용할 수 있는 *단어 오류 요금*을 제공 합니다. 정확도에 만족 하는 경우 음성 서비스 Api를 직접 사용할 수 있습니다. 5%-20%의 상대적 평균으로 정확도를 높이려면 포털의 **학습** 탭을 사용 하 여 사용자 레이블이 지정 된 성적 증명서 및 관련 텍스트와 같은 추가 학습 데이터를 업로드 하세요.

5. [정확도 향상](how-to-custom-speech-improve-accuracy.md) -시나리오에 따라 추가 학습 데이터를 전략적으로 선택 하 여 음성-텍스트 모델의 품질을 향상 시킵니다.

6. [모델 학습](how-to-custom-speech-train-model.md) -오디오 테스트 데이터와 함께 기록 된 기록 (10-1000 시간) 및 관련 텍스트 (<200 MB)를 제공 하 여 음성-텍스트 모델의 정확도를 높입니다. 이 데이터는 음성 텍스트 모델을 학습 하는 데 도움이 됩니다. 학습 후 다시 테스트 하 고 결과에 만족 하는 경우 모델을 배포할 수 있습니다.

7. [모델 배포](how-to-custom-speech-deploy-model.md) -음성 텍스트 모델에 대 한 사용자 지정 끝점을 만들어 응용 프로그램, 도구 또는 제품에서 사용 합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

[Custom Speech 포털](https://speech.microsoft.com/customspeech) 을 사용 하 여 사용자 지정 모델을 만들려면 먼저 음성 서비스 구독이 필요 합니다. 표준 음성 서비스 구독을 만들려면 다음 지침을 따르세요. [음성 구독을 만듭니다](get-started.md#new-resource).

> [!NOTE]
> 표준 (S0) 구독을 만들어야 합니다. 무료 (F0) 구독은 지원 되지 않습니다.

Azure 계정과 음성 서비스 구독을 만든 후에는 [Custom Speech 포털](https://speech.microsoft.com/customspeech) 에 로그인 하 여 구독을 연결 해야 합니다.

1. Azure Portal에서 음성 서비스 구독 키를 가져옵니다.
2. [Custom Speech 포털](https://aka.ms/custom-speech)에 로그인 합니다.
3. 작업 해야 하는 구독을 선택 하 고 음성 프로젝트를 만들어야 합니다.
4. 구독을 수정 하려면 위쪽 탐색에 있는 **코그** 아이콘을 사용 합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점과 같은 콘텐츠는 [Custom Speech 포털](https://speech.microsoft.com/customspeech)에서 **프로젝트로** 구성 됩니다. 각 프로젝트는 도메인 및 국가/언어에만 적용 됩니다. 예를 들어 미국의 영어를 사용 하는 콜 센터에 대 한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **음성 텍스트/사용자 지정 음성을**선택한 다음 **새 프로젝트**를 클릭 합니다. 마법사에서 제공 하는 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터**, **테스트**, **학습**및 **배포**의 네 가지 탭이 표시 됩니다. [다음 단계](#next-steps) 에서 제공 된 링크를 사용 하 여 각 탭을 사용 하는 방법을 알아보세요.

> [!IMPORTANT]
> [Custom Speech 포털이](https://aka.ms/custom-speech) 최근에 업데이트 되었습니다. CRIS.ai 포털에서 또는 Api를 사용 하 여 이전 데이터, 모델, 테스트 및 게시 된 끝점을 만든 경우 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [데이터 평가](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
