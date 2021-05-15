---
title: Azure Stack Edge Mini R Wi-Fi 관리
description: Azure Portal을 사용하여 Azure Stack Edge에서 Wi-Fi를 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043770"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>로컬 웹 UI를 사용하여 Azure Stack Edge Mini R에서 무선 연결 관리

이 문서에서는 Azure Stack Edge Mini R 디바이스에서 무선 네트워크 연결을 관리하는 방법을 설명합니다. Azure Stack Edge Mini R 디바이스에서 로컬 웹 UI를 사용하여 Wi-Fi 프로필을 추가, 연결 및 삭제할 수 있습니다.

## <a name="about-wi-fi"></a>Wi-Fi 정보

Azure Stack Edge Mini R 디바이스는 네트워크에 유선으로 연결하거나 무선 네트워크를 통해 작동할 수 있습니다. 디바이스에는 디바이스를 무선 네트워크에 연결할 수 있도록 사용하도록 설정해야 하는 Wi-Fi 포트가 있습니다. 

디바이스에는 포트 1에서 포트 4까지와 Wi-Fi 포트까지, 총 다섯개의 포트가 있습니다. 다음은 무선 네트워크에 연결되었을 때 Mini R 디바이스의 후면 다이어그램입니다.

![Wi-Fi용 케이블 연결](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Wi-Fi 프로필 추가 및 연결

디바이스의 로컬 UI에서 다음 단계를 수행하여 Wi-Fi 프로필을 추가하고 연결합니다.

1. 디바이스의 로컬 웹 UI에서 **시작** 페이지로 이동합니다. **네트워크** 타일에서 **구성** 을 선택합니다.  
    
    물리적 디바이스에 5개의 네트워크 인터페이스가 있습니다. 포트 1 및 포트 2는 1Gbps 네트워크 인터페이스입니다. PORT 3 및 PORT 4는 모두 10-Gbps 네트워크 인터페이스입니다. 다섯 번째 포트는 Wi-Fi 포트입니다. 

    [![로컬 웹 UI "네트워크 설정" 페이지 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Wi-Fi 포트를 선택하고 포트 설정을 구성합니다. 
    
    > [!IMPORTANT]
    > Wi-Fi 포트의 고정 IP 주소를 구성하는 것이 좋습니다.  

    ![로컬 웹 UI "네트워크 설정" 페이지 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Wi-Fi 포트 설정을 적용한 후 **네트워크** 페이지를 업데이트합니다.

    ![로컬 웹 UI "네트워크 설정" 페이지 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. **Wi-Fi 프로필 추가** 를 선택하고 Wi-Fi 프로필을 업로드합니다. 

    ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    무선 네트워크 프로필은 무선 네트워크에 연결할 수 있도록 SSID(네트워크 이름), 암호 키 및 보안 정보를 포함합니다. 네트워크 관리자로부터 사용자 환경에 대한 Wi-Fi 프로필을 가져올 수 있습니다.

    Wi-Fi 프로필 준비에 대한 자세한 내용은 [Azure Stack Edge Mini R 디바이스에서 Wi-Fi 프로필 사용](azure-stack-edge-mini-r-use-wifi-profiles.md)을 참조하세요.

    ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    프로필을 추가하면 새 프로필을 반영하도록 Wi-Fi 프로필 목록이 업데이트됩니다. 프로필에 **연결 상태** 가 **연결 끊김** 으로 표시됩니다. 

    ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. 무선 네트워크 프로필이 성공적으로 로드되면 이 프로필에 연결합니다. **Wi-Fi 프로필에 연결** 을 선택합니다. 

    ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. 이전 단계에서 추가한 Wi-Fi 프로필을 선택하고 **적용** 을 선택합니다. 

    ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **연결 상태** 가 **연결됨** 으로 업데이트되어야 합니다. 신호의 품질을 나타내기 위해 신호 강도가 업데이트됩니다. 

    ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > 많은 양의 데이터를 전송하려면 무선 네트워크 대신 유선 연결을 사용하는 것이 좋습니다. 


## <a name="download-wi-fi-profile"></a>Wi-Fi 프로필 다운로드

무선 네트워크 연결에 사용 중인 Wi-Fi 프로필을 다운로드 할 수 있습니다.

1. 디바이스의 로컬 웹 UI에서 **구성 > 네트워크** 로 이동합니다. 

2. Wi-Fi 프로필 설정에서 **프로필 다운로드** 를 선택합니다. 현재 사용중인 Wi-Fi 프로필을 다운로드해야 합니다.


## <a name="delete-wi-fi-profile"></a>Wi-Fi 프로필 삭제

무선 네트워크 연결에 사용 중인 Wi-Fi 프로필을 삭제할 수 있습니다.


1. 디바이스의 로컬 웹 UI에서 **구성 > 네트워크** 로 이동합니다. 

2. Wi-Fi 프로필 설정에서 **Wi-Fi 프로필 삭제** 를 선택합니다.

3. **Wi-Fi 프로필 삭제** 블레이드에서 삭제하려는 프로필을 선택합니다. **적용** 을 선택합니다.


## <a name="configure-cisco-wi-fi-profile"></a>Cisco Wi-Fi 프로필 구성

다음은 디바이스에서 Cisco 무선 컨트롤러 및 액세스 포인트를 관리하고 구성하는 방법에 대한 몇 가지 참고 자료입니다. 

### <a name="dhcp-bridging-mode"></a>DHCP 브리징 모드

디바이스에 Cisco 무선 컨트롤러를 사용하려면 무선 LAN 컨트롤러(WLC)에서 DHCP(Dynamic Host Configuration Protocol) 브리징 모드를 사용하도록 설정해야 합니다.

자세한 내용은 [DHCP 브리징 모드](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9)를 참조하세요.

#### <a name="bridging-configuration-example"></a>브리징 구성 예제

컨트롤러에서 DHCP 브리징 기능을 사용하도록 설정하려면 컨트롤러에서 DHCP 프록시 기능을 사용하지 않도록 설정해야 합니다. 명령줄을 사용하여 DHCP 브리징 사용하도록 설정하기:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

DHCP 서버가 클라이언트와 동일한 L2(레이어 2) 네트워크에 없으면 IP 도우미를 사용하여 클라이언트 게이트웨이에서 DHCP 서버로 브로드 캐스트를 전달해야 합니다. 다음은 이 구성의 샘플입니다:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

DHCP 브리징 기능은 전체 설정이므로 컨트롤러 내의 모든 DHCP 트랜잭션에 영향을 줍니다. 컨트롤러에서 필요한 모든 VLAN(Virtual Local Area Network)에 대해 유선 인프라에 IP 도우미 문을 추가해야 합니다.

### <a name="enable-the-passive-client-for-wlan"></a>WLAN에 수동 클라이언트를 사용하도록 설정

Cisco 무선 컨트롤러에서 WLAN(Wireless Local Area Network)에 대한 수동 클라이언트 기능을 사용하도록 설정하기:

* WLAN에 연결된 인터페이스에는 VLAN 태그 지정이 사용하도록 설정되어 있어야 합니다.
* WLAN에 대해 멀티캐스트 VLAN을 사용하도록 설정해야 합니다.
* WLC에서 GARP 전달을 사용하도록 설정해야 합니다.

자세한 내용은 [멀티캐스트 최적화에 대한 멀티캐스트 VLAN 정보](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html)를 참조하십시오.

### <a name="troubleshoot"></a>문제 해결

Azure Stack Edge Mini R 디바이스에서 실행되는 VM에서 IP 주소 할당에 문제가 발생하는 경우 네트워크 환경에서 위의 구성 설정에 대한 유효성을 검사해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Mini R 디바이스 배포](azure-stack-edge-mini-r-deploy-prep.md) 하는 방법을 알아보십시오.
