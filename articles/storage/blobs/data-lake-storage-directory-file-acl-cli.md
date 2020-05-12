---
title: Azure Data Lake Storage Gen2에서 파일 & Acl에 Azure CLI 사용
description: 계층 구조가 있는 저장소 계정의 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 하려면 Azure CLI을 사용 합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 79a87f02c8730e0b2110e7475de721f11beda568
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120609"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Azure CLI를 사용 하 여의 디렉터리, 파일 및 Acl 관리 Azure Data Lake Storage Gen2

이 문서에서는 [CLI (Azure 명령줄 인터페이스](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) )를 사용 하 여 계층 네임 스페이스가 있는 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

[Gen1 To Gen2 mapping](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  |  [사용자 의견 제공](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임 스페이스 (HNS)를 사용 하도록 설정 된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 새로 만듭니다.
> * 이상 버전을 Azure CLI `2.5.1` 합니다.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>올바른 버전의 Azure CLI 설치 되어 있는지 확인 합니다.

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)을 열거나 Azure CLI를 로컬로 [설치한](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 경우 Windows PowerShell과 같은 명령 콘솔 응용 프로그램을 엽니다.

2. 다음 명령을 사용 하 여 설치 된 Azure CLI 버전이 이상 인지 확인 `2.5.1` 합니다.

   ```azurecli
    az --version
   ```
   Azure CLI 버전이 보다 낮은 경우 `2.5.1` 최신 버전을 설치 합니다. [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="connect-to-the-account"></a>계정에 연결

1. Azure CLI를 로컬로 사용 하는 경우 로그인 명령을 실행 합니다.

   ```azurecli
   az login
   ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면에서 브라우저 페이지를 열고 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 터미널에 표시 된 인증 코드를 입력 합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인 합니다.

   다른 인증 방법에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 blob 또는 큐 데이터에 대 한 액세스 권한 부여](../common/authorize-data-operations-cli.md)를 참조 하세요.

2. Id가 둘 이상의 구독과 연결 된 경우 정적 웹 사이트를 호스트 하는 저장소 계정의 구독으로 활성 구독을 설정 합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>`자리 표시자 값을 구독의 ID로 바꿉니다.

> [!NOTE]
> 이 문서에 제공 된 예제는 AD (Azure Active Directory) 인증을 보여 줍니다. 권한 부여 방법에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 blob 또는 큐 데이터에 대 한 액세스 권한 부여](../common/authorize-data-operations-cli.md)를 참조 하세요.

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. 명령을 사용 하 여 만들 수 있습니다 `az storage fs create` . 

이 예에서는 라는 파일 시스템을 만듭니다 `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>파일 시스템 속성 표시

명령을 사용 하 여 콘솔에 파일 시스템의 속성을 인쇄할 수 있습니다 `az storage fs show` .

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>파일 시스템 내용 나열

명령을 사용 하 여 디렉터리의 내용을 나열 합니다 `az storage fs file list` .

이 예에서는 이라는 파일 시스템의 내용을 나열 `my-file-system` 합니다.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>파일 시스템 삭제

명령을 사용 하 여 파일 시스템을 삭제 `az storage fs delete` 합니다.

이 예에서는 이라는 파일 시스템을 삭제 `my-file-system` 합니다. 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>디렉터리 만들기

명령을 사용 하 여 디렉터리 참조를 만듭니다 `az storage fs directory create` . 

이 예에서는 라는 `my-directory` 계정에 있는 라는 파일 시스템에 라는 디렉터리를 추가 `my-file-system` `mystorageaccount` 합니다.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>디렉터리 속성 표시

명령을 사용 하 여 디렉터리의 속성을 콘솔에 인쇄할 수 있습니다 `az storage fs directory show` .

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

명령을 사용 하 여 디렉터리 이름을 바꾸거나 이동 `az storage fs directory move` 합니다.

이 예에서는 이름에서 `my-directory` `my-new-directory` 동일한 파일 시스템의 이름으로 디렉터리의 이름을 바꿉니다.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

이 예에서는 디렉터리를 라는 파일 시스템으로 이동 `my-second-file-system` 합니다.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>디렉터리 삭제

명령을 사용 하 여 디렉터리를 삭제 `az storage fs directory delete` 합니다.

이 예에서는 라는 디렉터리를 삭제 `my-directory` 합니다. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>디렉터리가 있는지 확인

명령을 사용 하 여 파일 시스템에 특정 디렉터리가 있는지 확인 `az storage fs directory exists` 합니다.

이 예에서는 라는 디렉터리가 `my-directory` 파일 시스템에 있는지 여부를 나타냅니다 `my-file-system` . 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

명령을 사용 하 여 디렉터리에서 파일을 다운로드 `az storage fs file download` 합니다.

이 예제에서는 라는 디렉터리에서 라는 파일을 다운로드 `upload.txt` `my-directory` 합니다. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

명령을 사용 하 여 디렉터리의 내용을 나열 합니다 `az storage fs file list` .

이 예제에서는 `my-directory` `my-file-system` 라는 저장소 계정의 파일 시스템에 있는 디렉터리의 콘텐츠를 나열 `mystorageaccount` 합니다. 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

명령을 사용 하 여 디렉터리에 파일을 업로드 `az storage fs directory upload` 합니다.

이 예제에서는 라는 디렉터리에 라는 파일을 업로드 `upload.txt` `my-directory` 합니다. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>파일 속성 표시

명령을 사용 하 여 콘솔에 파일의 속성을 인쇄할 수 있습니다 `az storage fs file show` .

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>파일 이름 바꾸기 또는 이동

명령을 사용 하 여 파일의 이름을 바꾸거나 파일을 이동 `az storage fs file move` 합니다.

이 예에서는 이름에서 이름으로 파일의 이름을 바꿉니다 `my-file.txt` `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>파일 삭제

명령을 사용 하 여 파일을 삭제 `az storage fs file delete` 합니다.

이 예제에서는 이름이`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>사용 권한 관리

디렉터리 및 파일의 액세스 권한을 가져오고 설정 하 고 업데이트할 수 있습니다.

> [!NOTE]
> Azure Active Directory (Azure AD)를 사용 하 여 명령에 권한을 부여 하는 경우 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조 하세요.

### <a name="get-an-acl"></a>ACL 가져오기

명령을 사용 하 여 **디렉터리** 의 ACL을 가져옵니다 `az storage fs access show` .

이 예에서는 디렉터리의 ACL을 가져온 다음 해당 ACL을 콘솔에 출력 합니다.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

명령을 사용 하 여 **파일** 에 대 한 액세스 권한을 가져옵니다 `az storage fs access show` . 

이 예제에서는 파일의 ACL을 가져온 다음 해당 ACL을 콘솔에 출력 합니다.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

다음 이미지는 디렉터리의 ACL을 가져온 후의 출력을 보여 줍니다.

![ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

이 예제에서 소유 하는 사용자에 게는 읽기, 쓰기 및 실행 권한이 있습니다. 소유 그룹에는 읽기 및 실행 권한만 있습니다. 액세스 제어 목록에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2 access control](data-lake-storage-access-control.md)을 참조 하세요.

### <a name="set-an-acl"></a>ACL 설정

명령을 사용 `az storage fs access set` 하 여 **디렉터리**의 ACL을 설정 합니다. 

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 디렉터리의 ACL을 설정 하 고 콘솔에 ACL을 인쇄 합니다.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 디렉터리의 *기본* acl을 설정 하 고 콘솔에 acl을 인쇄 합니다.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

명령을 사용 `az storage fs access set` 하 여 **파일**의 acl을 설정 합니다. 

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 파일에 ACL을 설정 하 고 콘솔에 ACL을 인쇄 합니다.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

다음 이미지는 파일의 ACL을 설정한 후의 출력을 보여 줍니다.

![ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

이 예제에서 소유 하는 사용자 및 소유 그룹에는 읽기 및 쓰기 권한만 있습니다. 다른 모든 사용자에 게는 쓰기 및 실행 권한이 있습니다. 액세스 제어 목록에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2 access control](data-lake-storage-access-control.md)을 참조 하세요.

### <a name="update-an-acl"></a>ACL 업데이트

이 사용 권한을 설정 하는 또 다른 방법은 명령을 사용 하는 것입니다 `az storage fs access set` . 

`-permissions`매개 변수를 acl의 약식 형식으로 설정 하 여 디렉터리 또는 파일의 acl을 업데이트 합니다.

이 예에서는 **디렉터리**의 ACL을 업데이트 합니다.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

이 예에서는 **파일**의 ACL을 업데이트 합니다.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

`--owner`또는 `group` 매개 변수를 사용자의 엔터티 ID 또는 UPN (사용자 계정 이름)으로 설정 하 여 디렉터리 또는 파일의 소유 사용자 및 그룹을 업데이트할 수도 있습니다. 

이 예에서는 디렉터리의 소유자를 변경 합니다. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

이 예에서는 파일의 소유자를 변경 합니다. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>참고 항목

* [Gen1 to Gen2 mapping](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [피드백 제공](https://github.com/Azure/azure-cli-extensions/issues)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


