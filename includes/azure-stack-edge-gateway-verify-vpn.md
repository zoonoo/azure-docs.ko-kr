---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467383"
---
VPN을 확인 하려면 만든 가상 네트워크 에서만 액세스할 수 있는 저장소 계정을 만들 수 있습니다. 이 저장소 계정을 만들고 만든 가상 네트워크에 연결 하려면 다음 단계를 수행 합니다.

1. 스토리지 계정을 만듭니다. Azure Stack Edge 장치에서 사용 하려는 저장소 계정을 사용할 수 있습니다. 외부 네트워크 (선택한 네트워크 외부)에서 저장소 계정에 액세스 해 보세요. 저장소 계정에 액세스할 수 있어야 합니다.
2. Azure Portal에서 저장소 계정으로 이동 합니다. 
3. **방화벽 및 가상 네트워크** 로 이동 합니다. **에서 액세스 허용** 의 오른쪽 창에서 **선택한 네트워크** 를 선택 합니다. **가상 네트워크를 사용 하 여 저장소 계정 보안** 에서 **+ 기존 가상 네트워크 추가를 선택 합니다.**

    ![VPN 1 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. **네트워크 추가** 블레이드에서 다음을 수행 합니다.

    - 구독을 선택합니다. 이는 Azure Stack Edge/Data Box Gateway 리소스와 연결 된 구독과 동일 합니다. 
    - 드롭다운 목록에서이 저장소 계정에 연결할 가상 네트워크를 선택 합니다. 이전 단계에서 만든 가상 네트워크를 선택 합니다.
    - **서브넷** 에서 **_default_* _ 및 _GatewaySubnet *를 선택 합니다. 이 가상 네트워크/서브넷 조합에 대해 서비스 끝점을 사용할 수 있습니다. 액세스를 설정 하는 작업은 완료 하는 데 최대 15 분이 걸립니다.
    - **사용** 을 선택합니다.

    ![VPN 2 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. **설정을** 저장 합니다.

    ![VPN 3 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. 설정이 저장 된 후에는 가상 네트워크를 사용 하도록 설정 된 서브넷을 볼 수 있습니다.

    ![VPN 4 확인](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. 이제 VPN을 통해서만 데이터가 이동 하는지 확인 하려면 다음 단계를 수행 합니다. 
    - 외부 네트워크 (선택한 네트워크 외부)에서 저장소 계정에 액세스 해 보세요. 저장소 계정에 액세스할 수 없습니다. 
    - 선택한 네트워크에서 사용 하도록 설정한 가상 네트워크/서브넷에서 저장소 계정에 액세스를 시도 합니다. 저장소 계정에 액세스할 수 있어야 합니다. 
 
VPN을 사용 하도록 설정한 경우에만이 저장소 계정에 액세스할 수 있습니다. VPN을 사용 하지 않도록 설정 하는 경우 저장소 계정의 네트워크 설정도 조정 해야 합니다. 

자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../articles/storage/common/storage-network-security.md)을 참조 하세요. 

