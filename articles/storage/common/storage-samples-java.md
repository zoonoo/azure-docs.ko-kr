---
title: Java를 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. Java 스토리지 클라이언트 라이브러리를 사용하여 BLOB, 큐, 테이블 및 파일에 대한 예제 시작을 검색합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: b295c5a945f41059ca98e96d742bc5e1f6b4bf9f
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744800"
---
# <a name="azure-storage-samples-using-java"></a>Java를 사용한 Azure Storage 샘플

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

## <a name="blob-samples"></a>Blob 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| Blob 추가 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 블록 Blob | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 클라이언트 쪽 암호화 | [Java에서 Azure 클라이언트 쪽 암호화 시작](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| Blob 복사 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 컨테이너 만들기 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Blob 삭제 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 컨테이너 삭제 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB 메타데이터/속성/통계 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 컨테이너 ACL/메타데이터/속성 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| 페이지 범위 가져오기 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| BLOB/컨테이너 임대 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB/컨테이너 나열 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| 페이지 Blob | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [SAS 테스트 샘플](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| 서비스 속성 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Blob 스냅샷 | [Java에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples"></a>파일 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 공유/디렉터리/파일 만들기 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 공유/디렉터리/파일 삭제 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 디렉터리 속성/메타데이터 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 파일 다운로드 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 파일 속성/메타데이터/메트릭 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 파일 서비스 속성 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| 디렉터리 및 파일 나열 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 공유 나열 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 속성/메타데이터/통계 공유 | [Java에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples"></a>큐 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 메시지 추가 | [Java에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| 클라이언트 쪽 암호화 | [Java에서 Azure 클라이언트 쪽 암호화 시작](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| 큐 만들기 | [Java에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 메시지/큐 삭제 | [Java에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 메시지 보기 | [Java에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| 큐 ACL/메타데이터/통계 | [Java에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| 큐 서비스 속성 | [Java에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| 메시지 업데이트 | [Java에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples"></a>테이블 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 테이블 만들기 | [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 엔터티/테이블 삭제 | [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 엔터티 삽입/병합/바꾸기 | [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 엔터티 쿼리 | [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 쿼리 테이블 | [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| 테이블 ACL/속성 | [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| 엔터티 업데이트 | [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Azure 코드 샘플 라이브러리

코드 샘플 라이브러리를 보려면 다운로드하여 로컬로 실행할 수 있는 Azure Storage에 대한 샘플이 포함되어 있는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=storage) 라이브러리로 이동합니다. 코드 샘플 라이브러리에서 .zip 형식으로 샘플 코드를 제공합니다. 또는 각 샘플에 대한 GitHub 리포지토리를 찾아 복제할 수 있습니다.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

* [Java에서 Azure Blob Service 시작](../blobs/storage-quickstart-blobs-java.md)
* [Java에서 Azure Queue Service 시작](../queues/storage-java-how-to-use-queue-storage.md)
* [Java에서 Azure Table Service 시작](../../cosmos-db/table-storage-how-to-use-java.md)
* [Java에서 Azure File Service 시작](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

* .NET: [.NET을 사용한 Azure Storage 샘플](storage-samples-dotnet.md)
* Python: [Python을 사용한 Azure Storage 샘플](storage-samples-python.md)
* 모든 기타 언어: [Azure Storage 샘플](storage-samples.md)
