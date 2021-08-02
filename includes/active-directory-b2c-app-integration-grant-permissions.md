---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 6d62dda341c6acb783bd2525c2f6ba7a76b312ec
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073071"
---
1. **관리** 아래에서 **API 권한** 을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가** 를 선택합니다.
1. **내 API** 탭을 선택합니다.
1. 웹 애플리케이션에 액세스 권한을 부여할 API를 선택합니다. 예를 들어 *my-api1* 을 입력합니다.
1. **권한** 에서 **작업** 을 펼친 다음, 앞에서 정의한 범위를 선택합니다. 예를 들어 *tasks.read* 및 *tasks.write* 를 선택합니다.
1. **권한 추가** 를 선택합니다.
1. **(테넌트 이름)에 대한 관리자 동의 허용** 을 선택합니다.
1. **예** 를 선택합니다.
1. **새로 고침** 을 선택한 다음, 두 범위 모두 **상태** 아래에 "...에 대해 허용됨"이 표시되는지 확인합니다.
1. **구성된 권한** 목록에서 범위를 선택하고 범위 전체 이름을 복사합니다. 

    ![웹 애플리케이션 범위 가져오기](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  