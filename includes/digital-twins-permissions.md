---
title: 포함 파일
description: 포함 파일
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 09/19/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 1887efd741f4779a5186707d60b27ca66fc3c06f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283986"
---
1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 연 다음, **속성** 창을 엽니다. **디렉터리 ID**를 임시 파일에 복사합니다. 이 값은 다음 섹션에서 샘플 응용 프로그램을 구성하는 데 사용됩니다.

    ![Azure Active Directory 디렉터리 ID](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. **앱 등록** 창을 열고 **새 응용 프로그램 등록** 단추를 클릭합니다.
    
    ![Azure Active Directory 앱 등록 새로 만들기](./media/digital-twins-permissions/aad-app-reg-start.png)

1. **이름** 필드에 이 앱 등록의 이름을 입력합니다. **응용 프로그램 유형**으로  **_네이티브_** 를 선택하고, **리디렉션 URI**로 **_https://microsoft.com_** 을 선택합니다. **만들기**를 클릭합니다.

    ![Azure Active Directory 앱 등록 만들기](./media/digital-twins-permissions/aad-app-reg-create.png)

1. 등록된 앱을 열고, **응용 프로그램 ID** 필드의 값을 임시 파일에 복사합니다. 이 값은 Azure Active Directory 앱을 식별합니다. 응용 프로그램 ID는 다음 섹션에서 샘플 응용 프로그램을 구성하는 데 사용됩니다.

    ![Azure Active Directory 응용 프로그램 ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. 앱 등록 창을 열고 **설정** > **필요한 권한**을 클릭합니다.
    - 왼쪽 위에서 **추가**를 클릭하여 **API 액세스 추가** 창을 엽니다.
    - **API 선택**을 클릭하고 **Azure Digital Twins**를 검색합니다. 검색에서 API를 찾을 수 없는 경우 **Azure Smart Spaces**을 대신 검색합니다.
    - **Azure Digital Twins(Azure Smart Spaces 서비스)** 옵션을 선택하고 **선택**을 클릭합니다.
    - **사용 권한 선택**을 클릭합니다. **읽기/쓰기 액세스** 위임된 권한 상자를 클릭하고 **선택**을 클릭합니다.
    - **API 액세스 추가** 창에서 **완료**를 클릭합니다.
    - **필요한 권한** 창에서 **권한 부여** 단추를 클릭하고, 나타나는 확인 메시지를 수락합니다.

       ![Azure Active Directory 앱 등록 api 추가](./media/digital-twins-permissions/aad-app-req-permissions.png)
