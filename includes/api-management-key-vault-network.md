---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491017"
---
#### <a name="requirements-for-key-vault-firewall"></a>Key Vault 방화벽에 대 한 요구 사항

키 자격 증명 모음에서 방화벽을 사용 하도록 설정 [Key Vault](../articles/key-vault/general/network-security.md) 경우 추가 요구 사항은 다음과 같습니다.

* 키 자격 증명 모음에 액세스 하려면 API Management 인스턴스의 **시스템 할당** 관리 id를 사용 해야 합니다.
* Key Vault 방화벽에서 **신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 우회 하도록 허용** 옵션을 사용 하도록 설정 합니다.

#### <a name="virtual-network-requirements"></a>가상 네트워크 요구 사항

API Management 인스턴스가 가상 네트워크에 배포 된 경우 다음 네트워크 설정도 구성 합니다.

* API Management 서브넷에 Azure Key Vault [서비스 끝점](../articles/key-vault/general/overview-vnet-service-endpoints.md) 을 사용 하도록 설정 합니다.
* AzureKeyVault 및 AzureActiveDirectory [service 태그로](../articles/virtual-network/service-tags-overview.md)의 아웃 바운드 트래픽을 허용 하도록 nsg (네트워크 보안 그룹) 규칙을 구성 합니다. 

자세한 내용은 [가상 네트워크에 연결](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)의 네트워크 구성 정보를 참조 하세요.