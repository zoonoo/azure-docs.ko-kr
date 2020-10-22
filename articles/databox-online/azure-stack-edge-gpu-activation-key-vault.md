---
title: Azure Stack에 지 리소스 및 장치 활성화와 Azure Key Vault 통합
description: Azure Stack Edge Pro 장치를 활성화 하는 동안 Azure Key Vault 비밀 관리와 연결 되는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
ms.openlocfilehash: 8957d8982a3bfe1da2811dc10d0c3e77a72fc288
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367604"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Azure Stack Edge와 Azure Key Vault 통합 

Azure Key Vault는 비밀 관리를 위해 Azure Stack에 지 리소스와 통합 됩니다. 이 문서에서는 장치를 정품 인증 하는 동안 Azure Stack에 지 리소스에 대해 Azure Key Vault를 만드는 방법에 대 한 세부 정보를 제공 하며, 암호 관리에 사용 됩니다. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Key vault 및 Azure Stack Edge 정보

Azure Key Vault 클라우드 서비스는 토큰, 암호, 인증서, API 키 및 기타 암호에 대 한 액세스를 안전 하 게 저장 하 고 제어 하는 데 사용 됩니다. 또한 Key Vault를 사용 하면 데이터를 암호화 하는 데 사용 되는 암호화 키를 쉽게 만들고 제어할 수 있습니다. 허용 되는 트랜잭션과 해당 요금에 대 한 자세한 내용은 [Azure Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조 하세요.

Azure Stack Edge 서비스의 경우 사용 되는 암호 중 하나는 HK (채널 무결성 키)입니다. 이 키를 사용 하 여 암호를 암호화할 수 있습니다. 키 자격 증명 모음을 통합 하 여 CIK는 키 자격 증명 모음에 안전 하 게 저장 됩니다. 자세한 내용은 [안전 하 게 비밀 및 키 저장](../key-vault/general/overview.md#securely-store-secrets-and-keys)을 참조 하세요.


## <a name="key-vault-creation"></a>키 자격 증명 모음 만들기

키 자격 증명 모음은 활성화 키 생성 프로세스 중에에 지 리소스에 대 한 Azure Stack에 생성 됩니다. 

- Edge 리소스 Azure Stack 만들 때 *Microsoft. KeyVault* 리소스 공급자를 등록 해야 합니다. 구독에 대 한 소유자 또는 참가자 액세스 권한이 있는 경우 리소스 공급자가 자동으로 등록 됩니다. Key vault는 동일한 구독 및 리소스 그룹에 Azure Stack Edge 리소스로 생성 됩니다. 

- Azure Stack Edge 리소스를 만들 때 리소스의 수명 동안 지속 되 고 클라우드의 리소스 공급자와 통신 하는 MSI (관리 서비스 ID)도 만들어집니다. 

    MSI를 사용 하도록 설정 하면 Azure는 Azure Stack Edge 리소스에 대해 신뢰할 수 있는 id를 만듭니다.

- Azure Stack Edge 리소스를 만들고 Azure Portal에서 활성화 키를 생성 한 후에는 key vault가 만들어집니다. 이 키 자격 증명 모음은 비밀 관리에 사용 되며, Azure Stack에 지 리소스가 있는 경우에도 지속 됩니다. 

    ![활성화 키 생성 중에 생성 Key Vault](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- 기본 키 이름을 적용 하거나 키 자격 증명 모음에 대 한 사용자 지정 이름을 지정 하도록 선택할 수 있습니다. 키 자격 증명 모음 이름은 3 자에서 24 자 사이 여야 합니다. 이미 사용 중인 주요 자격 증명 모음을 사용할 수 없습니다. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![Azure Stack Edge 리소스 생성 중에 생성 되는 MSI](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Azure 주요 자격 증명 모음을 찾아보려면 Azure Stack Edge 리소스의 **속성** 으로 이동 하 고 키 자격 증명 모음 이름을 선택 합니다. 

- 실수로 인 한 삭제를 방지 하기 위해 키 자격 증명 모음에서 리소스 잠금이 사용 됩니다. 또한 키 자격 증명 모음에 대 한 일시 삭제는 키 자격 증명 모음을 90 복원 하는 데 사용할 수 있습니다. 자세한 내용은 [Azure Key Vault 일시 삭제 개요](../key-vault/general/soft-delete-overview.md) 를 참조 하세요.

    키 자격 증명 모음을 실수로 삭제 하 고 제거 보호 기간 90 일이 경과 하지 않은 경우 다음 단계에 따라 [주요 자격 증명 모음을 복구](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)합니다. 

- Azure Key Vault Azure Stack Edge 리소스와 통합 되기 전에 기존 Azure Stack Edge 리소스가 있는 경우에는 영향을 받지 않습니다. 기존 Azure Stack Edge 리소스를 계속 사용할 수 있습니다. 

- Azure Stack Edge 리소스가 삭제 되 면 해당 리소스를 사용 하 여 Azure Key Vault도 삭제 됩니다. 확인하라는 메시지가 표시됩니다. 이 주요 자격 증명 모음을 삭제 하지 않으려는 경우 동의를 제공 하지 않도록 선택할 수 있습니다. 키 자격 증명 모음을 그대로 유지 하는 Azure Stack에 지 리소스만 삭제 됩니다. 

- 이 키 자격 증명 모음을 사용 하 여 다른 키를 저장 한 경우에도 90 일 이내에 복원할 수 있습니다. 이 제거 보호 기간 중에는 키 자격 증명 모음 이름을 사용 하 여 새 키 자격 증명 모음을 만들 수 없습니다.

키 자격 증명 모음 및 장치 활성화와 관련 된 문제가 발생 하는 경우 [장치 활성화 문제 해결](azure-stack-edge-gpu-troubleshoot-activation.md)을 참조 하세요.

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>다음 단계

- [활성화 키를 생성](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)하는 방법에 대해 자세히 알아보세요.

