---
title: Azure Key Vault에서 고객이 관리 하는 키를 사용 하 여 계정 암호화 관리
titleSuffix: Azure Storage
description: 사용자 고유의 암호화 키를 사용 하 여 저장소 계정에서 데이터를 보호할 수 있습니다. 고객 관리 키를 지정 하는 경우 해당 키는 데이터를 암호화 하는 키에 대 한 액세스를 보호 하 고 제어 하는 데 사용 됩니다. 고객 관리 키를 사용 하면 더 많은 유연성을 제공 하 여 액세스 제어를 관리할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 07/20/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d53818c91d32bc7435d1328c2ae73a8eb3172cd4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029793"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Azure Key Vault에서 고객이 관리 하는 키를 사용 하 여 Azure Storage 암호화 관리

사용자 고유의 암호화 키를 사용 하 여 저장소 계정에서 데이터를 보호할 수 있습니다. 고객 관리 키를 지정 하는 경우 해당 키는 데이터를 암호화 하는 키에 대 한 액세스를 보호 하 고 제어 하는 데 사용 됩니다. 고객 관리 키를 사용 하면 더 많은 유연성을 제공 하 여 액세스 제어를 관리할 수 있습니다.

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. 저장소 계정 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](../../key-vault/general/overview.md)를 참조 하세요.

## <a name="about-customer-managed-keys"></a>고객 관리형 키 정보

다음 다이어그램에서는 Azure Storage Azure Active Directory 및 Azure Key Vault를 사용 하 여 고객이 관리 하는 키를 사용 하 여 요청 하는 방법을 보여 줍니다.

![에서 고객이 관리 하는 키가 작동 하는 방법을 보여 주는 다이어그램 Azure Storage](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

다음은 다이어그램의 번호가 매겨진 단계에 대해 설명하는 목록입니다.

1. Azure Key Vault 관리자는 저장소 계정과 연결 된 관리 id에 암호화 키에 대 한 사용 권한을 부여 합니다.
2. Azure Storage 관리자는 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 구성 합니다.
3. Azure Storage는 저장소 계정과 연결 된 관리 되는 id를 사용 하 여 Azure Active Directory를 통해 Azure Key Vault에 대 한 액세스를 인증 합니다.
4. Azure Storage은 Azure Key Vault의 고객 키를 사용 하 여 계정 암호화 키를 래핑합니다.
5. 읽기/쓰기 작업의 경우 Azure Storage는 Azure Key Vault에 대 한 요청을 보내 암호화 및 암호 해독 작업을 수행 하기 위해 계정 암호화 키를 래핑 해제 합니다.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>큐 및 테이블에 대 한 고객 관리 키를 지 원하는 계정 만들기

저장소 계정에 대해 고객이 관리 하는 키를 사용 하는 경우 큐 및 테이블 서비스에 저장 된 데이터는 고객 관리 키로 자동으로 보호 되지 않습니다. 이 보호에 포함할 저장소 계정을 만들 때 필요에 따라 이러한 서비스를 구성할 수 있습니다.

큐 및 테이블에 대 한 고객 관리 키를 지 원하는 저장소 계정을 만드는 방법에 대 한 자세한 내용은 [테이블 및 큐에 대 한 고객 관리 키를 지 원하는 계정 만들기](account-encryption-key-create.md)를 참조 하세요.

Blob 및 파일 서비스의 데이터는 고객 관리 키가 저장소 계정에 대해 구성 된 경우 항상 고객이 관리 하는 키에 의해 보호 됩니다.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>저장소 계정에 대해 고객이 관리 하는 키 사용

고객 관리 키를 구성 하는 경우 연결 된 키 자격 증명 모음에서 고객 관리 키를 사용 하 여 계정에 대 한 루트 데이터 암호화 키를 Azure Storage 래핑합니다. 고객 관리 키를 사용 하도록 설정 해도 성능에 영향을 주지 않으며 즉시 적용 됩니다.

고객 관리 키를 사용 하거나 사용 하지 않도록 설정 하거나, 키 또는 키 버전을 수정할 때 루트 암호화 키의 보호는 변경 되지만 Azure Storage 계정의 데이터는 다시 암호화 하지 않아도 됩니다.

고객 관리 키는 기존 저장소 계정 에서만 사용할 수 있습니다. 저장소 계정과 연결 된 관리 되는 id에 대 한 권한을 부여 하는 액세스 정책을 사용 하 여 key vault를 구성 해야 합니다. 관리 ID는 스토리지 계정이 만들어진 후에만 사용할 수 있습니다.

언제 든 지 고객이 관리 하는 키와 Microsoft 관리 키를 전환할 수 있습니다. Microsoft 관리 키에 대 한 자세한 내용은 [암호화 키 관리](storage-service-encryption.md#about-encryption-key-management)정보를 참조 하세요.

Azure Storage 암호화를 위해 Azure Key Vault에서 고객이 관리 하는 키를 사용 하는 방법을 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Azure Portal에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 고객 관리 키는 Azure AD의 기능인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에서 고객 관리 키를 구성 하는 경우 관리 id는 저장소 계정에 자동으로 할당 됩니다. 이후에 구독, 리소스 그룹 또는 저장소 계정을 Azure AD 디렉터리 간에 이동 하는 경우 저장소 계정과 연결 된 관리 id는 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 Faq에서 **AZURE AD 디렉터리 간 구독 전송** [및 azure 리소스에 대 한 관리 id의 알려진 문제](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)를 참조 하세요.  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객이 관리 하는 키 저장

저장소 계정에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 Azure key vault를 사용 하 여 키를 저장 해야 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 모두 사용 하도록 설정 해야 합니다.

Azure storage 암호화는 2048, 3072 및 4096 크기의 RSA 및 RSA HSM 키를 지원 합니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)정보를 참조 하세요.

Azure Key Vault 사용에는 비용이 수반 됩니다. 자세한 내용은 [Key Vault 가격 책정](/pricing/details/key-vault/)을 참조 하세요.

## <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리형 키를 순환할 수 있습니다. 고객 관리 키를 회전 하는 두 가지 옵션이 있습니다.

- **자동 회전:** 고객 관리 키의 자동 회전을 구성 하려면 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 사용 하도록 설정할 때 키 버전을 생략 합니다. 키 버전을 생략하면 Azure Storage가 매일 Azure Key Vault에서 새 버전의 고객 관리 키를 확인합니다. 새 키 버전을 사용할 수 있는 경우 Azure Storage는 자동으로 최신 버전의 키를 사용합니다.
- **수동 회전:** Azure Storage 암호화에 특정 키 버전을 사용 하려면 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 사용 하도록 설정 하는 경우 해당 키 버전을 지정 합니다. 키 버전을 지정 하는 경우 키 버전을 수동으로 업데이트할 때까지 Azure Storage에서 해당 버전을 암호화에 사용 합니다.

    키를 수동으로 회전 하는 경우 새 키 버전 URI를 사용 하도록 저장소 계정을 업데이트 해야 합니다. Azure Portal에서 새 버전의 키를 사용 하도록 저장소 계정을 업데이트 하는 방법을 알아보려면 [키 버전 수동 업데이트](storage-encryption-keys-portal.md#manually-update-the-key-version)를 참조 하세요.

고객 관리 키를 회전 해도 저장소 계정의 데이터에 대 한 다시 암호화는 트리거되지 않습니다. 사용자에 게 필요한 추가 작업은 없습니다.

## <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대 한 액세스 취소

언제 든 지 고객 관리 키에 대 한 저장소 계정의 액세스 권한을 해지할 수 있습니다. 고객 관리 키에 대 한 액세스가 취소 되거나 키가 사용 되지 않도록 설정 되거나 삭제 된 후에 클라이언트는 blob 또는 해당 메타 데이터에서 읽거나 쓰는 작업을 호출할 수 없습니다. 모든 사용자에 대해 다음 작업을 호출 하려고 하면 실패 하 고 오류 코드 403 (사용할 수 없음)이 발생 합니다.

- [List Blobs](/rest/api/storageservices/list-blobs) `include=metadata` 요청 URI에서 매개 변수를 사용 하 여 호출 하는 경우 blob을 나열 합니다.
- [Blob 가져오기](/rest/api/storageservices/get-blob)
- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [스냅숏 Blob](/rest/api/storageservices/snapshot-blob), 요청 헤더를 사용 하 여 호출 되는 경우 `x-ms-meta-name`
- [Blob 복사](/rest/api/storageservices/copy-blob)
- [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url)
- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)
- [블록 배치](/rest/api/storageservices/put-block)
- [URL에서 블록 배치](/rest/api/storageservices/put-block-from-url)
- [추가 블록](/rest/api/storageservices/append-block)
- [URL의 블록 추가](/rest/api/storageservices/append-block-from-url)
- [Blob 배치](/rest/api/storageservices/put-blob)
- [페이지 배치](/rest/api/storageservices/put-page)
- [URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url)
- [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob)

이러한 작업을 다시 호출 하려면 고객이 관리 하는 키에 대 한 액세스를 복원 합니다.

이 섹션에 나열 되지 않은 모든 데이터 작업은 고객이 관리 하는 키가 해지 되거나 키가 비활성화 되거나 삭제 된 후에도 계속 될 수 있습니다.

고객 관리 키에 대 한 액세스를 취소 하려면 [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) 또는 [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys)를 사용 합니다.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure managed disks에 대 한 고객 관리 키

고객 관리 키를 사용 하 여 Azure managed disks의 암호화를 관리할 수도 있습니다. 고객 관리 키는 Azure Storage 리소스 보다는 관리 디스크에서 다르게 작동 합니다. 자세한 내용은 Windows 용 [azure managed disks의 서버 쪽 암호화](../../virtual-machines/windows/disk-encryption.md) 또는 Linux 용 [azure Managed disks의 서버 쪽 암호화](../../virtual-machines/linux/disk-encryption.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-cli.md)
- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
