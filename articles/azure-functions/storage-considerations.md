---
title: Azure 함수에 대한 저장소 고려 사항
description: Azure Functions의 저장소 요구 사항 및 저장된 데이터 암호화에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276583"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure 함수에 대한 저장소 고려 사항

Azure 함수는 함수 앱 인스턴스를 만들 때 Azure Storage 계정이 필요합니다. 다음 저장소 서비스는 함수 앱에서 사용할 수 있습니다.


|스토리지 서비스  | 기능 사용  |
|---------|---------|
| [Azure Blob 스토리지](../storage/blobs/storage-blobs-introduction.md)     | 바인딩 상태 및 함수 키를 유지 관리합니다.  <br/>또한 [내구 함수의 작업 허브에서 사용됩니다.](durable/durable-functions-task-hubs.md) |
| [Azure 파일](../storage/files/storage-files-introduction.md)  | [사용 계획에서](functions-scale.md#consumption-plan)함수 앱 코드를 저장하고 실행하는 데 사용되는 파일 공유입니다. |
| [Azure 큐 저장소](../storage/queues/storage-queues-introduction.md)     | [내구 기능의 작업 허브에서 사용됩니다.](durable/durable-functions-task-hubs.md)   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [내구 기능의 작업 허브에서 사용됩니다.](durable/durable-functions-task-hubs.md)       |

> [!IMPORTANT]
> 소비 호스팅 계획을 사용할 경우 함수 코드 및 바인딩 구성 파일은 기본 스토리지 계정의 Azure File Storage에 저장됩니다. 기본 스토리지 계정을 삭제하면 이 콘텐츠는 삭제되고 복구할 수 없습니다.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

함수 앱을 만들 때 Blob, Queue 및 테이블 저장소를 지원하는 범용 Azure Storage 계정을 만들거나 링크해야 합니다. 이는 함수가 트리거 관리 및 로깅 함수 실행과 같은 작업에 Azure Storage를 의존하기 때문입니다. 일부 저장소 계정은 큐와 테이블을 지원하지 않습니다. 이러한 계정에는 Blob 전용 저장소 계정, Azure Premium 저장소 및 ZRS 복제가 있는 범용 저장소 계정이 포함됩니다. 지원되지 않는 이러한 계정은 함수 앱을 만들 때 저장소 계정 블레이드에서 필터링됩니다.

저장소 계정 유형에 대한 자세한 내용은 [Azure 저장소 서비스 소개를](../storage/common/storage-introduction.md#azure-storage-services)참조하십시오. 

함수 앱에서 기존 저장소 계정을 사용할 수 있지만 이러한 요구 사항을 충족하는지 확인해야 합니다. 함수 앱 생성 흐름의 일부로 생성된 저장소 계정은 이러한 저장소 계정 요구 사항을 충족하도록 보장됩니다.  

## <a name="storage-account-guidance"></a>스토리지 계정 지침

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제되면 함수 앱이 실행되지 않습니다. 저장소 관련 문제를 해결하려면 [저장소 관련 문제를 해결하는 방법을](functions-recover-storage-account.md)참조하세요. 함수 앱에서 사용하는 저장소 계정에는 다음과 같은 추가 고려 사항이 적용됩니다.

### <a name="storage-account-connection-setting"></a>저장소 계정 연결 설정

저장소 계정 연결은 [AzureWebJobsStorage 응용 프로그램 설정에서](./functions-app-settings.md#azurewebjobsstorage)유지 관리됩니다. 

저장소 키를 다시 생성할 때 저장소 계정 연결 문자열을 업데이트해야 합니다. [저장소 키 관리에 대한 자세한 내용은 여기를 참조하십시오.](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

### <a name="shared-storage-accounts"></a>공유 저장소 계정

여러 기능 앱이 문제 없이 동일한 저장소 계정을 공유할 수 있습니다. 예를 들어 Visual Studio에서 Azure 저장소 에뮬레이터를 사용하여 여러 앱을 개발할 수 있습니다. 이 경우 에뮬레이터는 단일 저장소 계정처럼 작동합니다. 함수 앱에서 사용하는 동일한 저장소 계정을 사용하여 응용 프로그램 데이터를 저장할 수도 있습니다. 그러나 이 방법은 프로덕션 환경에서 항상 좋은 생각은 아닙니다.

### <a name="optimize-storage-performance"></a>스토리지 성능 최적화

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>스토리지 데이터 암호화

Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화합니다. 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 암호화를](../storage/common/storage-service-encryption.md)참조하십시오.

기본적으로 데이터는 Microsoft 에서 관리하는 키로 암호화됩니다. 암호화 키를 추가로 제어하려면 Blob 및 파일 데이터의 암호화에 사용할 고객 관리 키를 제공할 수 있습니다. 이러한 키는 저장소 계정에 액세스할 수 있도록 기능에 대한 Azure 키 자격 증명 모음에 있어야 합니다. 자세한 내용은 [Azure 포털을 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 구성을](../storage/common/storage-encryption-keys-portal.md)참조하십시오.  

## <a name="mount-file-shares-linux"></a>마운트 파일 공유 (리눅스)

Linux 기능 앱에 기존 Azure Files 공유를 탑재할 수 있습니다. Linux 기능 앱에 공유를 탑재하면 기존 기계 학습 모델 이나 함수의 다른 데이터를 활용할 수 있습니다. 이 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) 명령을 사용하여 기존 공유를 Linux 기능 앱에 탑재할 수 있습니다. 

이 명령에서 `share-name` 기존 Azure Files 공유의 이름이며 `custom-id` 함수 앱에 탑재할 때 공유를 고유하게 정의하는 문자열일 수 있습니다. `mount-path` 또한 함수 앱에서 공유에 액세스하는 경로도 있습니다. `mount-path`형식이어야 `/dir-name`하며 로 시작할 `/home`수 없습니다.

전체 예제에서는 [Python 함수 앱 만들기의 스크립트를 참조하고 Azure Files 공유를 마운트합니다.](scripts/functions-cli-mount-files-storage-linux.md) 

현재 는 `storage-type` a만 `AzureFiles` 지원됩니다. 지정된 함수 앱에만 5개의 공유를 탑재할 수 있습니다. 파일 공유를 탑재하면 저장소 계정이 다른 지역에 있을 때 콜드 시작 시간이 200~300ms 이상 증가하거나 더 늘어남에 따라 콜드 시작 시간이 늘어남을 수 있습니다.

마운트된 공유는 `mount-path` 지정된 기능 코드에서 사용할 수 있습니다. 예를 들어 `mount-path` `/path/to/mount`다음과 같은 Python 예제와 같이 파일 시스템 API별로 대상 디렉터리에 액세스할 수 있습니다.

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>다음 단계

Azure Functions 호스팅 옵션에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Functions 크기 조정 및 호스팅](functions-scale.md)


