---
title: Azure 데이터 레이크 저장소 Gen2의 ACL& 파일에 자바스크립트 사용
description: JavaScript용 Azure 저장소 데이터 레이크 클라이언트 라이브러리를 사용하여 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리 및 파일 및 디렉터리 액세스 제어 목록(ACL)을 관리합니다.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061550"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>자바스크립트를 사용하여 Azure 데이터 레이크 스토리지 Gen2에서 디렉터리, 파일 및 ACL을 관리합니다.

이 문서에서는 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리하기 위해 JavaScript를 사용하는 방법을 보여 주며 있습니다. 

[패키지(노드 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [샘플이](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [피드백을 제공합니다.](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임스페이스(HNS)가 활성화된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 만들 수 있습니다.
> * Node.js 응용 프로그램에서 이 패키지를 사용하는 경우 Node.js 8.0.0 이상이 필요합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

터미널 창을 열고 다음 명령을 입력하여 JavaScript용 Data Lake 클라이언트 라이브러리를 설치합니다.

```javascript
npm install @azure/storage-file-datalake
```

이 `storage-file-datalake` 문을 코드 파일 의 맨 위에 배치하여 패키지를 가져옵니다. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>계정에 연결 

이 문서의 스니펫을 사용하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용하여 연결

이렇게 하면 계정에 연결하는 가장 쉬운 방법이 있습니다. 

이 예제는 계정 키를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

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
> 이 권한 부여 방법은 Node.js 응용 프로그램에서만 작동합니다. 브라우저에서 코드를 실행하려는 경우 Azure Active Directory(AD)를 사용하여 권한을 부여할 수 있습니다. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active 디렉터리(AD)를 사용하여 연결

[JS에 대한 Azure ID 클라이언트 라이브러리를](https://www.npmjs.com/package/@azure/identity) 사용하여 Azure AD를 사용하여 응용 프로그램을 인증할 수 있습니다.

이 예제에서는 클라이언트 ID, 클라이언트 암호 및 테넌트 ID를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 가져오려면 [클라이언트 응용 프로그램에서 요청을 승인하기 위해 Azure AD에서 토큰 가져오기를](../common/storage-auth-aad-app.md)참조하십시오.

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> 자세한 예는 JS 설명서에 [대한 Azure ID 클라이언트 라이브러리를](https://www.npmjs.com/package/@azure/identity) 참조하십시오.

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. **FileSystemClient** 인스턴스를 얻은 다음 **FileSystemClient.Create** 메서드를 호출하여 하나를 만들 수 있습니다.

이 예제는 .라는 `my-file-system`파일 시스템을 만듭니다. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>디렉터리 만들기

**디렉터리클라이언트** 인스턴스를 얻은 다음 **DirectoryClient.create** 메서드를 호출하여 디렉터리 참조를 만듭니다.

이 예제는 파일 `my-directory` 시스템에 명명된 디렉터리를 추가합니다. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**DirectoryClient.rename** 메서드를 호출하여 디렉터리 이름을 바꾸거나 이동합니다. 원하는 디렉터리 매개 변수의 경로를 전달합니다. 

이 예제는 하위 디렉터리 이름을 `my-directory-renamed`이름으로 바꿉니다.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

이 예제는 `my-directory-renamed` `my-directory-2`명명된 디렉터리를 .라는 디렉터리라는 디렉터리로 이동합니다. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>디렉터리 삭제

**DirectoryClient.delete** 메서드를 호출 하여 디렉터리를 삭제합니다.

이 예제는 .라는 `my-directory`디렉터리를 삭제합니다.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>디렉터리 ACL 관리

이 예제는 . `my-directory` 이 예제에서는 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 액세스 권한을 부여합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

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

## <a name="upload-a-file-to-a-directory"></a>디렉토리에 파일 업로드

먼저 파일을 읽습니다. 이 예제에서는 Node.js 모듈을 `fs` 사용합니다. 그런 다음 **FileClient** 인스턴스를 만든 다음 **FileClient.create** 메서드를 호출하여 대상 디렉터리에서 파일 참조를 만듭니다. **FileClient.append** 메서드를 호출하여 파일을 업로드합니다. **FileClient.flush** 메서드를 호출하여 업로드를 완료해야 합니다.

이 예제는 텍스트 파일을 라는 `my-directory`디렉터리로 업로드합니다.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>파일 ACL 관리

이 예제는 . `upload-file.txt` 이 예제에서는 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 액세스 권한을 부여합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

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

## <a name="download-from-a-directory"></a>디렉토리에서 다운로드

먼저 다운로드할 파일을 나타내는 **FileSystemClient** 인스턴스를 만듭니다. **FileSystemClient.read** 메서드를 사용하여 파일을 읽습니다. 그런 다음 파일을 작성합니다. 이 예제에서는 Node.js `fs` 모듈을 사용하여 이를 수행합니다. 

> [!NOTE]
> 파일을 다운로드하는 이 방법은 Node.js 응용 프로그램에서만 작동합니다. 브라우저에서 코드를 실행하려는 경우 브라우저에서 이 작업을 수행하는 방법에 대한 예제는 JavaScript 읽기 파일에 [대한 Azure 저장소 파일 데이터 레이크 클라이언트 라이브러리를](https://www.npmjs.com/package/@azure/storage-file-datalake) 참조하세요. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

이 예제에서는 `my-directory`.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>참조

* [패키지(노드 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)