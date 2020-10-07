---
title: 오디오 파일을 사용하여 모델 테스트 - Speech Studio
titleSuffix: Azure Cognitive Services
description: 이 방법에서는 Speech Studio를 사용하여 오디오 파일의 음성 인식을 테스트합니다.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: v-demjoh
ms.openlocfilehash: 9101944a567df5000d3584ed48eff24e4c5e0057
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89565806"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Speech Studio에서 오디오 파일을 사용하여 모델 테스트

이 방법에서는 Speech Studio를 사용하여 음성을 오디오 파일에서 텍스트로 변환합니다. Speech Studio를 사용하면 관련 텍스트, 사람이 레이블 지정된 기록과 오디오, 사용자가 제공하는 발음 지침을 사용하여 음성 인식 모델을 테스트하고, 비교하고, 향상시키고, 배포할 수 있습니다

## <a name="prerequisites"></a>필수 구성 요소

음성 포털을 사용하기 전에 [다음 지침에 따라 Azure 계정을 만들고 Speech service를 구독합니다](../how-to-custom-speech.md#set-up-your-azure-account). 이 통합 구독은 음성 텍스트, 텍스트 음성 변환, 음성 번역 및 Custom Speech 포털에 대한 액세스를 제공합니다.

## <a name="download-an-audio-file"></a>오디오 파일 다운로드

다음 단계에 따라 음성이 포함된 오디오 파일을 다운로드하고 zip 파일로 패키지 합니다.

1. 마우스 오른쪽 단추로 링크를 클릭하고 **[이 링크에서 샘플 wav 파일](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** 을 다운로드 하고 **다른 이름으로 링크 저장하기**를 선택합니다. `whatstheweatherlike.wav`파일을 다운로드하고 **저장하기**를 클릭합니다.
2. Zip 도구를 포함한 파일 탐색기 또는 터미널 창을 사용하여 다운로드 한 `whatstheweatherlike.wav`파일을 포함하는 `whatstheweatherlike.zip`이라는 zip 파일을 만듭니다. Windows에서 Windows 탐색기를 열고 `Downloads` 폴더로 이동한 후 `whatstheweatherliike.wav`을 마우스 오른쪽 단추로 클릭하고 **보내기를 클릭하여** 로 보내고 **압축(zip) 폴더**를 클릭한 다음 엔터키를 눌러 기본 파일 이름을 적용합니다.

## <a name="create-a-project-in-the-custom-speech-portal"></a>Custom Speech 포털에서 프로젝트를 만듭니다.

다음 단계를 수행하여 하나의 오디오 파일의 zip을 포함하는 프로젝트를 만듭니다.

1. [Speech Studio](https://speech.microsoft.com/)를 연 다음 **새 프로젝트**를 클릭합니다. 이 프로젝트의 이름을 입력하고 **만들기**를 클릭합니다. 프로젝트가 Custom Speech 목록에 표시됩니다.
2. 프로젝트의 이름을 클릭합니다. 데이터 탭에서 **데이터 업로드**를 클릭합니다.
3. Speech 데이터 형식은 기본적으로 **오디오 전용**이므로 **다음**을 클릭합니다.
4. 새 음성 데이터 세트의 이름을 `MyZipOfAudio`로 정하고 **다음**을 클릭합니다.
5. **파일 찾아보기...** 를 클릭하고 `whatstheweatherlike.zip`파일로 이동한 후 **열기**를 클릭합니다.
6. **업로드** 단추를 클릭합니다. 브라우저는 zip 파일을 Speech Studio에 업로드하고, Speech Studio는 콘텐츠를 처리 합니다.

## <a name="test-a-model"></a>모델 테스트

Speech Studio에서 zip 파일의 콘텐츠를 처리한 후에는 전사를 검사하는 동안 원본 오디오를 재생하여 오류 또는 누락을 찾을 수 있습니다. 브라우저에서 전사 품질을 검사하려면 다음 단계를 수행합니다.

1. **테스트** 탭을 클릭하고 **테스트 추가**를 클릭합니다.
2. 이 테스트에서는 오디오 전용 데이터의 품질을 검사하므로 **다음** 클릭하여 이 테스트 형식을 적용합니다.
3. 이 테스트의 이름을 `MyModelTest`로 한 후 **다음**을 클릭합니다.
4. `MyZipOfAudio` 왼쪽의 라디오 단추를 클릭하고 **다음**을 클릭합니다.
5. **모델 1** 드롭다운은 최신 인식 모델로 기본 설정되므로 **만들기**를 클릭합니다. 오디오 데이터 세트의 콘텐츠를 처리한 후 테스트 상태가 **성공**으로 변경됩니다.
6. **MyModelTest**를 클릭합니다. 음성 인식 결과가 나타납니다. 원 안의 오른쪽을 가리키는 삼각형을 클릭하여 오디오를 듣고 원 텍스트에 표시되는 내용과 비교합니다.

## <a name="download-detailed-results"></a>자세한 보고서 다운로드

전사를 자세하게 설명하는 파일을 다운로드할 수 있습니다. 파일에는 오디오 파일의 음성의 어휘 형식 및 각 단어에 대한 오프셋, 기간 및 전사 신뢰도 세부 정보가 포함된 JSON 파일이 포함됩니다. 이러한 파일을 보려면 다음 단계를 따르세요.

1. **다운로드**를 클릭합니다.
2. 다운로드 대화 상자에서 **오디오**를 선택 취소하고 **다운로드**를 클릭합니다.
3. 다운로드한 zip 파일의 압축을 풀고 압축을 푼 파일을 검사합니다.

## <a name="next-steps"></a>다음 단계

[사용자 지정 모델](../how-to-custom-speech-test-and-train.md)을 학습하여 음성 인식의 정확도를 향상시키는 방법을 알아봅니다.