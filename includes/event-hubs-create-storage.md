---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66154561"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>이벤트 처리기 호스트에 대한 저장소 계정 만들기
이벤트 처리기 호스트는 영구적 검사점을 관리하여 Event Hubs의 이벤트 수신을 간소화하고 병렬 수신을 수행하는 지능형 에이전트입니다. 검사점 지정을 위해 이벤트 처리기 호스트에는 저장소 계정이 필요합니다. 다음 예제에서는 저장소 계정을 만들고 액세스를 위해 키를 가져오는 방법을 보여 줍니다.

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기**를 선택합니다.

2. **저장소**를 선택한 다음, **저장소 계정 - Blob, 파일, 테이블, 큐**를 선택합니다.
   
    ![Storage 계정 선택](./media/event-hubs-create-storage/create-storage1.png)

3. **저장소 계정 만들기** 페이지에서 다음 단계를 수행합니다. 

   1. 저장소 계정의 이름을 입력합니다. 
   2. 이벤트 허브를 포함하는 Azure 구독을 선택합니다.
   3. 이벤트 허브가 있는 리소스 그룹을 선택합니다.
   4. 리소스를 만들 위치를 선택합니다. 
   5. **검토 + 만들기**를 클릭합니다.
   
      ![저장소 계정 만들기 - 페이지](./media/event-hubs-create-storage/create-storage2.png)

4. **검토 + 만들기** 페이지에서 값을 검토하고 **만들기**를 선택합니다. 

    ![저장소 계정 설정 검토 및 만들기](./media/event-hubs-create-storage/review-create-storage-account.png)
5. 표시 되 면 합니다 **배포 성공** 메시지를 선택 **리소스로 이동** 페이지의 맨 위에 있는 합니다. 리소스 목록에서 저장소 계정을 선택하여 [저장소 계정] 페이지를 시작할 수도 있습니다.  

    ![배포에서 저장소 계정 선택](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. **Essentials** 창에서 **Blob**을 선택합니다. 

    ![Blob 서비스 선택](./media/event-hubs-create-storage/select-blobs-service.png)
1. 위쪽에서 **+ 컨테이너**를 선택하고, 컨테이너에 대한 **이름**을 입력하고, **확인**을 선택합니다. 

    ![Blob 컨테이너 만들기](./media/event-hubs-create-storage/create-blob-container.png)
1. 왼쪽 메뉴에서 **액세스 키**를 선택하고 **key1**의 값을 복사합니다. 

    다음 값을 메모장 또는 다른 임시 위치에 저장합니다.
    - 저장소 계정 이름
    - 저장소 계정에 대한 액세스 키
    - 컨테이너 이름
