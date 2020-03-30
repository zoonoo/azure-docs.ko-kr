---
title: Blob 저장소에 대한 요청에 암호화 키 제공
titleSuffix: Azure Storage
description: Azure Blob 저장소에 대해 요청하는 클라이언트는 요청별로 암호화 키를 제공할 수 있습니다(미리 보기). 요청에 암호화 키를 포함하면 Blob 저장소 작업에 대한 암호화 설정을 세부적으로 제어할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410065"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Blob 저장소에 대한 요청에 암호화 키 제공(미리 보기)

Azure Blob 저장소에 대해 요청하는 클라이언트는 요청별로 암호화 키를 제공할 수 있습니다(미리 보기). 요청에 암호화 키를 포함하면 Blob 저장소 작업에 대한 암호화 설정을 세부적으로 제어할 수 있습니다. 고객이 제공한 키는 Azure 키 볼트 또는 다른 키 저장소에 저장할 수 있습니다.

## <a name="encrypting-read-and-write-operations"></a>읽기 및 쓰기 작업 암호화

클라이언트 응용 프로그램이 요청에 대한 암호화 키를 제공하는 경우 Azure Storage는 Blob 데이터를 읽고 쓰는 동안 암호화 및 암호 해독을 투명하게 수행합니다. Azure Storage는 Blob의 내용과 함께 암호화 키의 SHA-256 해시를 작성합니다. 해시는 Blob에 대한 모든 후속 작업이 동일한 암호화 키를 사용하는지 확인하는 데 사용됩니다.

Azure Storage는 클라이언트가 요청과 함께 보내는 암호화 키를 저장하거나 관리하지 않습니다. 암호화 또는 암호 해독 프로세스가 완료되는 즉시 키를 안전하게 삭제합니다.

클라이언트가 요청에서 고객이 제공한 키를 사용하여 Blob을 만들거나 업데이트하는 경우 해당 Blob에 대한 후속 읽기 및 쓰기 요청도 키를 제공해야 합니다. 고객이 제공한 키로 이미 암호화된 Blob에 대한 요청에 키가 제공되지 않으면 오류 코드 409(충돌)로 요청이 실패합니다.

클라이언트 응용 프로그램이 요청에 대한 암호화 키를 보내고 저장소 계정도 Microsoft 관리 키 또는 고객 관리 키를 사용하여 암호화되는 경우 Azure Storage는 암호화 및 암호 해독 요청에 제공된 키를 사용합니다.

요청의 일부로 암호화 키를 보내려면 클라이언트는 HTTPS를 사용하여 Azure Storage에 대한 보안 연결을 설정해야 합니다.

각 Blob 스냅숏에는 자체 암호화 키가 있을 수 있습니다.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>고객이 제공한 키 를 지정하기 위한 헤더 요청

REST 호출의 경우 클라이언트는 다음 헤더를 사용하여 Blob 저장소에 요청에 대한 암호화 키 정보를 안전하게 전달할 수 있습니다.

|요청 헤더 | 설명 |
|---------------|-------------|
|`x-ms-encryption-key` |쓰기 및 읽기 요청 모두에 필요합니다. Base64 인코딩된 AES-256 암호화 키 값입니다. |
|`x-ms-encryption-key-sha256`| 쓰기 및 읽기 요청 모두에 필요합니다. 암호화 키의 Base64 인코딩된 SHA256입니다. |
|`x-ms-encryption-algorithm` | 쓰기 요청에 필요하고 읽기 요청에는 선택 사항입니다. 지정된 키를 사용하여 데이터를 암호화할 때 사용할 알고리즘을 지정합니다. AES256이어야 합니다. |

요청에 암호화 키를 지정하는 것은 선택 사항입니다. 그러나 쓰기 작업에 대해 위에 나열된 헤더 중 하나를 지정하는 경우 모두 지정해야 합니다.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>고객이 제공한 키를 지원하는 Blob 저장소 작업

다음 Blob 저장소 작업은 요청에 따라 고객이 제공한 암호화 키를 전송하는 것을 지원합니다.

- [Blob 배치](/rest/api/storageservices/put-blob)
- [블록 목록 배치](/rest/api/storageservices/put-block-list)
- [블록 배치](/rest/api/storageservices/put-block)
- [URL에서 차단 넣기](/rest/api/storageservices/put-block-from-url)
- [페이지 배치](/rest/api/storageservices/put-page)
- [URL에서 페이지 넣기](/rest/api/storageservices/put-page-from-url)
- [추가 블록](/rest/api/storageservices/append-block)
- [Blob 속성 설정](/rest/api/storageservices/set-blob-properties)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 가져오기](/rest/api/storageservices/get-blob)
- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [Blob 스냅샷](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>고객이 제공한 키 회전

Blob을 암호화하는 데 사용된 암호화 키를 회전하려면 Blob을 다운로드한 다음 새 암호화 키로 다시 업로드합니다.

> [!IMPORTANT]
> Azure 포털을 사용하거나 요청에 제공된 키로 암호화된 컨테이너 또는 Blob에 쓸 수 없습니다.
>
> Azure Key Vault와 같은 보안 키 저장소에서 Blob 저장소에 대한 요청에 대해 제공하는 암호화 키를 보호해야 합니다. 암호화 키 없이 컨테이너 또는 Blob에서 쓰기 작업을 시도하면 작업이 실패하고 개체에 대한 액세스 권한이 손실됩니다.

## <a name="next-steps"></a>다음 단계

- [.NET을 사용하여 Blob 저장소에 대한 요청에 고객이 제공한 키 지정](../blobs/storage-blob-customer-provided-key.md)
- [미사용 데이터에 대한 Azure 저장소 암호화](storage-service-encryption.md)
