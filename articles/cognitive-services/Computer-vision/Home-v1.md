---
title: Computer Vision API란?
titlesuffix: Azure Cognitive Services
description: Computer Vision API는 개발자에게 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 812f957ffe2a3f24ead4ef0fe66a25c3cfd17995
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866915"
---
# <a name="what-is-computer-vision-api-version-10"></a>Computer Vision API 버전 1.0이란?

> [!IMPORTANT]
> 이제 새 버전의 Computer Vision API를 사용할 수 있습니다. 다음을 참조하세요.
>- [개요](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Computer Vision API 버전 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

클라우드 기반 Computer Vision API를 사용하면 개발자가 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 액세스할 수 있습니다. 이미지를 업로드하거나 이미지 URL을 지정하면 Microsoft Computer Vision 알고리즘이 입력 및 사용자 선택에 따라 다양한 방식으로 시각적 콘텐츠를 분석할 수 있습니다. Computer Vision API를 통해 사용자는 이미지를 분석하여 다음을 수행할 수 있습니다.
* 콘텐츠를 기준으로 이미지에 태그 지정
* 이미지 분류
* 이미지의 형식 및 품질 식별
* [사람 얼굴 감지 및 좌표 반환](#Faces)
* 도메인 특정 콘텐츠 인식
* 콘텐츠에 대한 설명 생성
* 광학 문자 인식을 사용하여 이미지에서 찾은 인쇄된 텍스트 식별
* 필기한 텍스트 인식
* 색 구성표 구분
* 성인 콘텐츠에 플래그 지정
* 썸네일로 사용할 사진 자르기

## <a name="requirements"></a>요구 사항
* 지원되는 입력 방법: application/octet stream 또는 이미지 URL 형식의 원시 이미지 이진.
* 지원되는 이미지 형식: JPEG, PNG, GIF, BMP.
* 이미지 파일 크기: 4MB 미만.
* 이미지 크기: 50 x 50픽셀 초과.

## <a name="tagging-images"></a>이미지에 태그 지정
Computer Vision API에서는 수천 개의 인식할 수 있는 사물, 생물, 경치 및 작업을 기준으로 태그를 반환합니다. 태그가 모호하거나 누구나 알 수 있는 것이 아닌 경우 API 응답은 알려진 설정의 컨텍스트에서 태그의 의미를 명확히 설명하는 ‘힌트’를 제공합니다. 태그는 분류로 구성되지 않으며 상속 계층이 없습니다. 콘텐츠 태그 컬렉션은 완전한 문장으로 작성되고, 사람이 읽을 수 있는 언어로 표시되는 이미지 ‘설명’의 토대가 됩니다. 이 시점에서 이미지 설명에 대해 지원되는 언어는 영어뿐입니다.

이미지를 업로드하거나 이미지 URL을 지정한 후 Computer Vision API의 알고리즘은 이미지에서 식별된 사물, 생물 및 작업을 기준으로 태그를 출력합니다. 태그 지정은 전면에 있는 사람과 같은 주 피사체로 제한되지 않고 설정(실내 또는 옥외), 가구, 도구, 식물, 동물, 액세서리, 가젯 등도 포함합니다.

### <a name="example"></a>예
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>이미지 분류
Computer Vision API는 태그 지정 및 설명 외에도 이전 버전에서 정의된 분류 기반 범주를 반환합니다. 이러한 범주는 부모/자식 계층 구조를 가진 분류로 구성되어 있습니다. 모든 범주는 영어로 표시됩니다. 범주는 단독으로 또는 새 모델과 함께 사용할 수 있습니다.

### <a name="the-86-category-concept"></a>86개 범주 개념
다음 다이어그램에 표시된 86개 개념 목록에 따라 이미지에서 찾은 시각적 기능을 대분류에서 소분류까지 분류할 수 있습니다. 전체 분류를 텍스트 형식으로 보려면 [범주 분류](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)를 참조하세요.

![범주 분석](./Images/analyze_categories.png)

이미지                                                  | response
------------------------------------------------------ | ----------------
![여성 지붕](./Images/woman_roof.png)                 | 사람
![가족 사진](./Images/family_photo.png)             | people_crowd
![귀여운 개](./Images/cute_dog.png)                     | animal_dog
![옥외 산](./Images/mountain_vista.png)       | outdoor_mountain
![비전 분석 음식 빵](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>이미지 형식 식별
이미지를 분류하는 몇 가지 방법이 있습니다. Computer Vision API는 부울 플래그를 설정하여 이미지가 흑백인지, 컬러인지를 나타낼 수 있습니다. 플래그를 설정하여 이미지가 선 그리기인지 여부를 나타낼 수도 있습니다. 또한 이미지가 클립 아트인지 여부를 나타내고 0-3의 눈금에 품질을 표시할 수 있습니다.

### <a name="clip-art-type"></a>클립 아트 유형
이미지가 클립 아트인지 여부를 감지합니다.  

값 | 의미
----- | --------------
0     | 클립 아트 아님
1     | 모호함
2     | 보통 클립 아트
3     | 좋은 클립 아트

이미지|response
----|----
![비전 분석 치즈 클립 아트](./Images/cheese_clipart.png)|3 좋은 클립 아트
![비전 분석 주택 야드](./Images/house_yard.png)|0 클립 아트 아님

### <a name="line-drawing-type"></a>선 그리기 형식
이미지가 선 그리기인지 여부를 감지합니다.

이미지|response
----|----
![비전 분석 사자 그리기](./Images/lion_drawing.png)|True
![비전 분석 꽃](./Images/flower.png)|False

### <a name="faces"></a>얼굴
사진에서 사람 얼굴을 감지하고 얼굴 좌표, 얼굴 사각형, 성별 및 연령을 생성합니다. 이러한 시각적 기능은 얼굴에 대해 생성된 메타데이터의 하위 집합입니다. 얼굴에 대해 보다 광범위한 메타데이터(얼굴 식별, 포즈 감지 등)를 생성하려면 Face API를 사용합니다.  

이미지|response
----|----
![비전 분석 여성 지붕 얼굴](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![비전 분석 엄마 딸 얼굴](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![비전 분석 가족 사진 얼굴](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>도메인 특정 콘텐츠

Computer Vision API는 태그 지정 및 최상위 수준 분류 외에도 특수(또는 도메인 특정) 정보도 지원합니다. 특수 정보는 독립 실행형 메서드로 구현하거나 상위 수준 분류를 사용하여 구현할 수 있습니다. 도메인 특정 모델을 추가하여 86개 범주 분류를 더욱 세분화하는 수단으로 사용됩니다.

현재 유일하게 지원되는 특수 정보는 유명인 인식과 랜드마크 인식입니다. 사람 및 사람 그룹 범주와 전 세계 랜드마크에 대한 도메인 특정 세분화입니다.

도메인 특정 모델을 사용하는 두 가지 옵션이 있습니다.

### <a name="option-one---scoped-analysis"></a>옵션 1 - 범위 지정 분석
HTTP POST 호출을 수행하여 선택한 모델만 분석합니다. 이 옵션의 경우 사용할 모델을 알고 있으면 모델 이름을 지정하고, 해당 모델과 관련된 정보만 가져옵니다. 예를 들어 이 옵션을 사용하여 유명인 인식만 찾을 수 있습니다. 응답에는 잠재적 일치 유명인 목록이 신뢰도 점수와 함께 포함됩니다.

### <a name="option-two---enhanced-analysis"></a>옵션 2 - 향상된 분석
분석을 통해 86개 범주 분류의 범주와 관련된 추가 세부 정보를 제공합니다. 이 옵션은 사용자가 하나 이상 도메인 특정 모델의 세부 정보 외에 일반 이미지 분석을 가져오려는 애플리케이션에서 사용할 수 있습니다. 이 메서드를 호출하는 경우 86개 범주 분류의 분류자가 먼저 호출됩니다. 범주 중 하나라도 알려진/일치 모델의 범주와 일치할 경우 분류자 호출의 두 번째 통과가 뒤따릅니다. 예를 들어 ‘details=all’ 또는 “details”에 ‘celebrities’가 포함된 경우 메서드는 86개 범주 분류자가 호출된 후 유명인 분류자를 호출합니다. 결과에는 ‘people_’로 시작하는 태그가 포함됩니다.

## <a name="generating-descriptions"></a>설명 생성 
Computer Vision API의 알고리즘은 이미지의 콘텐츠를 분석합니다. 이 분석은 완전한 문장의, 사람이 읽을 수 있는 언어로 표시되는 ‘설명’의 토대가 됩니다. 설명은 이미지에서 찾은 내용을 요약합니다. Computer Vision API의 알고리즘은 이미지에서 식별된 개체를 기준으로 다양한 설명을 생성합니다. 설명이 각각 평가되고 신뢰도 점수가 생성됩니다. 그런 다음, 가장 높은 신뢰도 점수부터 가장 낮은 점수 순으로 정렬된 목록이 반환됩니다. 이 기술을 사용하여 이미지 캡션을 생성하는 봇의 예는 [여기](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption)에서 찾을 수 있습니다.  

### <a name="example-description-generation"></a>예제 설명 생성
![B&W 건물](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>색 구성표 인식
Computer Vision 알고리즘은 이미지에서 색을 추출합니다. 추출한 색을 전경, 배경 및 전체의 세 가지 컨텍스트에서 분석합니다. 색은 12가지 주요 강조색으로 그룹화됩니다. 해당 강조색은 검정, 파랑, 갈색, 회색, 초록, 주황, 분홍, 자주, 청록, 흰색 및 노랑입니다. 이미지의 색에 따라 단순한 흑백 또는 강조색이 16진수 색 코드로 반환될 수 있습니다.

이미지                                                       | 전경 |백그라운드| 색
----------------------------------------------------------- | --------- | ------- | ------
![옥외 산](./Images/mountain_vista.png)            | 검정     | 검정   | 흰색
![비전 분석 꽃](./Images/flower.png)               | 검정     | 흰색   | 흰색, 검정, 녹색
![비전 분석 학습 스테이션](./Images/train_station.png) | 검정     | 검정   | 검정

### <a name="accent-color"></a>강조 색
이미지에서 추출된 색으로, 주요 색과 채도를 혼합하여 사용자에게 가장 잘 맞는 색을 나타냅니다.

이미지                                                       | response
----------------------------------------------------------- | ----
![옥외 산](./Images/mountain_vista.png)            | #BC6F0F
![비전 분석 꽃](./Images/flower.png)               | #CAA501
![비전 분석 학습 스테이션](./Images/train_station.png) | #484B83


### <a name="black--white"></a>흑백
이미지가 흑백인지 여부를 나타내는 부울 플래그입니다.

이미지                                                      | response
---------------------------------------------------------- | ----
![비전 분석 건물](./Images/bw_buildings.png)      | True
![비전 분석 주택 야드](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>성인 콘텐츠에 플래그 지정
다양한 시각적 범주 중 성인/외설 그룹을 사용하면 성인용 자료를 검색하고 성적인 콘텐츠를 포함하는 이미지 표시를 제한할 수 있습니다. 사용자의 기본 설정에 맞게 슬라이딩 눈금에 성인/외설 콘텐츠 검색 필터를 설정할 수 있습니다.

## <a name="optical-character-recognition-ocr"></a>OCR(광학 문자 인식)
OCR 기술은 이미지에서 텍스트 콘텐츠를 감지하고 식별된 텍스트를 기계로 판독 가능한 문자 스트림으로 추출합니다. 검색과 의료 기록, 보안, 뱅킹 등의 기타 다양한 용도로 결과를 사용할 수 있습니다. OCR은 언어를 자동으로 감지합니다. OCR을 사용하면 텍스트를 받아쓰는 대신 사진을 찍으면 되므로 간편하고 시간도 절약됩니다.

OCR은 25개 언어를 지원합니다. 해당 언어는 아랍어, 중국어 간체, 중국어 번체, 체코어, 덴마크어, 네덜란드어, 영어, 핀란드어, 프랑스어, 독일어, 그리스어, 헝가리어, 이탈리아어, 일본어, 한국어, 노르웨이어, 폴란드어, 포르투갈어, 루마니아어, 러시아어, 세르비아어(키릴 문자 및 라틴 문자), 슬로바키아어, 스페인어, 스웨덴어 및 터키어입니다.

필요한 경우, OCR은 가로 이미지 축을 중심으로 인식된 텍스트의 회전(도)을 수정합니다. OCR은 아래 그림과 같이 각 단어의 프레임 좌표를 제공합니다.

![OCR 개요](./Images/vision-overview-ocr.png) OCR에 대한 요구 사항:
- 입력 이미지의 크기는 40 x 40 픽셀에서 3200 x 3200 픽셀 사이여야 합니다.
- 이미지는 10메가픽셀보다 클 수 없습니다.

입력 이미지를 90도의 배수에 최대 40도의 작은 각도를 더한 값만큼 회전할 수 있습니다.

텍스트 인식의 정확도는 이미지 품질에 따라 다릅니다. 다음과 같은 상황에서는 값을 부정확하게 읽을 수 있습니다.
- 흐린 이미지
- 필기체 또는 흘림체 텍스트
- 꾸밈 글꼴 스타일
- 작은 텍스트 크기
- 복잡한 배경, 텍스트 위의 그림자 또는 눈부심, 원근 왜곡
- 단어 시작 부분에 있는 너무 크거나 누락된 대문자
- 아래 첨자, 위 첨자 또는 취소선 텍스트

 제한 사항: 텍스트가 많은 사진에서는 부분적으로 인식된 단어에서 가양성이 발생할 수 있습니다. 일부 사진, 특히 텍스트가 없는 사진에서는 이미지 형식에 따라 정밀도에 큰 차이가 있을 수 있습니다.

## <a name="recognize-handwritten-text"></a>필기한 텍스트 인식
이 기술을 통해 노트, 편지, 에세이, 화이트보드, 양식 등에서 필기한 텍스트를 감지하여 추출할 수 있습니다. 이 기술은 흰색 종이, 노란색 스티커 메모 및 화이트보드와 같은 다양한 표면 및 배경에서 작동합니다.

필기한 텍스트 인식은 시간과 수고를 줄여주고, 텍스트를 기록할 필요 없이 텍스트 이미지를 촬영할 수 있게 하여 생산성을 높일 수 있습니다. 메모를 디지털화할 수 있습니다. 이러한 디지털화를 통해 빠르고 쉬운 검색을 구현할 수 있습니다. 또한 종이 문서를 줄여줍니다.

입력 요구 사항:
- 지원되는 이미지 형식: JPEG, PNG 및 BMP.
- 이미지 파일 크기는 4MB 미만이어야 합니다.
- 이미지 크기는 최소 40 x 40, 최대 3200 x 3200이어야 합니다.

참고: 이 기술은 현재 미리 보기로 제공되고 있으며, 영어 텍스트에만 사용할 수 있습니다.

## <a name="generating-thumbnails"></a>썸네일 생성
썸네일은 전체 크기 이미지의 작은 표현입니다. 휴대폰, 태블릿, PC 등 디바이스가 다양해지면서 각기 다른 UX(사용자 환경) 레이아웃과 썸네일 크기가 필요하게 되었습니다. 이 Computer Vision API 기능은 스마트 자르기를 사용하여 문제 해결을 지원합니다.

이미지를 업로드하면 고품질의 썸네일이 생성되고, Computer Vision API 알고리즘이 이미지 내의 개체를 분석합니다. 그런 다음, ‘관심 영역’의 요구 사항에 맞게 이미지를 자릅니다. 출력은 아래 그림과 같이 특수 프레임워크 내에 표시됩니다. 사용자 요구를 수용하기 위해 원래 이미지의 가로 세로 비율과 다른 가로 세로 비율을 사용하여 생성된 썸네일을 표시할 수 있습니다.

썸네일 알고리즘은 다음과 같이 작동합니다.

1. 이미지에서 방해가 되는 요소를 제거하고 주 개체인 ‘관심 영역’을 인식합니다.
2. 식별된 관심 영역에 따라 이미지를 자릅니다.
3. 대상 썸네일 크기에 맞게 가로 세로 비율을 변경합니다.

![썸네일](./Images/thumbnail-demo.png)
