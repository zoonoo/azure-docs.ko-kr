---
title: 모델 및 학습 설정 관리 - 개인화
description: 자체 소스 제어 시스템에서 백업을 위해 기계 학습 모델 및 학습 설정을 내보낼 수 있습니다.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624329"
---
# <a name="how-to-manage-model-and-learning-settings"></a>모델 및 학습 설정을 관리하는 방법

자체 소스 제어 시스템에서 백업을 위해 기계 학습 모델 및 학습 설정을 내보낼 수 있습니다.

## <a name="export-the-personalizer-model"></a>Personalizer 모델 내보내기

**모델 및 학습 설정에**대한 리소스 관리 섹션에서 모델 만들기 및 마지막 업데이트 날짜를 검토하고 현재 모델을 내보냅니다. Azure Portal 또는 Personalizer API를 사용하여 보관을 위해 모델 파일을 내보낼 수 있습니다.

![현재 Personalizer 모델 내보내기](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>학습 루프에 대한 데이터 지우기

1. Azure 포털에서 개인 설정 리소스의 **경우 모델 및 학습 설정** 페이지에서 데이터 **지우기**를 선택합니다.
1. 모든 데이터를 지우고 학습 루프를 원래 상태로 재설정하려면 3개의 확인란을 모두 선택합니다.

    ![Azure 포털에서 개인 설정 자 리소스의 데이터를 지웁히 바습니다.](./media/settings/clear-data-from-personalizer-resource.png)

    |값|목적|
    |--|--|
    |기록된 개인 정보 및 보상 데이터.|이 로깅 데이터는 오프라인 평가에 사용됩니다. 리소스를 재설정하는 경우 데이터를 지웁울 수 있습니다.|
    |개인 설정 모델을 재설정합니다.|이 모델은 모든 재교육에 대해 변경됩니다. 이 교육 빈도는 **구성** 페이지의 **업로드 모델 빈도에** 지정됩니다. |
    |학습 정책을 기본값으로 설정합니다.|오프라인 평가의 일부로 학습 정책을 변경한 경우 원래 학습 정책으로 재설정됩니다.|

1. 지우기 프로세스를 시작하려면 **선택한 데이터 지우기를** 선택합니다. 상태는 오른쪽 상단 탐색에서 Azure 알림에 보고됩니다.

## <a name="import-a-new-learning-policy"></a>새 학습 정책 가져오기

[학습 정책](concept-active-learning.md#understand-learning-policy-settings) 설정은 모델 학습의 _하이퍼매개 변수를 결정합니다._ 오프라인 [평가를](how-to-offline-evaluation.md) 수행하여 새 학습 정책을 찾습니다.

1. Azure [포털을](https://portal.azure.com)열고 개인 설정 자 리소스를 선택합니다.
1. 리소스 관리 섹션에서 **모델 및 학습 설정을** **선택합니다.**
1. 학습 **가져오기 설정의** 경우 위에 지정된 JSON 형식으로 만든 파일을 선택한 다음 **업로드** 단추를 선택합니다.

    학습 정책이 성공적으로 업로드되었다는 알림을 기다립니다.

## <a name="export-a-learning-policy"></a>학습 정책 내보내기

1. Azure [포털을](https://portal.azure.com)열고 개인 설정 자 리소스를 선택합니다.
1. 리소스 관리 섹션에서 **모델 및 학습 설정을** **선택합니다.**
1. 학습 **가져오기 설정의** 경우 **학습 설정 내보내기** 단추를 선택합니다. 이렇게 하면 `json` 파일이 로컬 컴퓨터에 저장됩니다.

## <a name="next-steps"></a>다음 단계

[학습 정책을 관리하는 방법 알아보기](how-to-manage-model.md)
