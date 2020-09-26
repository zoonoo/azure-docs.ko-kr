---
title: Blob 저장소의 암호화 범위 (미리 보기)
description: 암호화 범위는 컨테이너 또는 개별 blob의 수준에서 암호화를 관리 하는 기능을 제공 합니다. 암호화 범위를 사용 하 여 동일한 저장소 계정에 있지만 다른 고객에 게 속한 데이터 간에 보안 경계를 만들 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 09/22/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 6fb3c9b6dbbab036ddb00edd7e1d5980bb425ebe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326119"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Blob 저장소의 암호화 범위 (미리 보기)

암호화 범위는 컨테이너 또는 개별 blob의 수준에서 암호화를 관리 하는 기능을 제공 합니다. 암호화 범위를 사용 하 여 동일한 저장소 계정에 있지만 다른 고객에 게 속한 데이터 간에 보안 경계를 만들 수 있습니다.

기본적으로 저장소 계정은 전체 저장소 계정으로 범위가 지정 된 키를 사용 하 여 암호화 됩니다. 암호화 범위를 사용 하 여 하나 이상의 컨테이너가 해당 컨테이너에만 한정 되는 키로 암호화 되도록 지정할 수 있습니다.

Microsoft 관리 키 또는 Azure Key Vault에 저장 된 고객 관리 키를 사용 하 여 데이터를 암호화 하는 키에 대 한 액세스를 보호 하 고 제어 하도록 선택할 수 있습니다. 동일한 저장소 계정에 있는 다양 한 암호화 범위는 Microsoft에서 관리 하거나 고객이 관리 하는 키 중 하나를 사용할 수 있습니다.

암호화 범위를 만든 후에는 컨테이너 또는 blob를 만들도록 요청에 대 한 암호화 범위를 지정할 수 있습니다. 암호화 범위를 만드는 방법에 대 한 자세한 내용은 [암호화 범위 만들기 및 관리 (미리 보기)](encryption-scope-manage.md)를 참조 하세요.

> [!NOTE]
> 미리 보기 중에는 읽기 액세스 지역 중복 저장소 (RA-GRS) 또는 읽기 액세스 지역 중복 저장소 (RA-GZRS) 계정에서 암호화 범위가 지원 되지 않습니다.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> 암호화 범위 미리 보기는 비프로덕션 용도로만 사용 됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.
>
> 예기치 않은 비용을 방지 하려면 현재 필요 하지 않은 모든 암호화 범위를 사용 하지 않도록 설정 해야 합니다.

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>암호화 범위를 사용 하 여 컨테이너 또는 blob 만들기

암호화 범위 아래에 생성 된 blob는 해당 범위에 대해 지정 된 키로 암호화 됩니다. Blob을 만들 때 개별 blob에 대 한 암호화 범위를 지정 하거나, 컨테이너를 만들 때 기본 암호화 범위를 지정할 수 있습니다. 컨테이너 수준에서 기본 암호화 범위를 지정 하는 경우 해당 컨테이너의 모든 blob은 기본 범위와 연결 된 키로 암호화 됩니다.

기본 암호화 범위가 있는 컨테이너에서 blob을 만드는 경우 기본 암호화 범위를 재정의할 수 있도록 컨테이너가 구성 된 경우 기본 암호화 범위를 재정의 하는 암호화 범위를 지정할 수 있습니다. 기본 암호화 범위를 재정의 하지 않도록 하려면 컨테이너를 구성 하 여 개별 blob에 대 한 재정의를 거부 합니다.

암호화 범위에 속하는 blob에 대 한 읽기 작업은 암호화 범위를 사용 하지 않는 한 투명 하 게 수행 됩니다.

## <a name="disable-an-encryption-scope"></a>암호화 범위를 사용 하지 않도록 설정

암호화 범위를 사용 하지 않도록 설정 하면 HTTP 오류 코드 403 (사용할 수 없음)이 발생 하 고 암호화 범위를 사용 하 여 수행 된 이후의 읽기 또는 쓰기 작업이 실패 합니다. 암호화 범위를 다시 사용 하도록 설정 하면 읽기 및 쓰기 작업이 다시 정상적으로 진행 됩니다.

암호화 범위를 사용 하지 않도록 설정 하면 더 이상 요금이 청구 되지 않습니다. 불필요 한 요금을 방지 하는 데 필요 하지 않은 모든 암호화 범위를 사용 하지 않도록 설정 합니다.

암호화 범위가 고객 관리 키로 보호 되는 경우 암호화 범위를 사용 하지 않도록 설정 하기 위해 키 자격 증명 모음에서 연결 된 키를 삭제할 수도 있습니다. 고객 관리 키는 키 자격 증명 모음에서 일시 삭제 및 제거 보호로 보호 되 고, 삭제 된 키는 해당 속성에 대해 정의 된 동작의 영향을 받습니다. 자세한 내용은 Azure Key Vault 설명서에서 다음 항목 중 하나를 참조 하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../../key-vault/general/soft-delete-powershell.md)
- [CLI에서 일시 삭제를 사용하는 방법](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> 암호화 범위를 삭제할 수는 없습니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
- [암호화 범위 만들기 및 관리 (미리 보기)](encryption-scope-manage.md)
- [Azure Storage 암호화를 위한 고객 관리 키](../common/customer-managed-keys-overview.md)
- [Azure Key Vault란?](../../key-vault/general/overview.md)
