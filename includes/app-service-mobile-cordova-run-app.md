---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 3217383b105c022aef42d8000f3a41cefea542fe
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132904"
---
1. [Azure Portal]을 방문합니다.
2. **App Services** > 생성한 백 엔드를 차례로 클릭합니다.
3. 모바일 앱 설정에서 **빠른 시작** > **Cordova**를 클릭합니다.
![Mobile Apps가 있는 Azure Portal - 강조 표시된 빠른 시작][quickstart]
4. **클라이언트 응용 프로그램 구성**에서 **새 앱 만들기**를 선택한 다음 **다운로드**를 클릭합니다.
2. 하드 드라이브의 디렉터리에 다운로드한 ZIP 파일 압축을 풀고, 솔루션 파일(.sln)로 이동하여 Visual Studio를 사용하여 엽니다.
3. Visual Studio에서 시작 화살표 옆에 있는 드롭다운 목록에서 솔루션 플랫폼(Android, iOS 또는 Windows)을 선택합니다. 녹색 화살표의 드롭다운을 클릭하여 특정 배포 디바이스 또는 에뮬레이터를 선택합니다. 기본 Android 플랫폼 및 Ripple 에뮬레이터를 사용할 수 있습니다. 더 많은 고급 자습서(예: 푸시 알림)를 보려면 지원되는 디바이스 또는 에뮬레이터를 선택해야 합니다.
4. Cordova 앱을 빌드하고 실행하려면 F5 키를 누르거나 녹색 화살표를 클릭합니다. 네트워크에 대한 액세스를 요청하는 에뮬레이터에서 보안 대화 상자가 표시되는 경우, 이를 수락합니다.
5. 디바이스 또는 에뮬레이터에서 앱을 시작한 후, **새 텍스트 입력**에 *자습서 완료*와 같은 의미 있는 텍스트를 입력한 다음 **추가** 단추를 클릭입니다.

요청의 백 엔드 데이터가 SQL Database의 TodoItem 테이블에 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.

다른 플랫폼에 대해 3-5단계를 반복할 수 있습니다.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
