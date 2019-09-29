---
title: 휴지 상태의 데이터에 대 한 Azure Storage 암호화 | Microsoft Docs
description: Azure Storage은 데이터를 클라우드로 유지 하기 전에 자동으로 암호화 하 여 데이터를 보호 합니다. Azure Storage의 모든 데이터는 256 비트 AES 암호화를 사용 하 여 투명 하 게 암호화 되 고 해독 되며 FIPS 140-2 규격입니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673056"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>휴지 상태의 데이터에 대 한 암호화 Azure Storage

Azure Storage는 클라우드로 데이터를 유지할 때 자동으로 데이터를 암호화 합니다. 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. Azure Storage의 데이터는 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 하 여 투명 하 게 암호화 되 고 해독 되며, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며 FIPS 140-2 규격입니다. Azure Storage 암호화는 Windows의 BitLocker 암호화와 비슷합니다.

Azure Storage 암호화는 모든 신규 및 기존 저장소 계정에 대해 사용 하도록 설정 되어 있으며 사용 하지 않도록 설정할 수 없습니다. 기본적으로 데이터 보안을 유지 하기 때문에 Azure Storage 암호화를 활용 하기 위해 코드 또는 응용 프로그램을 수정할 필요가 없습니다. 

저장소 계정은 성능 계층 (표준 또는 프리미엄) 또는 배포 모델 (Azure Resource Manager 또는 클래식)에 관계 없이 암호화 됩니다. 모든 Azure Storage 중복성 옵션은 암호화를 지원 하 고 저장소 계정의 모든 복사본은 암호화 됩니다. Blob, 디스크, 파일, 큐 및 테이블을 포함 하 여 모든 Azure Storage 리소스가 암호화 됩니다. 모든 개체 메타 데이터도 암호화 됩니다.

암호화는 Azure Storage 성능에 영향을 주지 않습니다. Azure Storage 암호화에 대 한 추가 비용은 없습니다.

Azure Storage 암호화의 기본 암호화 모듈에 대 한 자세한 내용은 [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)을 참조하세요.

## <a name="key-management"></a>키 관리

Microsoft 관리 키를 사용 하 여 저장소 계정의 암호화를 사용 하거나, 사용자 고유의 키를 사용 하 여 암호화를 Azure Key Vault와 함께 관리할 수 있습니다.

### <a name="microsoft-managed-keys"></a>Microsoft에서 관리 하는 키

기본적으로 저장소 계정은 Microsoft에서 관리 하는 암호화 키를 사용 합니다. 다음 그림에 표시 된 것 처럼 [Azure Portal](https://portal.azure.com)의 **암호화** 섹션에서 저장소 계정에 대 한 암호화 설정을 확인할 수 있습니다.

![Microsoft에서 관리 하는 키로 암호화 된 계정 보기](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>고객 관리 키

고객 관리 키를 사용 하 여 Azure Storage 암호화를 관리할 수 있습니다. 고객 관리 키를 통해 액세스 제어를 보다 유연 하 게 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있습니다. 데이터를 보호 하는 데 사용 되는 암호화 키를 감사할 수도 있습니다. 

Azure Key Vault를 사용 하 여 키를 관리 하 고 키 사용을 감사 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다. 스토리지 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](../../key-vault/key-vault-overview.md)를 참조 하세요.

고객 관리 키에 대 한 액세스를 취소 하려면 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조 하세요. 액세스를 취소 하면 Azure Storage 의해 암호화 키에 액세스할 수 없으므로 저장소 계정의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.

Azure Storage에서 고객 관리 키를 사용 하는 방법을 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Azure Portal에서 Azure Storage 암호화를 위해 고객이 관리하는 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화용 고객 관리형 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화를 사용 하 여 고객 관리 키 사용](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 고객 관리 키는 azure AD (Azure Active Directory 기능) 인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 한 Azure AD 디렉터리에서 다른 Azure AD 디렉터리로 구독을 전송 하는 경우 관리 id는 업데이트 되지 않으며 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 Faq에서 **AZURE AD 디렉터리 간 구독 전송** [및 azure 리소스에 대 한 관리 id의 알려진 문제](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)를 참조 하세요.  

> [!NOTE]  
> [Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md)에는 고객 관리 키가 지원 되지 않습니다.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage 암호화 및 디스크 암호화

Azure Storage 암호화를 사용 하 여 Azure 가상 머신 디스크를 반환 하는 페이지 blob을 포함 하 여 모든 Azure Storage 계정 및 포함 된 리소스가 암호화 됩니다. 또한 Azure 가상 머신 디스크는 [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md)로 암호화 될 수 있습니다. Azure Disk Encryption는 Windows에서 업계 표준 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 를 사용 하 고 Linux에서 [DM-](https://en.wikipedia.org/wiki/Dm-crypt) 컴퓨터를 사용 하 여 Azure Key Vault와 통합 된 운영 체제 기반 암호화 솔루션을 제공 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 키 자격 증명 모음이란?](../../key-vault/key-vault-overview.md)
