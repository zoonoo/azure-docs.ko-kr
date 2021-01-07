---
title: Azure Portal에서 Azure Stack Edge Pro R 디바이스에 대한 인증서를 구성하기 위한 자습서 | Microsoft Docs
description: Azure Stack Edge Pro R 배포에 대한 자습서에서는 물리적 디바이스에서 인증서를 구성하는 방법을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: fad3e5dcb0ecda82f3fb35cadf1719a62c99bd97
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464695"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>자습서: Azure Stack Edge Pro R을 위한 인증서 구성

이 자습서에서는 로컬 웹 UI를 사용하여 Azure Stack Edge Pro R 디바이스에 대한 인증서를 구성하는 방법을 설명합니다.

이 단계에 소요되는 시간은 선택한 특정 옵션 및 사용자 환경에서 인증서 흐름이 설정된 방법에 따라 달라질 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 대한 인증서 구성
> * VPN 구성
> * 저장 데이터 암호화 구성

## <a name="prerequisites"></a>필수 구성 요소

Azure Stack Edge Pro R 디바이스를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Pro R 설치](azure-stack-edge-pro-r-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.
* 사용자 고유의 인증서를 가져오려는 경우:
    - 서명 체인 인증서를 포함하는 적절한 형식으로 인증서를 준비해야 합니다. 인증서에 대한 자세한 내용은 [인증서 관리](azure-stack-edge-j-series-manage-certificates.md)를 참조하세요.



## <a name="configure-certificates-for-device"></a>디바이스에 대한 인증서 구성

1. **인증서** 페이지에서 인증서를 구성합니다. **디바이스** 페이지에서 디바이스 이름 또는 DNS 도메인을 변경했는지 여부에 따라 다음 인증서 옵션 중 하나를 선택할 수 있습니다.

    - 이전 단계에서 디바이스 이름 또는 DNS 도메인을 변경하지 않은 경우 이 단계를 건너뛰고 다음 단계를 진행할 수 있습니다. 디바이스에서 자체 서명된 인증서를 자동으로 생성하여 시작합니다. 

    - 디바이스 이름 또는 DNS 도메인을 변경한 경우 인증서 상태가 **유효하지 않음** 으로 표시됩니다. 

        ![로컬 웹 UI "인증서" 페이지 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        상태 세부 정보를 보고 싶은 인증서를 선택합니다.

        ![로컬 웹 UI "인증서" 페이지 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        인증서가 업데이트된 디바이스 이름 및 DNS 도메인(주체 이름 및 주체 대체 이름에 사용됨)을 반영하지 않기 때문입니다. 디바이스를 성공적으로 활성화하려면 사용자 고유의 서명된 엔드포인트 인증서와 해당 서명 체인을 가져올 수 있습니다. 먼저 서명 체인을 추가한 다음, 엔드포인트 인증서를 업로드합니다. 자세한 내용은 [Azure Stack Edge Pro R 디바이스에서 사용자 고유의 인증서 가져오기](#bring-your-own-certificates)를 참조하세요.

    - 디바이스 이름 또는 DNS 도메인을 변경하고 자체 인증서를 가져오지 않은 경우에는 **활성화가 차단** 됩니다.

    
#### <a name="bring-your-own-certificates"></a>사용자 고유의 인증서 가져오기

서명 체인을 포함한 사용자 고유의 인증서를 추가하려면 다음 단계를 수행합니다.

1. 인증서를 업로드하려면 **인증서** 페이지에서 **+ 인증서 추가** 를 선택합니다.

    ![로컬 웹 UI "인증서" 페이지 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. 먼저 서명 체인을 업로드하고 **유효성 검사 및 추가** 를 선택합니다.

    ![로컬 웹 UI "인증서" 페이지 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. 이제 다른 인증서를 업로드할 수 있습니다. 예를 들어 Azure Resource Manager 및 Blob Storage 엔드포인트 인증서를 업로드할 수 있습니다.

    ![로컬 웹 UI "인증서" 페이지 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    로컬 웹 UI 인증서를 업로드할 수도 있습니다. 이 인증서를 업로드한 후에는 브라우저를 시작하고 캐시를 지워야 합니다. 그런 다음, 디바이스 로컬 웹 UI에 연결해야 합니다.  

    ![로컬 웹 UI "인증서" 페이지 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    노드 인증서를 업로드할 수도 있습니다.

    ![로컬 웹 UI "인증서" 페이지 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    마지막으로 VPN 인증서를 업로드할 수 있습니다.

    ![로컬 웹 UI "인증서" 페이지 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    언제든지 인증서를 선택하고 세부 정보를 검토하여 업로드한 인증서와 일치하는지 확인할 수 있습니다.

    인증서 페이지가 새로 추가된 인증서를 반영하도록 업데이트됩니다.

    ![로컬 웹 UI "인증서" 페이지 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Azure 퍼블릭 클라우드를 제외하고, 모든 클라우드 구성(Azure Government 또는 Azure Stack)을 활성화하기 전에 서명 체인 인증서를 가져와야 합니다.

1. **< 시작으로 돌아가기** 를 선택합니다.

## <a name="configure-vpn"></a>VPN 구성

1. **보안** 타일에서 VPN에 대한 **구성** 을 선택합니다.

    ![로컬 웹 UI "VPN" 페이지](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    VPN을 구성하려면 먼저 Azure에서 필요한 모든 구성이 완료되었는지 확인해야 합니다. 자세한 내용은 [필수 구성 요소 구성](azure-stack-edge-placeholder.md) 및 [VPN에 대한 Azure 리소스 구성](azure-stack-edge-placeholder.md)을 참조하세요. 이 작업이 완료되면 로컬 UI에서 구성을 수행할 수 있습니다.
    
    1. VPN 페이지에서 **구성** 을 선택합니다.
    2. **VPN 구성** 블레이드에서:

    - **VPN 설정** 을 사용하도록 설정합니다.
    - **VPN 공유 비밀** 을 제공합니다. 이는 Azure VPN 연결 개체를 만드는 동안 입력한 공유 키입니다.
    - **VPN 게이트웨이 IP** 주소를 제공합니다. 이는 Azure 로컬 네트워크 게이트웨이 IP 주소입니다.
    - **PFS 그룹** 에 대해 **없음** 을 선택합니다. 
    - **DH 그룹** 에 대해 **Group2** 를 선택합니다.
    - **IPsec 무결성 방법** 에 대해 **SHA256** 을 선택합니다.
    - **IPsec 암호화 변환 상수** 에 대해 **GCMAES256** 을 선택합니다.
    - **IPsec 인증 변환 상수** 에 대해 **GCMAES256** 을 선택합니다.
    - **IKE 암호화 방법** 에 대해 **AES256** 을 선택합니다.
    - **적용** 을 선택합니다.

        ![로컬 UI 2 구성](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. VPN 경로 구성 파일을 업로드하려면 **업로드** 를 선택합니다. 
    
        ![로컬 UI 3 구성](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - 이전 단계에서 로컬 시스템에 다운로드한 VPN 구성 *json* 파일을 찾습니다.
        - 디바이스, 가상 네트워크 및 게이트웨이에 연결된 Azure 지역으로 지역을 선택합니다.
        - **적용** 을 선택합니다.
    
            ![로컬 UI 4 구성](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. 클라이언트 관련 경로를 추가하려면 VPN 전용을 사용하여 액세스할 수 있도록 IP 주소 범위를 구성합니다. 
    
        - **VPN 전용을 사용하여 액세스할 IP 주소 범위** 에서 **구성** 을 선택합니다.
        - 올바른 IPv4 범위를 제공하고 **추가** 를 선택합니다. 단계를 반복하여 다른 범위를 추가합니다.
        - **적용** 을 선택합니다.
    
            ![로컬 UI 5 구성](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. **< 시작으로 돌아가기** 를 선택합니다.

## <a name="configure-encryption-at-rest"></a>저장 데이터 암호화 구성

1. **보안** 타일에서 저장 데이터 암호화에 대한 **구성** 을 선택합니다. 이 설정은 필수이며 성공적으로 구성될 때까지 디바이스를 활성화할 수 없습니다. 

    공장에서 디바이스를 이미지로 만들면 볼륨 수준 BitLocker 암호화가 사용하도록 설정됩니다. 디바이스를 받은 후에는 저장 데이터 암호화를 구성해야 합니다. 스토리지 풀 및 볼륨이 다시 만들어지고 저장 데이터 암호화를 사용하도록 BitLocker 키를 제공할 수 있으므로 미사용 데이터에 대한 두 번째 암호화 계층을 만들 수 있습니다.

1. **저장 데이터 암호화** 창에서 32자 길이의 Base-64 인코딩 키를 제공합니다. 이 구성은 일회성 구성이며 이 키는 실제 암호화 키를 보호하는 데 사용됩니다. 자동으로 이 키를 생성하거나 키를 입력하도록 선택할 수 있습니다.

    ![로컬 웹 UI "저장 데이터 암호화" 창](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    키는 디바이스가 활성화된 후 **클라우드 세부 정보** 페이지의 키 파일에 저장됩니다.

1. **적용** 을 선택합니다. 이 작업은 몇 분 정도 걸리며 **보안** 타일에 작업 상태가 표시됩니다.

    ![로컬 웹 UI "저장 데이터 암호화" 페이지](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. 상태가 **완료** 로 표시되면 **< 시작으로 돌아가기** 를 선택합니다.

이제 디바이스가 활성화될 준비가 되었습니다. 


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 대한 인증서 구성
> * VPN 구성
> * 저장 데이터 암호화 구성

Azure Stack Edge Pro R 디바이스를 활성화하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R 디바이스 활성화](./azure-stack-edge-pro-r-deploy-activate.md)
