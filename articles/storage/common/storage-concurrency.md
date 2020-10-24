---
title: 동시성 관리
titleSuffix: Azure Storage
description: Blob, 큐, 테이블 및 파일 서비스에 대 한 Azure Storage에서 동시성을 관리 하는 방법에 대해 알아봅니다. 사용 되는 세 가지 주요 데이터 동시성 전략을 이해 합니다.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ac54282135759f14f17ed16b9779013f849bd8d7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488676"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Microsoft Azure Storage에서 동시성 관리

최신 인터넷 기반 애플리케이션에서는 대개 여러 사용자가 데이터를 동시에 보고 업데이트합니다. 응용 프로그램 개발자는 특히 여러 사용자가 동일한 데이터를 업데이트할 수 있는 예측 가능한 사용자 환경을 제공 하는 데 대해 신중 하 게 고려해 야 합니다. 개발자가 일반적으로 고려하는 주요 데이터 동시성 전략에는 다음의 세 가지가 있습니다.

1. 낙관적 동시성 – 업데이트를 수행 하는 응용 프로그램은 업데이트의 일환으로, 응용 프로그램에서 데이터를 마지막으로 읽은 후 데이터가 변경 되었는지 확인 합니다. 예를 들어 wiki 페이지를 보는 두 사용자가 같은 페이지에 대 한 업데이트를 만드는 경우 wiki 플랫폼은 두 번째 업데이트가 첫 번째 업데이트를 덮어쓰지 않도록 하 고 두 사용자 모두 업데이트가 성공 했는지 여부를 이해 해야 합니다. 이 전략은 웹 애플리케이션에서 가장 흔히 사용됩니다.
2. 비관적 동시성 – 업데이트를 수행 하려는 응용 프로그램은 개체에 대 한 잠금을 사용 하 여 잠금이 해제 될 때까지 다른 사용자가 데이터를 업데이트 하지 못하도록 합니다.
3. 마지막 기록기 우선 – 응용 프로그램이 데이터를 먼저 읽은 후 다른 응용 프로그램이 데이터를 업데이트 했는지 확인 하지 않고 모든 업데이트 작업을 계속할 수 있도록 하는 방법입니다. 이 전략은 여러 사용자가 동일한 데이터에 액세스할 가능성이 없도록 데이터를 분할할 때 주로 사용 됩니다. 일시적인 데이터 스트림을 처리하는 경우에도 이 전략이 유용할 수 있습니다.

이 문서에서는 이러한 세 가지 동시성 전략을 모두 지원 하 여 Azure Storage 개발을 간소화 하는 방법에 대 한 개요를 제공 합니다.

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage로 클라우드 개발 간소화

Azure Storage는 낙관적 및 비관적 동시성을 완벽 하 게 지원 하기 위한 다양 한 기능을 제공 하지만 세 가지 전략을 모두 지원 합니다. Azure Storage은 강력한 일관성 모델을 수용 하도록 설계 되었으므로 데이터 삽입 또는 업데이트 작업을 커밋할 때 해당 데이터에 대 한 모든 추가 액세스가 최신 업데이트를 볼 수 있도록 보장 합니다. 최종 일관성 모델을 사용 하는 저장소 플랫폼은 한 사용자가 쓰기를 수행 하는 경우와 업데이트 된 데이터가 다른 사용자에 게 표시 될 때 사이에 지연이 있습니다.

또한 개발자는 저장소 플랫폼의 변경 내용, 특히 여러 트랜잭션에서 동일한 개체의 변경 내용을 파악 해야 합니다. Azure Storage 서비스는 스냅숏 격리를 사용 하 여 단일 파티션 내에서 쓰기 작업과 동시에 읽기 작업이 수행 되도록 합니다. 다른 격리 수준과는 달리 스냅샷 격리에서는 업데이트가 수행되는 동안에도 모든 읽기 시 데이터의 일관된 스냅샷이 표시됩니다. 이를 위해 업데이트 트랜잭션을 처리하는 동안 마지막으로 커밋된 값을 반환합니다.

## <a name="managing-concurrency-in-blob-storage"></a>Blob Storage에서 동시성 관리

낙관적 동시성 모델이나 비관적 동시성 모델을 사용하여 Blob 서비스의 컨테이너와 Blob에 대한 액세스를 관리할 수 있습니다. 전략을 명시적으로 지정 하지 않으면 마지막 쓰기 우선 적용이 기본값입니다.

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Blob 및 컨테이너에 대한 낙관적 동시성

Storage 서비스는 저장되는 모든 개체에 식별자를 할당합니다. 이 식별자는 개체에 대해 업데이트 작업이 수행 될 때마다 업데이트 됩니다. 이러한 개체에 대해 업데이트를 수행하는 사용자는 조건부 헤더와 함께 원본 ETag를 보내 특정 조건이 충족된 경우에만 업데이트가 수행되도록 할 수 있습니다. 이러한 개체에 대 한 업데이트를 수행 하는 사용자는 조건부 헤더와 함께 원래 ETag를 전송 하 여 특정 조건이 충족 되는 경우에만 업데이트가 발생 하도록 할 수 있습니다 .이 경우에는 "If-Match" 헤더를 사용 해야 합니다 .이 헤더는 저장소 서비스가 업데이트 요청에 지정 된 ETag의 값이 Storage 서비스에 저장 된 값과 동일 해야 합니다.

이 프로세스는 대략적으로 다음과 같이 수행됩니다.

1. 스토리지 서비스에서 Blob를 검색합니다. 응답에는 스토리지 서비스에서 개체의 현재 버전을 식별하는 HTTP ETag 헤더 값이 포함됩니다.
2. Blob를 업데이트할 때는 서비스로 보내는 요청의 **If-Match** 조건부 헤더에 1단계에서 받은 ETag 값을 포함합니다.
3. 서비스가 요청의 ETag 값을 Blob의 현재 ETag 값과 비교합니다.
4. Blob의 현재 ETag 값이 요청의 **If-Match** 조건부 헤더에 포함된 ETag와 다른 버전이면 서비스는 클라이언트에 412 오류를 반환합니다. 이 오류는 클라이언트가 검색 한 후 다른 프로세스에서 blob를 업데이트 했음을 나타냅니다.
5. Blob의 현재 ETag 값이 요청의 **If-Match** 조건부 헤더에 포함된 ETag와 같은 버전이면 서비스는 요청된 작업을 수행하며 Blob의 현재 ETag 값을 업데이트하여 새 버전을 만들었음을 표시합니다.

클라이언트 스토리지 라이브러리 4.2.0을 사용하는 다음 C# 코드 조각은 이전에 검색했거나 삽입한 Blob의 속성에서 액세스할 수 있는 ETag 값을 기준으로 **If-Match AccessCondition** 을 생성하는 방법의 간단한 예제를 보여 줍니다. 그런 다음 Blob을 업데이트할 때 **AccessCondition** 개체를 사용합니다: **AccessCondition** 개체는 요청에 **If-Match** 헤더를 추가합니다. 다른 프로세스가 Blob을 업데이트한 경우 Blob 서비스는 HTTP 412(전재 조건 실패) 상태 메시지를 반환합니다. 여기서 전체 샘플을 다운로드할 수 있습니다. [Azure Storage를 사용하여 동시성 관리](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}
```

또한 Azure Storage에는 **if-수정**된 경우, 수정 되지 않은 **경우**-수정 되지 않은 **경우, 해당**헤더의 조합 등의 조건부 헤더에 대 한 지원도 포함 됩니다. 자세한 내용은 [Blob 서비스 작업의 조건부 헤더 지정](https://msdn.microsoft.com/library/azure/dd179371.aspx)을 참조하세요.

아래 표에는 요청에서 **If-Match** 와 같은 조건부 헤더를 수락하며 응답에서 ETag 값을 반환하는 컨테이너 작업이 요약되어 있습니다.

| 작업(Operation) | 컨테이너 ETag 값 반환 | 추가 헤더 수락 |
|:--- |:--- |:--- |
| 컨테이너 만들기 |예 |예 |
| 컨테이너 속성 가져오기 |예 |예 |
| 컨테이너 메타데이터 가져오기 |예 |예 |
| 컨테이너 메타데이터 설정 |예 |예 |
| 컨테이너 ACL 가져오기 |예 |예 |
| 컨테이너 ACL 설정 |예 |예(*) |
| 컨테이너 삭제 |예 |예 |
| 컨테이너 임대 |예 |예 |
| Blob 나열 |예 |예 |

(*) SetContainerACL에 의해 정의 된 사용 권한은 캐시 되 고 이러한 사용 권한에 대 한 업데이트는 업데이트 일관성을 유지 하는 기간 동안 전파 되는 데 30 초 정도 걸립니다.

아래 표에는 요청에서 **If-Match** 와 같은 조건부 헤더를 수락하며 응답에서 ETag 값을 반환하는 Blob 작업이 요약되어 있습니다.

| 작업(Operation) | ETag 값 반환 | 추가 헤더 수락 |
|:--- |:--- |:--- |
| Blob 배치 |예 |예 |
| Blob 가져오기 |예 |예 |
| Blob 속성 가져오기 |예 |예 |
| Blob 속성 설정 |예 |예 |
| Blob 메타데이터 가져오기 |예 |예 |
| Blob 메타데이터 설정 |예 |예 |
| Blob 임대(*) |예 |예 |
| Blob 스냅샷 |예 |예 |
| Blob 복사 |예 |예(원본 및 대상 Blob의 경우) |
| Blob 복사 중단 |예 |예 |
| Blob 삭제 |예 |예 |
| 블록 배치 |예 |예 |
| 블록 목록 배치 |예 |예 |
| 블록 목록 가져오기 |예 |예 |
| 페이지 가져오기 |예 |예 |
| 페이지 범위 가져오기 |예 |예 |

(*) Blob 임대는 blob에서 ETag를 변경 하지 않습니다.

### <a name="pessimistic-concurrency-for-blobs"></a>Blob에 대한 비관적 동시성

단독 사용을 위해 blob을 잠그려면 해당 blob에 대 한 [임대](https://msdn.microsoft.com/library/azure/ee691972.aspx) 를 획득 합니다. 임대를 획득 하는 경우 임대 기간을 지정 합니다. 기간 범위는 15에서 60 초 또는 무한대로, 배타적 잠금에 해당 합니다. 유한 임대를 갱신 하 여 확장 합니다. 완료 되 면 임대를 해제 합니다. Blob Storage는 만료 되는 경우 자동으로 유한 임대를 해제 합니다.

임대를 사용 하면 다양 한 동기화 전략을 지원할 수 있습니다. 전략에는 *배타적 쓰기/공유 읽기*, *배타적 쓰기/배타적 읽기*, *공유 쓰기/배타적 읽기*등이 있습니다. 임대가 있는 경우 Azure Storage는 배타적 쓰기 (put, set 및 delete 작업)를 적용 하지만 읽기 작업에 대 한 독점 성을 확인 하려면 개발자가 모든 클라이언트 응용 프로그램에서 임대 ID를 사용 하 고 한 번에 한 클라이언트만 유효한 임대 ID를 갖도록 해야 합니다. 임대 ID를 포함 하지 않는 읽기 작업에서 공유 읽기가 발생 합니다.

다음 C# 코드 조각은 Blob에 대해 30초 동안 배타적 임대를 획득하고 Blob의 내용을 업데이트한 후에 임대를 해제하는 예제를 보여 줍니다. 새 임대를 얻으려고 할 때 blob에 대 한 유효한 임대가 이미 있는 경우 Blob service는 "HTTP (409) 충돌" 상태 결과를 반환 합니다. 다음 코드 조각은 스토리지 서비스에서 Blob를 업데이트하기 위한 요청을 할 때 **AccessCondition** 개체를 사용하여 임대 정보를 캡슐화합니다.  여기서 전체 샘플을 다운로드할 수 있습니다. [Azure Storage를 사용하여 동시성 관리](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}
```

임대 ID를 전달하지 않고 임대한 Blob에 대해 쓰기 작업을 시도하면 요청이 실패하고 412 오류가 발생합니다. **UploadText** 메서드를 호출 하기 전에 임대가 만료 되지만 임대 ID를 전달 하는 경우에도 **412** 오류가 발생 하 여 요청이 실패 합니다. 임대 만료 시간 및 임대 ID를 관리하는 방법에 대한 자세한 내용은 [Blob 임대](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST 문서를 참조하세요.

다음 Blob 작업에서는 임대를 사용하여 비관적 동시성을 관리할 수 있습니다.

* Blob 배치
* Blob 가져오기
* Blob 속성 가져오기
* Blob 속성 설정
* Blob 메타데이터 가져오기
* Blob 메타데이터 설정
* Blob 삭제
* 블록 배치
* 블록 목록 배치
* 블록 목록 가져오기
* 페이지 가져오기
* 페이지 범위 가져오기
* 스냅샷 Blob - 임대가 있는 경우 임대 ID는 선택 사항임
* Blob 복사 - 대상 Blob에 대한 임대가 있는 경우 임대 ID는 필수임
* Blob 복사 중단 - 대상 Blob에 대한 무한 임대가 있는 경우 임대 ID는 필수임
* Blob 임대

### <a name="pessimistic-concurrency-for-containers"></a>컨테이너에 대한 비관적 동시성

컨테이너에 대 한 임대를 사용 하면 blob (*배타적 쓰기/공유 읽기*, *배타적 쓰기/배타적 읽기*, *공유 쓰기/배타적*읽기)에서와 동일한 동기화 전략을 지원할 수 있습니다. 단, 저장소 서비스는 삭제 작업에 독점 성을 적용 합니다. 임대가 활성 상태인 컨테이너를 삭제하려면 클라이언트가 삭제 요청에 활성 임대 ID를 포함해야 합니다. 기타 모든 컨테이너 작업은 임대 ID를 포함하지 않아도 임대한 컨테이너에서 성공합니다. 이 경우 해당 작업은 공유 작업입니다. 업데이트(배치 또는 설정) 또는 읽기 작업에서 독점성이 필요한 경우 개발자는 모든 클라이언트가 임대 ID를 사용하고 한 번에 한 클라이언트만 유효한 임대 ID를 사용하도록 해야 합니다.

다음 컨테이너 작업에서는 임대를 사용하여 비관적 동시성을 관리할 수 있습니다.

* 컨테이너 삭제
* 컨테이너 속성 가져오기
* 컨테이너 메타데이터 가져오기
* 컨테이너 메타데이터 설정
* 컨테이너 ACL 가져오기
* 컨테이너 ACL 설정
* 컨테이너 임대

자세한 내용은 다음을 참조하세요.

* [Blob 서비스 작업의 조건부 헤더 지정](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [컨테이너 임대](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Blob 임대](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Table Storage에서 동시성 관리

Table service는 낙관적 동시성 검사를 수행 하도록 명시적으로 선택 해야 하는 Blob service와 달리, 엔터티를 사용 하는 경우에만 낙관적 동시성 검사를 기본 동작으로 사용 합니다. 테이블과 Blob 서비스의 또 다른 차이점은 엔터티의 동시성 동작을 관리할 수만 있고 Blob service는 컨테이너와 blob의 동시성을 관리할 수 있다는 것입니다.

낙관적 동시성을 사용하고 엔터티를 Table Storage 서비스에서 검색한 이후 다른 프로세스에서 해당 엔터티를 수정했는지 확인하려는 경우 테이블 서비스에서 엔터티를 반환할 때 수신되는 ETag 값을 사용하면 됩니다. 이 프로세스는 대략적으로 다음과 같이 수행됩니다.

1. Table Storage 서비스에서 엔터티를 검색합니다. 응답에는 스토리지 서비스에서 해당 엔터티와 연결된 현재 식별자를 식별하는 ETag 값이 포함됩니다.
2. 엔터티를 업데이트할 때는 서비스로 보내는 요청의 필수 **If-Match** 헤더에 1단계에서 받은 ETag 값을 포함합니다.
3. 서비스가 요청의 ETag 값을 엔터티의 현재 ETag 값과 비교합니다.
4. 엔터티의 현재 ETag 값이 요청의 필수 **If-Match** 헤더에 포함된 ETag와 다르면 서비스는 클라이언트에 412 오류를 반환합니다. 클라이언트는 이 오류를 통해 엔터티를 검색한 후 다른 프로세스에서 Blob를 업데이트했음을 확인할 수 있습니다.
5. 엔터티의 현재 ETag 값이 요청의 필수 **인-match** 헤더의 etag와 동일 하거나 **if-match** 헤더에 와일드 카드 문자 (*)가 포함 된 경우 서비스는 요청 된 작업을 수행 하 고 엔터티의 현재 etag 값을 업데이트 하 여 업데이트 되었음을 표시 합니다.

테이블 서비스를 사용 하려면 클라이언트가 업데이트 요청에 **If-Match** 헤더를 포함 해야 합니다. 그러나 클라이언트가 요청에서 **If-Match** 헤더를 와일드카드 문자(*)로 설정하는 경우에는 무조건 업데이트(마지막 작성자의 업데이트 적용 전략)를 강제 지정하고 동시성 검사를 무시할 수 있습니다.

다음 C# 코드 조각은 이전에 만들거나 검색한 customer 엔터티의 메일 주소를 업데이트하는 작업을 보여 줍니다. 초기 삽입 또는 검색 작업에서는 ETag 값을 customer 개체에 저장합니다. 이 샘플은 바꾸기 작업을 실행할 때 같은 개체 인스턴스를 사용하므로 ETag 값을 테이블 서비스에 자동으로 다시 보내기 때문에 서비스가 동시성 위반을 검사할 수 있습니다. 다른 프로세스가 Table Storage에서 엔터티를 업데이트한 경우 서비스는 HTTP 412(전재 조건 실패) 상태 메시지를 반환합니다.  여기서 전체 샘플을 다운로드할 수 있습니다. [Azure Storage를 사용하여 동시성 관리](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}
```

동시성 검사를 명시적으로 사용하지 않도록 설정하려면 바꾸기 작업을 실행하기 전에 **employee** 개체의 **ETag** 속성을 “*”로 설정해야 합니다.

```csharp
customer.ETag = "*";
```

다음 표에는 테이블 엔터티 작업이 ETag 값을 사용하는 방식이 요약되어 있습니다.

| 작업(Operation) | ETag 값 반환 | If-Match 요청 헤더 필요 여부 |
|:--- |:--- |:--- |
| 엔터티 쿼리 |예 |예 |
| 엔터티 삽입 |예 |예 |
| 엔터티 업데이트 |예 |예 |
| 엔터티 병합 |예 |예 |
| 엔터티 삭제 |예 |예 |
| 엔터티 삽입 또는 바꾸기 |예 |예 |
| 엔터티 삽입 또는 병합 |예 |예 |

**엔터티 삽입 또는 바꾸기** 및 **삽입 또는 병합 엔터티** 작업은 ETag 값을 table service로 보내지 않기 때문에 동시성 검사를 수행 *하지 않습니다.*

일반적으로 테이블을 사용 하는 개발자는 낙관적 동시성을 사용 해야 합니다. 테이블에 액세스할 때 비관적 잠금이 필요한 경우 각 테이블에 대해 선택한 blob을 할당 하 고 테이블에 대해 작업을 수행 하기 전에 blob에 대 한 임대를 시도 합니다. 이 접근 방식을 사용 하려면 응용 프로그램이 테이블에서 작업 하기 전에 모든 데이터 액세스 경로에서 임대를 가져와야 합니다. 또한 최소 임대 시간은 15 초 이며, 확장성을 신중히 고려해 야 합니다.

자세한 내용은 다음을 참조하세요.

* [엔터티에 대한 작업](https://msdn.microsoft.com/library/azure/dd179375.aspx)

## <a name="managing-concurrency-in-the-queue-service"></a>큐 서비스에서 동시성 관리

큐 서비스에서 동시성이 중요한 시나리오 중 하나는 여러 클라이언트가 큐에서 메시지를 검색할 때입니다. 큐에서 메시지를 검색할 때 응답에는 메시지와 PopReceipt 값(메시지를 삭제할 때 필요함)이 포함됩니다. 메시지는 큐에서 자동으로 삭제 되지 않지만 검색 된 후에는 표시 시간 제한 매개 변수로 지정 된 시간 간격 동안 다른 클라이언트에 표시 되지 않습니다. 메시지를 검색 하는 클라이언트는 메시지를 처리 한 후에 삭제 해야 하며, 응답의 Ti Extvisible 요소에 지정 된 시간 이전에 표시 시간 제한 매개 변수 값에 따라 계산 됩니다. 표시 시간 제한 값은 메시지를 검색 하는 시간에 추가 되어 Ti Extvisible의 값을 확인 합니다.

큐 서비스는 낙관적 또는 비관적 동시성을 지원 하지 않으므로 큐에서 검색 된 메시지를 처리 하는 클라이언트는 idempotent 방식으로 메시지를 처리 해야 합니다. 마지막 기록기 wins 전략은 SetQueueServiceProperties, SetQueueMetaData, SetQueueACL 및 UpdateMessage와 같은 업데이트 작업에 사용 됩니다.

자세한 내용은 다음을 참조하세요.

* [큐 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [메시지 가져오기](https://msdn.microsoft.com/library/azure/dd179474.aspx)

## <a name="managing-concurrency-in-azure-files"></a>Azure Files에서 동시성 관리

서로 다른 두 프로토콜 엔드포인트인 SMB와 REST를 사용하여 파일 서비스에 액세스할 수 있습니다. REST 서비스는 낙관적 잠금 또는 비관적 잠금을 지원 하지 않으며 모든 업데이트는 마지막 기록기 wins 전략을 따릅니다. 파일 공유를 탑재 하는 SMB 클라이언트는 파일 시스템 잠금 메커니즘을 사용 하 여 비관적 잠금을 수행 하는 기능을 포함 하 여 공유 파일에 대 한 액세스를 관리할 수 있습니다. SMB 클라이언트는 파일을 열 때 파일 액세스 및 공유 모드를 모두 지정합니다. 파일 공유 모드를 "없음"으로 지정하는 동시에 파일 액세스 옵션을 "쓰기" 또는 "읽기/쓰기"로 설정하면 파일을 닫을 때까지 SMB 클라이언트가 파일을 잠급니다. SMB 클라이언트가 파일을 잠근 경우 해당 파일에 대해 REST 작업을 시도하면 REST 서비스가 상태 코드 409(충돌) 및 오류 코드 SharingViolation을 반환합니다.

SMB 클라이언트는 삭제를 위해 파일을 열 때 해당 파일에 대한 기타 모든 SMB 클라이언트 열기 핸들이 닫힐 때까지 파일을 삭제 보류 중으로 표시합니다. 파일이 삭제 보류 중으로 표시되어 있는 동안 해당 파일에 대해 REST 작업을 수행하면 상태 코드 409(충돌)와 오류 코드 SMBDeletePending이 반환됩니다. SMB 클라이언트가 파일을 닫기 전에 보류 중인 삭제 플래그를 제거할 수 있으므로 상태 코드 404 (찾을 수 없음)이 반환 되지 않습니다. 즉, 파일이 제거된 경우에만 상태 코드 404(찾을 수 없음)가 반환됩니다. 파일이 SMB 삭제 보류 중 상태인 경우 목록 파일 결과에 포함되지 않습니다. 또한 REST 파일 삭제 및 REST 디렉터리 삭제 작업은 원자성으로 커밋되며 삭제 보류 중 상태를 나타내지 않습니다.

자세한 내용은 다음을 참조하세요.

* [파일 잠금 관리](https://msdn.microsoft.com/library/azure/dn194265.aspx)

## <a name="next-steps"></a>다음 단계

이 블로그에서 참조하는 전체 샘플 애플리케이션은 다음 문서를 참조하세요.

* [Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

Azure Storage에 대 한 자세한 내용은 다음을 참조 하세요.

* [Microsoft Azure Storage 홈페이지](https://azure.microsoft.com/services/storage/)
* [Azure Storage 소개](storage-introduction.md)
* [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [테이블](../../cosmos-db/table-storage-how-to-use-dotnet.md), [큐](../storage-dotnet-how-to-use-queues.md) 및 [파일](../storage-dotnet-how-to-use-files.md)에 대한 스토리지 시작
* 스토리지 아키텍처 - [Azure Storage: 강력한 일관성을 지닌 고가용성 클라우드 스토리지 서비스](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)
