---
title: Custom Vision Service 기계 학습에 대한 개요 - Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service는 Azure 플랫폼에서 사용자 지정 이미지 분류자를 만들 수 있는 Microsoft Cognitive Service입니다.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087684"
---
# <a name="what-is-the-custom-vision-service"></a>Custom Vision Service란?

Custom Vision Service는 사용자 지정 이미지 분류자를 만들 수 있는 Microsoft Cognitive Service입니다. 이미지 분류자를 쉽고 빠르게 작성, 배포 및 개선할 수 있습니다. Custom Vision Service 는 이미지를 업로드하고 분류자를 학습시킬 수 있는 REST API와 웹 인터페이스를 제공합니다.

## <a name="what-does-custom-vision-service-do-well"></a>Custom Vision Service에는 어떤 좋은 기능이 있습니까?

Custom Vision Service는 분류하려는 항목이 이미지에서 두드러질 때 가장 잘 작동합니다. 

분류자나 감지기를 만드는 데는 이미지가 거의 필요하지 않습니다. 프로토타입을 시작하는 데 이미지 50개로 충분합니다. Custom Vision Service에 사용되는 메서드는 차이점에 강해서 아주 작은 데이터로도 프로토타이핑을 시작할 수 있습니다. 따라서 미묘한 차이를 감지하려는 시나리오에는 Custom Vision Service가 적합하지 않습니다. 예를 들면, 품질 보증 시나리오에서 사소한 균열이나 손상이 여기에 해당됩니다.

## <a name="release-notes"></a>릴리스 정보

### <a name="may-7-2018"></a>2018년 5월 7일
- 제한된 평가판 프로젝트에 대한 미리 보기 개체 감지 기능이 도입되었습니다.
- 2.0 API로 업그레이드
- S0 계층이 태그 250개 이미지 50,000까지 확장되었습니다. 
- 이미지 분류 프로젝트용 기계 학습 파이프 라인의 백 엔드가 크게 개선되었습니다. 2018년 4월 27일 이후에 학습된 프로젝트에는 이 업데이트가 도움이 됩니다.
- Windows ML에서 사용할 수 있도록 ONNX에 모델 내보내기가 추가되었습니다.
- Dockerfile에 모델 내보내기가 추가되었습니다. 따라서 아티팩트를 다운로드하여 자신만의 Windows 또는 Linux 컨테이너(예: DockerFile, TensorFlow 모델 및 서비스 코드)를 빌드할 수 있습니다. 
- 일반(컴팩트) 및 랜드마크(컴팩트) 도메인에서 TensorFlow로 내보낸 새로 학습된 모델의 경우 모든 프로젝트의 일관성을 위해 [평균값이 이제 (0,0,0)입니다](https://github.com/azure-samples/cognitive-services-android-customvision-sample). 

### <a name="march-1-2018"></a>2018년 3월 1일
- 유료 미리 보기로 전환되고 Azure Portal에 온보딩되었습니다. 이제 F0(무료) 또는 S0(표준) 계층으로 Azure 리소스에 프로젝트를 연결할 수 있습니다. 최대 태그 100개 이미지 25,000개까지 허용하는 S0 계층 프로젝트가 도입되었습니다. 
- 백 엔드가 기계 학습 파이프 라인/정규화 매개 변수로 변경됩니다. 따라서 확률 임계값을 조정할 때 고객이 정밀도-회수 상충 관계를 더 잘 제어할 수 있습니다. 이러한 변화의 일환으로, CustomVision.ai 포털의 기본 확률 임계값이 50%로 설정되었습니다.

### <a name="december-19-2017"></a>2017년 12월 19일

- 이전에 릴리스한 iOS로 내보내기(CoreML)외에 Android로 내보내기(TensorFlow)가 추가되었습니다. 따라서 학습된 컴팩트 모델을 내보내서 응용 프로그램에서 오프라인으로 실행할 수 있습니다.
- 소매 및 랜드마크 “컴팩트” 도메인이 추가되어 이러한 도메인에 대한 모델 내보내기가 가능합니다.
- 버전 [1.2 교육 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 및 [1.1 예측 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)가 릴리스되었습니다. 업데이트된 API는 모델 내보내기, "예측"에 이미지를 저장하지 않는 새로운 예측 작업을 지원하며 교육 API에 일괄 처리 작업을 도입했습니다.
- 반복 학습에 사용된 도메인을 확인하는 기능을 비롯한 UX가 조정되었습니다.
- [C# SDK 및 샘플](https://github.com/Microsoft/Cognitive-CustomVision-Windows)이 업데이트되었습니다.

## <a name="next-steps"></a>다음 단계

[분류자를 빌드하는 방법 알아보기](getting-started-build-a-classifier.md)
