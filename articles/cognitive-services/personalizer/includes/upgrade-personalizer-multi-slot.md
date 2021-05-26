---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: b61e9ba2374286a313444de7ab1e74b17c0a7af5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382341"
---
### <a name="upgrade-personalizer-instance-to-multislot"></a>Personalizer 인스턴스를 다중 슬롯으로 업그레이드

Azure Portal의 **모델 및 학습 설정** 페이지에 있는 Personalizer 리소스에서 **학습 설정 내보내기** 를 클릭합니다. 다운로드한 json 파일의 **인수** 필드는 **--cb_explore_adf** 로 시작됩니다. 파일의 나머지 부분을 그대로 유지하면서 이를 **--ccb_explore_adf** 로 변경합니다. 파일을 저장합니다. 

![변경 전 학습 설정](../media/settings/learning-settings-pre-upgrade.png)

![변경 후 학습 설정](../media/settings/learning-settings-post-upgrade.png)

포털의 동일한 탭에 있는 **학습 설정 가져오기** 에서 최근에 수정된 json 파일을 찾아 업로드합니다. 그러면 Personalizer 인스턴스가 "다중 슬롯" Personalizer로 업데이트되고 이제 다중 슬롯 순위 및 보상 호출이 지원됩니다.