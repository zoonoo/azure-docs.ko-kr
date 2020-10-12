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
ms.openlocfilehash: d6bcb5485833fbfaa3eb72191ee54b1ee69bab04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82594298"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Windows ML을 통해 Custom Vision에서 ONNX 모델 사용(미리 보기)

Windows ML을 사용하여 Custom Vision Service에서 내보낸 ONNX 모델을 사용하는 방법을 알아봅니다(미리 보기).

이 가이드에서는 Windows ML을 사용 하 여 Custom Vision Service에서 내보낸 ONNX 파일을 사용 하는 방법을 알아봅니다. 예제 UWP 응용 프로그램을 학습 된 이미지 분류자와 함께 사용 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Windows 10 버전 1809 이상
* 빌드 17763 이상용 Windows SDK
* __유니버설 Windows 플랫폼 개발__ 워크로드가 사용하도록 설정된 Visual Studio 2017 버전 15.7 이상.
* PC에서 개발자 모드를 사용 하도록 설정 합니다. 자세한 내용은 [개발에 디바이스 사용](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)을 참조하세요.

## <a name="about-the-example-app"></a>예제 앱 정보

포함 된 응용 프로그램은 일반 Windows UWP 앱입니다. 이를 통해 컴퓨터에서 이미지를 선택 하 여 로컬에 저장 된 분류 모델을 통해 처리할 수 있습니다. 모델에서 반환되는 태그 및 점수는 이미지 옆에 표시됩니다.

## <a name="get-the-example-code"></a>예제 코드 가져오기

예제 응용 프로그램은 GitHub의 [Cognitive Services ONNX Custom Vision 샘플](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) 리포지토리에서 사용할 수 있습니다. 로컬 컴퓨터에 복제 하 고 Visual Studio에서 *SampleOnnxEvaluationApp* 을 엽니다.

## <a name="test-the-application"></a>애플리케이션 테스트

1. `F5` 키를 사용하여 Visual Studio에서 애플리케이션을 시작합니다. 개발자 모드를 사용하도록 설정하라는 메시지가 표시될 수 있습니다.
1. 애플리케이션이 시작되면 단추를 사용하여 채점할 이미지를 선택합니다. 기본 ONNX 모델을 학습 하 여 다양 한 유형의 계획 k톤을 분류 합니다.

## <a name="use-your-own-model"></a>사용자 고유의 모델 사용

사용자 고유의 이미지 분류자 모델을 사용 하려면 다음 단계를 수행 합니다.

1. Custom Vision Service를 사용하여 분류자를 만들고 학습합니다. 이 작업을 수행 하는 방법에 대 한 지침은 [분류자 만들기 및 학습](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)을 참조 하세요. **일반 (압축)** 과 같은 **컴팩트** 도메인 중 하나를 사용 합니다. 
   * 다른 도메인을 사용 하는 기존 분류자가 있는 경우 프로젝트 설정에서 **압축** 으로 변환할 수 있습니다. 그런 다음 계속 하기 전에 프로젝트를 다시 학습 합니다.
1. 모델을 내보냅니다. 성능 탭으로 전환 하 고 **compact** 도메인으로 학습 된 반복을 선택 합니다. 표시 되는 **내보내기** 단추를 선택 합니다. 그런 다음 **Onnx**, **내보내기**를 차례로 선택 합니다. 파일이 준비되면 **다운로드** 단추를 선택합니다. 내보내기 옵션에 대 한 자세한 내용은 [모델 내보내기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)를 참조 하세요.
1. 다운로드 한 *.zip* 파일을 열고 *모델* 에서이 파일을 추출 합니다. 이 파일에는 분류자 모델이 포함 되어 있습니다.
1. Visual Studio의 솔루션 탐색기에서 **자산** 폴더를 마우스 오른쪽 단추로 클릭 하 고 __기존 항목 추가__를 선택 합니다. ONNX 파일을 선택 합니다.
1. 솔루션 탐색기에서 ONNX 파일을 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택 합니다. 파일의 다음 속성을 변경합니다.
   * __빌드 작업__ -> __콘텐츠__
   * __출력 디렉터리로 복사__ -> __변경된 내용만 복사__
1. 그런 다음 _MainPage.xaml.cs_ 를 열고의 값 `_ourOnnxFileName` 을 onnx 파일의 이름으로 변경 합니다.
1. 를 사용 `F5` 하 여 프로젝트를 빌드하고 실행 합니다.
1. 단추를 클릭하여 평가할 이미지를 선택합니다.

## <a name="next-steps"></a>다음 단계

Custom Vision 모델을 내보내고 사용하는 다른 방법을 알아보려면 다음 문서를 참조하세요.

* [모델 내보내기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Android 애플리케이션에서 내보낸 Tensorflow 모델 사용](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Swift iOS 애플리케이션에서 내보낸 CoreML 모델 사용](https://go.microsoft.com/fwlink/?linkid=857726)
* [Xamarin을 사용하는 iOS 애플리케이션에서 내보낸 CoreML 모델 사용](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML에서 ONNX 모델을 사용 하는 방법에 대 한 자세한 내용은 [WINDOWS ml을 사용 하 여 앱에 모델 통합](/windows/ai/windows-ml/integrate-model)을 참조 하세요.
