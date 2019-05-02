---
title: 얼굴 감지 및 특성 개념
titleSuffix: Azure Cognitive Services
description: 얼굴 감지 및 얼굴 특성에 대 한 개념에 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572542"
---
# <a name="face-detection-and-attributes"></a>얼굴 감지 및 특성

이 문서는 얼굴 감지 및 얼굴 특성 데이터의 개념을 설명합니다. 얼굴 감지는 작업이 이미지에서 사람 얼굴 확인 및 필요에 따라 다양 한 얼굴 관련 데이터를 반환 합니다.

사용 된 [얼굴 감지-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 이미지에서 얼굴을 감지 하는 작업입니다. 최소한 검색 된 각 얼굴에 해당 합니다는 **faceRectangle** 응답 필드입니다. 이것이 픽셀 좌표 (왼쪽, 위쪽, 너비, 높이) 집합이 있는 글꼴을 표시 합니다. 이러한 좌표를 사용 하 여, 해당 크기 뿐만 아니라 얼굴의 위치를 가져올 수 있습니다. API 응답에서 얼굴 가장 작은 가장 큰 크기 순서 대로 나열 됩니다.

## <a name="face-id"></a>Face ID(얼굴 ID)

얼굴 ID가 이미지에서 감지 된 각 얼굴에 대 한 고유 식별자 문자열 하기만 하면 됩니다. 얼굴 ID를 요청할 수 있습니다 하 [얼굴 감지-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 호출 합니다.

## <a name="face-landmarks"></a>얼굴 이정표

얼굴 랜드마크는는 삼아 같은 얼굴 찾기 쉽게 점의 집합 또는 코의 팁입니다. 기본적으로 27개의 이정표 지점이 사전 정의되어 있습니다. 다음 그림에는 모든 27 지점을 보여 줍니다.

![레이블이 지정 된 모든 27 개의 특징을 사용 하 여 얼굴 다이어그램](../Images/landmarks.1.jpg)

요소의 좌표를 픽셀 단위로 반환 됩니다.

## <a name="attributes"></a>특성

특성은 선택적으로 검색할 수 있는 추가 얼굴 기능 집합을 [얼굴 감지-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. 다음은 검색할 수 있는 특성입니다.

* **Age** 예상된 기간, 특정 얼굴 년에서입니다.
* **Blur** 이미지에서 얼굴의 흐린 합니다. 이 비공식적인 등급 ("낮음", "medium", "높음") 뿐만 아니라 0 및 1 사이의 값을 반환합니다.
* **노출** 이미지에서 얼굴의 노출 합니다. 이 비공식적인 등급 (예: "노출이", "goodExposure", "과다") 뿐만 아니라 0 및 1 사이의 값을 반환 합니다.
* **감정** 감정은 특정된 얼굴에 대 한 해당 검색 신뢰도 사용 하 여 목록입니다. 신뢰성 점수 정규화 됩니다: 최대 하나의 모든 감정 점수를 추가 합니다. 반환 된 감정을 행복, 슬픔, neutral, 분노, 경 멸, 혐오, 놀람, 및 걱정 합니다.
* **수염** 예상된 수염 현재 상태와 지정 된 글꼴의 길이입니다.
* **성별** 지정한 글꼴의 예상된 성별입니다. 가능한 값은 "male", "female", "genderless"입니다.
* **안경** 주어진된 얼굴 안경에 있는지 여부입니다. 가능한 값은 "NoGlasses", "ReadingGlasses", "선글라스" 및 "Swimming 보호 안경"입니다.
* **십자선** 십자선 스타일 글꼴입니다. 십자선이 표시 되는지 여부, 여부 baldness 검색 되 고 어떤 머리카락 색 감지 보여 줍니다.
* **머리 포즈** 3D 공간에서 발생 하는 방향입니다. 피치, 앨범 및 요 각도에서 설명 합니다. 값 범위는 [-90, 90] [-180, 180] 및 [-90, 90]도 각각. 각도 매핑에 대 한 다음 다이어그램을 참조 하세요.

    ![피치를 사용 하 여 헤드, 롤백 및 축 레이블이 yaw](../Images/headpose.1.jpg)
* **구성을** 발생 하는 구성에 있는지 여부입니다. 이 "eyeMakeup" 및 "lipMakeup"에 대 한 부울 값을 반환 합니다.
* **노이즈** 지저분한 얼굴 이미지에서 검색 합니다. 이 비공식적인 등급 ("낮음", "medium", "높음") 뿐만 아니라 0 및 1 사이의 값을 반환합니다.
* **폐색** 발생 하는 부분을 차단 하는 개체가 있는지 여부를 합니다. 이 "eyeOccluded", "foreheadOccluded" 및 "mouthOccluded"에 대 한 부울 값을 반환 합니다.
* **미소** 지정 된 글꼴의 웃는 얼굴 보내기 식입니다. 0 (없음 웃는 얼굴 보내기) 하나 (웃는 얼굴 보내기 지우기) 사이의 값입니다.

> [!IMPORTANT]
> 얼굴 특성을 예측 하는 통계 알고리즘을 사용 하 여 항상 정확 하지 않을 하며 특성 데이터를 기반으로 결정을 내릴 경우에 주의 해야 합니다.

## <a name="input-data"></a>데이터 입력

입력된 이미지 가장 정확한 검색 결과 얻을 수 있도록 다음 팁을 사용 합니다.

* 지원 되는 입력된 이미지 형식은 BMP, GIF (첫 번째 프레임), PNG, JPEG 없기 때문입니다.
* 이미지 파일 크기는 4MB를 넘지 않도록 해야 합니다.
* 감지 가능한 얼굴 크기 범위는 36x36 픽셀부터 4096x4096 픽셀 사이입니다. 이 범위 밖의 얼굴을 검색할 수 없습니다.
* 일부 얼굴 기술적인 문제 때문에 검색 되지 않을 수 있습니다. 극단적인 얼굴 각도 (머리 포즈) 또는 얼굴 폐색 (개체 선글라스 발생 하는 부분을 차단 하는 실습 등) 검색을 달라질 수 있습니다. 정면 및 정면에 가까운 얼굴이 최상의 결과 제공합니다.

## <a name="next-steps"></a>다음 단계

얼굴 검색 개념에 익숙한 했으므로 지정된 된 이미지에서 얼굴을 감지 하는 간단한 스크립트를 작성 하는 방법에 알아봅니다.

* [이미지에서 얼굴을 감지 하는 방법](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)