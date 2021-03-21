---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467371"
---
장치의 로컬 웹 UI에서 다음 단계를 수행 합니다. 이 단계에서는 VPN 구성 파일 (또는 서비스 태그 파일)의 업로드를 포함 하 여 15 분 정도 걸립니다. 

1. **구성 > VPN** 으로 이동 합니다. **구성** 을 선택합니다.

    ![로컬 UI 구성 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. **VPN 구성** 블레이드에서:

    - **VPN 설정** 을 사용하도록 설정합니다.
    - **VPN 공유 비밀** 을 제공합니다. Azure VPN 연결 리소스를 만드는 동안 입력 한 공유 키입니다.
    - **VPN 게이트웨이 IP** 주소를 제공합니다. 이는 Azure 로컬 네트워크 게이트웨이 IP 주소입니다.
    - **PFS 그룹** 에 대해 **없음** 을 선택합니다. 
    - **DH 그룹** 에 대해 **Group2** 를 선택합니다.
    - **IPsec 무결성 방법** 에 대해 **SHA256** 을 선택합니다.
    - **IPsec 암호화 변환 상수** 에 대해 **GCMAES256** 를 선택 합니다.
    - **IPsec 인증 변환 상수** 에 대해 **GCMAES256** 을 선택합니다.
    - **IKE 암호화 방법** 에 대해 **AES256** 을 선택합니다.
    - **적용** 을 선택합니다.

        ![로컬 UI 2 구성](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    지원 되는 암호화 알고리즘에 대 한 자세한 내용은 [암호화 요구 사항 및 AZURE VPN 게이트웨이](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)정보를 참조 하세요. 

3. VPN 경로 구성 파일을 업로드하려면 **업로드** 를 선택합니다. 

    ![로컬 UI 3 구성](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - 이전 단계에서 로컬 시스템에 다운로드 한 서비스 태그 *json* 파일을 찾습니다.
    - 디바이스, 가상 네트워크 및 게이트웨이에 연결된 Azure 지역으로 지역을 선택합니다.
    - **적용** 을 선택합니다.

        ![로컬 UI 4 구성](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    업로드는 장치에서 7-8 분 정도 걸립니다.

4. 클라이언트 관련 경로를 추가하려면 VPN 전용을 사용하여 액세스할 수 있도록 IP 주소 범위를 구성합니다. 

    - **VPN 전용을 사용하여 액세스할 IP 주소 범위** 에서 **구성** 을 선택합니다.
    - 올바른 IPv4 범위를 제공하고 **추가** 를 선택합니다. 단계를 반복하여 다른 범위를 추가합니다.
    - **적용** 을 선택합니다.

        ![로컬 UI 5 구성](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

