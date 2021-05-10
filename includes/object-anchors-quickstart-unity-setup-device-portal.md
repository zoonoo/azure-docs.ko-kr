---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104907"
---
### <a name="set-up-the-windows-device-portal"></a>Windows Device Portal 설정

WiFi를 통해 HoloLens에 연결하려면 다음 단계를 수행합니다.

1. 먼저 [HoloLens를 WiFi에 연결](/hololens/hololens-network)합니다.

2. 그런 다음, **설정 > 네트워크 및 인터넷 > Wi-Fi > 고급 옵션** 에서 디바이스의 IP 주소를 가져옵니다.

3. PC의 웹 브라우저에서 `https://<YOUR_HOLOLENS_IP_ADDRESS>`으로 이동합니다. 브라우저에 다음 메시지가 표시됩니다. "이 웹 사이트의 보안 인증서에 문제가 있습니다." 이 메시지는 Device Portal에 발급된 인증서가 자체 서명된 인증서이기 때문에 발생합니다. 인증서 오류를 무시하고 계속 진행할 수 있습니다.

Windows Device Portal에 대한 자세한 내용은 [여기](/windows/mixed-reality/using-the-windows-device-portal)를 참조하세요.