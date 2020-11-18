---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 5dd823c618a0d49bef29c473e6c293762b859149
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358299"
---
1. Visual Studio Code에서 src/edge로 이동합니다. 여러분이 만든 .env 파일이 몇 가지 배포 템플릿 파일과 함께 표시됩니다.

    배포 템플릿은 일부 자리 표시자 값이 있는 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. .env 파일에는 이러한 변수에 대한 값이 있습니다.
1. 다음으로, src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

   * operations.json - 이 파일에는 프로그램이 실행할 다양한 작업이 나열됩니다.
   * main.py - 다음을 수행하는 샘플 프로그램 코드입니다.
    
        * 앱 설정을 로드합니다.
        * Live Video Analytics on IoT Edge 모듈의 직접 메서드를 호출하여 토폴로지를 만들고, 그래프를 인스턴스화하고, 그래프를 활성화합니다.
        * **터미널** 창에서 그래프 출력을 검사하고 **출력** 창에서 IoT 허브로 전송된 이벤트를 검사할 수 있도록 일시 중지합니다.
        * 그래프 인스턴스를 비활성화하고, 그래프 인스턴스를 삭제하고, 그래프 토폴로지를 삭제합니다.
