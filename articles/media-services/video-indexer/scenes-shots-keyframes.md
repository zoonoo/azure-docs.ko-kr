---
title: Video Indexer 장면, 샷 및 키 프레임
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer 장면, 샷 및 키 프레임에 대 한 개요를 제공 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 3740c42c6b6721af4d885f7b63ee4ca4e58f6fa6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806717"
---
# <a name="scenes-shots-and-keyframes"></a>장면, 샷 및 키 프레임

Video Indexer는 구조적 및 의미 체계 속성에 따라 임시 단위로 비디오를 조각화 하는 것을 지원 합니다. 이 기능을 통해 고객은 다양 한 세분성을 기준으로 비디오 콘텐츠를 쉽게 탐색, 관리 및 편집할 수 있습니다. 예를 들어이 항목에서 설명 하는 장면, 샷 및 키프레임을 기반으로 합니다.   

![장면, 샷 및 키 프레임](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>장면 검색  
 
Video Indexer는 시각적 신호에 따라 비디오에서 화면이 변경 되는 시기를 결정 합니다. 장면은 단일 이벤트를 보여주고 의미상 관련 된 일련의 연속 샷을 구성 합니다. 장면 축소판 그림은 기본 샷의 첫 번째 키프레임입니다. 비디오 인덱서는 연속 촬영에서 색 일관성을 기반으로 비디오를 장면으로 분할 하 고 각 장면의 시작 및 종료 시간을 검색 합니다. 장면 검색은 비디오의 의미 체계 측면을 수량화 하는 것과 관련 하 여 까다로운 작업으로 간주 됩니다.

> [!NOTE]
> 3 개 이상의 장면을 포함 하는 비디오에 적용 됩니다.

## <a name="shot-detection"></a>샷 검색

Video Indexer 인접 한 프레임의 색 구성표에서 급격 한 전환 및 점진적인 전환을 모두 추적 하 여 시각적 신호에 따라 비디오에서 샷의 변화를 결정 합니다. 샷의 메타 데이터에는 시작 및 종료 시간과 해당 샷에 포함 된 키프레임 목록이 포함 됩니다. 이 샷을 동시에 동일한 카메라에서 가져온 연속 프레임입니다.

## <a name="keyframe-detection"></a>키 프레임 검색

샷을 가장 잘 나타내는 프레임을 선택 합니다. 키 프레임은 미적 속성 (예: contrast 및 stableness)을 기반으로 전체 비디오에서 선택한 대표 프레임입니다. Video Indexer는 키 프레임 미리 보기를 추출할 수 있는 고객을 기반으로 하 여 샷의 메타 데이터의 일부로 키 프레임 Id 목록을 검색 합니다. 

### <a name="extracting-keyframes"></a>키 프레임 풀기

비디오에 대 한 고해상도 키프레임을 추출 하려면 먼저 비디오를 업로드 하 고 인덱싱 해야 합니다.

![키프레임에서](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Video Indexer 웹 사이트

Video Indexer 웹 사이트를 사용 하 여 키 프레임을 추출 하려면 비디오를 업로드 하 고 색인 합니다. 인덱싱 작업이 완료 되 면 **다운로드** 단추를 클릭 하 고 **아티팩트 (ZIP)** 를 선택 합니다. 그러면 아티팩트 폴더가 컴퓨터에 다운로드 됩니다. 

![키프레임에서](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
압축을 풀고 폴더를 엽니다. *_KeyframeThumbnail* 폴더에서 비디오에서 추출한 모든 키 프레임을 찾습니다. 

#### <a name="with-the-video-indexer-api"></a>Video Indexer API 사용

Video Indexer API를 사용 하 여 키 프레임을 가져오려면 [비디오](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) 업로드 통화를 사용 하 여 비디오를 업로드 하 고 인덱싱합니다. 인덱싱 작업이 완료 되 면 [비디오 인덱스 가져오기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)를 호출 합니다. 그러면 JSON 파일의 콘텐츠에서 추출 Video Indexer 있는 모든 정보가 제공 됩니다.  

각 샷의 메타 데이터의 일부로 키 프레임 Id 목록을 가져옵니다. 

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

이제 [축소판 그림 가져오기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) 호출에서 이러한 각 키 프레임 id를 실행 해야 합니다. 그러면 각 키 프레임 이미지가 컴퓨터에 다운로드 됩니다. 

## <a name="editorial-shot-type-detection"></a>편집 샷 유형 검색

키 프레임은 출력 JSON의 샷과 연결 됩니다. 

Insights JSON의 개별 샷에 연결 된 샷 유형은 해당 편집 유형을 나타냅니다. 비디오를 클립, 트레일러 또는 미술 용의 특정 스타일을 검색 하는 경우 이러한 샷 유형 특성을 유용 하 게 사용할 수 있습니다. 각 샷의 첫 번째 키의 분석에 따라 다양 한 유형이 결정 됩니다. 샷을 첫 번째 키프레임에 나타나는 얼굴의 크기, 크기 및 위치로 식별 됩니다. 

샷 크기와 크기는 카메라와 프레임에 나타나는 얼굴 사이의 거리에 따라 결정 됩니다. 이러한 속성을 사용 하 여 Video Indexer는 다음과 같은 샷 유형을 검색 합니다.

* 넓게: 전체 사용자의 본문을 표시 합니다.
* 보통: 개인의 위쪽 본문과 얼굴을 표시 합니다.
* 닫기: 주로 사용자의 얼굴을 표시 합니다.
* 매우 가깝게: 화면을 채우는 사람의 얼굴을 표시 합니다. 

또한 샷 유형은 프레임의 중심을 기준으로 주제 문자의 위치에 따라 결정 될 수 있습니다. 이 속성은 Video Indexer에서 다음과 같은 샷 유형을 정의 합니다.

* 왼쪽: 사람이 프레임의 왼쪽에 표시 됩니다.
* 가운데 얼굴: 사람이 프레임의 중앙 영역에 나타납니다.
* 오른쪽 얼굴: 사람이 프레임의 오른쪽에 나타납니다.
* 옥외: 개인은 옥외 설정에 표시 됩니다.
* 실내: 개인이 실내 설정에 표시 됩니다.

추가 특성:

* 두 가지 샷: 중간 크기의 두 사람 얼굴을 표시 합니다.
* 여러 얼굴: 세 명 이상이 있습니다.


## <a name="next-steps"></a>다음 단계

[API에 의해 생성 된 Video Indexer 출력을 검사 합니다.](video-indexer-output-json-v2.md#scenes)
