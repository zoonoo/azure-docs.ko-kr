---
title: 지원 되지 않는 언어 배포-사용자 지정 변환기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services 사용자 지정 변환기에서 지원 되지 않는 언어 쌍을 배포 하는 방법을 보여 줍니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: ea2761712e4bbb7a3abd1483766f4eae47b7c350
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996366"
---
# <a name="unsupported-language-deployments"></a>지원되지 않는 언어 배포

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Microsoft Translator Hub의 출시 예정에 따라 Microsoft는 현재 허브를 통해 배포 된 모든 모델을 배포 취소 합니다. 사용자 지정 변환기에서 언어 쌍이 지원 되지 않는 허브에 모델을 배포 하는 경우가 많습니다.  이러한 상황에서 사용자가 콘텐츠를 번역 하는 데 못해 하지 않도록 합니다.

이제 사용자 지정 변환기를 통해 지원 되지 않는 모델을 배포할 수 있는 프로세스가 있습니다.  이 프로세스를 통해 최신 V3 API를 사용 하 여 계속 콘텐츠를 번역할 수 있습니다.  이러한 모델은 배포를 취소 하거나 사용자 지정 변환기에서 언어 쌍을 사용할 수 있게 될 때까지 호스팅됩니다.  이 문서에서는 지원 되지 않는 언어 쌍을 사용 하 여 모델을 배포 하는 프로세스를 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

모델이 배포 후보가 되려면 다음 조건을 충족 해야 합니다.
* 모델을 포함 하는 프로젝트는 마이그레이션 도구를 사용 하 여 허브에서 사용자 지정 변환기로 마이그레이션해야 합니다.  프로젝트 및 작업 영역을 마이그레이션하는 프로세스는 [여기](how-to-migrate.md)에서 찾을 수 있습니다.
* 마이그레이션은 발생 하면 모델은 배포 된 상태 여야 합니다.  
* 모델의 언어 쌍은 사용자 지정 변환기에서 지원 되지 않는 언어 쌍 이어야 합니다.  언어가 영어에서 지원 되는 언어 쌍 이지만 쌍 자체에는 영어가 포함 되지 않으며 지원 되지 않는 언어 배포의 후보가 됩니다.  예를 들어 프랑스어에서 독일어로의 언어 쌍에 대 한 허브 모델은 지원 되는 언어 쌍으로 간주 됩니다.

## <a name="process"></a>Process
배포 후보가 되는 허브에서 모델을 마이그레이션한 후에는 작업 영역에 대 한 **설정** 페이지로 이동 하 여 **지원 되지 않는 Translator 허브 학습** 섹션이 표시 되는 페이지의 끝으로 스크롤하여 해당 모델을 찾을 수 있습니다.  이 섹션은 위에서 언급 한 필수 조건을 충족 하는 프로젝트가 있는 경우에만 표시 됩니다.

![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

**지원 되지 않는 변환기 허브 학습** 선택 페이지 내에서 **요청** 되지 않은 학습 탭은 배포에 적합 한 모델을 포함 합니다.  배포 하려는 모델을 선택 하 고 요청을 제출 합니다.   4 월 30 일 배포 최종 기한 이전에는 배포에 사용할 모델을 원하는 만큼 선택할 수 있습니다.
 
![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

제출한 후에는 요청 되지 않은 **학습** 탭에서 모델을 더 이상 사용할 수 없으며 **요청 된 학습** 탭에 대신 표시 됩니다.  언제 든 지 요청 된 학습를 볼 수 있습니다.

![허브에서 마이그레이션하는 방법](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>다음 단계

배포 하도록 선택한 모델은 허브가 서비스 해제 되 고 모든 모델의 배포를 취소 한 후에 저장 됩니다.  지원 되지 않는 모델의 배포에 대 한 요청을 24 년 5 월까지 제출할 수 있습니다.  이 모델은 6 월 15 일에 배포 되며,이 시점에서 변환기 V3 API를 통해 액세스할 수 있습니다.  또한 7 월 1 일까 지 V2 API를 통해 사용할 수 있습니다.  

허브를 사용 하지 않는 경우 중요 한 날짜에 대 한 자세한 내용은 [여기](https://www.microsoft.com/translator/business/hub/)를 참조 하세요.
배포 된 후에는 일반적인 호스팅 요금이 적용 됩니다.  자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) 을 참조 하세요.  

표준 사용자 지정 변환기 모델과 달리 허브 모델은 단일 지역 에서만 사용할 수 있으므로 다중 지역 호스팅 요금은 적용 되지 않습니다.  배포 된 후에는 마이그레이션된 사용자 지정 변환기 프로젝트를 통해 언제 든 지 허브 모델의 배포를 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델을 학습](how-to-train-model.md)합니다.
- [변환기 V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)을 통해 배포 된 사용자 지정 변환 모델 사용을 시작 합니다.
