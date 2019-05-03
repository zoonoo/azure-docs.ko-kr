---
title: 사용자 지정 음성-음성 서비스 시작
titlesuffix: Azure Cognitive Services
description: 사용자 지정 음성을 평가 하 여 응용 프로그램, 도구 및 제품에 대 한 Microsoft의 음성-텍스트 정확도 향상 시킬 수 있는 온라인 도구 집합이 있습니다. 시작 하는 데 걸리는 모든는 소수의 테스트 오디오 파일입니다. 사용자 지정 음성-텍스트 환경을 만들기를 시작 하려면 아래 링크를 따르세요.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: f2441d5660ed84a8680c1861e2e20a1a17ee4762
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026715"
---
# <a name="what-is-custom-speech"></a>사용자 지정 음성 란?

[사용자 지정 음성](https://aka.ms/custom-speech) 를 평가 하 여 응용 프로그램, 도구 및 제품에 대 한 Microsoft의 음성-텍스트 정확도 향상 시킬 수 있는 온라인 도구 집합이 있습니다. 시작 하는 데 걸리는 모든는 소수의 테스트 오디오 파일입니다. 사용자 지정 음성-텍스트 환경을 만들기를 시작 하려면 아래 링크를 따르세요.

## <a name="whats-in-custom-speech"></a>사용자 지정 음성에 대 한 기능

사용자 지정 음성으로 작업을 수행 하려면, 먼저 Azure 계정 및 Speech Services 구독 해야 합니다. 계정을 했으면 데이터 준비, 학습 및 모델을 테스트 합니다, 인식 품질 검사, 정확도 평가 하 고 궁극적으로 배포한 사용자 지정 음성-텍스트 모델을 사용 합니다.

이 다이어그램에는 사용자 지정 음성 포털을 구성 하는 부분 강조 표시 합니다. 각 단계에 자세히 알아보려면 아래 링크를 사용 합니다.

![사용자 지정 음성 포털을 구성 하는 다양 한 구성 요소를 강조 표시 합니다.](./media/custom-speech/custom-speech-overview.png)

1. [프로젝트를 만들고 구독](#set-up-your-azure-account) -Azure 계정을 만들고 음성 서비스를 구독 합니다. 이 통합된 구독 음성-텍스트, 텍스트 음성 변환, 음성 번역 및 사용자 지정 음성 포털에 액세스할 수 있습니다. 그런 다음 첫 번째 사용자 지정 음성 프로젝트를 만들 음성 서비스 구독을 사용 합니다.

2. [테스트 데이터 업로드](how-to-custom-speech-test-data.md) -Microsoft의 음성-텍스트 응용 프로그램, 도구 및 제품에 대 한 제품 평가에 테스트 데이터 업로드 (오디오 파일).

3. [인식 품질 검사](how-to-custom-speech-inspect-data.md) -사용자 지정 음성 포털을 사용 하 여 업로드 된 오디오를 재생 하 여 테스트 데이터의 음성 인식 품질 검사를 합니다. 정량 측정값을 참조 하세요 [데이터를 검사](how-to-custom-speech-inspect-data.md)합니다.

4. [정확도 평가](how-to-custom-speech-evaluate-data.md) -음성-텍스트 모델의 정확도 평가 합니다. 사용자 지정 음성 포털에서 제공 된 *Word 오류율*, 추가 교육 필요한 지 확인에 사용할 수 있는 합니다. 정확도 만족 하는 경우에 음성 서비스 Api를 직접 사용할 수 있습니다. 5%-20%를 상대 평균으로 정확도 개선 하려는 경우 사용 합니다 **교육** 사람이 레이블이 지정 된 기록 및 관련 된 텍스트와 같은 추가 학습 데이터를 업로드 하는 포털에서 탭 합니다.

5. [모델을 학습](how-to-custom-speech-train-model.md) -작성 된 대 본 (10-1,000 시간)를 제공 하 여 음성-텍스트 모델의 정확도 향상 하 고 텍스트와 관련 된 (< 200MB) 오디오와 함께 데이터를 테스트 합니다. 이 데이터 음성-텍스트 모델을 학습 하는 데 도움이 됩니다. 학습 한 후 다시, 테스트 및 결과 만족 하는 경우에 모델을 배포할 수 있습니다.

6. [모델 배포](how-to-custom-speech-deploy-model.md) -음성-텍스트 모델에 대 한 사용자 지정 끝점을 만들고 응용 프로그램, 도구 또는 제품에 사용 합니다.

## <a name="set-up-your-azure-account"></a>Azure 계정 설정

사용자 지정 음성 포털을 사용 하 여 사용자 지정 모델을 만들기 전에 음성 서비스 구독이 필요 합니다. 표준 음성 서비스 구독을 만들려면 이러한 지침을 따릅니다. [음성 구독 만들기](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#new-azure-account)합니다.

> [!NOTE]
> 표준 (S0) 구독을 만드는 해야 합니다 (F0) 무료 평가판 구독은 지원 되지 않습니다.

Azure 계정 및 음성 서비스 구독을 만든 후에 사용자 지정 음성 포털에 로그인 하 여 구독을 연결 하는 것이 해야 합니다.

1. Azure portal에서 음성 서비스 구독 키를 가져옵니다.
2. 에 로그인 합니다 [사용자 지정 음성 포털](https://aka.ms/custom-speech)합니다.
3. 작업 해야 하는 구독 및 음성 프로젝트 만들기를 선택 합니다.
4. 사용 하 여 구독을 수정 하려는 경우는 **코그** 아이콘에 위쪽 탐색에서 합니다.

## <a name="how-to-create-a-project"></a>프로젝트를 만드는 방법

데이터, 모델, 테스트 및 끝점으로 구성 됩니다와 같은 콘텐츠 **프로젝트** 사용자 지정 음성 포털에서 합니다. 각 프로젝트는 도메인 및 국가/언어에 따라 다릅니다. 예를 들어 미국에서 영어를 사용 하는 콜 센터에 대 한 프로젝트를 만들 수 있습니다.

첫 번째 프로젝트를 만들려면 선택 합니다 **음성-에-텍스트/사용자 지정 음성**, 클릭 **새 프로젝트**합니다. 프로젝트를 만드는 마법사에서 제공한 지침을 따릅니다. 프로젝트를 만든 후 4 개 탭이 표시 됩니다. **데이터**, **테스트**합니다 **학습**, 및 **배포**합니다. 에 나와 있는 링크를 사용 하 여 [다음 단계](#next-steps) 각 탭을 사용 하는 방법입니다.

## <a name="next-steps"></a>다음 단계

* [준비 및 테스트 데이터](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
