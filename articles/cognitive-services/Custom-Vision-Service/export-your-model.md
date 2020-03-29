---
title: 모바일로 모델 내보내기 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 모바일 응용 프로그램을 만드는 데 사용할 모델을 내보내거나 실시간 분류를 위해 로컬로 실행하는 방법을 보여 줍니다.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718960"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>모바일 디바이스에 사용할 모델 내보내기

Custom Vision Service를 사용하면 분류자를 내보내서 오프라인으로 실행할 수 있습니다. 내보낸 분류자를 애플리케이션에 포함하여 디바이스에서 실행하면 실시간 분류가 가능합니다.

## <a name="export-options"></a>내보내기 옵션

Custom Vision Service는 다음과 같은 내보내기를 지원합니다.

* __Tensorflow__ for __Android__.
* __CoreML__ for __iOS11__.
* __ONNX__ for __Windows ML__.
* __[비전 AI 개발자 키트](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* Windows, Linux 또는 ARM 아키텍처용 __도커 컨테이너입니다.__ 컨테이너에는 사용자 지정 비전 API를 사용하는 Tensorflow 모델 및 서비스 코드가 포함됩니다.

> [!IMPORTANT]
> Custom Vision Service는 __소형__ 도메인만 내보냅니다. 소형 도메인을 통해 생성된 모델은 모바일 디바이스의 실시간 분류 제약 조건에 맞게 최적화됩니다. 소형 도메인을 사용하여 만든 분류자는 교육 데이터의 양이 동일한 표준 도메인보다 정확도가 살짝 떨어질 수 있습니다.
>
> 분류자를 향상하는 방법은 [분류자 향상](getting-started-improving-your-classifier.md) 문서를 참조하세요.

## <a name="convert-to-a-compact-domain"></a>소형 도메인으로 변환

> [!NOTE]
> 이 섹션의 단계는 소형 도메인으로 설정되지 않은 기존 모델이 있는 경우에만 적용됩니다.

기존 모델의 도메인을 변환하려면 다음 단계를 수행합니다.

1. 사용자 [지정 비전 웹 사이트에서](https://customvision.ai) __홈__ 아이콘을 선택하여 프로젝트 목록을 봅니다.

    ![홈 아이콘 및 프로젝트 목록 이미지](./media/export-your-model/projects-list.png)

1. 프로젝트를 선택한 다음, 페이지 오른쪽 위에서 __기어__ 아이콘을 선택합니다.

    ![기어 아이콘 이미지](./media/export-your-model/gear-icon.png)

1. __도메인__ 섹션에서 __압축__ 도메인 중 하나를 선택합니다. __변경 내용 저장__을 선택하여 변경 내용을 저장합니다. 

    > [!NOTE]
    > 비전 AI 개발 키트의 경우 프로젝트는 __일반(소형)__ 도메인으로 만들어야 하며 **내보내기 기능** 섹션에서 **비전 AI 개발자 키트** 옵션을 지정해야 합니다.

    ![도메인 선택 이미지](./media/export-your-model/domains.png)

1. 페이지 위쪽에서 __교육__을 선택하여 새 도메인 사용 방법을 다시 학습합니다.

## <a name="export-your-model"></a>모델 내보내기

재교육 후 모델을 내보내려면 다음 단계를 수행합니다.

1. **성능** 탭으로 이동하여 __내보내기__를 선택합니다. 

    ![내보내기 아이콘 이미지](./media/export-your-model/export.png)

    > [!TIP]
    > __내보내기__ 항목을 사용할 수 없으면 선택한 반복에 소형 도메인이 사용되지 않습니다. 이 페이지의 __반복__ 섹션을 사용하여 소형 도메인을 사용하는 반복을 선택한 다음, __내보내기__를 선택합니다.

1. 원하는 내보내기 형식을 선택한 다음 __내보내기를__ 선택하여 모델을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

다음 문서 또는 샘플 중 하나를 탐색하여 내보낸 모델을 응용 프로그램에 통합합니다.

* [파이썬으로 텐서플로우 모델 사용](export-model-python.md)
* [Windows 기계 학습과 함께 ONNX 모델 사용](custom-vision-onnx-windows-ml.md)
* Swift를 사용하여 실시간 이미지 [분류는 iOS 응용 프로그램에서 CoreML 모델의](https://go.microsoft.com/fwlink/?linkid=857726) 샘플을 참조하십시오.
* 안드로이드에 대한 실시간 이미지 분류에 대한 [안드로이드 응용 프로그램에서 Tensorflow 모델에](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) 대한 샘플을 참조하십시오.
* Xamarin iOS 앱에서 실시간 이미지 분류는 [Xamarin이 있는 CoreML 모델의](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) 샘플을 참조하십시오.
