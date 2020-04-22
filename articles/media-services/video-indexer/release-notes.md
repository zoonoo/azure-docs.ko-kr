---
title: Azure 미디어 서비스 비디오 인덱서 릴리스 정보 | 마이크로 소프트 문서
description: 최신 개발 을 최신 상태로 유지하기 위해 이 문서에서는 Azure Media 서비스 비디오 인덱서에 대한 최신 업데이트를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: 5aa7abf7aafc14e71af5618cec892ef9f843d88a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733065"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 미디어 서비스 비디오 인덱서 릴리스 정보

>이 URL을 복사하여 붙여넣기하여 이 페이지를 다시 방문할 시기에 `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` 대한 알림을 RSS 피드 리더에 붙여넣습니다.

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="april-2020"></a>2020년 4월

### <a name="new-widget-parameters-capabilities"></a>새로운 위젯 매개 변수 기능

**Insights** 위젯에는 새로운 매개 `language` 변수가 포함되어 `control`있습니다.

**플레이어** 위젯에 새 `locale` 매개 변수가 있습니다. 둘 `locale` `language` 다 와 매개 변수는 플레이어의 언어를 제어합니다.

자세한 내용은 [위젯 유형](video-indexer-embed-widgets.md#widget-types) 섹션을 참조하세요. 

## <a name="new-player-skin"></a>새로운 플레이어 스킨

새로운 플레이어 스킨이 업데이트된 디자인으로 출시되었습니다.

## <a name="january-2020"></a>2020년 1월
 
### <a name="custom-language-support-for-additional-languages"></a>추가 언어에 대한 사용자 지정 언어 지원

이제 비디오 인덱서에서 `ar-SY` 에 `en-UK`대한 `en-AU` 사용자 지정 언어 모델을 지원하고 (API만).
 
### <a name="delete-account-timeframe-action-update"></a>계정 기간 작업 업데이트 삭제

계정 삭제 작업은 이제 48시간이 아닌 90일 이내에 계정을 삭제합니다.
 
### <a name="new-video-indexer-github-repository"></a>새 비디오 인덱서 GitHub 리포지토리

다양한 프로젝트를 갖춘 새로운 비디오 인덱서 GitHub, 시작 가이드 및 코드 샘플을 사용할 수 있습니다.https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>스와거 업데이트

비디오 인덱서 **통합 인증** 및 **작업을** 단일 [비디오 인덱서 OpenAPI 사양(swagger)으로](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)통합합니다. 개발자는 [비디오 인덱서 개발자 포털에서](https://api-portal.videoindexer.ai/)API를 찾을 수 있습니다.

## <a name="december-2019"></a>2019년 12월

### <a name="update-transcript-with-the-new-api"></a>새 API로 자막 업데이트

[업데이트-비디오-인덱스](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) API를 사용하여 성적 증명서의 특정 섹션을 업데이트합니다.

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>비디오 인덱서 포털에서 계정 구성 수정

이제 다음과 같은 문제에 대한 자가 지원을 위해 Media Services 연결 구성을 업데이트할 수 있습니다. 

* 잘못된 Azure 미디어 서비스 리소스
* 암호 변경
* 구독 간에 미디어 서비스 리소스가 이동되었습니다.  

계정 구성을 수정하려면 Video Indexer 포털에서 계정 설정(소유자)> 이동합니다.

### <a name="configure-the-custom-vision-account"></a>사용자 지정 비전 계정 구성

Video Indexer 포털을 사용하여 유료 계정에서 사용자 지정 비전 계정을 구성했습니다(이전에는 API에서만 지원되었습니다). 이렇게 하려면 비디오 인덱서 포털에 로그인하여 애니메이션 캐릭터 > 구성된 모델 사용자 지정 > 선택합니다. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>장면, 샷 및 키프레임 - 이제 하나의 인사이트 창에

이제 장면, 샷 및 키프레임이 하나의 인사이트로 병합되어 더 쉽게 소비하고 탐색할 수 있습니다. 원하는 장면을 선택하면 어떤 샷과 키프레임으로 구성되는지 확인할 수 있습니다. 

### <a name="notification-about-a-long-video-name"></a>긴 동영상 이름에 대한 알림

동영상 이름이 80자보다 길면 동영상 인덱서에 업로드 시 설명 오류가 표시됩니다.

### <a name="streaming-endpoint-is-disabled-notification"></a>스트리밍 끝점이 비활성화된 알림입니다.

스트리밍 끝점을 사용하지 않도록 설정하면 비디오 인덱서가 플레이어 페이지에 설명 오류가 표시됩니다.

### <a name="error-handling-improvement"></a>오류 처리 개선

이제 상태 코드 409가 [비디오 재색인자](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) 및 [비디오 인덱스 API 업데이트에서](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) 반환되어 실수로 현재 재색인자 변경이 재지정되지 않도록 비디오가 활발하게 인덱싱됩니다.

## <a name="november-2019"></a>2019년 11월
 
* 한국어 맞춤 언어 모델 지원

    비디오 인덱서이제 API와 포털`ko-KR`모두에서 한국어()의 사용자 지정 언어 모델을 지원합니다. 
* 음성-텍스트(STT)에 지원되는 새 언어

    비디오 인덱서 API는 이제 아랍어 레반틴 (ar-SY), 영어 영국 방언 (en-GB), 영어 호주 방언 (en-AU)에서 STT를 지원합니다.
    
    비디오 업로드의 경우 zh-HANS를 zh-CN으로 대체했으며 둘 다 지원되지만 zh-CN이 권장되고 더 정확합니다.
    
## <a name="october-2019"></a>2019년 10월
 
* 갤러리에서 애니메이션 캐릭터 검색

    이제 애니메이션 캐릭터를 인덱싱할 때 계정의 비디오 갤리에서 캐릭터를 검색할 수 있습니다. 자세한 내용은 [애니메이션 문자 인식](animated-characters-recognition.md)을 참조하십시오.

## <a name="september-2019"></a>2019년 9월
 
IBC 2019에서 발표된 여러 발전:
 
* 애니메이션 캐릭터 인식(공개 미리 보기)

    사용자 지정 비전과의 통합을 통해 애니메이션 콘텐츠의 캐릭터를 인식하는 그룹 광고를 감지하는 기능입니다. 자세한 내용은 [애니메이션 문자 검색](animated-characters-recognition.md)을 참조하십시오.
* 다국어 식별(공개 미리 보기)

    오디오 트랙에서 여러 언어로 세그먼트를 감지하고 이를 기반으로 다국어 성적 증명서를 만듭니다. 초기 지원: 영어, 스페인어, 독일어, 프랑스어. 자세한 내용은 [다국어 콘텐츠를 자동으로 식별하고 전사하는](multi-language-identification-transcription.md)것을 참조하십시오.
* 사람 및 위치에 대한 명명된 엔터티 추출

    NLP(자연어 처리)를 통해 음성 및 시각적 텍스트에서 브랜드, 위치 및 사람들을 추출합니다.
* 에디토리얼 샷 유형 분류

    클로즈업, 미디엄 샷, 투샷, 실내, 실외 등과 같은 에디토리얼 유형으로 샷 태그 지정 자세한 내용은 [에디토리얼 샷 유형 검색](scenes-shots-keyframes.md#editorial-shot-type-detection)을 참조하십시오.
* 주제 추론 향상 - 이제 레벨 2를 다루고 있습니다.
    
    주제 추론 모델은 이제 IPTC 분류의 더 깊은 세분성을 지원합니다. Azure Media [Services의 새로운 AI 기반 혁신에](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)대한 자세한 내용을 읽어보십시오.

## <a name="august-2019"></a>2019년 8월
 
### <a name="video-indexer-deployed-in-uk-south"></a>영국 남부에 배포된 비디오 인덱서

이제 영국 남부 지역에서 비디오 인덱서 유료 계정을 만들 수 있습니다.

### <a name="new-editorial-shot-type-insights-available"></a>새로운 에디토리얼 샷 유형 인사이트 사용 가능

비디오 샷에 추가된 새로운 태그는 에디토리얼 "샷 유형"을 제공하여 극단적인 클로즈업, 클로즈업, 와이드, 미디엄, 투샷, 실외, 실내, 왼쪽 얼굴 및 오른쪽 얼굴(JSON에서 사용 가능)과 같은 콘텐츠 제작 워크플로우에 사용되는 일반적인 에디토리얼 문구로 식별할 수 있습니다.

### <a name="new-people-and-locations-entities-extraction-available"></a>새 사람 및 위치 엔터티 추출 가능

비디오 인덱서는 비디오의 OCR 및 전사에서 자연어 처리(NLP)를 통해 명명된 위치와 사람을 식별합니다. 비디오 인덱서에서는 기계 학습 알고리즘을 사용하여 특정 위치(예: 에펠탑) 또는 사람(예: John Doe)이 비디오에서 호출되는 경우를 인식합니다.

### <a name="keyframes-extraction-in-native-resolution"></a>네이티브 해상도의 키프레임 추출

비디오 인덱서에서 추출한 키프레임은 비디오의 원래 해상도로 사용할 수 있습니다.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>이미지에서 사용자 정의 얼굴 모델을 교육하기위한 GA

미리 보기 모드에서 GA로 이동한 이미지의 얼굴 학습(API 및 포털을 통해 사용 가능).

> [!NOTE]
> "GA로의 미리 보기" 전환과 관련된 가격 영향은 없습니다.

### <a name="hide-gallery-toggle-option"></a>갤러리 토글 옵션 숨기기

사용자는 포털에서 갤러리 탭을 숨기도록 선택할 수 있습니다(샘플 탭 숨기기와 유사).
 
### <a name="maximum-url-size-increased"></a>최대 URL 크기 증가

비디오 인덱싱에 대한 URL 쿼리 문자열(2048대신)에 대한 지원입니다.
 
### <a name="support-for-multi-lingual-projects"></a>다국어 프로젝트 지원

이제 다른 언어로 인덱싱된 비디오(API만)를 기반으로 프로젝트를 만들 수 있습니다.

## <a name="july-2019"></a>2019년 7월

### <a name="editor-as-a-widget"></a>위젯으로 편집기

이제 비디오 인덱서 AI 편집기를 고객 애플리케이션에 포함할 위젯으로 사용할 수 있습니다.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>포털에서 닫힌 캡션 파일에서 사용자 지정 언어 모델 업데이트

고객은 포털의 사용자 지정 페이지에서 언어 모델에 대한 입력으로 VTT, SRT 및 TTML 파일 형식을 제공할 수 있습니다.

## <a name="june-2019"></a>2019년 6월

### <a name="video-indexer-deployed-to-japan-east"></a>일본 동부에 배포된 비디오 인덱서

이제 일본 동부 지역에서 비디오 인덱서 유료 계정을 만들 수 있습니다.

### <a name="create-and-repair-account-api-preview"></a>계정 API 생성 및 복구(미리 보기)

Azure Media 서비스 연결 [끝점 또는 키를 업데이트할](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)수 있는 새 API를 추가했습니다.

### <a name="improve-error-handling-on-upload"></a>업로드 시 오류 처리 개선 

기본 Azure Media Services 계정이 잘못 구성된 경우 설명 메시지가 반환됩니다.

### <a name="player-timeline-keyframes-preview"></a>플레이어 타임라인 키프레임 미리보기 

이제 플레이어의 타임라인에서 매번 이미지 미리보기를 볼 수 있습니다.

### <a name="editor-semi-select"></a>에디터 세미 셀렉티드

이제 편집기에서 특정 인사이트 기간을 선택한 결과로 선택된 모든 인사이트의 미리 보기를 볼 수 있습니다.

## <a name="may-2019"></a>2019년 5월

### <a name="update-custom-language-model-from-closed-caption-file"></a>닫힌 캡션 파일에서 사용자 지정 언어 모델 업데이트

[사용자 지정 언어 모델을 만들고](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) 사용자 지정 언어 모델 [API를 업데이트하면](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) 언어 모델에 대한 입력으로 VTT, SRT 및 TTML 파일 형식을 지원합니다.

[업데이트 비디오 성적 증명서 API를](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)호출할 때 성적 증명서가 자동으로 추가됩니다. 비디오와 연결된 학습 모델도 자동으로 업데이트됩니다. 언어 모델을 사용자 지정하고 학습하는 방법에 대한 자세한 내용은 [비디오 인덱서를 사용하여 언어 모델 사용자 지정을](customize-language-model-overview.md)참조하십시오.

### <a name="new-download-transcript-formats--txt-and-csv"></a>새로운 다운로드 성적 증명서 형식 - TXT 및 CSV

비디오 인덱서에서는 이미 지원되는 자막 형식(SRT, VTT 및 TTML) 외에도 TXT 및 CSV 형식으로 자막 다운로드를 지원합니다.

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md)
