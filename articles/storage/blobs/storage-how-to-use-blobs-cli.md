---
title: "Azure CLI를 사용하여 Azure Blob Storage(개체 저장소)에서 작업 수행 | Microsoft Docs"
description: "Azure Blob Storage에서 Blob을 업로드 및 다운로드하는 방법과, 저장소 계정에서 Blob에 대한 액세스를 관리하기 위해 SAS(공유 액세스 서명)를 생성하는 방법을 알아봅니다."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: tamram
ms.openlocfilehash: d47d85af7412def342437aedf35c3d129662451d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Azure CLI를 사용하여 Blob Storage 작업 수행

Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있는 다량의 구조화되지 않은 개체 데이터(예: 텍스트 또는 이진 데이터)를 저장할 수 있는 서비스입니다. 이 자습서에서는 Blob 업로드, 다운로드 및 삭제와 같은 Azure Blob Storage의 기본 작업에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 컨테이너 만들기
> * 컨테이너에 파일(Blob) 업로드
> * 컨테이너의 Blob 나열
> * 컨테이너에서 Blob 다운로드
> * 저장소 계정 간에 Blob 복사
> * Blob 삭제
> * Blob 속성 및 메타데이터 표시 및 수정
> * SAS(공유 액세스 서명)로 보안 관리

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 마치 컴퓨터의 디렉터리와 같습니다. 즉, 디렉터리에 파일을 정리하는 것과 마찬가지로 컨테이너에 Blob 그룹을 구성할 수 있습니다. 저장소 계정의 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 최대 500TB(저장소 계정에 허용된 최대 데이터 크기)의 Blob 데이터를 저장할 수 있습니다.

[az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령으로 Blob을 저장하기 위한 컨테이너를 만듭니다.

```azurecli-interactive
az storage container create --name mystoragecontainer
```

컨테이너 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 하이픈 문자(-)만 포함할 수 있습니다. Blob 및 컨테이너의 이름을 지정하는 방법에 대한 자세한 규칙은 [컨테이너, Blob, 메타데이터 이름 명명 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

## <a name="enable-public-read-access-for-a-container"></a>컨테이너에 대한 공용 읽기 액세스 설정

새로 만든 컨테이너는 개인(비공개)으로 기본 설정됩니다. 즉, [공유 액세스 서명](#create-a-shared-access-signature-sas)이나 저장소 계정의 액세스 키 없이는 아무도 컨테이너에 액세스할 수 없습니다. 이러한 동작을 수정하려면 Azure CLI를 사용하여 컨테이너 권한을 다음 세 가지 수준 중 하나로 설정할 수 있습니다.

| | |
|---|---|
| `--public-access off` | (기본값) 공용 읽기 액세스 없음 |
| `--public-access blob` | Blob에 대한 공용 읽기 액세스만 |
| `--public-access container` | Blob에 대한 공용 읽기 액세스, 컨테이너에 Blob을 나열할 수 있음 |

공용 액세스를 `blob` 또는 `container`로 설정하면 인터넷의 모든 사용자에 대해 읽기 전용 액세스가 설정됩니다. 예를 들어, 웹 사이트에 Blob으로 저장된 이미지를 표시하려면 공용 읽기 액세스를 설정해야 합니다. 읽기/쓰기 액세스를 사용하도록 설정하려면 대신 [SAS(공유 액세스 서명)](#create-a-shared-access-signature-sas)를 사용해야 합니다.

[az storage container set-permission](/cli/azure/storage/container#az_storage_container_create) 명령을 사용하여 컨테이너에 대한 공용 읽기 권한을 사용하도록 설정합니다.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob은 Azure Storage에 저장된 가장 일반적인 유형의 Blob입니다. 추가 Blob은 로깅과 같이 기존 내용을 수정하지 않고 기존 Blob에 데이터를 추가해야 할 때 사용됩니다. 페이지 Blob은 IaaS 가상 머신의 VHD 파일을 백업합니다.

이 예에서는 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 명령으로, 마지막 단계에서 만든 컨테이너에 Blob을 업로드합니다.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

이 작업은 Blob이 없는 경우 만들고, Blob이 있는 경우 덮어씁니다. 다음 단계에서 Blob을 나열하는 경우 여러 항목을 볼 수 있도록 여러 개 파일을 업로드합니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) 명령으로 컨테이너에 있는 Blob을 나열합니다.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

샘플 출력:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Blob 다운로드

[az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) 명령을 사용하여 이전 단계에서 업로드한 Blob을 다운로드합니다.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>저장소 계정 간에 Blob 복사

저장소 계정 및 지역 내 또는 전체에 걸쳐 비동기적으로 Blob을 복사할 수 있습니다.

다음 예제에서는 한 저장소 계정에서 다른 계정으로 Blob을 복사하는 방법을 보여줍니다. 먼저 해당 Blob에 대해 공용 읽기 액세스를 지정하여 원본 저장소 계정에 컨테이너를 만듭니다. 그런 다음 컨테이너에 파일을 업로드하고, 마지막으로 해당 컨테이너의 Blob을 대상 저장소 계정의 컨테이너에 복사합니다.

이 예제에서는 복사 작업이 성공하기 위해 대상 컨테이너가 대상 저장소 계정에 이미 있어야 합니다. 또한 `--source-uri` 인수에 지정된 원본 Blob은 SAS(공유 액세스 서명) 토큰을 포함하거나 이 예제에서처럼 공개적으로 액세스할 수 있어야 합니다.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Blob 삭제

[az storage blob delete](/cli/azure/storage/blob#az_storage_blob_delete) 명령을 사용하여 컨테이너에서 Blob을 삭제합니다.

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="set-the-content-type"></a>콘텐츠 형식 설정

콘텐츠 형식((MIME 형식이라고도 함))은 Blob의 데이터 형식을 식별합니다. 브라우저 및 기타 소프트웨어는 콘텐츠 형식을 사용하여 데이터를 처리할 방법을 결정합니다. 다음 예제는 콘텐츠 형식을 `image/png`로 설정합니다.

```azurecli-interactive
# Set the content type
az storage blob update
    --container-name mystoragecontainer 
    --name blobName 
    --content-type image/png
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Blob 속성 및 메타데이터 표시 및 수정

각 Blob에는 해당 이름, 형식, 길이 등을 포함하여 [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) 명령으로 표시할 수 있는 여러 개의 서비스 정의 속성이 있습니다. [az storage blob metadata update](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_update) 명령을 사용하여 사용자 고유의 속성 및 해당 값으로 Blob을 구성할 수도 있습니다.

이 예제에서는 먼저 Blob의 서비스 정의 속성을 표시한 후 사용자 고유의 메타데이터 속성 중 두 개로 Blob을 업데이트합니다. 마지막으로 [az storage blob metadata show](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_show) 명령을 사용하여 Blob의 메타데이터 속성과 해당 값을 표시합니다.

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>SAS(공유 액세스 서명) 만들기

SAS(공유 액세스 서명)를 사용하면 저장소 계정 액세스 키를 노출하지 않고 저장소 계정의 개체에 대한 제한된 액세스 권한을 부여할 수 있습니다. 개인 리소스에 대한 액세스를 허용하는 URI를 생성하려면 원하는 사용 권한 및 유효 기간이 있는 SAS 토큰을 만들고, 이를 리소스 URL에 쿼리 문자열로 추가하여 전체 SAS URI를 구성합니다. 그러면 누구나 이 SAS URI(리소스 URL 및 SAS 토큰)를 통해 SAS 토큰의 유효 기간 동안 액세스할 수 있습니다. 예를 들어 개인 Blob에 대한 읽기 권한을 2분 동안 허용하여 다른 사람이 볼 수 있도록 할 수 있습니다.

다음 단계에서는 컨테이너에 대한 공용 액세스를 사용하지 않도록 하고 개인 전용 액세스를 테스트한 후 SAS URI를 생성하여 테스트합니다.

### <a name="disable-container-public-access"></a>컨테이너에 대한 공용 액세스 설정 해제

먼저, 컨테이너의 액세스 수준을 `off`로 설정합니다. 이 수준은 공유 액세스 서명이나 저장소 계정 액세스 키 없이는 컨테이너 또는 Blob에 액세스할 수 없음을 나타냅니다.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>개인 액세스 확인

해당 컨테이너의 Blob에 대한 공용 읽기 권한이 없음을 확인하려면 [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url) 명령을 사용하여 해당 Blob 중 하나에 대한 URL을 가져옵니다.

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

개인 브라우저 창에서 Blob의 URL로 이동합니다. Blob이 개인(비공개) 설정되어 있고 공유 액세스 서명을 포함하지 않았으므로 `ResourceNotFound` 오류가 표시됩니다.

### <a name="create-a-sas-uri"></a>SAS URI 만들기

이제 Blob에 대한 액세스를 허용하는 SAS URI를 만들어 보겠습니다. 다음 예제에서는 먼저, [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url)로 Blob에 대한 URL로 변수를 채운 다음 [az storage blob generate-sas](/cli/azure/storage/blob#az_storage_blob_generate_sas) 명령으로 생성된 SAS 토큰으로 다른 변수를 채웁니다. 마지막으로 `?` 쿼리 문자열 구분 기호로 분리된 둘을 연결하여 Blob에 대한 전체 SAS URI를 출력합니다.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>SAS URI 테스트

개인 브라우저 창에서, 이전 코드 조각에서 `echo` 명령으로 표시한 전체 SAS URI로 이동합니다. 이때 오류가 표시되지 않으며 Blob을 보거나 다운로드할 수 있습니다.

URL이 만료될 때까지 충분히 기다린 후(이 예제의 경우 2분) 다른 개인 브라우저 창에서 URI로 이동합니다. 이제 SAS 토큰이 만료되었으므로 `AuthenticationFailed` 오류가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자가 만든 저장소 계정 및 이 자습서에서 업로드한 Blob을 비롯하여 리소스 그룹의 어떠한 리소스도 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령으로 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Storage에서 Blob으로 작업하는 기본 사항을 알아보았습니다.

> [!div class="checklist"]
> * 컨테이너 만들기
> * 컨테이너에 파일(Blob) 업로드
> * 컨테이너의 Blob 나열
> * 컨테이너에서 Blob 다운로드
> * 저장소 계정 간에 Blob 복사
> * Blob 삭제
> * Blob 속성 및 메타데이터 표시 및 수정
> * SAS(공유 액세스 서명)로 보안 관리

다음 리소스는 저장소 계정의 리소스 작업뿐만 아니라 Azure CLI로 작업하는 방법에 대한 추가 정보를 제공합니다.

* Azure CLI
  * [Azure CLI 2.0으로 로그인](/cli/azure/authenticate-azure-cli) - 무인 Azure CLI 스크립트를 실행하기 위해 [서비스 사용자](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal)를 통한 비대화식 로그인을 포함하여 CLI로 인증하는 다양한 방법에 대해 알아보세요.
  * [Azure CLI 2.0 명령 참조](/cli/azure/)
* Microsoft Azure Storage 탐색기
  * [Microsoft Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)는 Windows, MacOS 및 Linux에서 Azure Storage 데이터를 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
