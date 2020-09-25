---
title: 모델 및 학습 설정 관리-Personalizer
description: 컴퓨터에서 학습 한 모델 및 학습 설정은 자신의 원본 제어 시스템에서 백업용으로 내보낼 수 있습니다.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: f82adad5273f1c5559cbeb1924f59e57c863f6b6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303457"
---
# <a name="how-to-manage-model-and-learning-settings"></a>모델 및 학습 설정을 관리 하는 방법

컴퓨터에서 학습 한 모델 및 학습 설정은 자신의 원본 제어 시스템에서 백업용으로 내보낼 수 있습니다.

## <a name="export-the-personalizer-model"></a>Personalizer 모델 내보내기

**모델 및 학습 설정**에 대 한 리소스 관리 섹션에서 모델 생성 및 마지막 업데이트 날짜를 검토 하 고 현재 모델을 내보냅니다. Azure Portal 또는 Personalizer API를 사용하여 보관을 위해 모델 파일을 내보낼 수 있습니다.

![현재 Personalizer 모델 내보내기](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>학습 루프의 데이터 지우기

1. Azure Portal에서 Personalizer 리소스에 대해 **모델 및 학습 설정** 페이지에서 **데이터 지우기**를 선택 합니다.
1. 모든 데이터를 지우고 학습 루프를 원래 상태로 다시 설정 하려면 3 개의 확인란을 모두 선택 합니다.

    ![Azure Portal에서 Personalizer 리소스의 데이터를 지웁니다.](./media/settings/clear-data-from-personalizer-resource.png)

    |값|목적|
    |--|--|
    |개인 설정 및 보상 데이터를 기록 합니다.|이 로깅 데이터는 오프 라인 평가에 사용 됩니다. 리소스를 다시 설정 하는 경우 데이터를 지웁니다.|
    |Personalizer 모델을 다시 설정 합니다.|이 모델은 재 학습 마다 변경 됩니다. 이 학습 빈도는 **구성** 페이지의 **모델 업로드 빈도** 에서 지정 합니다. |
    |학습 정책을 기본값으로 설정 합니다.|오프 라인 평가의 일환으로 학습 정책을 변경한 경우 원래 학습 정책으로 다시 설정 됩니다.|

1. **선택한 데이터 지우기** 를 선택 하 여 지우기 프로세스를 시작 합니다. 상태는 Azure 알림에서 오른쪽 위 탐색에서 보고 됩니다.

## <a name="import-a-new-learning-policy"></a>새 학습 정책 가져오기

[학습 정책](concept-active-learning.md#understand-learning-policy-settings) 설정은 모델 학습의 하이퍼 _매개 변수_ 를 결정 합니다. [오프 라인 평가](how-to-offline-evaluation.md) 를 수행 하 여 새 학습 정책을 찾습니다.

1. [Azure Portal](https://portal.azure.com)를 열고 Personalizer 리소스를 선택 합니다.
1. **리소스 관리** 섹션에서 **모델 및 학습 설정** 을 선택 합니다.
1. **가져오기 학습 설정** 에서 위에 지정 된 JSON 형식을 사용 하 여 만든 파일을 선택 하 고 **업로드** 단추를 선택 합니다.

    학습 정책이 성공적으로 업로드 되었다는 알림을 기다립니다.

## <a name="export-a-learning-policy"></a>학습 정책 내보내기

1. [Azure Portal](https://portal.azure.com)를 열고 Personalizer 리소스를 선택 합니다.
1. **리소스 관리** 섹션에서 **모델 및 학습 설정** 을 선택 합니다.
1. **가져오기 학습 설정** 에 대해 **학습 설정 내보내기** 단추를 선택 합니다. 이렇게 하면 `json` 파일이 로컬 컴퓨터에 저장 됩니다.

## <a name="next-steps"></a>다음 단계

[학습 정책을 관리 하는 방법 알아보기](how-to-manage-model.md)
