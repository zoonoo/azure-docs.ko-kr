---
title: Video Indexer란?
titleSuffix: Azure Media Services
description: 이 문서에서는 Azure Media Services Video Indexer 서비스에 대 한 개요를 제공 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: juliako
ms.openlocfilehash: 1c7156da171a101f04db9f83df587f5b8bc71e8f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687107"
---
# <a name="what-is-video-indexer"></a>Video Indexer란?

VI (Video Indexer)은 Azure Media Services AI 솔루션 및 Azure Cognitive Services 브랜드의 일부입니다. Video Indexer는 여러 채널 (음성, 보컬, 시각적 개체)을 기반으로 기계 학습 모델을 사용 하 여 심층 통찰력을 추출 하는 기능을 제공 합니다 (데이터 분석 또는 코딩 기술을 사용 하지 않아도). 모델을 추가로 사용자 지정 하 고 학습할 수 있습니다. 이 서비스는 심층 검색을 지원 하 고, 운영 비용을 절감 하 고, 새로운 수익 화 기회를 지원 하 고, 대량 비디오 보관에 대 한 새로운 사용자 환경을 만들 수 있습니다 (낮은 항목 장벽 포함).

Video Indexer로 정보 추출을 시작 하려면 계정을 만들고 비디오를 업로드 해야 합니다. Video Indexer에 비디오를 업로드 하는 경우 서로 다른 AI 모델을 실행 하 여 시각적 개체와 오디오를 모두 분석 합니다. Video Indexer에서 비디오를 분석 하 여 AI 모델을 통해 추출 되는 정보를 분석 합니다.

다음 다이어그램은 Video Indexer 백 엔드에서 작동 하는 방법에 대 한 기술 설명이 아닌 그림입니다.

![흐름 다이어그램 Azure Media Services Video Indexer](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안

중요 한 알림으로 Video Indexer 사용 시 관련 법률을 준수 해야 하 고 다른 사용자의 권리를 위반 하거나 다른 사용자에 게 유해한 영향을 주는 방식으로 Video Indexer 또는 Azure 서비스를 사용 하지 못할 수 있습니다.

Video Indexer에 대 한 비디오/이미지를 업로드 하기 전에, Video Indexer 및 Azure에서 데이터 사용, 처리 및 저장을 위해 법률에서 요구 하는 경우, 비디오/이미지에서 개인의 모든 필수 동의 (있는 경우)를 포함 하 여 비디오/이미지를 사용할 수 있는 적절 한 권한이 있어야 합니다. 일부 관할지에서는 생체 인식 데이터와 같은 특정 범주의 데이터를 온라인으로 처리 하 고 저장 하는 데 적합 한 법적 요구 사항을 적용할 수 있습니다. 특별 한 법적 요구 사항이 적용 되는 데이터의 처리 및 저장을 위해 Video Indexer 및 Azure를 사용 하기 전에 사용자에 게 적용 될 수 있는 이러한 법적 요구 사항을 준수 하는지 확인 해야 합니다.

Video Indexer의 규정 준수, 개인 정보 보호 및 보안에 대 한 자세한 내용은 Microsoft [보안 센터](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)를 참조 하세요. 데이터를 삭제 하는 방법을 비롯 하 여 Microsoft의 개인 정보 취급 방침, 데이터 처리 및 보존 방법에 대 한 자세한 내용은 Microsoft [개인 정보](https://privacy.microsoft.com/PrivacyStatement)취급 방침, [Online Services 약관](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") 및 [데이터 처리 추 록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")을 참조 하세요. Video Indexer를 사용 하 여 OST, DPA 및 개인정보 처리 방침의 범위에 동의 하는 것입니다.

## <a name="what-can-i-do-with-video-indexer"></a>Video Indexer를 사용하여 무엇을 수행할 수 있나요?

Video Indexer의 정보는 다양 한 시나리오에 적용할 수 있습니다.

* *심층 검색*: 비디오에서 추출한 정보를 사용 하 여 비디오 라이브러리에서 검색 환경을 향상 시킵니다. 예를 들어, 음성 단어와 얼굴을 인덱싱하면 특정 단어를 스포크 하거나 두 사람이 함께 표시 되는 경우 비디오에서 시간을 찾는 검색 환경을 사용할 수 있습니다. 비디오에서 이러한 정보를 기반으로 하는 검색은 뉴스 기관, 교육용 institutes of, 방송사, 엔터테인먼트 콘텐츠 소유자, 엔터프라이즈 LOB 앱에 적용 되며 사용자가 검색 해야 하는 비디오 라이브러리가 있는 모든 업계에 적용 됩니다.
* *콘텐츠 만들기*: 정보를 사용 하 여 정보를 추출 하는 Video Indexer에 따라 트레일러를 만들거나, reels, 소셜 미디어 콘텐츠 또는 뉴스 클립을 선택 합니다. 사용자와 레이블 모양에 대 한 키 프레임, 장면 표식 및 타임 스탬프는 생성 프로세스를 훨씬 더 빠르고 쉽게 만들 수 있도록 하 고, 사용자가 만드는 콘텐츠에 필요한 비디오 부분으로 이동할 수 있도록 합니다.
* *내게 필요한 옵션*: 장애가 있는 사용자가 콘텐츠를 사용할 수 있도록 할지 또는 다른 언어를 사용 하 여 콘텐츠를 다른 지역에 배포 하려는 경우 여러 언어로 된 비디오 인덱서에서 제공 되는 기록 및 번역을 사용할 수 있습니다.
* *수익 화*: Video Indexer 비디오의 가치를 높이는 데 도움이 될 수 있습니다. 예를 들어 광고 수익 (뉴스 미디어, 소셜 미디어 등)을 사용 하는 업계는 ad 서버에 대 한 추가 신호로 추출 된 정보를 사용 하 여 관련 광고를 제공할 수 있습니다.
* *콘텐츠*조정: 텍스트 및 시각적 콘텐츠 중재 모델을 사용 하 여 사용자가 부적절 한 콘텐츠에서 안전 하 게 보호 하 고 게시 한 콘텐츠가 조직의 값과 일치 하는지 확인 합니다. 특정 비디오를 자동으로 차단 하거나 콘텐츠에 대 한 사용자에 게 경고할 수 있습니다.
* *권장 사항*: 비디오 정보를 사용 하 여 사용자 참여를 개선 하 고 사용자에 게 관련 된 비디오를 표시할 수 있습니다. 각 비디오에 추가 메타 데이터를 태그 지정 하 여 가장 관련성이 높은 비디오를 사용자에 게 제안 하 고 필요에 따라 비디오 부분을 강조할 수 있습니다.

## <a name="features"></a>기능

다음 목록에서는 Video Indexer 비디오 및 오디오 모델을 사용 하 여 비디오에서 검색할 수 있는 정보를 보여 줍니다.

### <a name="video-insights"></a>비디오 인사이트

* **얼굴 감지**: 비디오에 표시되는 얼굴을 감지하고 그룹화합니다.
* **유명인 id**: Video Indexer은 전 세계 지도자, 행위자, actresses, 선수, 연구원, 비즈니스 및 기술 리더와 같은 100만 유명인 이상에서 자동으로 식별 합니다. 이러한 유명인에 대 한 데이터는 다양 한 웹 사이트 (IMDB, 위키백과 등) 에서도 찾을 수 있습니다.
* **계정 기반 얼굴 인식**: Video Indexer는 특정 계정에 대한 모델을 학습합니다. 그런 다음 학습 된 모델을 기반으로 비디오에서 얼굴을 인식 합니다. 자세한 내용은 [Video Indexer 웹 사이트에서 사용자 모델 사용자 지정](customize-person-model-with-website.md) 및 [Video Indexer API를 사용 하 여 개인 모델 사용자 지정](customize-person-model-with-api.md)을 참조 하세요.
* **얼굴에 대 한 미리 보기 추출** ("최상 얼굴"): 표면 (품질, 크기 및 전면 위치에 따라)의 각 그룹에서 가장 잘 캡처된 얼굴을 자동으로 식별 하 고 이미지 자산으로 추출 합니다.
* **시각적 텍스트 인식** (OCR): 비디오에 시각적으로 표시 되는 텍스트를 추출 합니다.
* **시각적 콘텐츠 조정**: 성인 및/또는 선정적 시각적 개체를 검색합니다.
* **레이블 식별**: 표시되는 시각적 개체 및 작업을 식별합니다.
* **장면**구분: 시각적 신호에 따라 비디오에서 화면이 변경 되는 시기를 결정 합니다. 장면은 단일 이벤트를 보여주고 의미상 관련 된 일련의 연속 샷을 구성 합니다.
* **샷 검색**: 시각적 신호에 따라 비디오에서 샷을 변경 하는 시기를 결정 합니다. 샷은 동일한 이동 그림 카메라에서 가져온 일련의 프레임입니다. 자세한 내용은 [장면, 샷 및 키 프레임](scenes-shots-keyframes.md)을 참조 하세요.
* **검정 프레임 감지**: 비디오에 표시되는 검은색 프레임을 식별합니다.
* **키 프레임 추출**: 비디오에서 안정적인 키 프레임을 검색합니다.
* **롤링 크레딧**: TV 쇼 및 동영상의 끝에 있는 롤링 크레딧의 시작과 끝을 식별 합니다.
* **애니메이션 문자 검색** (미리 보기): [Cognitive Services 사용자 지정 비전과](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)의 통합을 통해 애니메이션 내용에서 문자를 검색, 그룹화 및 인식 합니다. 자세한 내용은 [문자 검색에 애니메이션 적용](animated-characters-recognition.md)을 참조 하세요.
* **편집 샷 유형 검색**: 다양 한 유형 (예: 넓은 샷, 중간 샷, 근접, 매우 근접 한 가까이, 두 샷, 여러 사람, 실외, 실내 등)을 기반으로 하 여 샷을 태그를 지정 합니다. 자세한 내용은 [편집 샷 유형 검색](scenes-shots-keyframes.md#editorial-shot-type-detection)을 참조 하세요.

### <a name="audio-insights"></a>오디오 인사이트

* **오디오 녹음**: 음성을 12 개 언어로 텍스트로 변환 하 고 확장을 허용 합니다. 지원 되는 언어는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 북경어 중국어, 일본어, 아랍어, 러시아어, 포르투갈어 (브라질), 힌디어, 한국어를 포함 합니다.
* **자동 언어 감지**: 기준 음성 언어를 자동으로 식별합니다. 지원 되는 언어는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 북경어 중국어, 일본어, 러시아어 및 포르투갈어 (브라질)입니다. 언어를 신뢰할 수 있는 언어로 식별할 수 없는 경우 Video Indexer는 음성 언어를 영어로 가정 합니다. 자세한 내용은 [언어 식별 모델](language-identification-model.md)을 참조 하세요.
* **다국어 음성 식별 및** 기록 (미리 보기): 오디오의 다른 세그먼트에서 음성 언어를 자동으로 식별 합니다. 미디어 파일의 각 세그먼트를 transcribed로 보낸 후 기록을 다시 하나의 통합 된 기록으로 결합 합니다. 자세한 내용은 [다중 언어 콘텐츠 자동 식별 및 높여줄](multi-language-identification-transcription.md)를 참조 하세요.
* **자막**: 자막을 VTT, TTML, SRT의 세 가지 형식으로 만듭니다.
* **두 채널 처리**: 자동 검색은 개별 기록 및 병합을 단일 타임 라인으로 검색 합니다.
* **노이즈 감소**: 전화 통신 오디오 또는 잡음이 있는 기록 (Skype 필터 기반)을 지웁니다.
* **성적 사용자 지정** (cris): 텍스트 모델에 대 한 사용자 지정 음성을 학습 하 여 산업별 성적 증명서를 만듭니다. 자세한 내용은 [Video Indexer 웹 사이트에서 언어 모델 사용자 지정](customize-language-model-with-website.md) 및 [Video Indexer api를 사용 하 여 언어 모델 사용자 지정](customize-language-model-with-api.md)을 참조 하세요.
* **스피커 열거**: 지도와 어떤 발표자가 어떤 단어와 시기를 결정 합니다.
* **스피커 통계**: 스피커의 음성 비율에 대 한 통계를 제공 합니다.
* **텍스트 콘텐츠 조정**: 오디오 전사에서 명시적 텍스트를 검색합니다.
* **오디오 효과**: 손 모양 ap, 음성 및 침묵 같은 오디오 효과를 식별 합니다.
* **Emotion 검색**: 음성 (언급 되는 항목) 및 음성 tonality (설명)를 기반으로 하는 감정을를 식별 합니다. Emotion는, sadness 분노 또는 그에 대 한 걱정을 받을 수 있습니다.
* **변환**: 오디오 전사의 변환을 서로 다른 54개의 언어로 만듭니다.

### <a name="audio-and-video-insights-multi-channels"></a>오디오 및 비디오 정보 (멀티 채널)

한 채널을 사용 하 여 인덱싱하는 경우 해당 모델에 대 한 부분 결과를 사용할 수 있습니다.

* **키워드 추출**: 음성 및 시각적 텍스트에서 키워드를 추출 합니다.
* **명명 된 엔터티 추출**: NLP (자연어 처리)를 통해 음성 및 시각적 텍스트에서 브랜드, 위치 및 사람을 추출 합니다.
* **주제 유추**: 전사에서 주요 주제를 추정합니다. 두 번째 수준 IPTC 분류가 포함 됩니다.
* **아티팩트**: 각 모델에 대한 풍부한 아티팩트 세트("다음 수준의 세부 정보")를 추출합니다.
* **감정 분석**: 음성 및 시각적 텍스트에서 긍정적, 부정적 및 중립적 감정을 식별합니다.

## <a name="how-can-i-get-started-with-video-indexer"></a>Video Indexer를 시작 하려면 어떻게 해야 하나요?

다음 세 가지 방법으로 Video Indexer 기능에 액세스할 수 있습니다.

* Video Indexer 포털: 제품을 평가 하 고, 계정을 관리 하 고, 모델을 사용자 지정할 수 있는 사용 하기 쉬운 솔루션입니다.

    포털에 대 한 자세한 내용은 [Video Indexer 웹 사이트 시작](video-indexer-get-started.md)을 참조 하세요.  

* API 통합: 응용 프로그램 및 인프라에 솔루션을 통합할 수 있는 REST API를 통해 Video Indexer의 모든 기능을 사용할 수 있습니다.

    개발자로 시작 하려면 [Video Indexer REST API 사용](video-indexer-use-apis.md)을 참조 하세요.

* 포함 가능한 위젯: Video Indexer insights, 플레이어 및 편집자 환경을 앱에 포함할 수 있습니다.

    자세한 내용은 [응용 프로그램에 시각적 위젯 포함](video-indexer-embed-widgets.md)을 참조 하세요.

웹 사이트를 사용 하는 경우 정보는 메타 데이터로 추가 되 고 포털에 표시 됩니다. Api를 사용 하는 경우에는 JSON 파일로 정보를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Video Indexer를 시작할 준비가 되었습니다. 자세한 내용은 다음 아티클을 참조하세요.

- [Video Indexer 웹 사이트를 시작](video-indexer-get-started.md)하세요.
- [Video Indexer REST API를 사용 하 여 콘텐츠를 처리](video-indexer-use-apis.md)합니다.
- [응용 프로그램에 시각적 위젯을 포함](video-indexer-embed-widgets.md)합니다.
