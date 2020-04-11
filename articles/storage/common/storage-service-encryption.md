---
title: 미사용 데이터에 대한 Azure 저장소 암호화
description: Azure Storage는 데이터를 클라우드에 유지하기 전에 자동으로 암호화하여 데이터를 보호합니다. 저장소 계정의 데이터 암호화를 위해 Microsoft에서 관리하는 키를 사용하거나 자신의 키로 암호화를 관리할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f112a4523bc5af9ecae57e93dfb90795d3fe9c50
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113270"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>미사용 데이터에 대한 Azure 저장소 암호화

Azure Storage는 데이터가 클라우드에 유지되면 자동으로 암호화됩니다. Azure Storage 암호화는 데이터를 보호하고 조직 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다.

## <a name="about-azure-storage-encryption"></a>Azure 저장소 암호화 정보

Azure Storage의 데이터는 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 [AES 암호화를](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)사용하여 투명하게 암호화되고 해독되며 FIPS 140-2를 준수합니다. Azure 저장소 암호화는 Windows의 BitLocker 암호화와 유사합니다.

Azure 저장소 암호화는 리소스 관리자 및 클래식 저장소 계정을 모두 포함한 모든 저장소 계정에 대해 활성화됩니다. Azure 저장소 암호화를 사용할 수 없습니다. 데이터는 기본적으로 보호되므로 Azure Storage 암호화를 활용하기 위해 코드 나 응용 프로그램을 수정할 필요가 없습니다.

저장소 계정의 데이터는 성능 계층(표준 또는 프리미엄), 액세스 계층(핫 또는 쿨) 또는 배포 모델(Azure 리소스 관리자 또는 클래식)에 관계없이 암호화됩니다. 아카이브 계층의 모든 Blob도 암호화됩니다. 모든 Azure Storage 중복 옵션은 암호화를 지원하며, 지역 복제가 활성화되면 기본 및 보조 지역의 모든 데이터가 암호화됩니다. Blob, 디스크, 파일, 큐 및 테이블을 포함하여 모든 Azure Storage 리소스가 암호화됩니다. 모든 개체 메타데이터도 암호화됩니다. Azure 저장소 암호화에 대한 추가 비용은 없습니다.

2017년 10월 20일 이후에 Azure Storage에 작성된 모든 블록 Blob, 부속 Blob 또는 페이지 Blob은 암호화됩니다. 이 날짜 이전에 생성된 Blob은 백그라운드 프로세스에 의해 계속 암호화됩니다. 2017년 10월 20일 이전에 생성된 Blob의 암호화를 강제로 만들려면 Blob을 다시 작성할 수 있습니다. Blob의 암호화 상태를 확인하는 방법을 알아보려면 [Blob의 암호화 상태 확인을](../blobs/storage-blob-encryption-status.md)참조하십시오.

Azure Storage 암호화의 기본 암호화 모듈에 대한 자세한 내용은 [암호화 API: 차세대](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)를 참조하십시오.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

새 저장소 계정의 데이터는 Microsoft에서 관리하는 키로 암호화됩니다. 데이터 암호화를 위해 Microsoft에서 관리하는 키를 사용하거나 자체 키로 암호화를 관리할 수 있습니다. 자신의 키로 암호화를 관리하도록 선택한 경우 다음 두 가지 옵션이 있습니다.

- Blob 저장소 및 Azure 파일에서 데이터를 암호화하고 해독하는 데 사용할 Azure Key Vault를 사용하여 *고객 관리 키를* 지정할 수 있습니다. <sup>1,2</sup> 고객 관리 키에 대한 자세한 내용은 [Azure Key Vault에서 고객 관리 키 사용을 사용하여 Azure Storage 암호화를 관리합니다.](encryption-customer-managed-keys.md)
- Blob 저장소 작업에 *고객이 제공한 키를* 지정할 수 있습니다. Blob 저장소에 대해 읽기 또는 쓰기 요청을 하는 클라이언트에는 Blob 데이터가 암호화되고 해독되는 방법을 세부적으로 제어하기 위한 요청에 암호화 키가 포함될 수 있습니다. 고객이 제공한 키에 대한 자세한 내용은 [Blob 저장소에 대한 요청에 대한 암호화 키 제공(미리 보기)을](encryption-customer-provided-keys.md)참조하십시오.

다음 표는 Azure Storage 암호화에 대한 주요 관리 옵션을 비교합니다.

|                                        |    마이크로소프트에서 관리되는 키                             |    고객 관리형 키                                                                                                                        |    고객이 제공한 키                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    암호화/암호 해독 작업    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    지원되는 Azure 저장소 서비스    |    모두                                                |    Blob 저장소, Azure 파일<sup>1,2</sup>                                                                                                               |    Blob Storage                                                                  |
|    키 스토리지                         |    마이크로소프트 키 스토어    |    Azure Key Vault                                                                                                                              |    Azure 키 볼트 또는 기타 키 저장소                                                                 |
|    키 회전 책임         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    키 제어                          |    Microsoft                                     |    Customer                                                                                                                    |    Customer                                                                 |

<sup>1</sup> Queue 저장소를 사용하여 고객 관리 키를 사용하는 데 지원하는 계정을 만드는 것에 대한 자세한 내용은 [큐에 대한 고객 관리 키를 지원하는 계정 만들기를](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)참조하십시오.<br />
<sup>2</sup> Table 저장소를 사용하여 고객 관리 키를 사용하는 데 지원하는 계정을 만드는 것에 대한 자세한 내용은 [테이블에 대한 고객 관리 키를 지원하는 계정 만들기를](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../../key-vault/key-vault-overview.md)
- [Azure Portal에서 Azure Storage 암호화를 위해 고객이 관리하는 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화용 고객 관리형 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화용 고객 관리형 키 구성](storage-encryption-keys-cli.md)
