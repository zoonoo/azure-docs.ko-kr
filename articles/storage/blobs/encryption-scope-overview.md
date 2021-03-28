---
title: Blob 저장소의 암호화 범위
description: 암호화 범위는 컨테이너 또는 개별 blob의 수준에서 암호화를 관리 하는 기능을 제공 합니다. 암호화 범위를 사용 하 여 동일한 저장소 계정에 있지만 다른 고객에 게 속한 데이터 간에 보안 경계를 만들 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640462"
---
# <a name="encryption-scopes-for-blob-storage"></a>Blob 저장소의 암호화 범위

암호화 범위를 사용 하면 컨테이너 또는 개별 blob으로 범위가 지정 된 키를 사용 하 여 암호화를 관리할 수 있습니다. 암호화 범위를 사용 하 여 동일한 저장소 계정에 있지만 다른 고객에 게 속한 데이터 간에 보안 경계를 만들 수 있습니다.

암호화 범위 작업에 대 한 자세한 내용은 [암호화 범위 만들기 및 관리](encryption-scope-manage.md)를 참조 하세요.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>암호화 범위 작동 방법

기본적으로 저장소 계정은 전체 저장소 계정으로 범위가 지정 된 키를 사용 하 여 암호화 됩니다. 암호화 범위를 정의할 때 컨테이너 또는 개별 blob으로 범위가 지정 될 수 있는 키를 지정 합니다. 암호화 범위가 blob에 적용 되 면 해당 키로 blob이 암호화 됩니다. 암호화 범위를 컨테이너에 적용 하는 경우 해당 컨테이너에 업로드 된 모든 blob이 동일한 키로 암호화 될 수 있도록 해당 컨테이너의 blob에 대 한 기본 범위로 사용 됩니다. 컨테이너의 모든 blob에 대해 기본 암호화 범위를 적용 하도록 컨테이너를 구성 하거나, 기본 blob 이외의 암호화 범위를 사용 하 여 개별 blob을 컨테이너에 업로드 하도록 허용할 수 있습니다.

암호화 범위를 사용 하 여 만든 blob에 대 한 읽기 작업은 암호화 범위를 사용 하지 않는 한 투명 하 게 수행 됩니다.

### <a name="key-management"></a>키 관리

암호화 범위를 정의할 때 범위가 Microsoft 관리 키로 보호 되는지 또는 Azure Key Vault에 저장 된 고객 관리 키를 사용 하 여 보호 되는지 여부를 지정할 수 있습니다. 동일한 저장소 계정에 있는 다양 한 암호화 범위는 Microsoft에서 관리 하거나 고객이 관리 하는 키 중 하나를 사용할 수 있습니다. 암호화 범위를 보호 하는 데 사용 되는 키 유형을 고객이 관리 하는 키에서 Microsoft 관리 키로 또는 그 반대로 전환할 수도 있습니다. 고객 관리 키에 대 한 자세한 내용은 [Azure Storage 암호화를 위한 고객 관리 키](../common/customer-managed-keys-overview.md)를 참조 하세요. Microsoft 관리 키에 대 한 자세한 내용은 [암호화 키 관리](../common/storage-service-encryption.md#about-encryption-key-management)정보를 참조 하세요.

고객 관리 키를 사용 하 여 암호화 범위를 정의 하는 경우 자동 또는 수동으로 키 버전을 업데이트 하도록 선택할 수 있습니다. 키 버전을 자동으로 업데이트 하도록 선택 하는 경우 Azure Storage는 새 버전의 고객 관리 키에 대 한 주요 자격 증명 모음 또는 관리 되는 HSM을 매일 확인 하 고 해당 키를 최신 버전으로 자동으로 업데이트 합니다. 고객 관리 키의 키 버전을 업데이트 하는 방법에 대 한 자세한 내용은 [키 버전 업데이트](../common/customer-managed-keys-overview.md#update-the-key-version)를 참조 하세요.

저장소 계정에는 키 버전이 자동으로 업데이트 되는 고객 관리 키로 보호 되는 최대 1만의 암호화 범위가 있을 수 있습니다. 자동으로 업데이트 되는 고객 관리 키로 보호 되는 1만 암호화 범위가 저장소 계정에 이미 있는 경우, 고객 관리 키로 보호 되는 추가 암호화 범위에 대해 키 버전을 수동으로 업데이트 해야 합니다.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>컨테이너 및 blob에 대 한 암호화 범위

컨테이너를 만들 때 나중에 해당 컨테이너에 업로드할 blob에 대 한 기본 암호화 범위를 지정할 수 있습니다. 컨테이너에 대해 기본 암호화 범위를 지정 하는 경우 기본 암호화 범위를 적용 하는 방법을 결정할 수 있습니다.

- 컨테이너에 업로드 된 모든 blob이 기본 암호화 범위를 사용 하도록 요구할 수 있습니다. 이 경우 컨테이너의 모든 blob은 동일한 키를 사용 하 여 암호화 됩니다.
- 기본 범위가 아닌 암호화 범위를 사용 하 여 blob을 업로드할 수 있도록 클라이언트에서 컨테이너에 대 한 기본 암호화 범위를 재정의 하도록 허용할 수 있습니다. 이 경우 컨테이너의 blob은 다른 키로 암호화 될 수 있습니다.

다음 표에는 컨테이너에 대 한 기본 암호화 범위를 구성 하는 방법에 따라 blob 업로드 작업의 동작이 요약 되어 있습니다.

| 컨테이너에 정의 된 암호화 범위는 ... | 기본 암호화 범위를 사용 하 여 blob을 업로드 하는 중 ... | 기본 범위 이외의 암호화 범위를 사용 하 여 blob을 업로드 하는 중 ... |
|--|--|--|
| 재정의가 허용 된 기본 암호화 범위 | 성공할 | 성공할 |
| 재정의가 허용 되지 않는 기본 암호화 범위 | 성공할 | 실패 |

컨테이너를 만들 때 컨테이너에 대해 기본 암호화 범위를 지정 해야 합니다.

컨테이너에 대해 기본 암호화 범위를 지정 하지 않은 경우 저장소 계정에 대해 정의한 모든 암호화 범위를 사용 하 여 blob을 업로드할 수 있습니다. Blob을 업로드할 때 암호화 범위를 지정 해야 합니다.

## <a name="disabling-an-encryption-scope"></a>암호화 범위를 사용 하지 않도록 설정

암호화 범위를 사용 하지 않도록 설정 하면 HTTP 오류 코드 403 (사용할 수 없음)이 발생 하 고 암호화 범위를 사용 하 여 수행 된 이후의 읽기 또는 쓰기 작업이 실패 합니다. 암호화 범위를 다시 사용 하도록 설정 하면 읽기 및 쓰기 작업이 다시 정상적으로 진행 됩니다.

암호화 범위를 사용 하지 않도록 설정 하면 더 이상 요금이 청구 되지 않습니다. 불필요 한 요금을 방지 하는 데 필요 하지 않은 모든 암호화 범위를 사용 하지 않도록 설정 합니다.

암호화 범위가 고객이 관리 하는 키로 보호 되는 경우 키 자격 증명 모음에서 키를 삭제 하면 데이터에 액세스할 수 없게 됩니다. 또한이에 대 한 요금이 부과 되지 않도록 하려면 암호화 범위를 사용 하지 않도록 설정 해야 합니다.

고객 관리 키는 키 자격 증명 모음에서 일시 삭제 및 제거 보호로 보호 되 고, 삭제 된 키는 해당 속성에 대해 정의 된 동작의 영향을 받습니다. 자세한 내용은 Azure Key Vault 설명서에서 다음 항목 중 하나를 참조 하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)
- [CLI에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> 암호화 범위를 삭제할 수는 없습니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
- [암호화 범위 만들기 및 관리](encryption-scope-manage.md)
- [Azure Storage 암호화를 위한 고객 관리 키](../common/customer-managed-keys-overview.md)
- [Azure Key Vault란?](../../key-vault/general/overview.md)