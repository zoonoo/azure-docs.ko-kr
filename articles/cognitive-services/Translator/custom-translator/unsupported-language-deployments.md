---
title: 지원되지 않는 언어 배포 - 사용자 지정 변환기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services 사용자 지정 변환기에 지원되지 않는 언어 쌍을 배포하는 방법을 보여 주며 있습니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837441"
---
# <a name="unsupported-language-deployments"></a>지원되지 않는 언어 배포

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

마이크로소프트 번역기 허브의 곧 은퇴와 함께, 마이크로소프트는 현재 허브를 통해 배포 하는 모든 모델을 배포 취소 됩니다. 사용자 지정 변환기에서 언어 쌍이 지원되지 않는 허브에 배포된 모델이 있는 분도 많습니다.  우리는이 상황에서 사용자가 자신의 콘텐츠를 번역에 대한 의지가 없는 것을 원하지 않는다.

이제 사용자 지정 변환기를 통해 지원되지 않는 모델을 배포할 수 있는 프로세스가 있습니다.  이 프로세스를 사용하면 최신 V3 API를 사용하여 콘텐츠를 계속 번역할 수 있습니다.  이러한 모델은 배포를 취소하거나 언어 쌍을 사용자 지정 변환기에서 사용할 수 있을 때까지 호스팅됩니다.  이 문서에서는 지원되지 않는 언어 쌍을 사용하여 모델을 배포하는 프로세스를 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

모델이 배포 후보가 되려면 다음 기준을 충족해야 합니다.
* 모델이 포함된 프로젝트는 마이그레이션 도구를 사용하여 허브에서 사용자 지정 변환기로 마이그레이션된 것이어야 합니다.  프로젝트 및 작업 영역 마이그레이션 프로세스는 [여기에서](how-to-migrate.md)찾을 수 있습니다.
* 마이그레이션이 발생할 때 모델은 배포된 상태여야 합니다.  
* 모델의 언어 쌍은 사용자 지정 변환기에서 지원되지 않는 언어 쌍이어야 합니다.  언어가 영어로 지원되지만 쌍 자체에 영어가 포함되지 않은 언어 쌍은 지원되지 않는 언어 배포의 후보입니다.  예를 들어 프랑스어에서 독일어로 된 언어 쌍의 허브 모델은 프랑스어에서 영어로, 독일어로 영어가 지원되는 언어 쌍이 지원되는 경우에도 지원되지 않는 언어 쌍으로 간주됩니다.

## <a name="process"></a>Process
배포 후보인 Hub에서 모델을 마이그레이션한 후에는 작업 영역의 **설정** 페이지로 이동하여 **지원되지 않는 번역기 허브 교육** 섹션이 표시되는 페이지 끝으로 스크롤하여 모델을 찾을 수 있습니다.  이 섹션은 위에서 언급한 필수 구성 조건을 충족하는 프로젝트가 있는 경우에만 나타납니다.

![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

**지원되지 않는 번역기 허브 교육** 선택 페이지 내의 **요청되지 않은 교육** 탭에는 배포할 수 있는 모델이 포함되어 있습니다.  배포할 모델을 선택하고 요청을 제출합니다.   배포 기한인 4월 30일 전에 배포에 필요한 만큼 모델을 선택할 수 있습니다.
 
![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

제출되면 **요청되지 않은 교육** 탭에서 모델을 더 이상 사용할 수 없으며 대신 **요청된 교육** 탭에 표시됩니다.  언제든지 요청한 교육을 볼 수 있습니다.

![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>다음 단계

배포를 위해 선택한 모델은 Hub가 서비스 해제되고 모든 모델이 배포되지 않으면 저장됩니다.  지원되지 않는 모델의 배포 요청을 제출하려면 5월 24일까지 제출해야 합니다.  6월 15일에 이러한 모델을 배포하여 번역기 V3 API를 통해 액세스할 수 있습니다.  또한 7월 1일까지 V2 API를 통해 사용할 수 있습니다.  

허브 체크의 사용 중단에 중요 한 날짜에 대 한 자세한 [내용은 여기를 확인](https://www.microsoft.com/translator/business/hub/)합니다.
배포되면 일반 호스팅 요금이 부과됩니다.  자세한 내용은 [가격 책정을](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) 참조하십시오.  

표준 사용자 지정 번역기 모델과 달리 허브 모델은 단일 리전에서만 사용할 수 있으므로 다중 지역 호스팅 요금이 부과되지 않습니다.  배포되면 마이그레이션된 사용자 지정 번역기 프로젝트를 통해 언제든지 허브 모델을 배포 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델 을 훈련.](how-to-train-model.md)
- 배포된 사용자 지정 번역 모델을 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)를 통해 사용합니다.
