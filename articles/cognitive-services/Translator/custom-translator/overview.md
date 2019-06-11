---
title: Custom Translator란?
titleSuffix: Azure Cognitive Services
description: Custom Translator는 Microsoft Translator Hub가 SMT(통계 기계 번역)에 제공하는 것과 비슷한 기능을 제공하지만, NMT(인공신경망 기계 번역) 시스템에만 단독으로 제공합니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: overview
ms.openlocfilehash: 5ce73e28d25f0e517da08895764adc5a31740a7e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389646"
---
# <a name="what-is-custom-translator"></a>Custom Translator란?

[Custom Translator](https://portal.customtranslator.azure.ai)는 Translator 엔터프라이즈, 앱 개발자 및 언어 서비스 공급자가 사용자 지정 NMT(인공신경망 기계 번역)를 빌드할 수 있게 해주는 Microsoft Translator 서비스의 기능입니다. 사용자 지정된 번역 시스템은 중단 없이 기존 애플리케이션, 워크플로 및 웹 사이트에 통합됩니다. [Custom Translator](https://portal.customtranslator.azure.ai/)는 [Microsoft Translator Hub](https://hub.microsofttranslator.com/)가 SMT(통계 기계 번역)에 제공하는 것과 비슷한 기능을 제공하지만, NMT(인공신경망 기계 번역) 시스템에만 단독으로 제공합니다.

[Custom Translator](https://portal.customtranslator.azure.ai)를 사용하여 빌드된 번역 시스템은 매일 수십억 건을 번역할 수 있는 동일한 클라우드 기반의 [안전하고](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality) 성능과 확장성이 우수한 Microsoft Translator [Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)를 통해 사용할 수 있습니다.

Custom Translator는 36개가 넘는 언어를 지원하며, NMT에 제공되는 언어에 직접 매핑됩니다. 전체 목록은 [Microsoft Translator 언어](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)를 참조하세요.

## <a name="features"></a>기능

Custom Translator는 사용자 지정 번역 시스템을 빌드하고 액세스하는 다양한 기능을 제공합니다.

|기능  |설명  |
|---------|---------|
|[인공신경망 기계 번역 기술 활용](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Custom translator가 제공하는 NMT(인공신경망 기계 번역)를 활용하여 번역 품질을 높일 수 있습니다.       |
|[비즈니스 용어를 알고 있는 시스템 빌드](what-are-parallel-documents.md)     |  병렬 문서를 사용하여 고객의 비즈니스 및 산업 분야에 사용되는 용어를 이해하는 시스템을 사용자 지정 및 빌드할 수 있습니다.       |
|[사전을 사용하여 모델 빌드](what-is-dictionary.md)     |   학습 데이터 세트가 없는 경우 사전 데이터만으로 모델을 학습시킬 수 있습니다.       |
|[다른 사람과 공동 작업](how-to-manage-settings.md#share-your-workspace)     |   다른 사용자와 작업을 공유하여 팀과 공동으로 작업할 수 있습니다.     |
|[사용자 지정 번역 모델에 액세스](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  언제든지 Microsoft Translator Text API V3를 통해 기존 애플리케이션/프로그램에서 사용자 지정 번역 모델에 액세스할 수 있습니다.       |

## <a name="get-better-translations"></a>보다 우수한 번역 얻기

Microsoft Translator는 2016년에 [NMT(인공신경망 기계 번역)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)를 출시했습니다. NMT의 번역 품질은 산업 표준 [SMT(통계 기계 번역)](https://en.wikipedia.org/wiki/Statistical_machine_translation) 기술보다 훨씬 발전했습니다. NMT는 번역하기 전에 전체 문장의 컨텍스트를 보다 정확하게 캡처하므로 보다 우수하고 사람의 말과 비슷하고 자연스러운 품질을 제공합니다. [Custom Translator](https://portal.customtranslator.azure.ai)는 사용자 지정 모델에 NMT를 제공하여 결과적으로 번역 품질을 개선합니다.

이전에 번역된 문서를 사용하여 번역 시스템을 빌드할 수 있습니다. 이러한 문서로는 도메인 관련 용어 및 스타일이 포함되며 일반 번역 시스템보다 우수합니다. 사용자는 ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX 및 XLSX 문서를 업로드할 수 있습니다.

또한 Custom Translator는 문서 수준에서 병렬인 데이터를 수락하여 데이터 수집 및 준비의 효율성을 높입니다. 사용자가 여러 언어로 되어 있으나 별도 문서에 있는 동일한 콘텐츠 버전에 액세스할 수 있는 경우 Custom Translator는 문서 간에 문장을 자동으로 일치시킬 수 있습니다.

적절한 형식과 양의 학습 데이터를 제공하면 Custom Translator를 사용하여 5-10점 사이의 [BLEU 점수](what-is-bleu-score.md)를 자주 볼 수 있습니다.

## <a name="be-productive-and-cost-effective"></a>생산성 및 경제성

[Custom Translator](https://portal.customtranslator.azure.ai)를 사용하면 사용자 지정 시스템을 교육하고 배포하는 데 프로그래밍 기술이 필요하지 않습니다.

사용자는 안전한 [Custom Translator](https://portal.customtranslator.azure.ai) 포털을 사용하여 직관적인 사용자 인터페이스를 통해 교육 데이터를 업로드하고, 시스템을 교육하고, 시스템을 테스트하고, 프로덕션 환경에 배포할 수 있습니다. 그러면 몇 시간 내에 시스템을 대규모로 사용할 수 있습니다(실제 시간은 교육 데이터 크기에 따라 다름).

[전용 API](https://custom-api.cognitive.microsofttranslator.com/swagger/)(현재 미리 보기로 제공)를 통해 프로그래밍 방식으로 [Custom Translator](https://portal.customtranslator.azure.ai)에 액세스할 수도 있습니다. API를 사용하면 사용자가 자체 앱 또는 웹 서비스를 통해 정기적으로 교유 데이터를 만들거나 업데이트할 수 있습니다.

사용자 지정 모델을 사용하여 콘텐츠를 번역하는 데 드는 비용은 사용자의 Translator Text API 가격 책정 계층에 따라 결정됩니다. 가격 책정 계층에 대한 자세한 내용은 Cognitive Services [Translator Text API 가격 책정 웹 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)를 참조하세요.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>언제, 어디서나 모든 앱 및 서비스에서 안전하게 번역

모든 디바이스에서 REST 기술을 사용하여 Microsoft Translator Text API를 통해 사용자 지정 시스템에 원활하게 액세스하고 모든 제품 또는 비즈니스 워크플로에 통합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [가격 정보](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)를 참조합니다.

- [빠른 시작](quickstart-build-deploy-custom-model.md)을 통해 Custom Translator에서 번역 모델을 빌드하는 방법을 알아봅니다.
