---
title: 별일 것 - 언어 이해 (LUIS)
description: 이 문서는 Azure 인지 서비스 언어 이해 API에 대한 뉴스로 정기적으로 업데이트됩니다.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 6334b157f68f09f2b165c6073ba63f45a0caf3c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538021"
---
# <a name="whats-new-in-language-understanding"></a>언어 이해의 새로운 내용

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목에는 릴리스 정보, 비디오, 블로그 게시물 및 기타 유형의 정보가 포함됩니다. 서비스를 최신 상태로 유지하려면 이 페이지에 책갈피를 설정합니다.

## <a name="release-notes"></a>릴리스 정보

### <a name="march-2020"></a>2020년 3월

* 이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

### <a name="november-4-2019---ignite"></a>2019년 11월 4일 - 점화

* 비디오 - [LUIS 및 Azure 코그너티브 서비스를 사용하는 고급 자연어 이해(NLU) 모델 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 향상된 개발자 생산성
    * [예측 끝점 V3의](luis-migration-api-v3.md)일반 공급.
    * .lu[(LUDown)](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)형식으로 앱을 가져오고 내보낼 수 있습니다. 이를 통해 효과적인 CI/CD 프로세스를 위한 토대가 됩니다.
* 언어 확장
    * 공개 미리보기에서 [아랍어와 힌디어.](luis-language-support.md)
* 미리 작성된 모델
    * [미리 빌드된 도메인은](luis-reference-prebuilt-domains.md) 이제 일반적으로 사용할 수 있습니다(GA)
    * [V3에서는](luis-reference-prebuilt-entities.md#japanese-entity-support) 연령, 통화, 번호 및 백분율이 지원되지 않습니다.
    * 이탈리아어 [미리 빌드된 엔터티](luis-reference-prebuilt-entities.md#italian-entity-support) - 나이, 통화, 차원, 수 및 백분율 해상도가 V2에서 변경되었습니다.
* [preview.luis.ai 포털에서](https://preview.luis.ai) 향상된 사용자 환경 - 복잡한 모델을 빌드하고 디버깅할 수 있도록 개선된 라벨링 환경입니다. 미리 보기 포털 자습서를 사용해 보십시오.
    * [의도만](tutorial-intents-only.md)
    * [분해 가능한 기계 학습 엔터티](tutorial-machine-learned-entity.md)
* 언어 이해 기능 의 발전 - 적은 노력으로 [정교한 언어 모델을 구축합니다.](luis-concept-entity-types.md)
* 모델 수준에서 기계 학습 기능을 정의하고 모델을 다른 모델의 신호로 사용할 수 있도록 설정합니다(예: 엔터티를 의도 및 다른 엔터티에 대한 기능으로 사용).
* 새 확장 [한도](luis-boundaries.md) - 구 목록 및 총 구에 대한 최대값, 기능 제한으로 새 모델
* 심층 계층 구조 형식으로 텍스트에서 정보를 추출하여 대화 응용 프로그램을 보다 강력하게 만듭니다.

    ![머신 학습 엔터티 이미지](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019년 9월 3일

* Azure 작성 리소스 - [지금 마이그레이션합니다.](luis-migration-authoring.md)
    * Azure 리소스당 500개의 앱
    * 앱당 100개 버전
* 미리 빌드된 엔터티에 대한 터키어 지원
* datetimeV2에 대한 이탈리아어 지원

### <a name="july-23-2019"></a>2019년 7월 23일

* [인식기-텍스트를](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 1.2.3으로 업데이트
    * 이탈리아어로 나이, 온도, 치수 및 통화 인식기.
    * 추수 감사절 기반 날짜를 올바르게 계산하기 위해 영어로 휴일 인식이 개선되었습니다.
    * 프랑스어 DateTime의 개선으로 비날짜 및 비시간 엔터티의 거짓 긍정을 줄입니다.
    * 캘린더/학교/회계 연도 및 영어 DateRange의 약어지원.
    * 중국어와 일본어로 전화 번호 인식이 향상되었습니다.
    * 영어로 NumberRange에 대한 지원이 향상되었습니다.
    * 성능 향상

### <a name="june-24-2019"></a>2019년 6월 24일

* [다음,](luis-reference-prebuilt-ordinal-v2.md) 이전 및 마지막과 같은 순서를 지원하기 위해 미리 빌드된 엔터티입니다. 영어 문화만.

### <a name="may-6-2019---build-conference"></a>2019년 5월 6일 - //빌드 컨퍼런스

빌드 2019 컨퍼런스에서 다음과 같은 기능이 릴리스되었습니다.

* [V3 API 마이그레이션 가이드 미리 보기](luis-migration-api-v3.md)
* [향상된 분석 대시보드](luis-how-to-use-dashboard.md)
* [미리 빌드된 도메인개선](luis-reference-prebuilt-domains.md)
* [동적 목록 엔터티](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [외부 엔터티](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>블로그

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>동영상

### <a name="2019-ignite-videos"></a>2019 점화 동영상

[LUIS 및 Azure 인지 서비스를 사용하는 고급 자연어 이해(NLU) 모델 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 비디오 빌드

[Azure 대화형 AI를 사용하여 차세대 비즈니스를 확장하는 방법](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>서비스 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)
