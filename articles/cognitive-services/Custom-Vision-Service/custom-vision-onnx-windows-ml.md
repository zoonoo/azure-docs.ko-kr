---
title: '자습서: Windows ML과 함께 ONNX 모델 사용 - Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 내보낸 ONNX 모델을 사용하는 Windows UWP 앱을 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 10/16/2018
ms.author: larryfr
ms.openlocfilehash: f9ffbdd34c6fe7e8fc0f5e8ad8a49a3ad922f88a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362975"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>자습서: Windows ML을 통해 Custom Vision에서 ONNX 모델 사용(미리 보기)

Windows ML을 사용하여 Custom Vision Service에서 내보낸 ONNX 모델을 사용하는 방법을 알아봅니다(미리 보기).

이 문서의 정보는 Windows ML을 통해 Custom Vision Service에서 내보낸 ONNX 파일을 사용하는 방법을 설명합니다. 예제 Windows UWP 응용 프로그램이 제공됩니다. 인식할 수 있도록 학습된 모델이 예제에 포함되어 있습니다. 또한 사용자가 이 예제로 사용자 고유의 모델을 사용할 수 있는 방법에 대한 단계가 제공됩니다.

> [!div class="checklist"]
> * 예제 앱 정보
> * 예제 코드 가져오기
> * 예제 실행
> * 사용자 고유의 모델 사용

## <a name="prerequisites"></a>필수 조건

* Windows 10 빌드 17738 이상

* 빌드 17738 이상용 Windows SDK

* __유니버설 Windows 플랫폼 개발__ 워크로드가 사용하도록 설정된 Visual Studio 2017 버전 15.7 이상.

* 사용하도록 설정된 개발자 모드. 자세한 내용은 [개발을 위해 장치를 사용하도록 설정](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) 문서를 참조하세요.

## <a name="about-the-example-app"></a>예제 앱 정보

응용 프로그램은 일반 Windows UWP 응용 프로그램입니다. 응용 프로그램을 사용하여 컴퓨터에서 이미지를 선택한 다음, 모델로 보낼 수 있습니다. 모델에서 반환되는 태그 및 점수는 이미지 옆에 표시됩니다.

## <a name="get-the-example-code"></a>예제 코드 가져오기

예제 응용 프로그램은 [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/)에서 사용할 수 있습니다.

## <a name="run-the-example"></a>예제 실행

1. `F5` 키를 사용하여 Visual Studio에서 응용 프로그램을 시작합니다. 개발자 모드를 사용하도록 설정하라는 메시지가 표시될 수 있습니다. 자세한 내용은 [개발을 위해 장치를 사용하도록 설정](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) 문서를 참조하세요.

2. 응용 프로그램이 시작되면 단추를 사용하여 채점할 이미지를 선택합니다.

## <a name="use-your-own-model"></a>사용자 고유의 모델 사용

사용자 고유의 모델을 사용하려면 다음 단계를 사용합니다.

1. Custom Vision Service를 사용하여 분류자를 [만들고 학습](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)합니다. 모델을 내보내려면 **일반(컴팩트)** 같은 __컴팩트__ 도메인을 선택합니다. 기존 분류자를 내보내려면 오른쪽 위에서 기어 아이콘을 선택하여 도메인을 컴팩트로 변환합니다. __설정__에서 컴팩트 모델을 선택하고 저장한 다음, 프로젝트를 학습합니다.  

1. [성능] 탭으로 이동하여 [모델을 내보냅니다](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model). 컴팩트 도메인으로 교육한 반복을 선택하면 "내보내기" 단추가 표시됩니다. *내보내기*, *ONNX*, *ONNX1.2*를 차례로 선택한 다음, *내보내기*를 선택합니다. 파일이 준비되면 *다운로드* 단추를 선택합니다.

1. ONNX 파일을 프로젝트의 __자산__ 폴더에 끌어 놓습니다. 

1. 솔루션 탐색기에서 Assets 폴더를 마우스 오른쪽 단추로 클릭하고 __기존 항목 추가__를 선택합니다. ONNX 파일을 선택합니다.

1. 솔루션 탐색기에서 Assets 폴더의 ONNX 파일을 선택합니다. 파일의 다음 속성을 변경합니다. 
    
    * __빌드 작업__ -> __콘텐츠__
    * __출력 디렉터리로 복사__ -> __변경된 내용만 복사__

1. `_onnxFileNames` 변수를 ONNX 파일의 이름으로 변경합니다. 또한 `ClassLabel`을 모델에 포함된 레이블 수로 수정합니다.

1. 빌드하고 실행합니다.

1. 단추를 클릭하여 평가할 이미지를 선택합니다.

## <a name="next-steps"></a>다음 단계

Custom Vision 모델을 내보내고 사용하는 다른 방법을 알아보려면 다음 문서를 참조하세요.

* [모델 내보내기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Android 응용 프로그램에서 내보낸 Tensorflow 모델 사용](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Swift iOS 응용 프로그램에서 내보낸 CoreML 모델 사용](https://go.microsoft.com/fwlink/?linkid=857726)
* [Xamarin을 사용하는 iOS 응용 프로그램에서 내보낸 CoreML 모델 사용](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML을 통한 ONNX 모델 사용에 대한 더 자세한 내용은 [Windows ML을 통해 앱에 모델 통합](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) 문서를 참조하세요.
