---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182870"
---
다음 단계에 따라 스토리지 계정에 연결하고 연결을 확인합니다.

1. Storage Explorer에서 **Azure Storage에 연결** 대화 상자를 엽니다. **Azure Storage에 연결** 대화 상자에서 **스토리지 계정 이름 및 키 사용**을 선택합니다.

    ![Data Box 대시보드](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. **계정 이름** 및 **계정 키**(로컬 웹 UI의 **연결 및 복사** 페이지에서 키 1 값)를 붙여넣습니다. Storage 엔드포인트 도메인을 **기타(아래 입력)** 으로 선택한 다음, 아래 표시된 것처럼 Blob 서비스 엔드포인트를 제공합니다. *http*를 통해 전송하는 경우에만 **HTTP 사용** 옵션을 선택합니다. *https*를 사용하는 경우 옵션을 선택하지 않은 상태로 둡니다. **다음**을 선택합니다.

    ![Data Box 대시보드](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. **연결 요약** 대화 상자에서 제공된 정보를 검토합니다. **연결**을 선택합니다.

    ![Data Box 대시보드](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. 성공적으로 추가한 계정은 Storage Explorer의 왼쪽 창에 해당 이름에 (외부, 기타)가 추가된 상태로 표시됩니다. 컨테이너를 보려면 **Blob 컨테이너**를 클릭합니다.

    ![Data Box 대시보드](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
