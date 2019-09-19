---
title: Python을 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. Python 스토리지 클라이언트 라이브러리를 사용하여 Blob, 큐, 테이블 및 파일에 대한 시작 샘플을 검색합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 5d823059842bf539f00ad372127fc8b636c74b65
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70745970"
---
# <a name="azure-storage-samples-using-python"></a>Python을 사용한 Azure Storage 샘플

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

## <a name="blob-samples"></a>Blob 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| Blob 추가 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| 블록 Blob | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| 클라이언트 쪽 암호화 | [Azure Key Vault에서 Python을 사용하여 스토리지 계정 키 관리](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Blob 복사 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| 컨테이너 만들기 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Blob 삭제 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| 컨테이너 삭제 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| BLOB 메타데이터/속성/통계 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| 컨테이너 ACL/메타데이터/속성 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| 페이지 범위 가져오기 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| BLOB/컨테이너 임대 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| BLOB/컨테이너 나열 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| 페이지 Blob | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Python의 공유 액세스 서명](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| 서비스 속성 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Blob 스냅샷 | [Python에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples"></a>파일 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 공유/디렉터리/파일 만들기 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| 공유/디렉터리/파일 삭제 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| 디렉터리 속성/메타데이터 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| 파일 다운로드 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| 파일 속성/메타데이터/메트릭 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| 파일 서비스 속성 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| 디렉터리 및 파일 나열 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| 공유 나열 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| 속성/메타데이터/통계 공유 | [Python에서 Azure 파일 서비스 시작](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples"></a>큐 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 메시지 추가 | [Python에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| 클라이언트 쪽 암호화 | [Azure Key Vault에서 Python을 사용하여 스토리지 계정 키 관리](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| 큐 만들기 | [Python에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| 메시지/큐 삭제 | [Python에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| 메시지 보기 | [Python에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| 큐 ACL/메타데이터/통계 | [Python에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| 큐 서비스 속성 | [Python에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| 메시지 업데이트 | [Python에서 Azure 큐 서비스 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples"></a>테이블 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 테이블 만들기 | [Python에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| 엔터티/테이블 삭제 | [Python에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| 엔터티 삽입/병합/바꾸기 | [Python에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| 엔터티 쿼리 | [Python에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| 쿼리 테이블 | [Python에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| 테이블 ACL/속성 | [Python에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| 엔터티 업데이트 | [Python에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Azure 코드 샘플 라이브러리

코드 샘플 라이브러리를 보려면 다운로드하여 로컬로 실행할 수 있는 Azure Storage에 대한 샘플이 포함되어 있는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=storage) 라이브러리로 이동합니다. 코드 샘플 라이브러리에서 .zip 형식으로 샘플 코드를 제공합니다. 또는 각 샘플에 대한 GitHub 리포지토리를 찾아 복제할 수 있습니다.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

* [Python에서 Azure Blob Service 시작](../blobs/storage-quickstart-blobs-python.md)
* [Python에서 Azure 큐 서비스 시작](../queues/storage-python-how-to-use-queue-storage.md)
* [Python에서 Azure Table Service 시작](../../cosmos-db/table-storage-how-to-use-python.md)
* [Python에서 Azure 파일 서비스 시작](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

* .NET: [.NET을 사용한 Azure Storage 샘플](storage-samples-dotnet.md)
* Java: [Java를 사용한 Azure Storage 샘플](storage-samples-java.md)
* 모든 기타 언어: [Azure Storage 샘플](storage-samples.md)
