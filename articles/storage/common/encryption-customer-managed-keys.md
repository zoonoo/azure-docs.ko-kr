---
title: Azure Key Vault를 사용하여 고객 관리 키를 사용하여 계정 암호화 관리
titleSuffix: Azure Storage
description: 자체 암호화 키를 사용하여 저장소 계정의 데이터를 보호할 수 있습니다. 고객 관리 키를 지정하면 해당 키가 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리 키는 액세스 제어를 관리할 수 있는 더 큰 유연성을 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6b5712094b9821dfa041cd5ba8617e86f7231bde
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478013"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Azure 키 볼트를 사용하여 고객 관리 키를 사용하여 Azure 저장소 암호화 관리

자체 암호화 키를 사용하여 저장소 계정의 데이터를 보호할 수 있습니다. 고객 관리 키를 지정하면 해당 키가 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리 키는 액세스 제어를 관리할 수 있는 더 큰 유연성을 제공합니다.

Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 저장해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. 저장소 계정과 키 자격 증명 모음은 동일한 리전과 동일한 Azure Active Directory(Azure AD) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](../../key-vault/key-vault-overview.md)

## <a name="about-customer-managed-keys"></a>고객 관리 키 소개

다음 다이어그램에서는 Azure Storage에서 Azure Active Directory 및 Azure Key Vault를 사용하여 고객 관리 키를 사용하여 요청을 하는 방법을 보여 주며 있습니다.

![Azure 저장소에서 고객 관리 키가 작동하는 방식을 보여 주어 도표](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

다음 목록은 다이어그램의 번호가 매겨진 단계를 설명합니다.

1. Azure Key Vault 관리자는 저장소 계정과 연결된 관리되는 ID에 암호화 키에 대한 권한을 부여합니다.
2. Azure Storage 관리자는 저장소 계정에 대해 고객 관리 키로 암호화를 구성합니다.
3. Azure Storage는 저장소 계정과 연결된 관리되는 ID를 사용하여 Azure Active Directory를 통해 Azure 키 자격 증명에 대한 액세스를 인증합니다.
4. Azure Storage는 Azure 키 자격 증명 모음에서 고객 키를 사용 하 고 계정 암호화 키를 래핑합니다.
5. 읽기/쓰기 작업의 경우 Azure Storage는 암호화 및 암호 해독 작업을 수행하기 위해 계정 암호화 키의 래핑을 해제하기 위해 Azure Key Vault에 요청을 보냅니다.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>큐 및 테이블에 대한 고객 관리 키를 지원하는 계정 만들기

Queue 및 Table 서비스에 저장된 데이터는 저장소 계정에 대해 고객 관리 키가 활성화될 때 고객 관리 키로 자동으로 보호되지 않습니다. 이 보호에 포함할 저장소 계정을 만들 때 이러한 서비스를 선택적으로 구성할 수 있습니다.

큐 및 테이블에 대한 고객 관리 키를 지원하는 저장소 계정을 만드는 방법에 대한 자세한 내용은 [테이블 및 큐에 대해 고객 관리 키를 지원하는 계정 만들기를](account-encryption-key-create.md)참조하십시오.

Blob 및 File 서비스의 데이터는 저장소 계정에 대해 고객 관리 키가 구성될 때 항상 고객 관리 키로 보호됩니다.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>저장소 계정에 대한 고객 관리 키 사용

고객 관리 키는 기존 저장소 계정에서만 사용할 수 있습니다. 키 자격 증명 모음은 저장소 계정과 연결된 관리되는 ID에 키 권한을 부여하는 액세스 정책으로 프로비전되어야 합니다. 관리되는 ID는 저장소 계정을 만든 후에만 사용할 수 있습니다.

고객 관리 키를 구성할 때 Azure Storage는 연결된 키 자격 증명 모음에서 고객 관리 키를 사용하여 계정에 대한 루트 데이터 암호화 키를 래핑합니다. 고객 관리 키를 사용하도록 설정해도 성능에 영향을 미치지 않으며 즉시 적용됩니다.

고객 관리 키를 활성화 또는 비활성화하거나, 키 버전을 업데이트하거나, 다른 키를 지정하여 Azure Storage 암호화에 사용되는 키를 수정할 때 루트 키의 암호화가 변경되지만 Azure Storage 계정의 데이터를 다시 암호화할 필요는 없습니다.

고객 관리 키를 사용 하거나 비활성화 하거나 키 또는 키 버전을 수정할 때 루트 암호화 키의 보호 변경 하지만 Azure Storage 계정의 데이터를 다시 암호화 할 필요가 없습니다.

Azure 저장소 암호화에 대 한 Azure 키 자격 증명 모음을 사용 하 여 고객 관리 키를 사용 하는 방법을 알아보려면 다음 문서 중 하나를 참조 하십시오.

- [Azure 포털에서 Azure 저장소 암호화에 대한 키 자격 증명 모음으로 고객 관리 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure 저장소 암호화에 대한 키 자격 증명 모음으로 고객 관리 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure 저장소 암호화에 대한 키 자격 증명 모음으로 고객 관리 키 구성](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 고객 관리 키는 Azure AD의 기능인 Azure 리소스에 대해 관리되는 ID를 사용합니다. 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure 포털에서 고객 관리 키를 구성하면 관리되는 ID가 저장소 계정에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 저장소 계정을 한 Azure AD 디렉터리에서 다른 Azure AD 디렉터리로 이동하면 저장소 계정과 연결된 관리되는 ID가 새 테넌트로 전송되지 않으므로 고객 관리 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 **FAQ의 Azure AD 디렉터리 간의 구독 전송** 및 Azure [리소스에 대한 관리ID에 대한 알려진 문제를](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)참조하십시오.  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure 키 볼트에 고객 관리 키 저장

저장소 계정에서 고객 관리 키를 사용하려면 Azure Key Vault를 사용하여 키를 저장해야 합니다. 키 자격 증명 모음에서 **소프트 삭제** 및 삭제 **안 함을** 모두 사용하도록 설정해야 합니다.

Azure 저장소 암호화를 통해 2048비트 RSA 및 RSA-HSM 키만 지원됩니다. 키에 대한 자세한 내용은 Azure [키 볼트 정보 키, 비밀 및 인증서의](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)키 자격 증명 모음 **키를** 참조하십시오.

## <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리 키를 회전할 수 있습니다. 키가 회전되면 새 키 버전 URI를 사용하려면 저장소 계정을 업데이트해야 합니다. Azure 포털에서 키의 새 버전을 사용하도록 저장소 계정을 업데이트하는 방법을 알아보려면 [Azure 포털을 사용하여 Azure 저장소에 대한 고객 관리 키 구성의](storage-encryption-keys-portal.md) **키 버전 업데이트** 섹션을 참조하십시오.

키를 회전해도 저장소 계정의 데이터 재암호화가 트리거되지 않습니다. 사용자에게 필요한 추가 작업이 없습니다.

## <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대한 액세스 취소

언제든지 고객 관리 키에 대한 저장소 계정의 액세스를 취소할 수 있습니다. 고객 관리 키에 대한 액세스가 취소되거나 키가 비활성화되거나 삭제된 후에는 클라이언트가 Blob 또는 해당 메타데이터에서 읽거나 쓰는 작업을 호출할 수 없습니다. 다음 작업 중 한 개만 호출하려는 시도는 모든 사용자에 대해 오류 코드 403(금지됨)으로 실패합니다.

- [Blob 을 나열합니다.](/rest/api/storageservices/list-blobs) `include=metadata`
- [Blob 가져오기](/rest/api/storageservices/get-blob)
- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [스냅숏 Blob](/rest/api/storageservices/snapshot-blob)- `x-ms-meta-name` 요청 헤더로 호출될 때
- [복사 Blob](/rest/api/storageservices/copy-blob)
- [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url)
- [BLOB 계층 설정](/rest/api/storageservices/set-blob-tier)
- [블록 배치](/rest/api/storageservices/put-block)
- [URL에서 블록 넣기](/rest/api/storageservices/put-block-from-url)
- [추가 블록](/rest/api/storageservices/append-block)
- [URL에서 블록 을 부화](/rest/api/storageservices/append-block-from-url)
- [Blob 배치](/rest/api/storageservices/put-blob)
- [페이지 배치](/rest/api/storageservices/put-page)
- [URL에서 페이지 넣기](/rest/api/storageservices/put-page-from-url)
- [증분 복사 Blob](/rest/api/storageservices/incremental-copy-blob)

이러한 작업을 다시 호출하려면 고객 관리 키에 대한 액세스를 복원합니다.

이 섹션에 나열되지 않은 모든 데이터 작업은 고객 관리 키가 해지되거나 키가 비활성화되거나 삭제된 후에 계속 될 수 있습니다.

고객 관리 키에 대한 액세스를 취소하려면 [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) 또는 [Azure CLI를](storage-encryption-keys-cli.md#revoke-customer-managed-keys)사용합니다.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure 관리 디스크에 대한 고객 관리 키

Azure 관리 디스크의 암호화를 관리하는 데도 고객 관리 키를 사용할 수 있습니다. 고객 관리 키는 관리되는 디스크에 대해 Azure Storage 리소스와 다르게 행동합니다. 자세한 내용은 Windows용 [Azure 관리 디스크의 서버 측 암호화](../../virtual-machines/windows/disk-encryption.md) 또는 Linux용 Azure 관리 [디스크의 서버 측 암호화를](../../virtual-machines/linux/disk-encryption.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure 포털에서 Azure 저장소 암호화에 대한 키 자격 증명 모음으로 고객 관리 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure 저장소 암호화에 대한 키 자격 증명 모음으로 고객 관리 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure 저장소 암호화에 대한 키 자격 증명 모음으로 고객 관리 키 구성](storage-encryption-keys-cli.md)
- [미사용 데이터에 대한 Azure 저장소 암호화](storage-service-encryption.md)
