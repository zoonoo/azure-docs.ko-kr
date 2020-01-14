---
title: 학습 정책 관리-Personalizer
description: 이 문서에는 Personalizer에 대 한 자주 묻는 질문에 대 한 답변이 포함 되어 있습니다.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75843500"
---
# <a name="manage-learning-policy"></a>학습 정책 관리

학습 정책 설정은 모델 학습의 하이퍼 _매개 변수_ 를 결정 합니다. 학습 정책은 `.json` 파일에 정의 되어 있습니다.

## <a name="import-a-new-learning-policy"></a>새 학습 정책 가져오기

1. [Azure Portal](https://portal.azure.com)를 열고 Personalizer 리소스를 선택 합니다.
1. **리소스 관리** 섹션에서 **모델 및 학습 설정** 을 선택 합니다.
1. **가져오기 학습 설정** 에서 위에 지정 된 JSON 형식을 사용 하 여 만든 파일을 선택 하 고 **업로드** 단추를 선택 합니다.

    학습 정책이 성공적으로 업로드 되었다는 알림을 기다립니다.

## <a name="export-a-learning-policy"></a>학습 정책 내보내기

1. [Azure Portal](https://portal.azure.com)를 열고 Personalizer 리소스를 선택 합니다.
1. **리소스 관리** 섹션에서 **모델 및 학습 설정** 을 선택 합니다.
1. **가져오기 학습 설정** 에 대해 **학습 설정 내보내기** 단추를 선택 합니다. 그러면 `json` 파일이 로컬 컴퓨터에 저장 됩니다.

## <a name="next-steps"></a>다음 단계

학습 정책 [개념](concept-active-learning.md#learning-settings) 에 대 한 자세한 정보

[지역 가용성에 대해 알아보기](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)