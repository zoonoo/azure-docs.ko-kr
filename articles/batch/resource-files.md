---
title: 리소스 파일 만들기 및 사용 - Azure 일괄 처리
description: 다양한 입력 소스에서 Batch 리소스 파일을 만드는 방법에 대해 알아봅니다. 이 문서에서는 VM을 만들고 배치하는 방법에 대한 몇 가지 일반적인 방법을 설명합니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531144"
---
# <a name="creating-and-using-resource-files"></a>리소스 파일 만들기 및 사용

Azure Batch 작업은 처리하기 위해 일종의 데이터가 필요한 경우가 많습니다. 리소스 파일은 작업을 통해 이 데이터를 VM(Batch 가상 시스템)에 제공하는 방법입니다. 모든 유형의 작업은 리소스 파일을 지원합니다: 작업, 시작 작업, 작업 준비 작업, 작업 릴리스 작업 등 이 문서에서는 리소스 파일을 만들고 VM에 배치하는 방법에 대한 몇 가지 일반적인 방법을 설명합니다.  

리소스 파일은 Batch의 VM에 데이터를 배치하지만 데이터 유형과 사용 방식은 유연합니다. 그러나 다음과 같은 몇 가지 일반적인 사용 사례가 있습니다.

1. 시작 작업의 리소스 파일을 사용하여 각 VM에서 공통 파일 프로비전
1. 작업에서 처리할 입력 데이터 프로비전

예를 들어, 작업이 실행되는 응용 프로그램을 설치하는 데 사용되는 시작 작업의 파일일 수 있습니다. 입력 데이터는 원시 이미지 또는 비디오 데이터 또는 Batch에서 처리할 모든 정보일 수 있습니다.

## <a name="types-of-resource-files"></a>리소스 파일 유형

리소스 파일을 생성하는 데 사용할 수 있는 몇 가지 다른 옵션이 있습니다. 리소스 파일 의 생성 프로세스는 원본 데이터가 저장되는 위치에 따라 다릅니다.

리소스 파일을 만들기 위한 옵션:

- [저장소 컨테이너 URL:](#storage-container-url)Azure의 모든 저장소 컨테이너에서 리소스 파일을 생성합니다.
- [저장소 컨테이너 이름](#storage-container-name): Batch에 연결된 Azure 저장소 계정의 컨테이너 이름에서 리소스 파일을 생성합니다.
- [웹 끝점](#web-endpoint): 유효한 HTTP URL에서 리소스 파일을 생성합니다.

### <a name="storage-container-url"></a>저장소 컨테이너 URL

저장소 컨테이너 URL을 사용하면 올바른 사용 권한을 사용하여 Azure의 모든 저장소 컨테이너에서 파일에 액세스할 수 있습니다. 

이 C# 예제에서는 파일이 이미 Azure 저장소 컨테이너에 Blob 저장소로 업로드되었습니다. 리소스 파일을 만드는 데 필요한 데이터에 액세스하려면 먼저 저장소 컨테이너에 액세스해야 합니다.

저장소 컨테이너에 액세스할 수 있는 올바른 권한을 사용하여 공유 액세스 서명(SAS) URI를 만듭니다. SAS에 대한 만료 시간 및 사용 권한을 설정합니다. 이 경우 시작 시간이 지정되지 않으므로 SAS가 즉시 유효해지고 생성된 후 2시간 후에 만료됩니다.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 컨테이너 액세스의 경우 둘 `Read` `List` 다와 사용 권한이 있어야 하지만 Blob 액세스의 경우 권한만 있으면 됩니다. `Read`

사용 권한이 구성되면 SAS 토큰을 만들고 저장소 컨테이너에 대한 액세스를 위해 SAS URL의 서식을 지정합니다. 저장소 컨테이너에 대해 서식이 지정된 SAS URL을 [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)사용하여 을 사용하여 리소스 파일을 생성합니다.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

SAS URL을 생성하는 대안은 Azure Blob 저장소에서 컨테이너 및 해당 Blob에 대한 익명의 공용 읽기 액세스를 활성화하는 것입니다. 이렇게 하면 계정 키를 공유하지 않고 SAS를 요구하지 않고도 이러한 리소스에 대한 읽기 전용 액세스 권한을 부여할 수 있습니다. 공용 읽기 액세스는 일반적으로 익명 읽기 액세스에 대해 특정 Blob을 항상 사용할 수 있도록 하려는 시나리오에 사용됩니다. 이 시나리오가 솔루션에 적합한 경우 Blob 데이터에 대한 액세스 관리에 대한 자세한 내용은 [Blob에](../storage/blobs/storage-manage-access-to-resources.md) 대한 익명 액세스를 참조하세요.

### <a name="storage-container-name"></a>저장소 컨테이너 이름

SAS URL을 구성하고 만드는 대신 Azure 저장소 컨테이너의 이름을 사용하여 Blob 데이터에 액세스할 수 있습니다. 사용하는 저장소 컨테이너는 Batch 계정에 연결된 Azure 저장소 계정에 있어야 합니다. 해당 저장소 계정을 자동 저장소 계정이라고 합니다. 자동 저장 컨테이너를 사용하면 구성을 우회하여 SAS URL을 만들어 저장소 컨테이너에 액세스할 수 있습니다.

이 예제에서는 리소스 파일 생성에 사용할 데이터가 Batch 계정에 연결된 Azure Storage 계정에 이미 있다고 가정합니다. 자동 저장 계정이 없는 경우 계정을 만들고 연결하는 방법에 대한 자세한 내용은 [일괄 처리 계정 만들기의](batch-account-create-portal.md) 단계를 참조하세요.

연결된 저장소 계정을 사용하면 저장소 컨테이너에 SAS URL을 만들고 구성할 필요가 없습니다. 대신 연결된 저장소 계정에 저장소 컨테이너의 이름을 입력합니다.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>웹 끝점

Azure Storage에 업로드되지 않은 데이터는 여전히 리소스 파일을 만드는 데 사용할 수 있습니다. 입력 데이터가 포함된 유효한 HTTP URL을 지정할 수 있습니다. URL은 Batch API에 제공된 다음 데이터가 리소스 파일을 만드는 데 사용됩니다.

다음 C# 예제에서 입력 데이터는 가상GitHub 끝점에서 호스팅됩니다. API는 유효한 웹 끝점에서 파일을 검색하고 작업에서 사용할 리소스 파일을 생성합니다. 이 시나리오에는 자격 증명이 필요하지 않습니다.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

각 Azure Batch 작업은 파일을 다르게 사용하므로 Batch에는 작업에서 파일을 관리하는 데 사용할 수 있는 옵션이 있습니다. 다음 시나리오는 포괄적인 것이 아니라 몇 가지 일반적인 상황을 다루고 권장 사항을 제공합니다.

### <a name="many-resource-files"></a>많은 리소스 파일

Batch 작업에는 모두 동일한 공통 파일을 사용하는 여러 작업이 포함될 수 있습니다. 일반적인 작업 파일이 여러 작업 간에 공유되는 경우 리소스 파일을 사용하는 대신 응용 프로그램 패키지를 사용하여 파일을 포함하는 것이 더 나은 옵션이 될 수 있습니다. 응용 프로그램 패키지는 다운로드 속도에 대한 최적화를 제공합니다. 또한 응용 프로그램 패키지의 데이터는 작업 간에 캐시되므로 작업 파일이 자주 변경되지 않는 경우 응용 프로그램 패키지가 솔루션에 적합할 수 있습니다. 응용 프로그램 패키지를 사용하면 여러 리소스 파일을 수동으로 관리하거나 Azure Storage의 파일에 액세스하기 위해 SAS URL을 생성할 필요가 없습니다. 일괄 처리는 Azure Storage를 사용하여 노드를 계산하기 위해 응용 프로그램 패키지를 저장하고 배포하는 백그라운드에서 작동합니다.

각 작업에 고유한 파일이 많은 경우 고유한 파일을 사용하는 작업을 업데이트하거나 교체해야 하는 경우가 많으며 응용 프로그램 패키지 콘텐츠로는 쉽게 수행할 수 없기 때문에 리소스 파일이 가장 좋은 옵션입니다. 리소스 파일은 개별 파일을 업데이트, 추가 또는 편집할 수 있는 추가적인 유연성을 제공합니다.

### <a name="number-of-resource-files-per-task"></a>작업당 리소스 파일 수

작업에 지정된 수백 개의 리소스 파일이 있는 경우 Batch는 작업을 너무 큰 것으로 거부할 수 있습니다. 작업 자체의 리소스 파일 수를 최소화하여 작업을 작게 유지하는 것이 가장 좋습니다.

작업에 필요한 파일 수를 최소화할 방법이 없는 경우 리소스 파일의 저장소 컨테이너를 참조하는 단일 리소스 파일을 만들어 작업을 최적화할 수 있습니다. 이렇게 하려면 리소스 파일을 Azure Storage 컨테이너에 넣고 리소스 파일에 대해 다른 "컨테이너" [메서드를](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) 사용합니다. Blob 접두사 옵션을 사용하여 작업에 다운로드할 파일 컬렉션을 지정합니다.

## <a name="next-steps"></a>다음 단계

- 리소스 파일의 대안으로 [응용 프로그램 패키지에](batch-application-packages.md) 대해 알아봅니다.
- 리소스 파일에 컨테이너를 사용하는 것에 대한 자세한 내용은 [컨테이너 워크로드를](batch-docker-container-workloads.md)참조하십시오.
- 작업에서 출력 데이터를 수집하고 저장하는 방법을 알아보려면 [작업 및 작업 출력 유지](batch-task-output.md)를 참조하십시오.
- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
