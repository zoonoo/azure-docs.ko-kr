---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c301ed2b612c2f3a7aca40ed5ed733800323adcc
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231047"
---
사용자 고유의 키를 사용하여 각 관리 디스크 수준에서 암호화를 관리할 수 있습니다. 고객 관리형 키를 사용하는 관리 디스크에 대한 서버 쪽 암호화는 Azure Key Vault와의 통합 환경을 제공합니다. [사용자의 RSA 키](../articles/key-vault/keys/hsm-protected-keys.md)를 Key Vault로 가져오거나 Azure Key Vault에서 새 RSA 키를 생성할 수 있습니다. 

Azure Managed Disks는 [봉투(Envelope) 암호화](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)를 사용하여 완전히 투명한 방식으로 암호화 및 암호 해독을 처리합니다. 키를 사용하여 보호되는 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 기반 DEK(데이터 암호화 키)를 사용하여 데이터를 암호화합니다. 스토리지 서비스는 데이터 암호화 키를 생성하고 RSA 암호화를 사용하여 고객 관리형 키로 이를 암호화합니다. 봉투(Envelope) 암호화를 사용하면 VM에 영향을 주지 않고 규정 준수 정책에 따라 정기적으로 키를 회전(변경)할 수 있습니다. 키를 회전하면 스토리지 서비스가 새로운 고객 관리형 키를 사용하여 데이터 암호화 키를 다시 암호화합니다. 

#### <a name="full-control-of-your-keys"></a>키에 대 한 모든 권한

DEK를 암호화 하 고 암호를 해독 하는 데 키를 사용 하려면 Key Vault에서 관리 디스크에 대 한 액세스 권한을 부여 해야 합니다. 이를 통해 데이터 및 키를 완전히 제어할 수 있습니다. 언제든지 키를 사용하지 않도록 설정하거나 관리 디스크에 대한 액세스를 취소할 수 있습니다. 또한 Azure Key Vault 모니터링을 통해 암호화 키 사용을 감사하여 관리 디스크 또는 다른 신뢰할 수 있는 Azure 서비스만 키에 액세스하도록 할 수 있습니다.

키를 사용 하지 않도록 설정 하거나 삭제 하면 해당 키를 사용 하는 디스크가 있는 모든 Vm이 자동으로 종료 됩니다. 그 후에 키를 다시 사용하도록 설정하지 않거나 새 키를 할당하지 않으면 VM을 사용할 수 없습니다.

프리미엄 SSD, 표준 SSD 및 표준 HDD의 경우: 키를 사용하지 않도록 설정하거나 삭제하면 해당 키를 사용하는 디스크가 있는 모든 VM이 자동으로 종료됩니다. 그 후에 키를 다시 사용하도록 설정하지 않거나 새 키를 할당하지 않으면 VM을 사용할 수 없습니다.    

Ultra disks의 경우: 키를 사용 하지 않도록 설정 하거나 삭제 하면 키를 사용 하는 울트라 디스크가 있는 모든 Vm이 자동으로 종료 되지 않습니다. VM의 할당을 취소하고 다시 시작한 후에는 디스크에서 키 사용이 중지되고 VM은 다시 온라인 상태가 되지 않습니다. VM을 다시 온라인 상태로 전환하려면 새 키를 할당하거나 기존 키를 사용하도록 설정해야 합니다.    

다음 다이어그램은 관리 디스크에서 고객 관리형 키를 사용하여 요청하기 위해 Azure Active Directory 및 Azure Key Vault를 사용하는 방법을 보여 줍니다.

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="관리 되는 디스크 및 고객이 관리 하는 키 워크플로. 관리자는 Azure Key Vault 만든 다음 디스크 암호화 집합을 만들고 디스크 암호화 집합을 설정 합니다. 집합은 디스크에서 Azure AD를 사용 하 여 인증할 수 있도록 하는 VM에 연결 되어 있습니다.":::

다음 목록에서는 다이어그램에 대해 보다 자세히 설명합니다.

1. Azure Key Vault 관리자가 Key Vault 리소스를 만듭니다.
1. Key Vault 관리자가 RSA 키를 Key Vault로 가져오거나 Key Vault에서 새 RSA 키를 생성합니다.
1. 관리자는 Azure Key Vault ID와 키 URL을 지정하는 디스크 암호화 집합 리소스의 인스턴스를 만듭니다. 디스크 암호화 집합은 관리 디스크에 대한 키 관리를 간소화하기 위해 도입된 새로운 리소스입니다. 
1. 디스크 암호화 집합이 만들어지면 [시스템이 할당한 관리 ID](../articles/active-directory/managed-identities-azure-resources/overview.md)가 Azure AD(Active Directory)에서 생성되고 디스크 암호화 집합과 연결됩니다. 
1. 그 다음, Azure Key Vault 관리자가 Key Vault에서 작업을 수행할 수 있는 관리 ID 권한을 부여합니다.
1. VM 사용자는 디스크 암호화 집합과 연결하여 디스크를 만듭니다. 또한 VM 사용자는 디스크 암호화 집합과 연결하여 기존 리소스에 대해 고객 관리형 키와 함께 서버 쪽 암호화를 사용하도록 설정할 수 있습니다. 
1. 관리 디스크는 관리 ID를 사용하여 Azure Key Vault에 요청을 보냅니다.
1. 데이터를 읽거나 쓰는 경우 관리 디스크는 데이터 암호화 및 암호 해독을 수행하기 위해 데이터 암호화 키를 암호화(래핑)하고 해독(래핑 해제)하는 요청을 Azure Key Vault로 보냅니다. 

고객 관리형 키에 대한 액세스를 취소하려면 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조하세요. 액세스를 취소하면 Azure Storage에서 암호화 키에 액세스할 수 없으므로 스토리지 계정의 모든 데이터에 대한 액세스가 효과적으로 차단됩니다.
