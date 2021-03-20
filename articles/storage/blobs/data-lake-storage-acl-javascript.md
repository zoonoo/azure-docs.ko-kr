---
title: JavaScript를 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 설정
description: JavaScript 용 Azure Storage Data Lake 클라이언트 라이브러리를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 4d3e13c6593c0e11df84131a9a07eb2868277d2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654237"
---
# <a name="use-javascript-to-manage-acls-in-azure-data-lake-storage-gen2"></a>JavaScript를 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리

이 문서에서는 JavaScript를 사용 하 여 디렉터리 및 파일의 액세스 제어 목록을 가져오고, 설정 하 고, 업데이트 하는 방법을 보여 줍니다. 

[패키지 (노드 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

- 다음 보안 권한 중 하나입니다.

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 프로 비전 된 AD (Azure Active Directory) [보안 주체](../../role-based-access-control/overview.md#security-principal) 입니다.  

  - ACL 설정을 적용 하려는 대상 컨테이너 또는 디렉터리를 소유 하는 사용자입니다. Acl을 재귀적으로 설정 하기 위해 대상 컨테이너 또는 디렉터리의 모든 자식 항목을 포함 합니다.
  
  - 저장소 계정 키입니다.

## <a name="set-up-your-project"></a>프로젝트 설정

터미널 창을 열고 다음 명령을 입력 하 여 JavaScript 용 Data Lake 클라이언트 라이브러리를 설치 합니다.

```javascript
npm install @azure/storage-file-datalake
```

`storage-file-datalake`이 문을 코드 파일의 맨 위에 배치 하 여 패키지를 가져옵니다. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

> [!NOTE]
> Azure AD (Azure Active Directory)를 사용 하 여 액세스 권한을 부여 하는 경우 보안 주체가 [저장소 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은  [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)을 참조 하세요.

Azure AD를 사용 하 여 응용 프로그램을 인증 하는 데 [JS 용 azure id 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/identity) 를 사용할 수 있습니다.

클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 가져옵니다. 이렇게 하려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조 하세요. 해당 프로세스의 일부로 다음 [azure 역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/overview.md) 역할 중 하나를 보안 주체에 할당 해야 합니다. 

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.  

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> 더 많은 예제는 [JS 용 Azure id 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/identity) 설명서를 참조 하세요.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> 이 권한 부여 방법은 Node.js 응용 프로그램에만 적용 됩니다. 브라우저에서 코드를 실행 하려는 경우 AD (Azure Active Directory)를 사용 하 여 권한을 부여할 수 있습니다.

## <a name="get-and-set-a-directory-acl"></a>디렉터리 ACL 가져오기 및 설정

이 예제에서는 이름이 인 디렉터리의 ACL을 가져온 다음 설정 합니다 `my-directory` . 이 예제에서는 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 권한을 부여 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 권한이 적용되는 방식과 권한 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](./data-lake-storage-access-control.md)를 참조하세요.

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

컨테이너의 루트 디렉터리에 대 한 ACL을 가져오고 설정할 수도 있습니다. 루트 디렉터리를 가져오려면 `/` **DataLakeFileSystemClient client** 메서드에 빈 문자열 ()을 전달 합니다.

### <a name="get-and-set-a-file-acl"></a>파일 ACL 가져오기 및 설정

이 예제에서는 라는 파일의 ACL을 가져온 다음 설정 합니다 `upload-file.txt` . 이 예제에서는 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 권한을 부여 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 권한이 적용되는 방식과 권한 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](./data-lake-storage-access-control.md)를 참조하세요.

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>참고 항목

- [패키지(Node 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [피드백 보내기](https://github.com/Azure/azure-sdk-for-java/issues)
- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2의 Acl (액세스 제어 목록)](data-lake-storage-access-control.md)