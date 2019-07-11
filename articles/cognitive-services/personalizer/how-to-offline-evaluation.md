---
title: 오프 라인 평가-Personalizer
titleSuffix: Azure Cognitive Services
description: 오프라인 평가를 통해 학습 루프를 분석하는 방법 알아보기
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b719e6e693471415350007a4f4fabed917b8e12d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722320"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>오프라인 평가를 통해 학습 루프를 분석하는 방법


오프라인 평가를 완료하고 결과를 이해하는 방법을 알아봅니다.

오프라인 평가를 통해 Personalizer가 애플리케이션의 기본 동작에 비해 얼마나 효율적인지 측정하고, 개인 설정에 가장 많이 기여하는 기능이 무엇인지 알아보고, 새 기계 학습 설정을 자동으로 검색할 수 있습니다.

자세한 내용은 [오프라인 평가](concepts-offline-evaluation.md)를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

1. Personalizer 루프가 구성되어 있어야 합니다.
1. 의미 있는 평가 결과를 얻으려면 Personalizer 루프의 로그에 50,000개 이상의 이벤트가 있어야 합니다.

필요에 따라 이전에 내보낸 _학습 정책_ 파일을 동일한 평가에서 비교하고 테스트할 수 있습니다.

## <a name="steps-to-start-a-new-offline-evaluation"></a>새 오프라인 평가를 시작하는 단계

1. Azure Portal에서 개인 설정 루프 리소스를 찾습니다.
1. "평가" 섹션으로 이동합니다.
1. [새 평가]를 클릭합니다.
1. 오프라인 평가의 시작 및 종료 날짜를 선택합니다. 두 날짜는 평가에 사용할 데이터 범위를 지정하는 과거의 날짜입니다. 이 데이터는 [데이터 보존](how-to-settings.md) 설정에 지정된 대로 로그에 있어야 합니다.
1. 필요에 따라 사용자 고유의 학습 정책을 업로드할 수도 있습니다. 
1. Personalizer가 이 기간 동안 관찰된 사용자 동작을 기반으로 최적화된 학습 정책을 만들어야 하는지 여부를 지정합니다.
1. 평가를 시작합니다.

## <a name="results"></a>결과

처리할 데이터의 양, 비교할 학습 정책의 수, 최적화 요청 여부에 따라 평가가 오래 걸릴 수 있습니다.

평가가 완료되면 다음과 같은 결과를 볼 수 있습니다.

1. 다음을 포함한 학습 정책 비교:
    * **온라인 정책**: Personalizer에서 사용되는 현재 학습 정책입니다.
    * **기준**: 애플리케이션의 기본값(순위 호출에서 보낸 첫 번째 작업에 의해 결정)입니다.
    * **임의 정책**: 제공된 작업 중에서 항상 임의의 작업을 선택하여 반환하는 가상 순위 동작입니다.
    * **사용자 지정 정책**: 평가를 시작할 때 업로드되는 추가 학습 정책입니다.
    * **최적화된 정책**: 최적화된 정책을 검색하는 옵션을 사용하여 평가를 시작한 경우 최적화된 정책 비교가 수행되며, 최적화된 정책을 다운로드하거나 온라인 학습 정책으로 만들어서 현재 정책을 대체할 수 있습니다.

1. 작업 및 컨텍스트에 대한 [기능](concepts-features.md)의 효율성


## <a name="more-information"></a>추가 정보

* [오프라인 평가 수행 방법](concepts-offline-evaluation.md)을 알아보세요.