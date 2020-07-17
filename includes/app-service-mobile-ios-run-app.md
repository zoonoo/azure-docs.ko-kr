---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240263"
---
1. Xcode를 사용하여 다운로드한 클라이언트 프로젝트를 엽니다.

2. [Azure 포털로](https://portal.azure.com/) 이동하여 만든 모바일 앱으로 이동합니다. 블레이드에서 `Overview` 모바일 앱의 공개 끝점인 URL을 찾습니다. 예 - 내 앱 이름 "test123"의 https://test123.azurewebsites.net사이트 이름은 .

3. 신속한 프로젝트의 경우 이 `ToDoTableViewController.swift` 폴더에서 파일을 엽니다. 응용 프로그램 `ZUMOAPPNAME`이름은 입니다.

4. 메서드에서 `viewDidLoad()` 매개 `ZUMOAPPURL` 변수를 위의 공용 끝점으로 바꿉꿉습니다.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    다음이 됩니다.
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Objective-C 프로젝트의 경우 이 `QSTodoService.m` 폴더의 파일을 엽니다. 응용 프로그램 `ZUMOAPPNAME`이름은 입니다.

6. 메서드에서 `init` 매개 `ZUMOAPPURL` 변수를 위의 공용 끝점으로 바꿉꿉습니다.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    다음이 됩니다.
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 시뮬레이터에서 앱을 시작합니다.

8. 앱에서 더하기 ()**+** 아이콘을 클릭하고 자습서 *완료와*같은 의미 있는 텍스트를 입력한 다음 저장 단추를 클릭합니다. 그러면 이전에 배포한 Azure 백 엔드에 POST 요청을 보냅니다. 요청의 백 엔드 데이터가 TodoItem SQL 테이블로 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.

   ![iOS에서 실행 중인 빠른 시작 앱](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
