---
title: 지원되지 않는 언어 배포 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Custom Translator에서 지원되지 않는 언어 쌍을 배포하는 방법을 보여 줍니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: ca2fab82dd08ea999186a430b78b6329c64ae816
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111409598"
---
# <a name="unsupported-language-deployments"></a>지원되지 않는 언어 배포

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Microsoft Translator Hub에 대한 지원이 곧 종료됨에 따라 Microsoft는 현재 허브를 통해 배포된 모든 모델의 배포를 취소할 예정입니다. 많은 사용자가 Custom Translator에서 지원되지 않는 언어 쌍의 모델을 허브에 배포했었습니다.  이 상황의 사용자가 더 이상 콘텐츠 번역에 의존하지 않기를 바랍니다.

이제 Custom Translator를 통해 지원되지 않는 모델을 배포할 수 있는 프로세스를 제공합니다.  이 프로세스를 통해 최신 V3 API를 사용하여 콘텐츠를 계속 번역할 수 있습니다.  이러한 모델은 사용자가 배포를 취소하거나 Custom Translator에서 언어 쌍을 사용할 수 있을 때까지 호스팅됩니다.  이 문서에서는 지원되지 않는 언어 쌍이 있는 모델을 배포하는 프로세스를 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

모델이 배포 후보가 되려면 다음 기준을 충족해야 합니다.
* 모델이 포함된 프로젝트는 마이그레이션 도구를 사용하여 허브에서 Custom Translator로 마이그레이션되어야 합니다.  
* 마이그레이션이 발생할 때 모델은 배포된 상태여야 합니다.  
* 모델의 언어 쌍은 Custom Translator에서 지원되지 않는 언어 쌍이어야 합니다.  영어를 원본 또는 대상으로 지원하지만 언어 쌍 자체에 영어가 포함되지 않으면 이 언어 쌍은 지원되지 않는 언어 배포의 후보가 됩니다.  예를 들어 프랑스어-영어 및 영어-독일어 언어 쌍이 지원되더라도 프랑스어-독일어 쌍에 대한 허브 모델은 지원되지 않는 언어 쌍으로 간주됩니다.

## <a name="process"></a>Process
허브에서 배포 후보인 모델을 마이그레이션한 후에는 작업 영역의 **설정** 페이지로 이동하고 **지원되지 않는 Translator 허브 학습** 섹션이 있는 페이지 끝으로 스크롤하여 모델을 찾을 수 있습니다.  이 섹션은 위에서 언급한 전제 조건을 충족하는 프로젝트가 있는 경우에만 나타납니다.

![지원되지 않는 Translator 허브 학습 섹션이 강조 표시된 스크린샷](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

**지원되지 않는 Translator 허브 학습** 선택 페이지의 **요청되지 않은 학습** 탭에는 배포할 수 있는 모델이 포함되어 있습니다.  배포하려는 모델을 선택하고 요청을 제출합니다.   4월 30일 배포 기한까지는 배포할 모델을 원하는 수만큼 선택할 수 있습니다.
 
![요청되지 않은 학습 탭을 보여 주는 스크린샷](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

제출된 모델은 더 이상 **요청되지 않은 학습** 탭에 표시되지 않으며 대신 **요청된 학습** 탭에 표시됩니다. 언제든지 요청한 학습을 볼 수 있습니다.

![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>다음 작업

배포를 위해 선택한 모델은 허브가 해제되고 모든 모델이 배포 취소된 후에 저장됩니다.  5월 24일까지 지원되지 않는 모델에 대한 배포 요청을 제출해야 합니다.  이러한 모델은 Translator V3 API를 통해 액세스할 수 있게 되는 6월 15일에 배포할 예정입니다.  또한 7월 1일까지 V2 API를 통해 사용할 수 있습니다.  

허브 지원이 중단되는 중요한 날짜에 대한 자세한 내용은 [여기](https://www.microsoft.com/translator/business/hub/)에서 확인합니다.
배포된 후에는 일반 호스팅 요금이 적용됩니다.  자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)을 참조하세요.  

표준 Custom Translator 모델과 달리 허브 모델은 단일 지역에서만 사용할 수 있으므로 다중 지역 호스팅 요금이 적용되지 않습니다.  배포된 후에는 마이그레이션된 Custom Translator 프로젝트를 통해 언제든지 허브 모델의 배포를 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델을 학습합니다](how-to-train-model.md).
- 배포된 사용자 지정 번역 모델을 [Microsoft Translator Text API V3](../reference/v3-0-translate.md?tabs=curl)를 통해 사용합니다.
