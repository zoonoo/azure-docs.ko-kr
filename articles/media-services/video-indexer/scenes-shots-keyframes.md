---
title: Video Indexer 장면, 샷 및 키 프레임-Azure
titlesuffix: Azure Media Services
description: 이 항목에서는 Video Indexer 장면, 샷 및 키 프레임에 대 한 개요를 제공 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815660"
---
# <a name="scenes-shots-and-keyframes"></a>장면, 샷 및 키 프레임

Video Indexer는 구조적 및 의미 체계 속성에 따라 임시 단위로 비디오를 조각화 하는 것을 지원 합니다. 이 기능을 통해 고객은 다양 한 세분성을 기준으로 비디오 콘텐츠를 쉽게 탐색, 관리 및 편집할 수 있습니다. 예를 들어이 항목에서 설명 하는 장면, 샷 및 키프레임을 기반으로 합니다.   

![장면, 샷 및 키 프레임](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>장면 검색  
 
Video Indexer는 시각적 신호에 따라 비디오에서 화면이 변경 되는 시기를 결정 합니다. 장면은 단일 이벤트를 보여주고 의미상 관련 된 일련의 연속 샷을 구성 합니다. 장면 축소판 그림은 기본 샷의 첫 번째 키프레임입니다. 비디오 인덱서는 연속 촬영에서 색 일관성을 기반으로 비디오를 장면으로 분할 하 고 각 장면의 시작 및 종료 시간을 검색 합니다. 장면 검색은 비디오의 의미 체계 측면을 수량화 하는 것과 관련 하 여 까다로운 작업으로 간주 됩니다.

> [!NOTE]
> 3 개 이상의 장면을 포함 하는 비디오에 적용 됩니다.

## <a name="shot-detection"></a>샷 감지

Video Indexer 인접 한 프레임의 색 구성표에서 급격 한 전환 및 점진적인 전환을 모두 추적 하 여 시각적 신호에 따라 비디오에서 샷의 변화를 결정 합니다. 샷의 메타 데이터에는 시작 및 종료 시간과 해당 샷에 포함 된 키프레임 목록이 포함 됩니다. 이 샷을 동시에 동일한 카메라에서 가져온 연속 프레임입니다.

## <a name="keyframe-detection"></a>키 프레임 검색

샷을 가장 잘 나타내는 프레임을 선택 합니다. 키 프레임은 미적 속성 (예: contrast 및 stableness)을 기반으로 전체 비디오에서 선택한 대표 프레임입니다. Video Indexer는 키 프레임 미리 보기를 추출할 수 있는 고객을 기반으로 하 여 샷의 메타 데이터의 일부로 키 프레임 Id 목록을 검색 합니다. 

키 프레임은 출력 JSON의 샷과 연결 됩니다. 

## <a name="next-steps"></a>다음 단계

[API에 의해 생성 된 Video Indexer 출력을 검사 합니다.](video-indexer-output-json-v2.md#scenes)
