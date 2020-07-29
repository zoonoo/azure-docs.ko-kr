---
title: Azure Functions의 스토리지 고려 사항
description: Azure Functions의 스토리지 요구 사항 및 저장된 데이터 암호화에 관해 알아봅니다.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: aefd9a35235a09d94973f383603349f6862bbdd9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318184"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions의 스토리지 고려 사항

함수 앱 인스턴스를 만들 때 Azure Functions에는 Azure Storage 계정이 필요합니다. 함수 앱에서 사용할 수 있는 스토리지 서비스는 다음과 같습니다.


|스토리지 서비스  | Functions 사용  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | 바인딩 상태 및 함수 키를 유지 관리합니다.  <br/>[Durable Functions의 작업 허브](durable/durable-functions-task-hubs.md)에서도 사용됩니다. |
| [Azure 파일](../storage/files/storage-files-introduction.md)  | [소비 계획](functions-scale.md#consumption-plan) 및 [프리미엄 계획](functions-scale.md#premium-plan)에서 함수 앱 코드를 저장 하 고 실행 하는 데 사용 되는 파일 공유입니다. |
| [Azure Queue storage](../storage/queues/storage-queues-introduction.md)     | [Durable Functions의 작업 허브](durable/durable-functions-task-hubs.md)에서 사용됩니다.   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [Durable Functions의 작업 허브](durable/durable-functions-task-hubs.md)에서 사용됩니다.       |

> [!IMPORTANT]
> 소비/프리미엄 호스팅 계획을 사용 하는 경우 함수 코드 및 바인딩 구성 파일은 주 저장소 계정의 Azure File storage에 저장 됩니다. 기본 스토리지 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

함수 앱을 만들 때 Blob, Queue 및 Table 스토리지를 지원하는 범용 Azure Storage 계정을 만들거나 해당 계정에 연결해야 합니다. 이는 Functions가 트리거 관리 및 함수 실행 기록 등의 작업에 Azure Storage를 사용하기 때문입니다. 일부 스토리지 계정은 큐 및 테이블을 지원하지 않습니다. 해당 계정에는 Blob 전용 스토리지 계정, Azure Premium Storage 및 ZRS 복제를 포함한 범용 스토리지 계정이 포함됩니다. 이 지원되지 않는 계정은 함수 앱을 만들 때 스토리지 계정 블레이드에서 필터링됩니다.

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#core-storage-services)를 참조하세요. 

함수 앱에서 기존 스토리지 계정을 사용할 수 있지만 관련 요구 사항을 충족하는지 확인해야 합니다. 함수 앱 만들기 흐름의 일부로 생성된 스토리지 계정은 관련 스토리지 계정 요구 사항을 충족합니다.  

## <a name="storage-account-guidance"></a>스토리지 계정 지침

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제되면 함수 앱이 실행되지 않습니다. 스토리지 관련 문제를 해결하려면 [스토리지 관련 문제를 해결하는 방법](functions-recover-storage-account.md)을 참조하세요. 함수 앱에서 사용하는 스토리지 계정에는 다음 추가 고려 사항이 적용됩니다.

### <a name="storage-account-connection-setting"></a>스토리지 계정 연결 설정

스토리지 계정 연결은 [AzureWebJobsStorage 애플리케이션 설정](./functions-app-settings.md#azurewebjobsstorage)에서 유지 관리됩니다. 

스토리지 키를 다시 생성하는 경우 스토리지 계정 연결 문자열을 업데이트해야 합니다. [여기에서 스토리지 키 관리에 관해 자세히 알아보세요.](../storage/common/storage-account-create.md)

### <a name="shared-storage-accounts"></a>공유 스토리지 계정

여러 함수 앱에서 문제 없이 동일한 스토리지 계정을 공유할 수 있습니다. 예를 들어 Visual Studio에서 Azure Storage 에뮬레이터를 사용하여 여러 앱을 개발할 수 있습니다. 이 경우 에뮬레이터는 단일 스토리지 계정처럼 작동합니다. 함수 앱에서 사용하는 동일한 스토리지 계정을 사용하여 애플리케이션 데이터를 저장할 수도 있습니다. 그러나 프로덕션 환경에서는 이 접근 방법이 항상 좋은 것은 아닙니다.

### <a name="optimize-storage-performance"></a>스토리지 성능 최적화

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>스토리지 데이터 암호화

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="mount-file-shares-linux"></a>파일 공유 탑재(Linux)

기존 Azure Files 공유를 Linux 함수 앱에 탑재할 수 있습니다. Linux 함수 앱에 공유를 탑재하면 기존 기계 학습 모델 또는 함수의 기타 데이터를 활용할 수 있습니다. [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) 명령을 사용하여 Linux 함수 앱에 기존 공유를 탑재할 수 있습니다. 

이 명령에서 `share-name`은 기존 Azure Files 공유의 이름이며 `custom-id`는 함수 앱에 탑재될 때 공유를 고유하게 정의하는 문자열일 수 있습니다. 또한 `mount-path`는 함수 앱에서 공유에 액세스하는 경로입니다. `mount-path`는 `/dir-name` 형식이어야 하며 `/home`으로 시작할 수 없습니다.

전체 예제는 [Python 함수 앱 만들기 및 Azure Files 공유 탑재](scripts/functions-cli-mount-files-storage-linux.md)의 스크립트를 참조하세요. 

현재는 `AzureFiles`의 `storage-type`만 지원됩니다. 지정된 함수 앱에는 5개의 공유만 탑재할 수 있습니다. 파일 공유를 탑재하면 콜드 부팅 시간이 최소한 200~300ms 증가하며 스토리지 계정이 다른 지역에 있는 경우 훨씬 더 증가할 수 있습니다.

탑재된 공유는 지정된 `mount-path`에서 함수 코드에 사용할 수 있습니다. 예를 들어 `mount-path`가 `/path/to/mount`인 경우 다음 Python 예제와 같이 파일 시스템 API를 통해 대상 디렉터리에 액세스할 수 있습니다.

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>다음 단계

Azure Functions 호스팅 옵션에 관해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Functions 비율 크기 조정 및 호스팅](functions-scale.md)
