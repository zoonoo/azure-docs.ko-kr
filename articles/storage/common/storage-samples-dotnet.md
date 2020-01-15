---
title: .NET을 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. .NET 스토리지 클라이언트 라이브러리를 사용하여 BLOB, 큐, 테이블 및 파일에 대한 예제 시작을 검색합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 043a21aec5753dd16f558ad29d2300ada7a35d72
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431767"
---
# <a name="azure-storage-samples-using-net"></a>.NET을 사용한 Azure Storage 샘플

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

> [!NOTE]
> 이러한 샘플은 Azure Storage .NET v11 라이브러리를 사용합니다. v12 코드는 GitHub 리포지토리의 [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)을 참조하세요.

## <a name="blob-samples"></a>Blob 샘플

| **시나리오** | **예제 코드** |
|--------------|-----------------|
| Blob 추가 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| 블록 Blob | [Azure Blob Storage 사진 갤러리 웹 애플리케이션](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| 클라이언트 쪽 암호화 | [BLOB 암호화 샘플](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| Blob 복사 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 컨테이너 만들기 | [Azure Blob Storage 사진 갤러리 웹 애플리케이션](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Blob 삭제 | [Azure Blob Storage 사진 갤러리 웹 애플리케이션](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| 컨테이너 삭제 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB 메타데이터/속성/통계 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 컨테이너 ACL/메타데이터/속성 | [Azure Blob Storage 사진 갤러리 웹 애플리케이션](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| 페이지 범위 가져오기 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB/컨테이너 임대 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB/컨테이너 나열 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| 페이지 Blob | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| 서비스 속성 | [BLOB 시작](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Blob 스냅샷 | [증분 스냅샷을 사용하여 Azure Virtual Machine 디스크 백업](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples"></a>파일 샘플

| **시나리오** | **예제 코드** |
|--------------|-----------------|
| 공유/디렉터리/파일 만들기 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 공유/디렉터리/파일 삭제 | [.NET에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| 디렉터리 속성/메타데이터 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 파일 다운로드 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 파일 속성/메타데이터/메트릭 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 파일 서비스 속성 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 디렉터리 및 파일 나열 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 공유 나열 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 속성/메타데이터/통계 공유 | [Azure Storage .NET File Storage 샘플](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>큐 샘플

| **시나리오** | **예제 코드** |
|--------------|-----------------|
| 메시지 추가 | [.NET에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 클라이언트 쪽 암호화 | [Azure Storage .NET 큐 클라이언트 쪽 암호화](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| 큐 만들기 | [.NET에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 메시지/큐 삭제 | [.NET에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 메시지 보기 | [.NET에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| 큐 ACL/메타데이터/통계 | [.NET에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| 큐 서비스 속성 | [.NET에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| 메시지 업데이트 | [.NET에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples"></a>테이블 샘플

| **시나리오** | **예제 코드** |
|--------------|-----------------|
| 테이블 만들기 | [Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| 엔터티/테이블 삭제 | [.NET에서 Azure Table Storage 시작](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 엔터티 삽입/병합/바꾸기 | [Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| 엔터티 쿼리 | [.NET에서 Azure Table Storage 시작](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 쿼리 테이블 | [.NET에서 Azure Table Storage 시작](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| 테이블 ACL/속성 | [.NET에서 Azure Table Storage 시작](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| 엔터티 업데이트 | [Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Azure 코드 샘플 라이브러리

코드 샘플 라이브러리를 보려면 다운로드하여 로컬로 실행할 수 있는 Azure Storage에 대한 샘플이 포함되어 있는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=storage) 라이브러리로 이동합니다. 코드 샘플 라이브러리에서 .zip 형식으로 샘플 코드를 제공합니다. 또는 각 샘플에 대한 GitHub 리포지토리를 찾아 복제할 수 있습니다.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

* [.NET에서 Azure Blob 서비스 시작](../blobs/storage-dotnet-how-to-use-blobs.md)
* [.NET에서 Azure 큐 서비스 시작](../storage-dotnet-how-to-use-queues.md)
* [.NET에서 Azure 테이블 서비스 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET에서 Azure 파일 서비스 시작](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

* Java: [Java를 사용한 Azure Storage 샘플](storage-samples-java.md)
* JavaScript/node.js: [JavaScript를 사용한 Azure Storage 샘플](storage-samples-javascript.md)
* Python: [Python을 사용한 Azure Storage 샘플](storage-samples-python.md)
* 모든 기타 언어: [Azure Storage 샘플](../storage-samples.md)
