---
title: 사용자 지정 음성-음성 서비스에 대 한 데이터 품질 검사
titlesuffix: Azure Cognitive Services
description: 사용자 지정 음성 오디오 데이터 해당 인식 결과와 비교 하 여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공 합니다. 사용자 지정 음성 포털에서 업로드 된 오디오를 재생 하 고 제공 된 인식 결과가 올바른지 확인 하는 경우 확인할 수 있습니다.  이 도구를 사용 하면 신속 하 게 모든 오디오 데이터를 기록 하지 않고도 사용자 지정 모델을 학습 된 Microsoft의 기본 음성-텍스트 모델의 품질을 검사할 수 있습니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026713"
---
# <a name="inspect-custom-speech-data"></a>사용자 지정 음성 데이터를 검사 합니다.

> [!NOTE]
> 이 페이지 읽었다고 가정 [사용자 지정 음성에 대 한 테스트 데이터 준비](how-to-custom-speech-test-data.md) 및 검사에 대 한 데이터 집합을 업로드 합니다.

사용자 지정 음성 오디오 데이터 해당 인식 결과와 비교 하 여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공 합니다. 사용자 지정 음성 포털에서 업로드 된 오디오를 재생 하 고 제공 된 인식 결과가 올바른지 확인 하는 경우 확인할 수 있습니다. 이 도구를 사용 하면 신속 하 게 모든 오디오 데이터를 기록 하지 않고도 사용자 지정 모델을 학습 된 Microsoft의 기본 음성-텍스트 모델의 품질을 검사할 수 있습니다.

이 문서를 시각적으로 이전에 업로드 하는 학습 데이터를 사용 하 여 모델의 품질을 검사 하는 방법을 배웁니다.

이 페이지에서는 Microsoft의 기본 음성-텍스트 모델 및/또는 학습 하는 사용자 지정 모델의 품질을 시각적으로 검사 하는 방법을 배웁니다. 에 업로드 한 데이터를 사용 합니다 **데이터** 테스트에 대 한 탭 합니다.

## <a name="create-a-test"></a>테스트 만들기

테스트를 만들려면 이러한 지침을 따릅니다.

1. 이동할 **음성-텍스트 > 사용자 지정 음성 > 테스트**합니다.
2. 클릭 **테스트 추가**합니다.
3. 선택 **품질 (오디오 전용 데이터)를 검사**합니다. 테스트 이름, 설명, 제공 및 오디오 데이터 집합을 선택 합니다.
4. 테스트 하려는 하는 최대 두 개의 모델을 선택 합니다.
5. **만들기**를 클릭합니다.

테스트를 만든 후에 성공적으로, 병렬 모델을 비교할 수 있습니다.

## <a name="side-by-side-model-comparisons"></a>Side-by-side-모델 비교

테스트 상태가 *Succeeded*, 테스트의 세부 정보를 보려면 테스트 항목 이름을 클릭 합니다. 이 세부 정보 페이지에 제출 된 데이터 집합에서 기록 함께 두 모델의 인식 결과 나타내는 데이터 집합에 모든 길이 발언을 나열 합니다.

Side-by-side-비교 검사를 위해 삽입, 삭제 및 대체를 비롯 한 여러 가지 오류 형식을 전환할 수 있습니다. 오디오 듣기 (사람이 레이블이 지정 된 기록 및 두 가지 음성-텍스트 모델의 결과 표시) 하는 각 열에서 인식 결과 비교 하는 여 요구를 충족 하는 모델 및 향상 된 필요한 위치를 결정할 수 있습니다.

품질 테스트 검사 하는 것은 음성 인식 끝점의 품질 응용 프로그램에 대 한 충분 한 경우의 유효성을 검사 하는 데 유용 합니다.  오디오 기록된, 필요한 정확도 측정 하는 목표에 대 한 테스트에 있는 지침을 따르세요. 정확도 평가 합니다.

## <a name="next-steps"></a>다음 단계

* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

* [사용자 지정 음성에 대 한 테스트 데이터를 준비 합니다.](how-to-custom-speech-test-data.md)
