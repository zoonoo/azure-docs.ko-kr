---
title: Azure Media Services Video Indexer 릴리스 정보 | Microsoft Docs
description: 가장 최근 개발 작업을 사용 하 여 최신 상태로 유지 하기에이 문서에서는 Azure Media Services Video Indexer에서 최신 업데이트.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454023"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer 릴리스 정보

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="june-2019"></a>2019년 6월

### <a name="video-indexer-deployed-to-japan-east"></a>일본 동부에 배포 하는 비디오 인덱서

이제 일본 동부 지역의 계정에에서 지불을 Video Indexer를 만들 수 있습니다.

### <a name="create-and-repair-account-api-preview"></a>만들기 및 복구 계정 API (미리 보기)

할 수 있는 새 API가 추가 되었습니다 [Azure 미디어 서비스 연결 끝점 또는 키를 업데이트](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)합니다.

### <a name="improve-error-handling-on-upload"></a>업로드에서 오류를 처리를 개선 합니다. 

기본 Azure Media Services 계정의 잘못 된 구성의 경우 설명이 포함 된 메시지가 반환 됩니다.

### <a name="player-timeline-keyframes-preview"></a>플레이어 타임 라인 키프레임 미리 보기 

이제 플레이어의 타임 라인의 각 시간에 대 한 이미지 미리 보기를 볼 수 있습니다.

### <a name="editor-semi-select"></a>편집기 반 선택

이제 편집기에서 시야 시간 범위 선택의 결과로 선택 된 모든 insights 미리 보기를 볼 수 있습니다.

## <a name="may-2019"></a>2019년 5월

### <a name="update-custom-language-model-from-closed-caption-file"></a>선택된 자막 파일에서 사용자 지정 언어 모델 업데이트

[사용자 지정 언어 모델을 만들](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) 하 고 [사용자 지정 언어 모델을 업데이트](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) Api는 이제 VTT, SRT, 지원 및 TTML 파일 형식을 언어 모델에 대 한 입력으로 지정 합니다.

호출 하는 경우는 [업데이트 비디오 대 본 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), 자막을 자동으로 추가 됩니다. 비디오와 관련 된 학습 모델도 자동으로 업데이트 됩니다. 사용자 지정 언어 모델을 학습 하는 방법에 대 한 자세한 내용은 [Video Indexer를 사용 하 여 언어 모델을 사용자 지정](customize-language-model-overview.md)합니다.

### <a name="new-download-transcript-formats--txt-and-csv"></a>새 다운로드 기록 형식 – TXT 및 CSV

닫힌 캡션 이미 지원 되는 형식 (SRT, VTT, 및 TTML), 외에도 Video Indexer는 이제 TXT 및 CSV 형식으로 대 본을 다운로드 합니다.

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md)
