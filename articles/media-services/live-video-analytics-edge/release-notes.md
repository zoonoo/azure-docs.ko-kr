---
title: IoT Edge 릴리스 정보에 대 한 라이브 비디오 분석-Azure
description: 이 항목에서는 IoT Edge 릴리스, 개선 사항, 버그 수정 및 알려진 문제에 대 한 라이브 비디오 분석의 릴리스 정보를 제공 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260326"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 릴리스 정보에 대 한 라이브 비디오 분석

>이 URL(`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`)을 RSS 피드 판독기에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 하는 시기에 대한 알림을 받습니다.

이 문서에서는 다음에 대한 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="june-1-2020"></a>2020년 6월 1일

이 릴리스는 IoT Edge에서 라이브 비디오 분석의 첫 번째 공개 미리 보기 릴리스입니다. 릴리스 태그는

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>지원 되는 기능
* 원하는 AI 모듈을 사용 하 여 라이브 비디오 스트림을 분석 하 고 필요에 따라 edge 장치 또는 클라우드에서 비디오 녹화
* IoT Edge에서 [지 원하는](https://docs.microsoft.com/azure/iot-edge/support) Linux AMD64 운영 체제에서 사용
* Azure Portal 또는 Visual Studio Code를 사용 하 여 IoT Hub를 통해 모듈을 배포 하 고 구성 합니다.
* 다음 직접 메서드 호출을 통해 [그래프 토폴로지 및 그래프 인스턴스](media-graph-concept.md#media-graph-topologies-and-instances) 를 원격 또는 로컬로 관리

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>다음 단계

[개요](overview.md)
