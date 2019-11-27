---
title: Azure Data Lake Storage Gen2 (미리 보기)에서 파일 & Acl의 Azure CLI 사용
description: 계층 구조가 있는 저장소 계정의 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 하려면 Azure CLI을 사용 합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 95bb43f1531b6234ccb90eb7d66404ccc66f60f3
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485147"
---
# <a name="use-azure-cli-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2 (미리 보기)에서 파일 & Acl의 Azure CLI 사용

이 문서에서는 [CLI (Azure 명령줄 인터페이스](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) )를 사용 하 여 계층 네임 스페이스가 있는 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> 이 문서에서 설명 하는 `storage-preview` 확장은 현재 공개 미리 보기로 제공 됩니다.

Gen1 [샘플](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen2 매핑](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [피드백 제공](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>선행 조건

> [!div class="checklist"]
> * Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임 스페이스 (HNS)를 사용 하도록 설정 된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 새로 만듭니다.
> * Azure CLI 버전 `2.0.67` 이상.

## <a name="install-the-storage-cli-extension"></a>Storage CLI 확장을 설치 합니다.

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)을 열거나 Azure CLI를 로컬로 [설치한](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 경우 Windows PowerShell과 같은 명령 콘솔 응용 프로그램을 엽니다.

2. 다음 명령을 사용 하 여 설치 된 Azure CLI 버전이 `2.0.67` 이상 인지 확인 합니다.

   ```azurecli
    az --version
   ```
   Azure CLI 버전이 `2.0.67`보다 낮은 경우 최신 버전을 설치 합니다. [Azure CLI 설치를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)참조 하세요.

3. `storage-preview` 확장을 설치 합니다.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>계정에 연결

1. Azure CLI를 로컬로 사용 하는 경우 로그인 명령을 실행 합니다.

   ```azurecli
   az login
   ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 에서 브라우저 페이지를 열고 터미널에 표시 된 인증 코드를 입력 합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인 합니다.

   다른 인증 방법에 대 한 자세한 내용은 Azure CLI를 사용 하 여 로그인을 참조 하세요.

2. Id가 둘 이상의 구독과 연결 된 경우 정적 웹 사이트를 호스트 하는 저장소 계정의 구독으로 활성 구독을 설정 합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. `az storage container create` 명령을 사용 하 여 만들 수 있습니다. 

이 예에서는 `my-file-system`이라는 파일 시스템을 만듭니다.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>디렉터리 만들기

`az storage blob directory create` 명령을 사용 하 여 디렉터리 참조를 만듭니다. 

이 예제에서는 이름이 `mystorageaccount`계정에 있는 `my-file-system` 이라는 파일 시스템에 `my-directory` 라는 디렉터리를 추가 합니다.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>디렉터리 속성 표시

`az storage blob show` 명령을 사용 하 여 콘솔에 대 한 디렉터리 속성을 인쇄할 수 있습니다.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

`az storage blob directory move` 명령을 사용 하 여 디렉터리의 이름을 바꾸거나 디렉터리를 이동 합니다.

이 예에서는 `my-directory` 이름에서 `my-new-directory`이름으로 디렉터리의 이름을 바꿉니다.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>디렉터리 삭제

`az storage blob directory delete` 명령을 사용 하 여 디렉터리를 삭제 합니다.

이 예에서는 `my-directory`라는 디렉터리를 삭제 합니다. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>디렉터리가 있는지 확인

`az storage blob directory exist` 명령을 사용 하 여 파일 시스템에 특정 디렉터리가 있는지 확인 합니다.

이 예에서는 `my-directory` 라는 디렉터리가 `my-file-system` 파일 시스템에 있는지 여부를 나타냅니다. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

`az storage blob directory download` 명령을 사용 하 여 디렉터리에서 파일을 다운로드 합니다.

이 예제에서는 이름이 `my-directory`인 디렉터리에서 `upload.txt` 파일을 다운로드 합니다. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

이 예에서는 전체 디렉터리를 다운로드 합니다.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

`az storage blob directory list` 명령을 사용 하 여 디렉터리의 내용을 나열 합니다.

이 예제에서는 `mystorageaccount`이라는 저장소 계정의 `my-file-system` 파일 시스템에 있는 `my-directory` 디렉터리의 콘텐츠를 나열 합니다. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

`az storage blob directory upload` 명령을 사용 하 여 디렉터리에 파일을 업로드 합니다.

이 예제에서는 이름이 `upload.txt` 인 파일을 `my-directory`라는 디렉터리에 업로드 합니다. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

이 예에서는 전체 디렉터리를 업로드 합니다.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>파일 속성 표시

`az storage blob show` 명령을 사용 하 여 콘솔에 파일의 속성을 인쇄할 수 있습니다.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>파일 이름 바꾸기 또는 이동

`az storage blob move` 명령을 사용 하 여 파일의 이름을 바꾸거나 파일을 이동 합니다.

이 예에서는 `my-file.txt` 이름에서 `my-file-renamed.txt`이름으로 파일의 이름을 바꿉니다.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>파일 삭제

`az storage blob delete` 명령을 사용 하 여 파일을 삭제 합니다.

이 예에서는 라는 파일을 삭제 `my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>권한 관리

디렉터리 및 파일의 액세스 권한을 가져오고 설정 하 고 업데이트할 수 있습니다.

### <a name="get-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 가져오기

`az storage blob directory access show` 명령을 사용 하 여 **디렉터리** 의 ACL을 가져옵니다.

이 예에서는 디렉터리의 ACL을 가져온 다음 해당 ACL을 콘솔에 출력 합니다.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

`az storage blob access show` 명령을 사용 하 여 **파일** 에 대 한 액세스 권한을 가져옵니다. 

이 예제에서는 파일의 ACL을 가져온 다음 해당 ACL을 콘솔에 출력 합니다.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

다음 이미지는 디렉터리의 ACL을 가져온 후의 출력을 보여 줍니다.

![ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

이 예제에서 소유 하는 사용자에 게는 읽기, 쓰기 및 실행 권한이 있습니다. 소유 그룹에는 읽기 및 실행 권한만 있습니다. 액세스 제어 목록에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2 access control](data-lake-storage-access-control.md)을 참조 하세요.

### <a name="set-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 설정

`az storage blob directory access set` 명령을 사용 하 여 **디렉터리**의 ACL을 설정 합니다. 

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 디렉터리의 ACL을 설정 하 고 콘솔에 ACL을 인쇄 합니다.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

`az storage blob access set` 명령을 사용 하 여 **파일**의 acl을 설정 합니다. 

이 예에서는 소유 사용자, 소유 그룹 또는 기타 사용자에 대 한 파일에 ACL을 설정 하 고 콘솔에 ACL을 인쇄 합니다.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
다음 이미지는 파일의 ACL을 설정한 후의 출력을 보여 줍니다.

![ACL 출력 가져오기](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

이 예제에서 소유 하는 사용자 및 소유 그룹에는 읽기 및 쓰기 권한만 있습니다. 다른 모든 사용자에 게는 쓰기 및 실행 권한이 있습니다. 액세스 제어 목록에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2 access control](data-lake-storage-access-control.md)을 참조 하세요.

### <a name="update-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 업데이트

이 사용 권한을 설정 하는 또 다른 방법은 `az storage blob directory access update` 또는 `az storage blob access update` 명령을 사용 하는 것입니다. 

`-permissions` 매개 변수를 ACL의 약식 형식으로 설정 하 여 디렉터리 또는 파일의 ACL을 업데이트 합니다.

이 예에서는 **디렉터리**의 ACL을 업데이트 합니다.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

이 예에서는 **파일**의 ACL을 업데이트 합니다.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

`--owner` 또는 `group` 매개 변수를 사용자의 엔터티 ID 또는 UPN (사용자 계정 이름)으로 설정 하 여 디렉터리 또는 파일의 소유 사용자 및 그룹을 업데이트할 수도 있습니다. 

이 예에서는 디렉터리의 소유자를 변경 합니다. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

이 예에서는 파일의 소유자를 변경 합니다. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>사용자 정의 메타 데이터 관리

하나 이상의 이름-값 쌍이 있는 `az storage blob directory metadata update` 명령을 사용 하 여 파일 또는 디렉터리에 사용자 정의 메타 데이터를 추가할 수 있습니다.

이 예에서는 `my-directory` directory 라는 디렉터리에 대 한 사용자 정의 메타 데이터를 추가 합니다.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

이 예제에서는 이름이 `my-directory`디렉터리에 대 한 모든 사용자 정의 메타 데이터를 표시 합니다.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>참고자료

* [샘플](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 to Gen2 mapping](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [사용자 의견 제공](https://github.com/Azure/azure-cli-extensions/issues)
* [알려진 기능 간격](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [소스 코드](https://github.com/Azure/azure-cli-extensions/tree/master/src)

