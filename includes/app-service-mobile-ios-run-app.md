---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: f4ba467b6d80c9ccafba0a91c1f04152b92cf869
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140657"
---
1. Mac에서 [Azure Portal]을 방문합니다. **모든 서비스** > **App Services** > 방금 만든 백 엔드를 클릭합니다. 모바일 앱 설정에서 기본 언어를 선택합니다.

   - Objective-C &ndash; **빠른 시작** > **iOS(Objective-C)**
   - Swift &ndash; **빠른 시작** > **iOS(Swift)**

     **3. 클라이언트 애플리케이션 구성** 아래에서 **다운로드**를 클릭합니다. 그러면 백 엔드에 연결하도록 사전 구성된 완전한 Xcode 프로젝트가 다운로드됩니다. Xcode를 사용하여 프로젝트를 엽니다.

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 시뮬레이터에서 앱을 시작합니다.

1. 앱에서 더하기 아이콘(**+**)을 클릭하고, 의미 있는 텍스트(예: *Complete the tutorial*)를 입력한 다음, 저장 단추를 클릭합니다. 그러면 이전에 배포한 Azure 백 엔드에 POST 요청을 보냅니다. 요청의 백 엔드 데이터가 TodoItem SQL 테이블로 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.

   ![iOS에서 실행 중인 빠른 시작 앱](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
