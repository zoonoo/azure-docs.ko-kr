---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240335"
---
1. **Android Studio**에서 **프로젝트(Eclipse ADT, Gradle 등) 가져오기**를 사용하여 프로젝트를 엽니다. 모든 JDK 오류를 방지하려면 이 가져오기를 선택해야 합니다.

2. 이 폴더에서 파일을 `ToDoActivity.java` 엽니 다 - ZUMOAPPNAME / 응용 프로그램 / SRC / 메인 / 자바 / com / 예제 / zumoappname. 응용 프로그램 `ZUMOAPPNAME`이름은 입니다.

3. [Azure 포털로](https://portal.azure.com/) 이동하여 만든 모바일 앱으로 이동합니다. 블레이드에서 `Overview` 모바일 앱의 공개 끝점인 URL을 찾습니다. 예 - 내 앱 이름 "test123"의 https://test123.azurewebsites.net사이트 이름은 .

4. 메서드에서 `onCreate()` 매개 `ZUMOAPPURL` 변수를 위의 공용 끝점으로 바꿉꿉습니다.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    다음이 됩니다.
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. **'앱' 실행** 단추를 눌러 프로젝트를 빌드하고 Android 시뮬레이터에서 앱을 시작합니다.

4. 앱에서 *자습서 완료와* 같은 의미 있는 텍스트를 입력한 다음 '추가' 단추를 클릭합니다. 그러면 이전에 배포한 Azure 백 엔드에 POST 요청을 보냅니다. 요청의 백 엔드 데이터가 TodoItem SQL 테이블로 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.
    ![빠른 시작 안드로이드](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)