---
title: 비디오 인덱서 장면, 샷 및 키프레임
titleSuffix: Azure Media Services
description: 이 항목에서는 비디오 인덱서 장면, 샷 및 키프레임에 대한 개요를 제공합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245942"
---
# <a name="scenes-shots-and-keyframes"></a>장면, 샷 및 키 프레임

비디오 인덱서(Video Indexer)는 구조 및 의미 속성을 기반으로 비디오를 시간 단위로 분할하는 것을 지원합니다. 이 기능을 통해 고객은 다양한 세부 정보를 기반으로 비디오 콘텐츠를 쉽게 검색, 관리 및 편집할 수 있습니다. 예를 들어 이 항목에서 설명하는 장면, 샷 및 키프레임을 기반으로 합니다.   

![장면, 샷 및 키 프레임](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>장면 감지  
 
비디오 인덱서에서는 시각적 신호를 기반으로 비디오가 변경되는 시기를 결정합니다. 장면은 하나의 이벤트를 묘사하고 연속 샷의 시리즈로 구성되며, 이는 시맨티컬관련이 있습니다. 장면 축소판은 기본 샷의 첫 번째 키프레임입니다. 비디오 인덱서에서는 연속 된 샷의 색상 일관성을 기반으로 장면을 장면으로 분할하고 각 장면의 시작 및 종료 시간을 검색합니다. 장면 감지는 비디오의 의미 체계 적 측면을 정량화하는 것과 관련이 있으므로 어려운 작업으로 간주됩니다.

> [!NOTE]
> 3개 이상의 장면이 포함된 동영상에 적용됩니다.

## <a name="shot-detection"></a>샷 검색

비디오 인덱서(Video Indexer)는 인접 한 프레임의 색 구성표에서 갑작스럽고 점진적인 전환을 모두 추적하여 시각적 신호를 기반으로 비디오의 샷이 변경되는 시기를 결정합니다. 샷의 메타데이터에는 시작 및 종료 시간뿐만 아니라 해당 샷에 포함된 키프레임 목록이 포함됩니다. 샷은 동시에 동일한 카메라에서 촬영한 연속 프레임입니다.

## <a name="keyframe-detection"></a>키프레임 감지

비디오 인덱서에서는 각 샷을 가장 잘 나타내는 프레임을 선택합니다. 키프레임은 미적 특성(예: 대비 및 안정성)에 따라 전체 비디오에서 선택된 대표 프레임입니다. Video Indexer는 고객이 키프레임을 고해상도 이미지로 추출할 수 있는 데이터를 기반으로 샷 메타데이터의 일부로 키프레임 아이디 목록을 검색합니다.  

### <a name="extracting-keyframes"></a>키프레임 추출

동영상의 고해상도 키프레임을 추출하려면 먼저 동영상을 업로드하고 색인을 생성해야 합니다.

![키프레임을](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>비디오 인덱서 웹 사이트사용

비디오 인덱서 웹 사이트를 사용하여 키프레임을 추출하려면 비디오를 업로드하고 색인을 생성합니다. 인덱싱 작업이 완료되면 **다운로드** 버튼을 클릭하고 **ZIP(아티팩트)을 선택합니다.** 그러면 아티팩트 폴더가 컴퓨터에 다운로드됩니다. 

![키프레임을](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
압축을 풀고 폴더를 엽니다. *_KeyframeThumbnail* 폴더에서 비디오에서 추출 된 모든 키프레임을 찾을 수 있습니다. 

#### <a name="with-the-video-indexer-api"></a>비디오 인덱서 API 사용

비디오 인덱서 API를 사용하여 키프레임을 얻으려면 비디오 호출 [업로드를](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) 사용하여 비디오를 업로드하고 색인을 생성합니다. 인덱싱 작업이 완료되면 [비디오 인덱스 받기를](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)호출합니다. 이렇게 하면 비디오 인덱서가 JSON 파일의 콘텐츠에서 추출한 모든 통찰력을 얻을 수 있습니다.  

각 샷의 메타데이터의 일부로 키프레임 아이디 목록을 받게 됩니다. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

이제 [썸네일 받기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) 호출에서 이러한 각 키프레임 아이디를 실행해야 합니다. 이렇게 하면 각 키프레임 이미지가 컴퓨터에 다운로드됩니다. 

## <a name="editorial-shot-type-detection"></a>에디토리얼 샷 유형 감지

키프레임은 출력 JSON의 샷과 연결됩니다. 

인사이트 JSON의 개별 샷과 연관된 샷 유형은 에디토리얼 유형을 나타냅니다. 클립을 클립, 예고편으로 편집하거나 예술적 목적으로 특정 키프레임 스타일을 검색할 때 이러한 샷 유형 특성이 유용할 수 있습니다. 각 샷의 첫 번째 키프레임 분석을 기반으로 다양한 유형이 결정됩니다. 샷은 첫 번째 키프레임에 나타나는 얼굴의 배율, 크기 및 위치로 식별됩니다. 

샷 크기와 배율은 프레임에 나타나는 카메라와 얼굴 사이의 거리에 따라 결정됩니다. 이러한 속성을 사용하여 Video Indexer는 다음 샷 유형을 검색합니다.

* 와이드: 전체 사람의 몸을 보여줍니다.
* 중간: 사람의 상체와 얼굴을 표시합니다.
* 클로즈업: 주로 사람의 얼굴을 보여줍니다.
* 극단적인 클로즈업: 화면을 채우는 사람의 얼굴을 표시합니다. 

샷 유형은 프레임의 중심에 대해 피사체 캐릭터의 위치에 따라 결정될 수도 있습니다. 이 속성은 비디오 인덱서에서 다음 샷 유형을 정의합니다.

* 왼쪽 얼굴: 사람이 프레임의 왼쪽에 나타납니다.
* 가운데면: 프레임의 중앙 영역에 사람이 나타납니다.
* 오른쪽 얼굴: 프레임 오른쪽에 사람이 나타납니다.
* 야외: 사람이 야외 환경에서 나타납니다.
* 실내: 사람이 실내 설정에 나타납니다.

추가 특성:

* 두 장의 샷: 중간 크기의 두 사람의 얼굴을 보여줍니다.
* 여러 얼굴: 두 명 이상.


## <a name="next-steps"></a>다음 단계

[API에서 생성된 비디오 인덱서 출력 검사](video-indexer-output-json-v2.md#scenes)
