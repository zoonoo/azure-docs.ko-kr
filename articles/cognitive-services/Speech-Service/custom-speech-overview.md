---
title: Custom Speech 개요-음성 서비스
titleSuffix: Azure Cognitive Services
description: Custom Speech는 응용 프로그램, 도구 및 제품에 대 한 Microsoft의 음성-텍스트 정확도를 평가 하 고 개선할 수 있는 온라인 도구 집합입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 39370659e71a7d281914b360eea83eb0b68b25ba
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716570"
---
# <a name="what-is-custom-speech"></a>Custom Speech란?

[Custom Speech](https://aka.ms/customspeech) 는 응용 프로그램 및 제품에 대 한 Microsoft의 음성-텍스트 정확도를 평가 하 고 개선할 수 있는 UI 기반 도구 집합입니다. 시작 하는 데 사용 되는 모든 작업은 몇 가지 테스트 오디오 파일입니다. 사용자 지정 음성-텍스트 환경 만들기를 시작 하려면이 문서의 링크를 따르세요.

## <a name="whats-in-custom-speech"></a>Custom Speech은 무엇 인가요?

Custom Speech를 사용 하 여 모든 작업을 수행 하려면 Azure 계정 및 음성 서비스 구독이 필요 합니다. 계정이 있으면 데이터를 준비 하 고, 모델을 학습 및 테스트 하 고, 인식 품질을 검사 하 고, 정확성을 평가 하 고, 궁극적으로 사용자 지정 음성-텍스트 모델을 배포 하 고 사용할 수 있습니다.

이 다이어그램은 [Speech Studio의 Custom Speech 영역](https://aka.ms/customspeech)을 구성 하는 부분을 강조 합니다. 각 단계에 대 한 자세한 내용을 보려면 아래 링크를 사용 하세요.

![Speech Studio의 Custom Speech 영역을 구성 하는 구성 요소를 강조 표시 하는 다이어그램입니다.](./media/custom-speech/custom-speech-overview.png)

1. [프로젝트를 구독 하 고 만듭니다](#set-up-your-azure-account). Azure 계정을 만들고 음성 서비스를 구독 합니다. 이 통합 구독을 통해 음성 텍스트, 텍스트 음성 변환, 음성 변환 및 [음성 스튜디오](https://speech.microsoft.com/customspeech)에 액세스할 수 있습니다. 그런 다음 음성 서비스 구독을 사용 하 여 첫 번째 Custom Speech 프로젝트를 만듭니다.

1. [테스트 데이터를 업로드](./how-to-custom-speech-test-and-train.md)합니다. 응용 프로그램, 도구 및 제품에 대 한 Microsoft 음성-텍스트 제품을 평가 하는 테스트 데이터 (오디오 파일)를 업로드 합니다.

1. [인식 품질을 검사](how-to-custom-speech-inspect-data.md)합니다. [Speech Studio](https://speech.microsoft.com/customspeech) 를 사용 하 여 업로드 된 오디오를 재생 하 고 테스트 데이터의 음성 인식 품질을 검사 합니다. 양적 측정에 대해서는 [데이터 검사](how-to-custom-speech-inspect-data.md)를 참조 하세요.

1. [정확성을 평가 하 고 개선](how-to-custom-speech-evaluate-data.md)합니다. 음성 텍스트 모델의 정확도를 평가 하 고 개선 합니다. [음성 스튜디오](https://speech.microsoft.com/customspeech) 는 추가 교육이 필요한 지 여부를 결정 하는 데 사용할 수 있는 *단어 오류 요금* 을 제공 합니다. 정확도에 만족 하는 경우 음성 서비스 Api를 직접 사용할 수 있습니다. 5% ~ 20%의 상대적 평균으로 정확도를 높이려면 포털의 **학습** 탭을 사용 하 여 사용자 레이블이 지정 된 성적 증명서 및 관련 텍스트와 같은 추가 학습 데이터를 업로드 합니다.

1. [모델을 학습 하 고 배포](how-to-custom-speech-train-model.md)합니다. 오디오 테스트 데이터와 함께 기록 된 기록 (10-1000 시간) 및 관련 텍스트 (<200 MB)를 제공 하 여 음성-텍스트 모델의 정확도를 높입니다. 이 데이터는 음성 텍스트 모델을 학습 하는 데 도움이 됩니다. 학습 후 다시 테스트 합니다. 결과에 만족 하는 경우 사용자 지정 끝점에 모델을 배포할 수 있습니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

[Speech Studio](https://speech.microsoft.com/customspeech) 를 사용 하 여 사용자 지정 모델을 만들려면 먼저 Azure 계정 및 음성 서비스를 구독 해야 합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> 표준 (S0) 구독을 만들어야 합니다. 무료 (F0) 구독은 지원 되지 않습니다.

**오디오 데이터** 를 사용 하 여 사용자 지정 모델을 학습 하려는 경우 학습에 사용할 수 있는 전용 하드웨어가 있는 다음 지역 중 하나를 선택 합니다. 이렇게 하면 모델을 학습 하는 데 걸리는 시간이 줄어들고 학습에 더 많은 오디오를 사용할 수 있습니다. 이러한 지역에서 음성 서비스는 교육을 위해 최대 20 시간의 오디오를 사용 합니다. 다른 지역에는 최대 8 시간 까지만 사용 됩니다.

* 오스트레일리아 동부
* 캐나다 중부
* 인도 중부
* 미국 동부
* 미국 동부 2
* 미국 중북부
* 북유럽
* 미국 중남부
* 동남아시아
* 영국 남부
* US Gov 애리조나
* US Gov 버지니아
* 서유럽
* 미국 서부 2

Azure 계정과 음성 서비스 구독을 만든 후에는 [Speech Studio](https://speech.microsoft.com/customspeech) 에 로그인 하 여 구독을 연결 해야 합니다.

1. [Speech Studio](https://aka.ms/custom-speech)에 로그인 합니다.
1. 작업 해야 하는 구독을 선택 하 고 음성 프로젝트를 만들어야 합니다.
1. 구독을 수정 하려는 경우 상단 메뉴에서 코그 단추를 선택 합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점과 같은 콘텐츠는 [Speech Studio](https://speech.microsoft.com/customspeech)에서 *프로젝트로* 구성 됩니다. 각 프로젝트는 도메인 및 국가/언어에만 적용 됩니다. 예를 들어 미국의 영어를 사용 하는 콜 센터에 대 한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 **음성 텍스트/사용자 지정 음성** 을 선택한 다음 **새 프로젝트** 를 선택 합니다. 마법사에서 제공 하는 지침에 따라 프로젝트를 만듭니다. 프로젝트를 만든 후에는 **데이터**, **테스트**, **학습** 및 **배포** 의 네 가지 탭이 표시 됩니다. [다음 단계](#next-steps) 에서 제공 된 링크를 사용 하 여 각 탭을 사용 하는 방법을 알아보세요.

> [!IMPORTANT]
> 이전에 "Custom Speech 포털" 이라고 알려진 [Speech Studio](https://aka.ms/custom-speech) 를 최근에 업데이트 했습니다. CRIS.ai 포털에서 또는 Api를 사용 하 여 이전 데이터, 모델, 테스트 및 게시 된 끝점을 만든 경우 새 포털에서 새 프로젝트를 만들어 이러한 이전 엔터티에 연결 해야 합니다.

## <a name="model-lifecycle"></a>모델 수명 주기

Custom Speech는 *기본 모델과* *사용자 지정 모델* 을 모두 사용 합니다. 각 언어에는 하나 이상의 기본 모델이 있습니다. 일반적으로 새 음성 모델을 일반 음성 서비스에 릴리스할 때 새 기본 모델로 Custom Speech 서비스에도 가져옵니다. 3 ~ 6 개월 마다 업데이트 됩니다. 이전 모델은 일반적으로 최신 모델의 정확도가 높기 때문에 일반적으로 시간이 지남에 따라 더 유용 하지 않게 됩니다.

반면, 특정 고객 시나리오에 대해 선택한 기본 모델을 적용 하 여 사용자 지정 모델을 만듭니다. 사용자의 요구를 충족 하는 경우에는 오랜 시간 동안 특정 사용자 지정 모델을 계속 사용할 수 있습니다. 그러나 최신 기본 모델로 정기적으로 업데이트 하 고 추가 데이터를 사용 하 여 시간에 따라 다시 학습 하는 것이 좋습니다. 

모델 수명 주기와 관련 된 다른 주요 용어는 다음과 같습니다.

* **적응**: 기본 모델을 사용 하 고 텍스트 데이터 및/또는 오디오 데이터를 사용 하 여 도메인/시나리오로 사용자 지정 합니다.
* **디코딩**: 모델을 사용 하 고 음성 인식을 수행 합니다 (오디오를 텍스트로 디코딩).
* **끝점**: 지정 된 사용자 *만* 액세스할 수 있는 기본 모델 또는 사용자 지정 모델의 사용자별 배포입니다.

### <a name="expiration-timeline"></a>만료 타임 라인

새 모델 및 새로운 기능을 사용할 수 있게 되 고 이전에는 더 작은 모델을 사용할 수 없게 되 면 모델 및 끝점 만료에 대해 다음 타임 라인을 참조 하세요.

**기본 모델** 

* 적응: 1 년 동안 사용할 수 있습니다. 모델을 가져온 후에는 1 년 동안 사용자 지정 모델을 만드는 데 사용할 수 있습니다. 1 년이 지난 후 새로운 기본 모델 버전에서 새 사용자 지정 모델을 만들어야 합니다.  
* 디코딩: 가져온 후 2 년 동안 사용할 수 있습니다. 따라서이 모델을 사용 하 여 끝점을 만들고 2 년 동안 일괄 처리 기록을 사용할 수 있습니다. 
* 끝점: 디코딩과 동일한 타임 라인에서 사용할 수 있습니다.

**사용자 지정 모델**

* 디코딩: 모델을 만든 후 2 년 동안 사용할 수 있습니다. 따라서 두 연도 (배치/실시간/테스트)에 대해 사용자 지정 모델을 만든 후 사용할 수 있습니다. 2 년 후에는 기본 모델이 일반적으로 적응에 사용 되지 않기 때문에 *모델을 다시 학습 합니다* .  
* 끝점: 디코딩과 동일한 타임 라인에서 사용할 수 있습니다.

기본 모델이 나 사용자 지정 모델이 만료 되 면 항상 *최신 기본 모델 버전* 으로 대체 됩니다. 따라서 구현은 중단 되지 않지만 사용자 지정 모델이 만료에 도달 하면 *특정 데이터* 에 대해 정확 하지 않을 수 있습니다. Speech Studio의 Custom Speech 영역에 있는 다음 위치에서 모델에 대 한 만료를 확인할 수 있습니다.

* 모델 학습 요약
* 모델 학습 세부 정보
* 배포 요약
* 배포 세부 정보

[`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) JSON 응답의 속성 아래에 있는 및 사용자 지정 speech api를 통해 만료 날짜를 확인할 수도 있습니다 `deprecationDates` .

음성 스튜디오의 배포 섹션에 있는 끝점에서 사용 하는 모델을 변경 하거나 사용자 지정 speech API를 통해 사용자 지정 음성 끝점에서 모델을 업그레이드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [데이터 준비 및 테스트](./how-to-custom-speech-test-and-train.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
* [모델 정확도 평가 및 향상](how-to-custom-speech-evaluate-data.md)
* [모델 학습 및 배포](how-to-custom-speech-train-model.md)
