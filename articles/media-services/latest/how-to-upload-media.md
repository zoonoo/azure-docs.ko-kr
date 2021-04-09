---
제목: 미디어 업로드: Azure Media Services 설명: 스트리밍 또는 인코딩에 대한 미디어를 업로드하는 방법을 알아봅니다.
services: media-services documentationcenter: '' author: IngridAtMicrosoft 관리자: femila editor: ''

ms.service: media-services ms.workload: ms.topic: how-to ms.date: 2020/08/31 ms.author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>스트리밍 또는 인코딩에 미디어 업로드

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services에서 자산에 연결된 Blob 컨테이너에 디지털 파일(미디어)을 업로드합니다. [자산](/rest/api/media/operations/asset) 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다. 자산의 컨테이너에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

시작하기 전에 몇 가지 값을 수집하거나 고려해야 합니다.

1. 업로드할 파일의 로컬 파일 경로
1. 자산의 자산 ID(컨테이너)
1. 확장자를 포함하여 업로드된 파일에 사용할 이름
1. 사용하는 스토리지 계정의 이름
1. 스토리지 계정의 액세스 키

## <a name="portal"></a>[포털](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

코드가 이미 인증을 설정했으며 입력 자산을 이미 만들었다고 가정하면 다음 코드 조각을 사용하여 해당 자산(in_container)에 로컬 파일을 업로드합니다.

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
다른 메서드에 대한 자세한 내용은 [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md) 및 [JavaScript(node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md)에서 Blob 관련 작업에 대한 [Azure Storage 설명서](../../storage/blobs/index.yml)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [Media Services 개요](media-services-overview.md)
