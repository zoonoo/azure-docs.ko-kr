---
title: Azure 데이터 레이크 저장소 Gen2의 ACL & 파일에 Azure CLI 사용(미리 보기)
description: Azure CLI를 사용하여 계층적 네임스페이스가 있는 저장소 계정에서 디렉터리 및 파일 및 디렉터리 액세스 제어 목록(ACL)을 관리합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486137"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Azure CLI를 사용하여 Azure 데이터 레이크 저장소 Gen2의 디렉터리, 파일 및 ACL을 관리합니다(미리 보기)

이 문서에서는 [AZURE 명령줄 인터페이스(CLI)를](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 사용하여 계층적 네임스페이스가 있는 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> 이 `storage-preview` 문서에 소개된 확장 프로그램은 현재 공개 미리 보기상태입니다.

[샘플](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1에서 Gen2 매핑에](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [피드백 제공](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임스페이스(HNS)가 활성화된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 만들 수 있습니다.
> * Azure CLI `2.0.67` 버전 이상입니다.

## <a name="install-the-storage-cli-extension"></a>스토리지 CLI 확장 설치

1. Azure [클라우드 셸을](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)열거나 Azure CLI를 로컬로 [설치한](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 경우 Windows PowerShell과 같은 명령 콘솔 응용 프로그램을 엽니다.

2. 다음 명령을 사용하여 설치한 Azure CLI `2.0.67` 버전이 더 높은지 확인합니다.

   ```azurecli
    az --version
   ```
   Azure CLI 버전이 `2.0.67`보다 낮은 경우 이후 버전을 설치합니다. [Azure CLI 설치를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)참조하십시오.

3. `storage-preview` 확장을 설치합니다.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>계정에 연결

1. Azure CLI를 로컬로 사용하는 경우 로그인 명령을 실행합니다.

   ```azurecli
   az login
   ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면 브라우저 페이지를 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 열고 터미널에 표시된 권한 부여 코드를 입력합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인합니다.

   다른 인증 방법에 대한 자세한 내용은 Azure CLI로 로그인을 참조하세요.

2. ID가 두 개 이상의 구독과 연결된 경우 정적 웹 사이트를 호스팅할 저장소 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독ID로 바꿉습니다.

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. `az storage container create` 명령을 사용하여 만들 수 있습니다. 

이 예제는 .라는 `my-file-system`파일 시스템을 만듭니다.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>디렉터리 만들기

명령을 사용하여 디렉터리 참조를 `az storage blob directory create` 만듭니다. 

이 예제에서는 `my-directory` `my-file-system` `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>디렉터리 속성 표시

`az storage blob show` 명령을 사용하여 디렉터리 속성을 콘솔에 인쇄할 수 있습니다.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

`az storage blob directory move` 명령을 사용하여 디렉터리 이름을 바꾸거나 이동합니다.

이 예제는 디렉터리 이름을 `my-directory` 이름에서 `my-new-directory`이름으로 바꿉니다.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>디렉터리 삭제

명령을 사용하여 디렉토리를 삭제합니다. `az storage blob directory delete`

이 예제는 .라는 `my-directory`디렉터리를 삭제합니다. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>디렉터리가 있는지 확인

`az storage blob directory exist` 명령을 사용하여 파일 시스템에 특정 디렉터리가 있는지 확인합니다.

이 예제에서는 명명된 `my-directory` 디렉터리파일 `my-file-system` 시스템에 있는지 여부를 보여 줍니다. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>디렉토리에서 다운로드

명령을 사용하여 디렉토리에서 파일을 `az storage blob directory download` 다운로드합니다.

이 예제는 `upload.txt` `my-directory`에서 명명된 디렉터리에서 명명된 파일을 다운로드합니다. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

이 예제는 전체 디렉터리를 다운로드합니다.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

`az storage blob directory list` 명령을 사용하여 디렉터리 내용을 나열합니다.

이 예제에서는 `my-directory` `my-file-system` `mystorageaccount`라는 저장소 계정의 파일 시스템에 있는 라는 디렉터리의 내용을 나열 합니다. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>디렉토리에 파일 업로드

명령을 사용하여 디렉토리에 파일을 `az storage blob directory upload` 업로드합니다.

이 예제는 `upload.txt` `my-directory`을 라는 디렉터리로 파일을 업로드합니다. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

이 예제는 전체 디렉터리를 업로드합니다.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>파일 속성 표시

`az storage blob show` 명령을 사용하여 파일의 속성을 콘솔에 인쇄할 수 있습니다.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>파일 이름 바꾸기 또는 이동

명령을 사용하여 파일의 이름을 `az storage blob move` 바꾸거나 이동합니다.

이 예제는 파일의 이름을 `my-file.txt` name에서 `my-file-renamed.txt`name.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>파일 삭제

명령을 사용하여 파일을 `az storage blob delete` 삭제합니다.

이 예제는 명명된 파일을 삭제합니다.`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>사용 권한 관리

디렉터리 및 파일의 액세스 권한을 얻, 설정 및 업데이트할 수 있습니다.

> [!NOTE]
> Azure Active Directory(Azure AD)를 사용하여 명령을 승인하는 경우 보안 주체가 저장소 [Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

### <a name="get-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 받기

명령을 사용하여 **디렉터리ACL을** `az storage blob directory access show` 가져옵니다.

이 예제에서는 디렉터리의 ACL을 가져옵니다.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

명령을 사용하여 파일의 액세스 권한을 가져옵니다. **file** `az storage blob access show` 

이 예제는 파일의 ACL을 가져옵니다 하 고 콘솔에 ACL을 인쇄 합니다.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

다음 이미지는 디렉터리ACL을 받은 후의 출력을 보여 주며,

![ACL 출력 받기](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

이 예제에서는 소유 사용자에게 사용 권한을 읽고, 쓰고, 실행합니다. 소유 그룹에는 읽기 및 실행 권한만 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](data-lake-storage-access-control.md)참조하십시오.

### <a name="set-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 설정

`az storage blob directory access set` 명령을 사용하여 **디렉터리**의 ACL을 설정합니다. 

이 예제에서는 소유 사용자, 소유 그룹 또는 다른 사용자에 대한 디렉터리의 ACL을 설정하고 콘솔에 ACL을 인쇄합니다.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

이 예제에서는 소유 사용자, 소유 그룹 또는 다른 사용자에 대한 디렉터리의 *기본* ACL을 설정하고 콘솔에 ACL을 인쇄합니다.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

`az storage blob access set` 명령을 사용하여 **파일의**acl을 설정합니다. 

이 예제에서는 소유 사용자, 소유 그룹 또는 다른 사용자에 대한 파일의 ACL을 설정하고 콘솔에 ACL을 인쇄합니다.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
다음 이미지는 파일의 ACL을 설정한 후의 출력을 보여 주며,

![ACL 출력 받기](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

이 예제에서는 소유 사용자 및 소유 그룹에는 읽기 및 쓰기 권한만 있습니다. 다른 모든 사용자에게는 쓰기 및 실행 권한이 있습니다. 액세스 제어 목록에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](data-lake-storage-access-control.md)참조하십시오.

### <a name="update-directory-and-file-permissions"></a>디렉터리 및 파일 사용 권한 업데이트

이 권한을 설정하는 또 다른 `az storage blob directory access update` 방법은 `az storage blob access update` 또는 명령을 사용하는 것입니다. 

`-permissions` 매개 변수를 ACL의 짧은 형식으로 설정하여 디렉터리 또는 파일의 ACL을 업데이트합니다.

이 예제는 **디렉터리**의 ACL을 업데이트합니다.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

이 예제는 **파일의**ACL을 업데이트합니다.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

사용자의 엔터티 ID 또는 사용자 주체 이름(UPN)에 `--owner` `group` 또는 매개 변수를 설정하여 디렉터리 또는 파일의 소유 사용자 및 그룹을 업데이트할 수도 있습니다. 

이 예제는 디렉터리 의 소유자를 변경합니다. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

이 예제는 파일의 소유자를 변경합니다. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>사용자 정의 메타데이터 관리

하나 이상의 이름-값 쌍이 있는 명령을 사용하여 `az storage blob directory metadata update` 사용자 정의 메타데이터를 파일 또는 디렉터리에 추가할 수 있습니다.

이 예제는 명명된 `my-directory` 디렉터리에 대해 사용자 정의 메타데이터를 추가합니다.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

이 예제에서는 명명된 `my-directory`디렉터리에 대한 모든 사용자 정의 메타데이터를 보여 주며 있습니다.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>참조

* [예제](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen 1 ~ Gen2 매핑](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [사용자 의견 제공](https://github.com/Azure/azure-cli-extensions/issues)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [소스 코드](https://github.com/Azure/azure-cli-extensions/tree/master/src)

