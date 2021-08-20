---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: 0f181a28d1522fb433a274361c96e532c6a0b708
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255852"
---
### <a name="upgrade-personalizer-instance-to-multi-slot"></a>Personalizer 인스턴스를 다중 슬롯으로 업그레이드

> [!NOTE]
> 다중 슬롯 개인 설정(미리 보기)은 Personalizer 서비스의 다른 기능에 영향을 줍니다. 이 변경 작업은 실행 취소할 수 없습니다. 다중 슬롯 개인 설정을 사용하도록 설정하기 전에 [다중 슬롯 개인 설정(미리 보기)](../concept-multi-slot-personalization.md)을 참조하세요. 


1. 자동 최적화 사용 안 함 Azure Portal에 있는 **모델 및 학습 설정** 페이지의 **리소스 관리** 아래 Personalizer 리소스에서 자동 최적화를 끄고 저장합니다.

> [!NOTE]
> 자동 최적화를 사용하지 않도록 설정해야 다중 슬롯 개인 설정이 작동합니다. 다중 슬롯 개인 설정에 대한 자동 최적화는 나중에 지원될 예정입니다. 

2. 다중 슬롯에 대한 Personalizer 업데이트 Azure Portal에 있는 **모델 및 학습 설정** 페이지의 **리소스 관리** 아래 Personalizer 리소스에서 **학습 설정 내보내기** 를 클릭합니다. 다운로드한 json 파일의 **인수** 필드는 **--cb_explore_adf** 로 시작됩니다. 이 필드를 **--ccb_explore_adf** 로 변경하고 파일을 저장합니다. CB(Contextual Bandits) 및 CCB(Conditional Contextual Bandits)는 Personalizer에서 각각 단일 슬롯과 다중 슬롯 개인 설정에 사용하는 알고리즘입니다. ADF(작업 종속 기능)는 기능을 사용하여 작업을 표시/식별함을 의미합니다.

![변경 전 학습 설정](../media/settings/learning-settings-pre-upgrade.png)

![변경 후 학습 설정](../media/settings/learning-settings-post-upgrade.png)

포털의 동일한 탭에 있는 **학습 설정 가져오기** 에서 최근에 수정된 json 파일을 찾아 업로드합니다. 그러면 Personalizer 인스턴스가 "다중 슬롯" Personalizer로 업데이트되고 이제 다중 슬롯 순위 및 보상 호출이 지원됩니다.


