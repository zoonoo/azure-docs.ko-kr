---
title: Azure Portal에서 Azure Data Box Gateway에 연결하고, 구성하고, 활성화 | Microsoft Docs
description: Data Box Gateway 배포에 대한 세 번째 자습서에서는 가상 장치에 연결하고, 설정하고, 활성화하는 방법을 안내합니다.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: e2fe7c375525389da865a3c85b52fad4dd11e333
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957488"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>자습서: Azure Data Box Gateway(미리 보기)에 연결하고, 설정하고, 활성화 

## <a name="introduction"></a>소개

이 자습서에는 로컬 웹 UI를 사용하여 Data Box Gateway 장치에 연결하고, 설정하고, 활성화하는 방법을 설명합니다. 

설정 및 활성화 프로세스를 완료하는 데 10분 정도가 소요됩니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 장치에 연결
> * 가상 장치 설정 및 활성화

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


> [!IMPORTANT]
> - Data Box Gateway는 미리 보기로 제공되고 있습니다. 이 솔루션을 주문하고 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요. 


## <a name="prerequisites"></a>필수 조건

Data Box Gateway를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Hyper-V에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-hyperv.md) 또는 [VMware에서 Data Box Gateway 프로비전](data-box-gateway-deploy-provision-vmware.md)에서 설명한 대로 가상 장치를 프로비전하고 가상 장치에 연결된 URL을 가져왔습니다.
* Data Box Gateway 장치를 관리하기 위해 만든 Data Box Gateway 서비스의 활성화 키를 갖고 있습니다. 자세한 내용은 [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)를 참조하세요.

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>로컬 웹 UI 설정에 연결 

1. 브라우저 창을 열고 로컬 웹 UI에 연결합니다. 형식:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   이전 자습서에서 언급한 연결 URL을 사용합니다. 웹 사이트의 보안 인증서에 문제가 있음을 나타내는 오류가 표시됩니다. **이 웹 페이지에서 계속 진행**을 클릭합니다. (이러한 단계는 사용하는 브라우저에 따라 다를 수 있습니다.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. 가상 장치의 웹 UI에 로그인합니다. 기본 암호는 *Password1*입니다. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. 장치 관리자 암호를 변경하라는 메시지가 표시됩니다. 8~16자 길이의 새 암호를 입력합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

이제 장치의 **대시보드**에 있습니다.

## <a name="set-up-and-activate-the-virtual-device"></a>가상 장치 설정 및 활성화
 
1. 대시보드에서, 가상 장치를 구성하고 Data Box Gateway 서비스에 등록하는 데 필요한 다양한 설정으로 이동할 수 있습니다. **네트워크 설정**, **웹 프록시 설정**, **시간 설정**은 선택 사항입니다. 유일한 필수 설정은 **장치 이름** 및 **클라우드 설정**입니다.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. **장치 이름** 페이지에서 장치 이름을 구성합니다. 이름의 길이는 1~15 사이여야 하고 문자, 숫자 및 하이픈을 포함할 수 있습니다.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (선택 사항) **네트워크 설정**을 구성합니다. 기본 가상 머신에서 구성한 수에 따라 1개 이상의 네트워크 인터페이스가 표시됩니다. 아래와 같이 네트워크 인터페이스 하나가 설정된 가상 장치의 **네트워크 설정** 페이지가 표시됩니다.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    네트워크 설정을 구성할 때 다음 사항을 염두에 두어야 합니다.

    - 환경에서 DHCP를 사용하도록 설정하면 네트워크 인터페이스가 자동으로 구성됩니다. 따라서 IP 주소, 서브넷, 게이트웨이 및 DNS가 자동으로 할당됩니다.
    - DHCP를 사용하지 않는 경우 필요에 따라 고정 IP를 할당할 수 있습니다.
    - 네트워크 인터페이스를 IPv4로 구성할 수 있습니다.
   
4. 선택적으로 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항이지만 웹 프록시를 사용하면 여기서만 구성할 수 있습니다.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   **웹 프록시** 페이지에서:
   
   1. 다음 형식으로 **웹 프록시 URL** 공급: *http://&lt;호스트 IP 주소 또는 FDQN&gt;:포트 번호*. HTTPS URL은 지원되지 않습니다.
   2. **인증**은 **기본** 또는 **없음**으로 지정합니다.
   3. 인증을 사용하는 경우 **사용자 이름** 및 **암호**를 입력해야 합니다.
   4. **적용**을 클릭합니다. 구성된 웹 프록시 설정의 유효성을 검사하고 적용합니다.

5. (선택 사항) 장치에 대한 시간 설정(예: 표준 시간대 및 기본 및 보조 NTP 서버)을 구성합니다. 클라우드 서비스 공급자와 인증할 수 있도록 장치 시간을 동기화해야 하기 때문에 NTP 서버가 필요합니다.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    **시간 설정** 페이지에서:
    
    1. 드롭다운 목록에서 장치가 배포되는 지리적 위치를 기반으로 **표준 시간대** 를 설정합니다. 장치의 기본 표준 시간대는 PST입니다. 장치는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.
    2. 장치에 **기본 NTP 서버** 를 지정하거나 time.windows.com의 기본값을 적용합니다. 네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다.
    3. 선택적으로 장치에 대한 **보조 NTP 서버**를 지정합니다.
    4. **Apply**를 클릭합니다. 구성된 시간 설정의 유효성을 검사하고 적용합니다.

6. **클라우드 설정** 페이지에서, Azure Portal의 Data Box Gateway 서비스를 사용하여 장치를 활성화합니다.
    
    1. Data Box Gateway에 대한 **활성화 키 가져오기**에서 얻은 [활성화 키](data-box-gateway-deploy-prep.md#get-the-activation-key)를 입력합니다.

    2. **활성화**를 클릭합니다. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. 장치가 다시 시작됩니다. 장치가 활성화될 때까지 2-3분 정도 기다려야 할 수 있습니다. 장치가 다시 시작된 후 로그인 페이지가 열립니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Gateway 토픽에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가상 장치에 연결
> * 가상 장치 설정 및 활성화


다음 자습서로 넘어가서 Data Box Gateway를 사용하여 데이터를 전송하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Data Box Gateway를 통해 데이터 전송](./data-box-gateway-deploy-add-shares.md).