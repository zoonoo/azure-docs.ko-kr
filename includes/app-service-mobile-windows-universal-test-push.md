---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c86210208b9f747cbef1d9231528fa6cedbdb5d2
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42815062"
---
1. Windows 스토어 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭한 다음 F5 키를 눌러 Windows 스토어 앱을 실행합니다.

    앱이 시작되면 디바이스가 푸시 알림에 대해 등록됩니다.
2. Windows 스토어 앱을 중지하고 Windows Phone 스토어 앱에 대해 이전 단계를 반복합니다.

    이제 두 디바이스가 모두 푸시 알림을 받도록 등록되었습니다.

3. Windows 스토어 앱을 다시 실행하고 **TodoItem 삽입**에 텍스트를 입력한 후에 **저장**을 클릭합니다.

    삽입이 완료되면 Windows 스토어 및 Windows Phone 앱이 모두 WNS에서 푸시 알림을 수신합니다. 앱이 실행되고 있지 않을 때도 알림이 Windows Phone에 표시됩니다.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
