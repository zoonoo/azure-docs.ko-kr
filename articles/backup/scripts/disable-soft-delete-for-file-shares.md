---
title: 스크립트 샘플 - 파일 공유에 대해 일시 삭제 사용 안 함
description: 스토리지 계정의 파일 공유에 대해 일시 삭제를 사용 해제하는 스크립트 사용 방법을 알아봅니다.
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84121286"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>스토리지 계정의 파일 공유에 대해 일시 삭제 사용 안 함

이 문서는 스토리지 계정의 파일 공유에 대한 일시 삭제를 사용 해제하는 프로세스를 설명합니다.

다음 단계를 수행하세요.

1. armclient를 설치합니다. 이를 설치하는 방법에 대한 자세한 내용은 [이 링크](https://github.com/projectkudu/ARMClient)를 참조하세요.

2. 컴퓨터의 폴더에 다음 두 요청 본문 파일을 저장합니다.

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. 스토리지 계정 ARM(Azure Resource Manager) ID를 바로 사용할 수 있도록 편리하게 유지합니다. 예: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. **armclient login**을 실행하여 자격 증명을 사용하여 로그인합니다.

5. 스토리지 계정에서 파일 공유의 현재 일시 삭제 속성을 가져옵니다.

    다음 GET 작업은 *inquirytest* 계정의 파일 공유에 대한 일시 삭제 속성을 가져옵니다.

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. 스토리지 계정의 파일 공유에 대해 일시 삭제를 사용 해제합니다.

    다음 PUT 작업은 *inquirytest* 계정의 파일 공유에 대한 일시 삭제 속성을 해제합니다.

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. 일시 삭제를 다시 활성화하려면 다음 샘플을 사용합니다.

    다음 PUT 작업을 수행하면 "inquirytest" 계정의 파일 공유에 대한 일시 삭제 속성을 사용할 수 있습니다.

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
