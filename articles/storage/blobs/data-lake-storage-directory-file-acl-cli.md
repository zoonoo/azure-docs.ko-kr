---
title: Azure CLI를 사용 하 여 데이터 관리 (Azure Data Lake Storage Gen2)
description: 계층 구조가 있는 저장소 계정의 디렉터리와 파일을 관리 하려면 Azure CLI을 사용 합니다.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650357"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure CLI를 사용 하 여 Azure Data Lake Storage Gen2의 디렉터리 및 파일 관리

이 문서에서는 [CLI (Azure Command-Line Interface)](/cli/azure/) 를 사용 하 여 계층적 네임 스페이스를 포함 하는 저장소 계정의 디렉터리와 파일을 만들고 관리 하는 방법을 보여 줍니다.

디렉터리 및 파일의 ACL (액세스 제어 목록)을 가져오거나 설정 하 고 업데이트 하는 방법에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 Azure Data Lake Storage Gen2 acl 관리](data-lake-storage-acl-cli.md)를 참조 하세요.

[샘플](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [사용자 의견 제공](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 계층적 네임 스페이스를 사용 하는 저장소 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>올바른 버전의 Azure CLI를 설치했는지 확인

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. 다음 명령을 사용하여 설치된 Azure CLI 버전이 `2.6.0` 이상인지 확인합니다.

   ```azurecli
    az --version
   ```

   Azure CLI 버전이 `2.6.0`보다 낮은 경우 이후 버전을 설치합니다. [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="connect-to-the-account"></a>계정에 연결

1. Azure CLI를 로컬로 사용하는 경우 로그인 명령을 실행합니다.

   ```azurecli
   az login
   ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인합니다.

   다른 인증 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여](./authorize-data-operations-cli.md)를 참조하세요.

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

> [!NOTE]
> 이 문서에 제공 된 예제는 Azure Active Directory (Azure AD) 권한 부여를 표시 합니다. 권한 부여 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여I](./authorize-data-operations-cli.md)를 참조하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일에 대 한 파일 시스템 역할을 합니다. `az storage fs create` 명령을 사용하여 만들 수 있습니다. 

이 예제에서는 라는 컨테이너를 만듭니다 `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>컨테이너 속성 표시

명령을 사용 하 여 컨테이너의 속성을 콘솔에 인쇄할 수 있습니다 `az storage fs show` .

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>컨테이너 내용 나열

`az storage fs file list` 명령을 사용하여 디렉터리의 콘텐츠를 나열합니다.

이 예제에서는 라는 컨테이너의 내용을 나열 `my-file-system` 합니다.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>컨테이너 삭제

명령을 사용 하 여 컨테이너를 삭제 `az storage fs delete` 합니다.

이 예제에서는 라는 컨테이너를 삭제 `my-file-system` 합니다. 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>디렉터리 만들기

`az storage fs directory create` 명령을 사용하여 디렉터리 참조를 만듭니다. 

이 예제에서는 라는 계정에 있는 라는 컨테이너에 라는 디렉터리를 추가 `my-directory` `my-file-system` `mystorageaccount` 합니다.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>디렉터리 속성 표시

`az storage fs directory show` 명령을 사용하여 콘솔에 디렉터리의 속성을 출력할 수 있습니다.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

`az storage fs directory move` 명령을 사용하여 디렉터리의 이름을 바꾸거나 디렉터리를 이동합니다.

이 예에서는 이름에서 `my-directory` 동일한 컨테이너의 이름으로 디렉터리의 이름을 바꿉니다 `my-new-directory` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

이 예제에서는 디렉터리를 라는 컨테이너로 이동 `my-second-file-system` 합니다.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>디렉터리 삭제

`az storage fs directory delete` 명령을 사용하여 디렉터리를 삭제합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>디렉터리가 존재하는지 확인

명령을 사용 하 여 컨테이너에 특정 디렉터리가 있는지 확인 `az storage fs directory exists` 합니다.

이 예제에서는 라는 디렉터리가 컨테이너에 있는지 여부를 나타냅니다 `my-directory` `my-file-system` . 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

`az storage fs file download` 명령을 사용하여 디렉터리에서 파일을 다운로드합니다.

다음 예제에서는 `my-directory`라는 디렉터리에서 `upload.txt`라는 파일을 다운로드합니다. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

`az storage fs file list` 명령을 사용하여 디렉터리의 콘텐츠를 나열합니다.

이 예제에서는 `my-directory` `my-file-system` 라는 저장소 계정의 컨테이너에 있는 라는 디렉터리의 콘텐츠를 나열 합니다 `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

`az storage fs directory upload` 명령을 사용하여 디렉터리에 파일을 업로드합니다.

다음 예제에서는 `upload.txt`라는 파일을 `my-directory`라는 디렉터리에 업로드합니다. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>파일 속성 표시

`az storage fs file show` 명령을 사용하여 콘솔에 파일의 속성을 출력할 수 있습니다.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>파일 이름 바꾸기 또는 이동

`az storage fs file move` 명령을 사용하여 파일의 이름을 바꾸거나 파일을 이동합니다.

다음 예제에서는 파일의 이름을 `my-file.txt`에서 `my-file-renamed.txt`로 바꿉니다.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>파일 삭제

`az storage fs file delete` 명령을 사용하여 파일을 삭제합니다.

다음 예제에서는 `my-file.txt`라는 파일을 삭제합니다.

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>참고 항목

- [샘플](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [사용자 의견 제공](https://github.com/Azure/azure-cli-extensions/issues)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure CLI를 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리](data-lake-storage-acl-cli.md)