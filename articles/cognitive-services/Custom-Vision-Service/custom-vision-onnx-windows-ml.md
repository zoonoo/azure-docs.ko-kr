---
title: Windows ML과 함께 ONNX 모델 사용 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 내보낸 ONNX 모델을 사용하는 Windows UWP 앱을 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 5ae333d0693071f1a5a5d12e87d6aa155ceccd3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055572"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Windows ML을 통해 Custom Vision에서 ONNX 모델 사용(미리 보기)

Windows ML을 사용하여 Custom Vision Service에서 내보낸 ONNX 모델을 사용하는 방법을 알아봅니다(미리 보기).

이 가이드에서는 Windows ML을 사용하여 Custom Vision Service에서 내보낸 ONNX 파일을 사용하는 방법을 알아봅니다. 예제 UWP 애플리케이션을 학습된 이미지 분류자와 함께 사용합니다.

## <a name="prerequisites"></a>필수 조건

* Windows 10 버전 1809 이상
* 빌드 17763 이상용 Windows SDK
* __유니버설 Windows 플랫폼 개발__ 워크로드가 사용하도록 설정된 Visual Studio 2017 버전 15.7 이상.
* PC에서 개발자 모드를 사용하도록 설정합니다. 자세한 내용은 [개발에 디바이스 사용](/windows/uwp/get-started/enable-your-device-for-development)을 참조하세요.

## <a name="about-the-example-app"></a>예제 앱 정보

포함된 애플리케이션은 일반 Windows UWP 앱입니다. 컴퓨터에서 이미지를 선택한 다음, 로컬에 저장된 분류 모델을 통해 처리할 수 있습니다. 모델에서 반환되는 태그 및 점수는 이미지 옆에 표시됩니다.

## <a name="get-the-example-code"></a>예제 코드 가져오기

예제 애플리케이션은 GitHub의 [Cognitive Services ONNX Custom Vision 샘플](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) 리포지토리에서 사용할 수 있습니다. 로컬 컴퓨터에 복제하고 Visual Studio에서 *SampleOnnxEvaluationApp.sln* 을 엽니다.

## <a name="test-the-application"></a>애플리케이션 테스트

1. `F5` 키를 사용하여 Visual Studio에서 애플리케이션을 시작합니다. 개발자 모드를 사용하도록 설정하라는 메시지가 표시될 수 있습니다.
1. 애플리케이션이 시작되면 단추를 사용하여 채점할 이미지를 선택합니다. 기본 ONNX 모델은 다양한 유형의 플랑크톤을 분류하도록 학습되었습니다.

## <a name="use-your-own-model"></a>사용자 고유의 모델 사용

자체 이미지 분류자 모델을 사용하려면 다음 단계를 수행합니다.

1. Custom Vision Service를 사용하여 분류자를 만들고 학습합니다. 이 작업을 수행하는 방법에 대한 지침은 [분류자 만들기 및 학습](./getting-started-build-a-classifier.md)을 참조하세요. **일반(컴팩트)** 과 같은 **컴팩트** 도메인 중 하나를 사용합니다. 
   * 다른 도메인을 사용하는 기존 분류자가 있는 경우 프로젝트 설정에서 **컴팩트** 로 변환할 수 있습니다. 그런 다음, 계속하기 전에 프로젝트를 다시 학습합니다.
1. 모델을 내보냅니다. 성능 탭으로 전환하고 **컴팩트** 도메인으로 학습된 반복을 선택합니다. 표시되는 **내보내기** 단추를 선택합니다. 그런 다음, **ONNX**, **내보내기** 를 차례로 선택합니다. 파일이 준비되면 **다운로드** 단추를 선택합니다. 내보내기 옵션에 대한 자세한 내용은 [모델 내보내기](./export-your-model.md)를 참조하세요.
1. 다운로드한 *.zip* 파일을 열고 여기에서 *model.onnx* 파일의 압축을 풉니다. 이 파일에는 분류자 모델이 포함되어 있습니다.
1. Visual Studio의 솔루션 탐색기에서 **자산** 폴더를 마우스 오른쪽 단추로 클릭하고 __기존 항목 추가__ 를 선택합니다. ONNX 파일을 선택합니다.
1. 솔루션 탐색기에서 ONNX 파일을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 파일의 다음 속성을 변경합니다.
   * __빌드 작업__ -> __콘텐츠__
   * __출력 디렉터리로 복사__ -> __변경된 내용만 복사__
1. 그런 다음, _MainPage.xaml.cs_ 를 열고 `_ourOnnxFileName`의 값을 ONNX 파일의 이름으로 변경합니다.
1. `F5`를 사용하여 프로젝트를 빌드하고 실행합니다.
1. 단추를 클릭하여 평가할 이미지를 선택합니다.

## <a name="next-steps"></a>다음 단계

Custom Vision 모델을 내보내고 사용하는 다른 방법을 알아보려면 다음 문서를 참조하세요.

* [모델 내보내기](./export-your-model.md)
* [Android 애플리케이션에서 내보낸 Tensorflow 모델 사용](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Swift iOS 애플리케이션에서 내보낸 CoreML 모델 사용](https://go.microsoft.com/fwlink/?linkid=857726)
* [Xamarin을 사용하는 iOS 애플리케이션에서 내보낸 CoreML 모델 사용](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML에서 ONNX 모델을 사용하는 방법에 대한 자세한 내용은 [Windows ML을 통해 앱에 모델 통합](/windows/ai/windows-ml/integrate-model)을 참조하세요.