---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467383"
---
VPN을 확인하기 위해 만든 가상 네트워크에서만 액세스할 수 있는 스토리지 계정을 만들 수 있습니다. 다음 단계에 따라 이 스토리지 계정을 만들고 앞서 만든 가상 네트워크와 연결합니다.

1. 스토리지 계정을 만듭니다. Azure Stack Edge 디바이스에서 사용하려는 스토리지 계정을 사용할 수 있습니다. 외부 네트워크(선택한 네트워크 밖)에서 스토리지 계정에 액세스해 봅니다. 스토리지 계정에 액세스할 수 있어야 합니다.
2. Azure Portal에서 스토리지 계정으로 이동합니다. 
3. **방화벽 및 가상 네트워크** 로 이동합니다. **다음에서 액세스 허용** 의 오른쪽 창에서 **선택한 네트워크** 를 선택합니다. **Secure our storage account with virtual networks(가상 네트워크로 스토리지 계정 보호)** 에서 **+ 기존 가상 네트워크 추가** 를 선택합니다.

    ![VPN 1 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. **네트워크 추가** 블레이드에서 다음을 수행합니다.

    - 구독을 선택합니다. 이는 Azure Stack Edge/Data Box Gateway 리소스와 연결된 동일한 구독입니다. 
    - 드롭다운 목록에서 이 스토리지 계정에 연결할 가상 네트워크를 선택합니다. 이전 단계에서 만든 가상 네트워크를 선택합니다.
    - **서브넷** 에서 **_default_* _ 및 _GatewaySubnet*을 선택합니다. 이 가상 네트워크/서브넷 조합에 서비스 엔드포인트를 사용할 수 있습니다. 액세스를 사용하도록 설정하는 데 최대 15분이 걸립니다.
    - **사용** 을 선택합니다.

    ![VPN 2 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. **설정** 을 저장합니다.

    ![VPN 3 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. 설정이 저장된 후에는 가상 네트워크를 사용하도록 설정된 서브넷을 볼 수 있습니다.

    ![VPN 4 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. 이제 데이터가 VPN을 통해서만 이동하는지 확인하려면 다음 단계를 따르세요. 
    - 외부 네트워크(선택한 네트워크 밖)에서 스토리지 계정에 액세스해 봅니다. 스토리지 계정에 액세스할 수 없어야 합니다. 
    - 선택한 네트워크에서 사용하도록 설정한 가상 네트워크/서브넷에서 스토리지 계정에 액세스해 봅니다. 스토리지 계정에 액세스할 수 있어야 합니다. 
 
VPN을 사용하도록 설정한 경우에만 이 스토리지 계정에 액세스할 수 있습니다. VPN을 사용하지 않도록 설정하는 경우 스토리지 계정의 네트워크 설정도 조정해야 합니다. 

자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../articles/storage/common/storage-network-security.md)을 참조하세요. 

