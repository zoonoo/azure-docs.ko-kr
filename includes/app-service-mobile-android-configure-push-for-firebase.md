---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809478"
---
1. [Azure Portal](https://portal.azure.com/)에서 **모두 찾아보기** > **App Services** > Mobile Apps 백 엔드를 클릭합니다. **설정** 아래에서 **App Service 푸시**를 클릭한 후 알림 허브 이름을 클릭합니다.
2. **Google(GCM)** 로 이동하고, 이전 절차에서 Firebase로부터 얻은 **Server Key** 값을 입력한 다음 **저장**을 클릭합니다.

    ![포털에서 API 키 설정](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

이제 Mobile Apps 백 엔드가 Firebase Cloud Messaging을 사용하도록 구성됩니다. 알림 허브를 사용하여 Android 디바이스에서 실행 중인 앱에 푸시 알림을 보낼 수 있습니다.
