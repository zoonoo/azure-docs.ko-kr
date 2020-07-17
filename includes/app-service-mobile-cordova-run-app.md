---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240254"
---
1. 클라이언트 프로젝트(.sln)의 솔루션 파일로 이동하여 Visual Studio를 사용하여 엽니다.

2. Visual Studio에서 시작 화살표 옆에 있는 드롭다운 목록에서 솔루션 플랫폼(Android, iOS 또는 Windows)을 선택합니다. 녹색 화살표의 드롭다운을 클릭하여 특정 배포 디바이스 또는 에뮬레이터를 선택합니다. 기본 Android 플랫폼 및 Ripple 에뮬레이터를 사용할 수 있습니다. 더 많은 고급 자습서(예: 푸시 알림)를 보려면 지원되는 디바이스 또는 에뮬레이터를 선택해야 합니다.

3. 이 폴더에서 파일을 `ToDoActivity.java` 엽니 다 - ZUMOAPPNAME / 응용 프로그램 / SRC / 메인 / 자바 / com / 예제 / zumoappname. 응용 프로그램 `ZUMOAPPNAME`이름은 입니다.

4. [Azure 포털로](https://portal.azure.com/) 이동하여 만든 모바일 앱으로 이동합니다. 블레이드에서 `Overview` 모바일 앱의 공개 끝점인 URL을 찾습니다. 예 - 내 앱 이름 "test123"의 https://test123.azurewebsites.net사이트 이름은 .

5. ZUMOAPPNAME/www/js/index.js의 파일로 이동하고 `index.js` `onDeviceReady()` 메서드에서 `ZUMOAPPURL` 매개 변수를 위의 공용 끝점으로 바꿉니다.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    다음이 됩니다.
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Cordova 앱을 빌드하고 실행하려면 F5 키를 누르거나 녹색 화살표를 클릭합니다. 네트워크에 대한 액세스를 요청하는 에뮬레이터에서 보안 대화 상자가 표시되는 경우, 이를 수락합니다.

7. 장치 또는 에뮬레이터에서 앱이 시작된 후 자습서 완료와 같은 **새 텍스트 입력에서**의미 있는 텍스트를 입력한 다음 **추가** *단추를* 클릭합니다.

요청의 백 엔드 데이터가 SQL Database의 TodoItem 테이블에 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.

다른 플랫폼에 대해 3-5단계를 반복할 수 있습니다.