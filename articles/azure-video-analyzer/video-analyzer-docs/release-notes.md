---
title: Azure Video Analyzer 릴리스 정보 - Azure
description: 이 항목에서는 Azure Video Analyzer 릴리스, 개선 사항, 버그 수정 및 알려진 문제에 대한 릴리스 정보를 제공합니다.
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: ffb6381fafd4aabed3bd27b14f4778d23fa7441e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387829"
---
# <a name="azure-video-analyzer-release-notes"></a>Azure Video Analyzer 릴리스 정보

>이 URL(`https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us`)을 RSS 피드 판독기에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 하는 시기에 대한 알림을 받습니다.

이 문서에서는 다음에 대한 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

<hr width=100%>

## <a name="may-25-2021"></a>2021년 5월 25일

이 릴리스는 Azure Video Analyzer의 첫 번째 공개 미리 보기 릴리스입니다. 릴리스 태그는 다음과 같습니다.

```
     mcr.microsoft.com/media/video-analyzer:1.0.0
```
> [!NOTE]
> 빠른 시작 및 자습서에서 배포 매니페스트는 태그 1(video-analyzer: 1)을 사용합니다. 따라서 이러한 매니페스트를 다시 배포하면 최신 태그가 릴리스될 때 에지 디바이스의 모듈이 업데이트되어야 합니다.

### <a name="supported-functionalities"></a>지원되는 기능

* 원하는 AI 모듈을 사용하여 라이브 비디오 스트림을 분석하고 필요에 따라 에지 디바이스 또는 클라우드에서 비디오 녹화
* 클라우드에서 유추 메타데이터와 함께 비디오 녹화
* 자체 개체 감지 모델을 사용하여 개체가 선을 넘을 때 이벤트 트리거
* 자체 감지 모델로 감지된 개체 추적 
* Video Analyzer 플레이어 위젯(웹 구성 요소)을 사용하여 녹화된 비디오 및 유추 메타데이터 재생
* Azure Portal 또는 Visual Studio Code를 사용하여 IoT Hub를 통해 모듈 배포 및 구성
* [직접 메서드](direct-methods.md) 호출을 사용하여 원격 또는 로컬로 [파이프라인 토폴로지](pipeline.md#pipeline-topologies) 관리

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
