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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 0583c0093d240026e3ebcdad7b14494f07986ec2
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968726"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer 릴리스 정보

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="october-2019"></a>2019년 10월
 
* 갤러리에서 애니메이션 문자 검색

    애니메이션 문자를 인덱싱할 때 이제 계정의 비디오 교정쇄에서 검색할 수 있습니다. 자세한 내용은 [문자 인식](animated-characters-recognition.md)(영문)을 참조 하세요.

## <a name="september-2019"></a>2019년 9월
 
IBC 2019에서 여러 가지 기능이 발표 되었습니다.
 
* 문자 인식 애니메이션 (공개 미리 보기)

    사용자 지정 비전과의 통합을 통해 애니메이션 된 콘텐츠에서 그룹 ad 인식 문자를 검색 하는 기능입니다. 자세한 내용은 [문자 검색에 애니메이션 적용](animated-characters-recognition.md)을 참조 하세요.
* 다국어 id (공개 미리 보기)

    오디오 트랙에서 여러 언어로 된 세그먼트를 검색 하 고이를 기반으로 다국어 성적 증명서를 만듭니다. 초기 지원: 영어, 스페인어, 독일어 및 프랑스어 자세한 내용은 [다중 언어 콘텐츠 자동 식별 및 높여줄](multi-language-identification-transcription.md)를 참조 하세요.
* 사용자 및 위치에 대 한 명명 된 엔터티 추출

    NLP (자연어 처리)를 통해 음성 및 시각적 텍스트에서 브랜드, 위치 및 사람을 추출 합니다.
* 편집 샷 유형 분류

    종가, 중간 샷, 두 샷, 실내, 실외 등의 편집 형식으로 샷의 태그를 지정 합니다. 자세한 내용은 [편집 샷 유형 검색](scenes-shots-keyframes.md#editorial-shot-type-detection)을 참조 하세요.
* 토픽 추론 개선-이제 수준 2 포함
    
    추론 model 항목은 이제 IPTC 분류의 세부적인 세분성을 지원 합니다. [새 AI 지원 혁신을 Azure Media Services](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)에 대 한 자세한 내용을 읽으십시오.

## <a name="august-2019"></a>2019년 8월
 
### <a name="video-indexer-deployed-in-uk-south"></a>영국 남부에 배포 된 Video Indexer

이제 영국 남부 지역에서 Video Indexer 유료 계정을 만들 수 있습니다.

### <a name="new-editorial-shot-type-insights-available"></a>새 편집 샷 유형 정보 사용 가능

비디오 샷에 추가 된 새 태그는 매우 확대/확대, 확대, 중간, 두 샷, 실외, 실내, 좌 얼굴 및 오른쪽 (에서 사용할 수 있음)과 같은 콘텐츠 생성 워크플로에 사용 되는 일반적인 편집 구를 사용 하 여 해당 항목을 식별 하는 편집 "샷 유형"을 제공 합니다. JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>새 사용자 및 위치 엔터티 추출 사용 가능

Video Indexer는 비디오의 OCR 및 기록에서 NLP (자연어 처리)를 통해 명명 된 위치 및 사용자를 식별 합니다. Video Indexer는 기계 학습 알고리즘을 사용 하 여 특정 위치 (예: Eiffel 타워) 나 사람 (예: John Doe)을 비디오에서 호출 하는 경우를 인식 합니다.

### <a name="keyframes-extraction-in-native-resolution"></a>네이티브 해상도의 키 프레임 추출

Video Indexer에 의해 추출 된 키프레임은 비디오의 원래 해상도에서 사용할 수 있습니다.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>이미지에서 사용자 지정 얼굴 모델을 학습 하기 위한 GA

미리 보기 모드에서 GA (API 및 포털을 통해 사용 가능)로 이동 된 이미지의 교육 얼굴입니다.

> [!NOTE]
> "GA로 미리 보기" 전환과 관련 된 가격에는 영향을 주지 않습니다.

### <a name="hide-gallery-toggle-option"></a>갤러리 표시/숨기기 토글 옵션

사용자는 포털에서 갤러리 탭을 숨기도록 선택할 수 있습니다 (샘플 탭 숨기기와 유사).
 
### <a name="maximum-url-size-increased"></a>최대 URL 크기 증가

비디오를 인덱싱할 때 URL 쿼리 문자열 4096 (2048 대신)을 지원 합니다.
 
### <a name="support-for-multi-lingual-projects"></a>다국어 프로젝트 지원

이제 다른 언어로 인덱싱되는 비디오 (API만 해당)를 기반으로 프로젝트를 만들 수 있습니다.

## <a name="july-2019"></a>2019년 7월

### <a name="editor-as-a-widget"></a>Widget 편집기

이제는 Video Indexer AI 편집기를 고객 응용 프로그램에 포함 시킬 수 있는 위젯로 사용할 수 있습니다.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>포털에서 자막 파일의 사용자 지정 언어 모델 업데이트

고객은 포털의 사용자 지정 페이지에서 VTT, SRT 및 TTML 파일 형식을 언어 모델에 대 한 입력으로 제공할 수 있습니다.

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
