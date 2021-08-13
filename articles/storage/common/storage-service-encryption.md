---
title: 미사용 데이터에 대한 Azure Storage 암호화
description: Azure Storage는 데이터를 자동으로 암호화한 후 클라우드에 보관하여 보호합니다. Microsoft 관리 키를 스토리지 계정의 데이터 암호화에 사용하거나 고유한 키를 사용하여 암호화를 관리할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 05/11/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: efc1d1567da8a181ca8de336bf26729e3717729b
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790778"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>미사용 데이터에 대한 Azure Storage 암호화

Azure Storage는 SSE(서버 측 암호화)를 사용하여 데이터가 클라우드에 유지될 때 데이터를 자동으로 암호화합니다. Azure Storage 암호화는 데이터를 보호하고 조직의 보안과 규정 준수 노력에 부합하는 데 도움이 됩니다.

## <a name="about-azure-storage-encryption"></a>Azure Storage 암호화 정보

Azure Storage의 데이터는 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 투명하게 암호화 및 암호 해독되며 FIPS 140-2를 준수합니다. Azure Storage 암호화는 Windows의 BitLocker 암호화와 비슷합니다.

Azure Storage 암호화는 Resource Manager 및 클래식 스토리지 계정을 비롯한 모든 스토리지 계정에 사용됩니다. Azure Storage 암호화를 해제할 수 없습니다. 데이터가 기본적으로 보호되므로 Azure Storage 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

스토리지 계정의 데이터는 성능 계층(표준 또는 프리미엄), 액세스 계층(핫 또는 쿨) 또는 배포 모델(Azure Resource Manager 또는 클래식)에 관계없이 암호화됩니다. 보관 계층의 모든 BLOB도 암호화됩니다. 모든 Azure Storage 중복 옵션은 암호화를 지원 하며 지역 복제를 사용하면 기본 지역과 보조 지역의 모든 데이터가 암호화됩니다. BLOB, 디스크, 파일, 큐 및 테이블 등 모든 Azure Storage 리소스가 암호화됩니다. 모든 개체 메타데이터도 암호화됩니다. Azure Storage 암호화에 따른 추가 비용은 없습니다.

2017년 10월 20일 이후에 Azure Storage로 작성된 모든 블록 BLOB, 추가 BLOB 또는 페이지 BLOB이 암호화됩니다. 이 날짜 이전에 생성된 BLOB은 백그라운드 프로세스에서 계속 암호화됩니다. 2017년 10월 20일 이전에 생성된 BLOB 암호화를 강제 적용하려면 BLOB을 다시 작성하면 됩니다. BLOB 암호화 상태를 확인하는 방법은 [BLOB 암호화 상태 확인](../blobs/storage-blob-encryption-status.md)을 참조하세요.

암호화 모듈의 기본 Azure Storage 암호화에 대한 자세한 내용은 [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal)을 참조하세요.

Azure 관리 디스크의 암호화 및 키 관리는 [Azure 관리 디스크의 서버 측 암호화](../../virtual-machines/disk-encryption.md)를 참조하세요.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 새 스토리지 계정의 데이터는 Microsoft 관리 키로 암호화됩니다. Microsoft 관리 키를 데이터 암호화에 계속 사용하거나 고유한 키를 사용하여 암호화를 관리할 수도 있습니다. 고유한 키로 암호화를 관리하는 경우 두 가지 옵션이 있습니다. 두 가지 키 관리 유형 중 하나나 둘 다 사용할 수 있습니다.

- BLOB 스토리지와 Azure Files에서 데이터를 암호화 및 암호 해독하는 데 사용할 *고객 관리형 키* 를 지정할 수 있습니다.<sup>1,2</sup> 고객 관리형 키를 Azure Key Vault 또는 Azure Key Vault 관리형 HSM(하드웨어 보안 모델)(미리 보기)에 저장해야 합니다. 고객 관리형 키에 대한 자세한 내용은 [Azure Storage 암호화에 고객 관리형 키 사용](./customer-managed-keys-overview.md)을 참조하세요.
- BLOB 스토리지 작업에서 *고객이 제공한 키* 를 지정할 수 있습니다. BLOB 스토리지에 대해 읽기 또는 쓰기 요청을 전송하는 클라이언트는 BLOB 데이터를 암호화하고 암호 해독하는 방법을 세부적으로 제어할 수 있는 요청에 암호화 키를 포함할 수 있습니다. 고객 제공 키에 대한 자세한 내용은 [BLOB 스토리지에 요청 시 암호화 키 제공](../blobs/encryption-customer-provided-keys.md)을 참조하세요.

다음 표에서는 Azure Storage 암호화의 키 관리 옵션을 비교합니다.

| 키 관리 매개 변수 | Microsoft 관리형 키 | 고객 관리형 키 | 고객 제공 키 |
|--|--|--|--|
| 암호화/암호 해독 작업 | Azure | Azure | Azure |
| 지원되는 Azure Storage 서비스 | 모두 | BLOB 스토리지, Azure Files<sup>1,2</sup> | Blob Storage |
| 키 스토리지 | Microsoft 키 저장소 | Azure Key Vault 또는 Key Vault HSM | 고객의 고유한 키 저장소 |
| 키 회전 책임 | Microsoft | 고객 | 고객 |
| 키 제어 | Microsoft | 고객 | 고객 |

<sup>1</sup> 큐 스토리지에서 고객 관리형 키 사용을 지원하는 계정을 만드는 방법은 [큐에 고객 관리형 키를 지원하는 계정 만들기](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조하세요.<br />
<sup>2</sup> 테이블 스토리지에서 고객 관리형 키 사용을 지원하는 계정을 만드는 방법은 [테이블에 고객 관리형 키를 지원하는 계정 만들기](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)를 참조하세요.

> [!NOTE]
> Microsoft 관리 키는 규정 준수 요구 사항에 따라 적절하게 회전됩니다. 특정 키 회전 요구 사항이 있는 경우 회전을 직접 관리하고 감사할 수 있도록 고객 관리형 키로 이동하는 것이 좋습니다.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>인프라 암호화를 사용하여 데이터 이중 암호화

데이터가 안전하다는 높은 수준의 보증을 요구하는 고객은 Azure Storage 인프라 수준에서 256비트 AES 암호화를 사용할 수도 있습니다. 인프라 암호화를 사용하도록 설정하면 스토리지 계정의 데이터가 다른 암호화 알고리즘 2개와 다른 키 2개를 사용하여 두 번(서비스 수준에서 한 번, 인프라 수준에서 한 번) 암호화됩니다. Azure Storage 데이터의 이중 암호화는 암호화 알고리즘 또는 키 중 하나가 손상될 수 있는 시나리오로부터 보호합니다. 이 시나리오에서 추가 암호화 계층은 계속해서 데이터를 보호합니다.

서비스 수준 암호화는 Azure Key Vault에서 Microsoft 관리형 키나 고객 관리형 키 사용을 지원합니다. 인프라 수준 암호화는 Microsoft 관리 키를 기반으로 하며 항상 별도의 키를 사용합니다.

인프라 암호화를 사용하는 스토리지 계정을 만드는 방법에 대한 자세한 내용은 [데이터 이중 암호화에 사용되는 인프라 암호화로 스토리지 계정 만들기](infrastructure-encryption-enable.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../../key-vault/general/overview.md)
- [Azure Storage 암호화용 고객 관리형 키](customer-managed-keys-overview.md)
- [BLOB 스토리지의 암호화 범위](../blobs/encryption-scope-overview.md)
- [Blob 스토리지에 요청에 대한 암호화 키 제공](../blobs/encryption-customer-provided-keys.md)
