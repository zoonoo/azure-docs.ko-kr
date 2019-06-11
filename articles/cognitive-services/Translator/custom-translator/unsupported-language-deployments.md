---
title: 사용자 지정 변환기-지원 되지 않는 언어 배포
titleSuffix: Azure Cognitive Services
description: 사용자 지정 변환기에서 지원 되지 않는 언어 쌍을 배포 하는 방법입니다.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 0938ba2e839be603c557cc2a87dd0c5aa1dfe1e3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390564"
---
# <a name="unsupported-language-deployments"></a>지원되지 않는 언어 배포

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Microsoft Translator 허브의 예정 된 사용 중지, Microsoft는 허브를 통해 현재 배포 된 모든 모델 취소 됩니다. 대부분에 해당 언어 쌍 사용자 지정 변환기에서 지원 되지 않는 허브에서 배포 된 모델입니다.  이 경우 사용자가 해당 콘텐츠를 변환 하는 기능이 초본 하지 않을 것입니다.

이제 사용자 지정 변환기를 통해 지원 되지 않는 모델을 배포할 수 있도록 하는 프로세스입니다.  이 프로세스를 사용 하면 계속 최신 V3 API를 사용 하 여 콘텐츠를 변환할 수 있습니다.  이러한 모델은 해당 배포를 취소 하려는 또는 언어 쌍 사용자 지정 변환기에 사용할 수 있게 될 때까지 호스트 됩니다.  이 문서는 지원 되지 않는 언어 쌍을 사용 하 여 모델을 배포 하는 프로세스를 설명 합니다.

## <a name="prerequisites"></a>필수 조건

배포에 대 한 후보를 모델에 대해 순서 대로 다음 조건을 충족 해야 합니다.
* 모델을 포함 하는 프로젝트 마이그레이션 도구를 사용 하 여 사용자 지정 변환기에 대 한 허브에서 마이그레이션 되었습니다 해야 합니다.  마이그레이션 프로젝트와 작업 영역에 대 한 프로세스를 찾을 수 있습니다 [여기](how-to-migrate.md)합니다.
* 모델은 마이그레이션이 발생 하는 경우 배포 된 상태 여야 합니다.  
* 모델 언어 쌍에는 사용자 지정 변환기에는 지원 되지 않는 언어 쌍 이어야 합니다.  언어 쌍은 영어에서 언어를 사용할 수 있지만 자체 쌍 영어를 포함 하지 않습니다를 사용 하면 지원 되지 않는 언어 배포에 적합 합니다.  예를 들어 독일어 언어 쌍에 프랑스어에 대 한 허브 모델에는 지원 되지 않는 언어 쌍도 하지만 프랑스어 영어 및 독일어는 영어를 지원 되는 언어 쌍을 간주 됩니다.

## <a name="process"></a>Process
모델 배포에 대 한 적합 한 허브에서 마이그레이션한 후로 이동 하 여 찾을 수 있습니다 합니다 **설정을** 작업 영역 및 표시 되는 페이지의 끝으로 스크롤하거나 페이지는 **지원 되지 않음 Translator 허브 학습** 섹션입니다.  이 섹션에서는 위에서 언급 한 필수 구성 요소를 충족 하는 프로젝트가 있는 경우에 나타납니다.

![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

내는 **지원 되지 않는 Translator 허브 학습** 선택 페이지는 **교육 요청 하지 않은** 탭에 모델 배포에 적합 합니다.  배포 하 고 요청을 제출 하려는 모델을 선택 합니다.   4 월 30 일 배포 최종 기한 전에 배포 하려는 만큼 모델을 선택할 수 있습니다.
 
![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

제출 후 모델을 더 이상에서 사용할 수 없습니다는 **교육 요청 하지 않은** 탭 및 대신에 표시 됩니다는 **교육 요청** 탭 합니다.  요청 된 교육에 언제 든 지 볼 수 있습니다.

![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>다음 작업

허브 역할을 해제 하 고 모든 모델은 배포 된 배포를 위해 선택한 모델 저장 됩니다.  지원 되지 않는 모델의 배포에 대 한 요청을 제출 하려면 월 24 일까 지 해야 합니다.  6 월 15 일에 지점에 액세스할 수 없게 됩니다 Translator V3 API를 통해 이러한 모델 배포 됩니다.  또한 됩니다 V2 API를 통해 사용할 수 있는 7 월 1 일까 지.  

중요 한 날짜 허브 확인을 사용 중단에 대 한 자세한 내용은 [여기](https://www.microsoft.com/translator/business/hub/)합니다.
배포 후, 일반적인 호스팅 요금이 적용 됩니다.  참조 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) 세부 정보에 대 한 합니다.  

표준 사용자 지정 변환기 모델과 달리 허브 모델만 됩니다 단일 지역에서 사용할 수 있도록 다중 지역 호스팅 요금이 적용 되지 것입니다.  배포 되 면 마이그레이션된 사용자 지정 변환기 프로젝트를 통해 언제 든 허브 모델을 배포 취소 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델을 학습합니다](how-to-train-model.md).
- [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)을 통해 배포된 사용자 지정 번역 모델 사용을 시작합니다.
