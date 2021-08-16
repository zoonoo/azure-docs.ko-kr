---
title: Azure Key Vault 관리형 HSM 일시 삭제 | Microsoft Docs
description: 관리형 HSM에서 일시 삭제를 사용하면 삭제된 HSM 인스턴스 및 키를 복구할 수 있습니다.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 06/01/2021
ms.openlocfilehash: 673260fb8b78cb08ccd8581862238dc2e0341362
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415401"
---
# <a name="managed-hsm-soft-delete-overview"></a>관리형 HSM 일시 삭제 개요

> [!IMPORTANT]
> 일시 삭제는 관리형 HSM 리소스에 대해 기본적으로 설정되어 있습니다. 이 기능은 해제할 수 없습니다.

> [!IMPORTANT]
> 일시 삭제된 관리형 HSM 리소스는 제거될 때까지 시간 단위로 계속 청구됩니다.

관리형 HSM의 일시 삭제 기능을 사용하면 삭제된 HSM 및 키를 복구할 수 있습니다. 특히 이 기능은 다음과 같은 보호를 제공합니다.

- HSM 또는 키가 삭제되면 7~90일의 구성 가능 기간에는 복구할 수 있습니다. HSM 생성 중에 보존 기간을 설정할 수 있습니다. 값을 지정하지 않으면 기본 복구 기간이 90일로 설정됩니다. 이를 통해 사용자는 실수로 인한 키 또는 HSM 삭제를 확인하고 대응할 수 있는 충분한 시간을 확보할 수 있습니다.
- 키를 영구적으로 삭제하려면 두 가지 작업을 수행해야 합니다. 첫 번째 작업은 사용자가 키를 삭제하여 일시 삭제된 상태로 만드는 것입니다. 두 번째 작업은 사용자가 일시 삭제된 상태인 키를 제거하는 것입니다. 제거 작업을 수행하려면 사용자에게 "관리형 HSM 암호화 책임자" 역할을 할당해야 합니다. 이러한 추가 보호 기능은 사용자가 실수로 또는 악의적으로 키나 HSM을 삭제할 위험을 줄일 수 있습니다.


## <a name="soft-delete-behavior"></a>일시 삭제 동작

관리형 HSM 일시 삭제의 경우 기본적으로 설정되어 있습니다. 일시 삭제를 사용하지 않도록 설정하면 관리형 HSM 리소스를 만들 수 없습니다.

일시 삭제를 사용하도록 설정하면 삭제된 것으로 표시된 리소스는 지정된 기간(기본값은 90일) 동안 보존됩니다. 이 서비스는 삭제된 HSM 또는 키를 복구하여 삭제가 실행 최소될 수 있는 메커니즘을 제공합니다.

기본 보존 기간은 90일이지만 HSM 리소스를 만드는 동안 보존 정책 간격을 7일에서 90일 사이의 값으로 설정할 수 있습니다. 제거 보호 보존 정책은 동일한 간격을 사용합니다. 설정되면 보존 정책 간격을 변경할 수 없습니다.

보존 기간이 경과되고 HSM 리소스가 제거될 때까지 일시 삭제된 HSM 리소스의 이름을 다시 사용할 수 없습니다.

## <a name="purge-protection"></a>제거 보호

제거 보호는 선택적 동작이며 이 릴리스에서는 **기본적으로 사용 설정되지 않습니다**. [CLI](./recovery.md?tabs=azure-cli)나 [PowerShell](./recovery.md?tabs=azure-powershell)을 통해 설정할 수 있습니다.

제거 보호를 설정하면 보존 기간이 지나기 전에는 삭제된 상태의 HSM이나 키를 제거할 수 없습니다. 일시 삭제한 HSM 및 키는 여전히 복구할 수 있으므로 보존 정책을 준수할 수 있습니다.

기본 보존 기간은 90일이지만 HSM을 만들 때 보존 정책 간격을 7일에서 90일 사이의 값으로 설정할 수 있습니다. 보존 정책 간격은 HSM을 만들 때만 설정할 수 있습니다. 나중에 변경할 수 없습니다.

[CLI에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-cli#managed-hsm-cli) 또는 [PowerShell에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-powershell#managed-hsm-powershell)을 참조하세요.

## <a name="managed-hsm-recovery"></a>관리형 HSM 복구

HSM을 삭제하면 서비스가 구독 아래에 프록시 리소스를 만들고 복구하는 데 충분한 메타데이터를 추가합니다. 프록시 리소스는 삭제된 HSM과 동일한 위치에서 사용할 수 있는 저장된 개체입니다. 

## <a name="key-recovery"></a>키 복구

키를 삭제하면 서비스가 키를 삭제된 상태로 배치하므로 작업에서 키를 액세스할 수 없게 됩니다. 이러한 상태에서 키는 나열, 복구, 제거(영구 삭제)만 가능합니다. 개체를 보려면 Azure CLI `az keyvault key list-deleted` 명령([CLI를 사용한 관리형 HSM 일시 삭제 및 제거 보호](./recovery.md?tabs=azure-cli#keys-cli)에 설명) 또는 Azure PowerShell `-InRemovedState` 매개 변수([PowerShell을 사용한 관리형 HSM 일시 삭제 및 제거 보호](./recovery.md?tabs=azure-powershell#keys-powershell))를 사용합니다.  

이와 동시에 관리형 HSM은 미리 정해진 보존 간격 후에 실행하기 위해 삭제된 HSM 또는 키에 해당하는 기본 데이터의 삭제를 예약합니다. HSM에 해당하는 DNS 레코드도 보존 간격 기간 동안 유지됩니다.

## <a name="soft-delete-retention-period"></a>일시 삭제 보존 기간

일시 삭제된 리소스는 설정된 기간인 90일 동안 유지됩니다. 일시 삭제 보존 간격 중에는 다음이 적용됩니다.

- 구독에 대한 일시 삭제 상태의 HSM 및 키를 모두 나열하고 해당 항목에 대한 삭제 및 복구 정보에 액세스할 수 있습니다.
  - 관리형 HSM 기여자 역할이 있는 사용자만 삭제된 HSM을 나열할 수 있습니다. 사용자가 삭제된 자격 증명 모음 처리를 위해 이러한 특수 사용 권한을 가진 사용자 지정 역할을 만드는 것이 좋습니다.
- 동일한 위치에 동일한 이름의 관리형 HSM을 만들 수는 없습니다. 마찬가지로 키에 삭제된 상태인 동일한 이름의 키가 포함된 경우에는 지정된 HSM에 키를 만들 수 없습니다.
- "관리형 HSM 기여자" 역할이 있는 사용자만 관리형 HSM을 나열, 확인, 복구 및 제거할 수 있습니다.
- "관리형 HSM 암호화 책임자" 역할이 있는 사용자만 키를 나열, 보기, 복구 및 제거할 수 있습니다.
  
관리형 HSM 또는 키를 복구하지 않으면 보존 간격이 끝난 후 서비스가 일시 삭제된 HSM 또는 키를 제거합니다. 리소스 삭제를 다시 예약하지 못할 수 있습니다.

### <a name="billing-implications"></a>요금 청구 영향

관리형 HSM은 단일 테넌트 서비스입니다. 관리형 HSM을 만들 때 서비스는 HSM에 할당된 기본 리소스를 예약합니다. 이러한 리소스는 HSM이 삭제된 상태에 있는 경우에도 할당된 상태를 유지합니다. 따라서 HSM이 삭제된 상태에 있는 동안 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

다음 두 가이드는 일시 삭제 사용에 대한 기본 사용 시나리오를 제공합니다.

- [PowerShell에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-powershell) 
- [CLI에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-cli)
