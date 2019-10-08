---
title: JavaScript를 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. JavaScript/Node.js 스토리지 클라이언트 라이브러리를 사용하여 Blob, 큐, 테이블 및 파일에 대한 예제 시작을 검색합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 363739bbab67b92e78e0cdb56cf98cdb8e1446eb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350895"
---
# <a name="azure-storage-samples-using-javascript"></a>JavaScript를 사용한 Azure Storage 샘플

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

## <a name="blob-samples"></a>Blob 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 블록 Blob | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| 클라이언트 쪽 암호화 | [Azure Key Vault에서 JavaScript를 사용하여 스토리지 계정 키 관리](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| Blob 복사 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| 컨테이너 만들기 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| Blob 삭제 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| 컨테이너 삭제 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| Blob 메타데이터 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| Blob 속성 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| 컨테이너 ACL | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| 컨테이너 메타데이터 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| 컨테이너 속성 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| 페이지 범위 가져오기 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| Blob 임대 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| 컨테이너 임대 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| BLOB/컨테이너 나열 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| 페이지 Blob | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [JavaScript의 공유 액세스 서명](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| 서비스 속성 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| CORS 규칙 설정 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| Blob 스냅샷 | [JavaScript에서 Azure Blob Service 시작](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples"></a>파일 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 공유/디렉터리/파일 만들기 | [JavaScript에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| 공유/디렉터리/파일 삭제 | [JavaScript에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| 파일 다운로드 | [JavaScript에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| 디렉터리 및 파일 나열 | [JavaScript에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| 공유 나열 | [JavaScript에서 Azure File Service 시작](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples"></a>큐 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 메시지 추가 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| 클라이언트 쪽 암호화 | [Azure Key Vault에서 JavaScript를 사용하여 스토리지 계정 키 관리](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| 큐 만들기 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| 메시지 삭제 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| 큐 삭제 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| 큐 나열 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| 메시지 보기 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| 큐 ACL | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| 큐 CORS 규칙 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| 큐 메타데이터 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| 큐 서비스 속성 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| 큐 상태 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| 메시지 업데이트 | [JavaScript에서 Azure Queue Service 시작](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples"></a>테이블 샘플

| **시나리오** | **샘플 코드** |
|--------------|-----------------|
| 일괄 처리 엔터티 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| 테이블 만들기 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| 엔터티/테이블 삭제 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| 엔터티 삽입/병합/바꾸기 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| 테이블 나열 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| 엔터티 쿼리 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| 쿼리 테이블 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| 범위 쿼리 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [JavaScript의 공유 액세스 서명](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| 테이블 ACL | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| 테이블 CORS 규칙 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| 테이블 속성 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| 테이블 상태 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| 엔터티 업데이트 | [JavaScript에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Azure 코드 샘플 라이브러리

코드 샘플 라이브러리를 보려면 다운로드하여 로컬로 실행할 수 있는 Azure Storage에 대한 샘플이 포함되어 있는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=storage) 라이브러리로 이동합니다. 코드 샘플 라이브러리에서 .zip 형식으로 샘플 코드를 제공합니다. 또는 각 샘플에 대한 GitHub 리포지토리를 찾아 복제할 수 있습니다.

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

* [JavaScript에서 Azure Blob Service 시작](../blobs/storage-quickstart-blobs-nodejs.md)
* [JavaScript에서 Azure Queue Service 시작](../queues/storage-nodejs-how-to-use-queues.md)
* [JavaScript에서 Azure Table Service 시작](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

* .NET: [.NET을 사용한 Azure Storage 샘플](storage-samples-dotnet.md)
* Java: [Java를 사용한 Azure Storage 샘플](storage-samples-java.md)
* Python: [Python을 사용한 Azure Storage 샘플](storage-samples-python.md)
* 모든 기타 언어: [Azure Storage 샘플](storage-samples.md)
