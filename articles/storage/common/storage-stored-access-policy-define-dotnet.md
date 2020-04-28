---
title: .NET-Azure Storage를 사용 하 여 저장 된 액세스 정책 정의
description: .NET 클라이언트 라이브러리를 사용 하 여 저장 된 액세스 정책을 정의 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "68990742"
---
# <a name="define-a-stored-access-policy-with-net"></a>.NET을 사용 하 여 저장 된 액세스 정책 정의

저장 된 액세스 정책은 서버 쪽의 서비스 수준 SAS (공유 액세스 서명)에 대 한 추가 제어 수준을 제공 합니다. 저장 된 액세스 정책을 정의 하는 것은 공유 액세스 서명을 그룹화 하 고 정책에 의해 바인딩되는 공유 액세스 서명에 대 한 추가 제한 사항을 제공 하는 데 사용 됩니다. 저장 된 액세스 정책을 사용 하 여 SAS의 시작 시간, 만료 시간 또는 사용 권한을 변경 하거나 발급 된 후에 취소할 수 있습니다.
  
 다음 저장소 리소스에서는 저장된 액세스 정책을 지원합니다.  
  
- Blob 컨테이너  
- 파일 공유  
- 큐  
- 테이블  
  
> [!NOTE]
> 컨테이너에 대 한 저장 된 액세스 정책은 컨테이너 자체 나 컨테이너에 포함 된 blob에 대 한 권한을 부여 하는 공유 액세스 서명과 연결 될 수 있습니다. 마찬가지로, 파일 공유에 대 한 저장 된 액세스 정책은 공유 액세스에 권한을 부여 하는 공유 액세스 서명 또는 공유 액세스에 포함 된 파일에 연결 될 수 있습니다.  
>
> 저장 된 액세스 정책은 서비스 SAS에만 지원 됩니다. 계정 SAS 또는 사용자 위임 SAS에 대해 저장 된 액세스 정책이 지원 되지 않습니다.  

## <a name="create-a-stored-access-policy"></a>저장된 액세스 정책 만들기

다음 코드는 컨테이너에 저장된 액세스 정책을 만듭니다. 액세스 정책을 사용하여 컨테이너나 해당 Blob에 서비스 SAS에 대한 제약 조건을 지정할 수 있습니다.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>참고 항목

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](storage-sas-overview.md)
- [저장된 액세스 정책 정의](/rest/api/storageservices/define-stored-access-policy)

