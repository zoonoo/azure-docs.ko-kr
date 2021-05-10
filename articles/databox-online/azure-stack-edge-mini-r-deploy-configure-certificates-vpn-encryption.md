---
title: Azure Portal에서 Azure Stack Edge Mini R 디바이스에 대한 인증서를 구성하기 위한 자습서 | Microsoft Docs
description: Azure Stack Edge Mini R 배포에 대한 자습서에서는 물리적 디바이스에서 인증서를 구성하는 방법을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 42863c942314d3cb76c7536edc79b08e132f8fa2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063001"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>자습서: Azure Stack Edge Mini R에 대한 인증서, VPN, 암호화 구성

이 자습서에서는 로컬 웹 UI를 사용하여 Azure Stack Edge Mini R 디바이스에 대한 인증서, VPN 및 저장 데이터 암호화를 구성하는 방법을 설명합니다.

이 단계에 소요되는 시간은 선택한 특정 옵션 및 사용자 환경에서 인증서 흐름이 설정된 방법에 따라 달라질 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 대한 인증서 구성
> * VPN 구성
> * 저장 데이터 암호화 구성

## <a name="prerequisites"></a>필수 구성 요소

Azure Stack Edge Mini R 디바이스를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Mini R 설치](azure-stack-edge-mini-r-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.

* 사용자 고유의 인증서를 가져오려는 경우:
    - 서명 체인 인증서를 포함하는 적절한 형식으로 인증서를 준비해야 합니다. 인증서에 대한 자세한 내용은 [인증서 관리](azure-stack-edge-gpu-manage-certificates.md)를 참조하세요.

    - 디바이스가 Azure Government 또는 Azure Government Secret 또는 Azure Government 상위 비밀 클라우드에 배포되어 있고 Azure 퍼블릭 클라우드에 배포되지 않은 경우 디바이스를 활성화하려면 서명 체인 인증서가 필요합니다. 
    인증서에 대한 자세한 내용은 [인증서 관리](azure-stack-edge-gpu-manage-certificates.md)를 참조하세요.


## <a name="configure-certificates-for-device"></a>디바이스에 대한 인증서 구성

1. **인증서** 페이지에서 인증서를 구성합니다. **디바이스** 페이지에서 디바이스 이름 또는 DNS 도메인을 변경했는지 여부에 따라 다음 인증서 옵션 중 하나를 선택할 수 있습니다.

    - 이전 단계에서 기본 디바이스 이름 또는 기본 DNS 도메인을 변경하지 않았고 자체 인증서를 가져오지 않으려면 이 단계를 건너뛰고 다음 단계를 진행할 수 있습니다. 디바이스에서 자체 서명된 인증서를 자동으로 생성하여 시작합니다. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - 디바이스 이름 또는 DNS 도메인을 변경한 경우 인증서 상태가 **유효하지 않음** 으로 표시됩니다. 

        ![로컬 웹 UI "인증서" 페이지 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        상태 세부 정보를 보고 싶은 인증서를 선택합니다.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        인증서가 업데이트된 디바이스 이름 및 DNS 도메인(주체 이름 및 주체 대체 이름에 사용됨)을 반영하지 않기 때문에 인증서 상태가 **유효하지 않음** 입니다. 디바이스를 성공적으로 활성화하려면 사용자 고유의 서명된 엔드포인트 인증서와 해당 서명 체인을 가져올 수 있습니다. 먼저 서명 체인을 추가한 다음, 엔드포인트 인증서를 업로드합니다. 자세한 내용은 [Azure Stack Edge Mini R 디바이스에서 사용자 고유의 인증서 가져오기](#bring-your-own-certificates)를 참조하세요.


    - 디바이스 이름 또는 DNS 도메인을 변경하고 자체 인증서를 가져오지 않은 경우에는 **활성화가 차단** 됩니다.


#### <a name="bring-your-own-certificates"></a>사용자 고유의 인증서 가져오기

이전 단계에서 이 디바이스에 서명 체인을 이미 추가했습니다. 이제 엔드포인트 인증서, 노드 인증서, 로컬 UI 인증서 및 VPN 인증서를 업로드할 수 있습니다. 사용자 고유의 인증서를 추가하려면 다음 단계를 수행합니다.

1. 인증서를 업로드하려면 **인증서** 페이지에서 **+ 인증서 추가** 를 선택합니다.

    [![로컬 웹 UI "인증서" 페이지 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. 다른 인증서를 업로드할 수 있습니다. 예를 들어 Azure Resource Manager 및 Blob Storage 엔드포인트 인증서를 업로드할 수 있습니다.

    ![로컬 웹 UI "인증서" 페이지 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. 로컬 웹 UI 인증서를 업로드할 수도 있습니다. 이 인증서를 업로드한 후에는 브라우저를 시작하고 캐시를 지워야 합니다. 그런 다음, 디바이스 로컬 웹 UI에 연결해야 합니다.  

    ![로컬 웹 UI "인증서" 페이지 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. 노드 인증서를 업로드할 수도 있습니다.


    ![로컬 웹 UI "인증서" 페이지 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. 마지막으로 VPN 인증서를 업로드할 수 있습니다.
        
    ![로컬 웹 UI “인증서” 페이지](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. 언제든지 인증서를 선택하고 세부 정보를 검토하여 업로드한 인증서와 일치하는지 확인할 수 있습니다.

    [![로컬 웹 UI "인증서" 페이지 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    인증서 페이지가 새로 추가된 인증서를 반영하도록 업데이트됩니다.

    [![로컬 웹 UI "인증서" 페이지 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Azure 퍼블릭 클라우드를 제외하고, 모든 클라우드 구성(Azure Government 또는 Azure Stack Hub)을 활성화하기 전에 서명 체인 인증서를 가져와야 합니다.


## <a name="configure-vpn"></a>VPN 구성

1. **보안** 타일에서 VPN에 대한 **구성** 을 선택합니다. 

    VPN을 구성하려면 먼저 Azure에서 필요한 모든 구성이 완료되었는지 확인해야 합니다. 자세한 내용은 [Azure Stack Edge Mini R 디바이스에 대해 PowerShell을 통해 VPN 구성](azure-stack-edge-placeholder.md)을 참조하세요. 이 작업이 완료되면 로컬 UI에서 구성을 수행할 수 있습니다.
    
    1. VPN 페이지에서 **구성** 을 선택합니다.
        ![VPN 로컬 UI 1 구성](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. **VPN 구성** 블레이드에서:

        1. 전화 번호부를 입력으로 제공합니다.
        2. Azure 데이터 센터 IP 범위 JSON 파일을 입력으로 제공합니다. [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519)에서 이 파일을 다운로드합니다.
        3. 지역으로 **eastus** 를 선택합니다.
        4. **적용** 을 선택합니다.

        ![VPN 로컬 UI 2 구성](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. VPN 전용을 사용하여 액세스할 수 있는 IP 주소 범위를 구성합니다. 
    
        - **VPN 전용을 사용하여 액세스할 IP 주소 범위** 에서 **구성** 을 선택합니다.
        - Azure Virtual Network에 대해 선택한 VNET IPv4 범위를 입력합니다.
        - **적용** 을 선택합니다.
    
        ![VPN 로컬 UI 3 구성](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

이제 디바이스가 암호화될 준비가 되었습니다. 저장 데이터 암호화를 구성합니다.


## <a name="enable-encryption"></a>암호화 사용

1. **보안** 타일에서 저장 데이터 암호화에 대한 **구성** 을 선택합니다. 이 설정은 필수이며 성공적으로 구성될 때까지 디바이스를 활성화할 수 없습니다. 

    ![로컬 웹 UI "저장 데이터 암호화" 페이지 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    공장에서 디바이스를 이미지로 만들면 볼륨 수준 BitLocker 암호화가 사용하도록 설정됩니다. 디바이스를 받은 후에는 저장 데이터 암호화를 구성해야 합니다. 스토리지 풀 및 볼륨이 다시 만들어지고 저장 데이터 암호화를 사용하도록 BitLocker 키를 제공할 수 있으므로 미사용 데이터에 대한 두 번째 암호화 계층을 만들 수 있습니다.

1. **저장 데이터 암호화** 창에서 32자 길이(AES-256비트)의 Base-64 인코딩 키를 입력합니다. 이 구성은 일회성 구성이며 이 키는 실제 암호화 키를 보호하는 데 사용됩니다. 

    ![로컬 웹 UI "저장 데이터 암호화" 페이지 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    이 키도 자동으로 생성하도록 선택할 수 있습니다.

    ![로컬 웹 UI "저장 데이터 암호화" 페이지 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. **적용** 을 선택합니다. 이 작업은 몇 분 정도 걸리며 **보안** 타일에 작업 상태가 표시됩니다.

    ![로컬 웹 UI "저장 데이터 암호화" 페이지 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. 상태가 **완료** 로 표시되면 **시작** 으로 돌아갑니다.

이제 디바이스가 활성화될 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 대한 인증서 구성
> * VPN 구성
> * 저장 데이터 암호화 구성

Azure Stack Edge Mini R 디바이스를 활성화하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro Mini R 디바이스 활성화](./azure-stack-edge-mini-r-deploy-activate.md)
