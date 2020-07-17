---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095464"
---
사용자가 만든 백 엔드 서비스를 통해 알림 허브에서 등록 및 등록 취소할 수 있습니다. 

동작이 지정되고 앱이 전경에 있으면 경고가 표시됩니다. 그렇지 않으면 알림이 알림 센터에 표시됩니다.

> [!NOTE]
> 일반적으로 명시적인 사용자 등록/등록 취소 입력 없이 애플리케이션 수명 주기의 적절한 시점(또는 첫 실행 환경의 일부로) 동안 등록 및 등록 취소 작업을 수행합니다. 그러나 이 예제에서는 이 기능을 보다 쉽게 탐색하고 테스트할 수 있도록 명시적인 사용자 입력이 필요합니다.
