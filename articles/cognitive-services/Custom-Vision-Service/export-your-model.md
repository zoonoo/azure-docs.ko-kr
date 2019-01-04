---
title: 모바일로 모델 내보내기 - Custom Vision Service
titlesuffix: Azure Cognitive Services
description: 모바일 애플리케이션을 만드는 데 사용할 모델을 내보내는 방법을 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 50417e9e1722e69c24185c05ea5e286e6b13e8c7
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364944"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>모바일 디바이스에 사용할 모델 내보내기

Custom Vision Service를 사용하면 분류자를 내보내서 오프라인으로 실행할 수 있습니다. 내보낸 분류자를 응용 프로그램에 포함하여 디바이스에서 실행하면 실시간 분류가 가능합니다. 

Custom Vision Service는 다음과 같은 내보내기를 지원합니다.

* __Tensorflow__ for __Android__.
* __CoreML__ for __iOS11__.
* __ONNX__ for __Windows ML__.
* Windows 또는 Linux __컨테이너__. 컨테이너에는 Custom Vision Service API를 사용할 Tensorflow 모델 및 서비스 코드가 포함됩니다. 

> [!IMPORTANT]
> Custom Vision Service는 __소형__ 도메인만 내보냅니다. 소형 도메인을 통해 생성된 모델은 모바일 디바이스의 실시간 분류 제약 조건에 맞게 최적화됩니다. 소형 도메인을 사용하여 만든 분류자는 교육 데이터의 양이 동일한 표준 도메인보다 정확도가 살짝 떨어질 수 있습니다.
>
> 분류자를 향상하는 방법은 [분류자 향상](getting-started-improving-your-classifier.md) 문서를 참조하세요.

## <a name="convert-to-a-compact-domain"></a>소형 도메인으로 변환

> [!NOTE]
> 이 섹션의 단계는 소형 도메인으로 설정되지 않은 기존 분류자가 있는 경우에만 적용됩니다.
 
기존 분류자의 도메인을 변환하려면 다음 단계를 수행합니다.

1. [사용자 지정 비전 페이지](https://customvision.ai)에서 __홈__ 아이콘을 선택하여 프로젝트 목록을 살펴봅니다. [https://customvision.ai/projects](https://customvision.ai/projects)를 사용하여 프로젝트를 볼 수도 있습니다.

    ![홈 아이콘 및 프로젝트 목록 이미지](./media/export-your-model/projects-list.png)

2. 프로젝트를 선택한 다음, 페이지 오른쪽 위에서 __기어__ 아이콘을 선택합니다.

    ![기어 아이콘 이미지](./media/export-your-model/gear-icon.png)

3. __도메인__ 섹션에서 __소형__ 도메인을 선택합니다. __변경 내용 저장__을 선택하여 변경 내용을 저장합니다.

    ![도메인 선택 이미지](./media/export-your-model/domains.png)

4. 페이지 위쪽에서 __교육__을 선택하여 새 도메인 사용 방법을 다시 학습합니다.

## <a name="export-your-model"></a>모델 내보내기

재교육 후 모델을 내보내려면 다음 단계를 수행합니다.

1. **성능** 탭으로 이동하여 __내보내기__를 선택합니다. 

    ![내보내기 아이콘 이미지](./media/export-your-model/export.png)

    > [!TIP]
    > __내보내기__ 항목을 사용할 수 없으면 선택한 반복에 소형 도메인이 사용되지 않습니다. 이 페이지의 __반복__ 섹션을 사용하여 소형 도메인을 사용하는 반복을 선택한 다음, __내보내기__를 선택합니다.

2. 내보내기 형식을 선택한 다음, __내보내기__를 선택하여 모델을 내보냅니다.

## <a name="next-steps"></a>다음 단계

내보낸 모델을 애플리케이션에 통합합니다. 여러 애플리케이션 예제를 사용할 수 있습니다.

* Swift를 사용한 실시간 이미지 분류에 대한 [내보낸 CoreML 모델을 iOS 애플리케이션에서 사용](https://go.microsoft.com/fwlink/?linkid=857726) 샘플
* 실시간 이미지 분류에 대한 [내보낸 CoreML 모델을 Xamarin에 사용](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) 샘플 iOS 애플리케이션 
* 실시간 이미지 분류에 대한 [내보낸 Tensorflow 모델을 Android 애플리케이션에서 사용](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) 샘플 
* [Tensorflow 모델을 Windows에 사용](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-model-python)
* [내보낸 ONNX 모델을 Windows Machine Learning에 사용](https://azure.microsoft.com/resources/samples/cognitive-services-onnx-customvision-sample/) 샘플
