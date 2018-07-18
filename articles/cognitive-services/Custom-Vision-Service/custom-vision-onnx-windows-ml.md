---
title: Windows ML을 통해 Custom Vision ONNX 모델 - Cognitive Services | Microsoft Docs
description: Cognitive Services에서 내보낸 ONNX 모델을 사용하는 Windows UWP 앱을 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939430"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>자습서: Windows ML을 통해 Custom Vision에서 ONNX 모델 사용(미리 보기)

Windows ML을 사용하여 Custom Vision Service에서 내보낸 ONNX 모델을 사용하는 방법을 알아봅니다(미리 보기).

이 문서의 정보는 Windows ML을 통해 Custom Vision Service에서 내보낸 ONNX 파일을 사용하는 방법을 설명합니다. 예제 Windows UWP 응용 프로그램이 제공됩니다. 개와 고양이를 인식할 수 있는 학습된 모델이 예제에 포함되어 있습니다. 또한 사용자가 이 예제로 사용자 고유의 모델을 사용할 수 있는 방법에 대한 단계가 제공됩니다.

> [!div class="checklist"]
> * 예제 앱 정보
> * 예제 코드 가져오기
> * 예제 실행
> * 사용자 고유의 모델 사용

## <a name="prerequisites"></a>필수 조건

* Windows 10 장치 연결:

    * 카메라.

    * __유니버설 Windows 플랫폼 개발__ 워크로드가 사용하도록 설정된 Visual Studio 2017 버전 15.7 이상.

    * 사용하도록 설정된 개발자 모드. 자세한 내용은 [개발을 위해 장치를 사용하도록 설정](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) 문서를 참조하세요.

* (선택 사항) Custom Vision Service에서 내보낸 ONNX 파일. 자세한 내용은 [모바일 장치에 사용할 모델 내보내기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) 문서를 참조하세요.

    > [!NOTE]
    > 사용자 고유의 모델을 사용하려면 [사용자 고유의 모델 사용](#use-your-own-model) 섹션의 단계를 수행합니다.

## <a name="about-the-example-app"></a>예제 앱 정보

응용 프로그램은 일반 Windows UWP 응용 프로그램입니다. 모델에 이미지를 제공하기 위해 Windows 10 장치에서 카메라를 사용합니다. 모델에서 반환되는 점수 및 태그는 비디오 미리 보기 아래에 표시됩니다.

* 데이터가 카메라를 통해 들어오면 개별 프레임을 추출하는 데 [MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader)가 사용됩니다. 프레임은 점수를 매기기 위해 모델에 전송됩니다.

* 모델은 학습한 태그와 함께 이미지가 해당 항목을 얼마나 포함하는지를 나타내는 부동 소수점 값을 반환합니다.

### <a name="the-ui"></a>UI

예제 응용 프로그램에 대한 UI는 __CaptureElement__ 및 __TextBlock__ 컨트롤을 사용하여 만들어집니다. CaptureElement는 카메라 비디오의 미리 보기를 표시하고, TextBlock은 모델에서 반환된 결과를 표시합니다. 

### <a name="the-model"></a>모델

예제와 함께 제공된 모델(`cat-or-dog.onnx`)은 Cognitive Services Custom Vision Service를 사용하여 만들어지고 학습됩니다. 그런 다음, 학습된 모델을 ONNX 모델로 내보냅니다. 이 서비스 사용에 대한 자세한 내용은 [분류자를 빌드하는 방법](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) 및 [모바일 장치에 사용할 모델 내보내기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) 문서를 참조하세요.

> [!IMPORTANT]
> 예제에 제공된 모델은 개와 고양이 이미지의 작은 집합으로 학습되었습니다. 따라서 개와 고양이 인식에 대해 세계 최고가 아닐 수도 있습니다.

### <a name="the-model-class-file"></a>모델 클래스 파일

ONNX 파일을 Windows UWP 응용 프로그램에 추가하는 경우 .cs 파일을 만듭니다. 이 파일은 `.onnx` 파일(`cat-or-dog` 이 예에서)과 동일한 이름이 있으며 C#에서 모델로 작업하는 데 사용되는 클래스를 포함합니다. 그러나 생성된 클래스의 엔터티는 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`와 같은 이름일 수 있습니다. 이러한 항목의 이름을 안전하게 친숙한 이름으로 바꿀 수 있습니다(마우스 오른쪽 단추로 클릭, 이름 바꾸기).

> [!NOTE]
> 예제 코드는 생성된 클래스 및 메서드 이름을 다음과 같이 리팩터링했습니다.
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>카메라 액세스

`Package.appxmanifest` 파일의 __기능__ 탭은 웹캠 및 마이크에 대한 액세스를 허용하도록 구성됩니다.

> [!NOTE]
> 이 예제에서는 오디오를 사용하지 않지만, 장치의 카메라에 액세스하려면 마이크를 사용하도록 설정해야 했습니다.

응용 프로그램은 사용할 수 있는 경우 장치 뒷면에 있는 카메라를 가져오려고 시도합니다. [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) 클래스를 사용하여 카메라에서 비디오 캡처를 시작합니다. 비디오 프레임을 캡처하고 모델에 전송하는 데 [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader)가 사용됩니다.

## <a name="get-the-example-code"></a>예제 코드 가져오기

예제 응용 프로그램은 [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP)에서 사용할 수 있습니다.

## <a name="run-the-example"></a>예제 실행

1. `F5` 키를 사용하여 Visual Studio에서 응용 프로그램을 시작합니다. 개발자 모드를 사용하도록 설정하라는 메시지가 표시될 수 있습니다. 자세한 내용은 [개발을 위해 장치를 사용하도록 설정](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) 문서를 참조하세요.

2. 메시지가 표시되면 응용 프로그램이 사용자 장치의 카메라 및 마이크에 액세스하도록 허용합니다.

3. 카메라를 개 또는 고양이에게 향합니다. 이미지에 개나 고양이가 포함되는지 여부에 대한 점수가 응용 프로그램에서 미리 보기 아래에 표시됩니다.

    > [!TIP]
    > 가까이에 개나 고양이가 없는 경우 개나 고양이 사진을 사용할 수 있습니다.

## <a name="use-your-own-model"></a>사용자 고유의 모델 사용

사용자 고유의 모델을 사용하려면 다음 단계를 사용합니다.

> [!IMPORTANT]
> 이 섹션의 단계에서는 현재 모델(cat-or-dog.cs)의 이름을 변경하며, 새 모델의 클래스 및 메서드 이름을 리팩터링합니다. 예제 모델과의 이름 충돌을 방지하기 위해서입니다.

1. Custom Vision Service를 사용하여 모델을 학습합니다. 모델을 학습하는 방법에 대한 정보는 [분류자를 빌드하는 방법](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)을 참조하세요.

2. ONNX 모델로 학습된 모델을 내보냅니다. 모델을 내보내는 방법에 대한 정보는 [모바일 장치에 사용할 모델 내보내기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) 문서를 참조하세요.

3. 솔루션 탐색기에서 __cat-or-dog.cs__를 마우스 오른쪽 단추로 클릭하고 이름을 __cat-or-dog.txt__로 바꿉니다. 이름을 변경하면 새 모델과 이름 충돌이 발생하지 않습니다.

    > [!TIP]
    > 새 모델에서 클래스 이름에 다른 이름을 사용할 수도 있습니다. 하지만 기존의 이름을 다시 사용하는 것이 더 쉽습니다.

4. 솔루션 탐색기에서 __VisionApp__ 항목을 마우스 오른쪽 단추로 클릭한 다음, __추가__ > __기존 항목...__ 을 선택합니다.

5. 모델에 대한 클래스를 생성하려면 가져올 ONNX 파일을 선택한 다음, __추가__ 단추를 선택합니다. ONNX 파일과 동일한 이름의 새 클래스(단, `.cs` 확장)가 솔루션 탐색기에 추가됩니다.

6. 생성된 .cs 파일을 열고 다음 항목의 이름을 찾습니다.

    > [!IMPORTANT]
    > 예제 `cat-or-dog.txt` 파일을 가이드로 사용하여 클래스 및 함수를 인식합니다.

    * 모델 입력을 정의하는 클래스입니다. 생성된 이름은 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`과 유사할 수 있습니다. 이 클래스의 이름을 __ModelInput__으로 바꿉니다.
    * 모델 출력을 정의하는 클래스입니다. 생성된 이름은 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`과 유사할 수 있습니다. 이 클래스의 이름을 __ModelOutput__으로 바꿉니다.
    * 모델을 정의하는 클래스입니다. 생성된 이름은 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`과 유사할 수 있습니다. 이 클래스의 이름을 __Model__로 바꿉니다.
    * 모델을 만드는 메서드입니다. 생성된 이름은 `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`과 유사할 수 있습니다. 이 메서드의 이름을 __CreateModel__로 바꿉니다.

7. 솔루션 탐색기에서 `.onnx` 파일을 __Assets__ 폴더로 이동합니다. 

8. ONNX 파일을 응용 프로그램 패키지에 포함시키려면 `.onnx` 파일을 선택하고 __Build Action__을 속성의 __Content__로 설정합니다.

9. __MainPage.xaml.cs__ 파일을 엽니다. 다음 줄을 찾아 파일 이름을 새 `.onnx` 파일로 변경합니다.

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    그러면 런타임 시 새 모델이 로드됩니다.

10. 앱을 빌드 및 실행합니다. 이제 새 모델을 사용하여 이미지 점수를 매깁니다.

## <a name="next-steps"></a>다음 단계

Custom Vision 모델을 내보내고 사용하는 다른 방법을 알아보려면 다음 문서를 참조하세요.

* [모델 내보내기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Android 응용 프로그램에서 내보낸 Tensorflow 모델 사용](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Swift iOS 응용 프로그램에서 내보낸 CoreML 모델 사용](https://go.microsoft.com/fwlink/?linkid=857726)
* [Xamarin을 사용하는 iOS 응용 프로그램에서 내보낸 CoreML 모델 사용](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML을 통한 ONNX 모델 사용에 대한 더 자세한 내용은 [Windows ML을 통해 앱에 모델 통합](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model) 문서를 참조하세요.
