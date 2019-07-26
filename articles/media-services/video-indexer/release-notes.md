---
title: Azure Media Services Video Indexer 릴리스 정보 | Microsoft Docs
description: 최신 개발을 최신 상태로 유지 하기 위해이 문서에서는 Azure Media Services Video Indexer 최신 업데이트를 제공 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: fd67e867b5eac58838551ac29e1fca5e860b15ff
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414223"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer 릴리스 정보

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="june-2019"></a>2019년 6월

### <a name="video-indexer-deployed-to-japan-east"></a>일본 동부에 배포 Video Indexer

이제 일본 동부 지역에서 Video Indexer 유료 계정을 만들 수 있습니다.

### <a name="create-and-repair-account-api-preview"></a>계정 API 만들기 및 복구 (미리 보기)

[Azure Media Service 연결 끝점 또는 키를 업데이트할](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)수 있는 새 API가 추가 되었습니다.

### <a name="improve-error-handling-on-upload"></a>업로드 시 오류 처리 향상 

설명 메시지는 기본 Azure Media Services 계정의 잘못 된 구성의 경우 반환 됩니다.

### <a name="player-timeline-keyframes-preview"></a>플레이어 타임 라인 키 프레임 미리 보기 

이제 플레이어의 타임 라인에서 각 시간에 대 한 이미지 미리 보기를 볼 수 있습니다.

### <a name="editor-semi-select"></a>편집기 세미 선택

이제 편집기에서 특정 통찰력을 선택한 결과로 선택 된 모든 정보에 대 한 미리 보기를 볼 수 있습니다.

## <a name="may-2019"></a>2019년 5월

### <a name="update-custom-language-model-from-closed-caption-file"></a>닫힌 캡션 파일에서 사용자 지정 언어 모델 업데이트

[사용자 지정 언어 모델 만들기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) 및 [사용자 지정 언어 모델 업데이트](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) api는 이제 VTT, SRT 및 TTML 파일 형식을 언어 모델에 대 한 입력으로 지원 합니다.

[비디오 성적 업데이트 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)를 호출 하면 자동으로 기록이 추가 됩니다. 비디오와 연결 된 학습 모델도 자동으로 업데이트 됩니다. 언어 모델을 사용자 지정 하 고 학습 하는 방법에 대 한 자세한 내용은 [Video Indexer를 사용 하 여 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조 하세요.

### <a name="new-download-transcript-formats--txt-and-csv"></a>새 다운로드 기록 형식 – TXT 및 CSV

이미 지원 되는 (SRT, VTT 및 TTML Video Indexer) 폐쇄 캡션 형식 외에도 이제는 TXT 및 CSV 형식으로 성적 증명서 다운로드를 지원 합니다.

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md)
