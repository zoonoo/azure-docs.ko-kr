---
title: Azure Portal에서 Azure Data Box Edge에 연결하고, 구성하고, 활성화 | Microsoft Docs
description: Data Box Edge 배포에 대한 세 번째 자습서에서는 물리적 디바이스에 연결하고, 설정하고, 활성화하는 방법을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166579"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>자습서: Azure Data Box Edge(미리 보기)에 연결하고, 설정하고, 활성화 

이 자습서에는 로컬 웹 UI를 사용하여 Data Box Edge 디바이스에 연결하고, 설정하고, 활성화하는 방법을 설명합니다. 

설정 및 활성화 프로세스를 완료하는 데 20분 정도가 소요됩니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 물리적 디바이스에 연결
> * 물리적 디바이스 설정 및 활성화

> [!IMPORTANT]
> Data Box Edge는 미리 보기로 있습니다. 이 솔루션을 주문하고 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요. 


## <a name="prerequisites"></a>필수 조건

Data Box Edge를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Data Box Edge 설치](data-box-edge-deploy-install.md)에서 설명한 대로 물리적 장치를 설치했습니다.
* Data Box Edge 디바이스를 관리하기 위해 만든 Data Box Edge 서비스의 활성화 키를 갖고 있습니다. 자세한 내용은 [Azure Data Box Edge 배포 준비](data-box-edge-deploy-prep.md)를 참조하세요.

## <a name="connect-to-the-local-web-ui-setup"></a>로컬 웹 UI 설정에 연결 

1. 고정 IP 주소가 192.168.100.5이고 서브넷이 255.255.255.0인 Edge 디바이스에 연결하는 데 사용하는 컴퓨터에서 이더넷 어댑터를 구성합니다.
2. 디바이스의 포트 1에 컴퓨터를 연결합니다. 
3. 브라우저 창을 열고 https://192.168.100.10에서 디바이스의 로컬 웹 UI에 액세스합니다. 이 작업은 디바이스를 켠 후 몇 분 정도 걸릴 수 있습니다. 
4. 웹 사이트의 보안 인증서에 문제가 있음을 나타내는 오류 또는 경고가 표시됩니다. **이 웹 페이지에서 계속 진행**을 클릭합니다. (이러한 단계는 사용하는 브라우저에 따라 다를 수 있습니다.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. 디바이스의 웹 UI에 로그인합니다. 기본 암호는 *Password1*입니다. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. 디바이스 관리자 암호를 변경하라는 메시지가 표시됩니다. 8~16자 길이의 새 암호를 입력합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다.

이제 디바이스의 **대시보드**에 있습니다.

## <a name="set-up-and-activate-the-physical-device"></a>물리적 디바이스 설정 및 활성화
 
1. 대시보드에서, 물리적 디바이스를 구성하고 Data Box Edge 서비스에 등록하는 데 필요한 다양한 설정으로 이동할 수 있습니다. **장치 이름**, **네트워크 설정**, **웹 프록시 설정** 및 **시간 설정**은 선택 사항입니다. 유일한 필수 설정은 **클라우드 설정**입니다.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. **장치 이름** 페이지에서 장치 이름을 구성합니다. 이름은 1~15자 길이의 문자, 숫자 및 하이픈을 포함할 수 있습니다.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (선택 사항) **네트워크 설정**을 구성합니다. 물리적 디바이스에는 6개의 네트워크 인터페이스가 표시됩니다. 포트 1 및 포트 2는 1Gbps 네트워크 인터페이스입니다. 포트 3, 포트 4, 포트 5 및 포트 6은 모두 25Gbps 네트워크 인터페이스입니다. 포트 1은 관리 전용 포트로 자동으로 구성되지만 포트 2 및 포트 6은 모두 데이터 포트입니다. 아래 표시된 **네트워크 설정** 페이지입니다.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    네트워크 설정을 구성할 때 다음에 주의하세요.

    - 환경에서 DHCP를 사용하도록 설정하면 네트워크 인터페이스가 자동으로 구성됩니다. IP 주소, 서브넷, 게이트웨이 및 DNS가 자동으로 할당됩니다.
    - DHCP를 사용하지 않는 경우 필요에 따라 고정 IP를 할당할 수 있습니다.
    - 네트워크 인터페이스를 IPv4로 구성할 수 있습니다.
   
4. 선택적으로 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항이지만 웹 프록시를 사용할 경우 여기서만 구성할 수 있습니다.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   **웹 프록시** 페이지에서:
   
   1. `http://host-IP address or FDQN:Port number` 형식으로 **웹 프록시 URL**을 제공합니다. HTTPS URL은 지원되지 않습니다.
   2. **인증**은 **기본** 또는 **없음**으로 지정합니다.
   3. 인증을 사용하는 경우 **사용자 이름** 및 **암호**를 입력해야 합니다.
   4. **적용**을 클릭하여 구성된 웹 프록시 설정의 유효성을 검사하고 설정을 적용합니다.

5. (선택 사항) 장치에 대한 시간 설정(예: 표준 시간대 및 기본 및 보조 NTP 서버)을 구성합니다. 클라우드 서비스 공급자와 인증할 수 있도록 장치 시간을 동기화해야 하기 때문에 NTP 서버가 필요합니다.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    **시간 설정** 페이지에서:
    
    1. 드롭다운 목록에서 디바이스가 배포되는 지리적 위치를 기반으로 **표준 시간대**를 설정합니다. 디바이스의 기본 표준 시간대는 PST입니다. 장치는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.
    2. 디바이스에 **기본 NTP 서버**를 지정하거나 time.windows.com의 기본값을 적용합니다. 네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다.
    3. 선택적으로 장치에 대한 **보조 NTP 서버**를 지정합니다.
    4. **적용**을 클릭하여 구성된 시간 설정의 유효성을 검사하고 설정을 적용합니다.

6. **클라우드 설정** 페이지에서, Azure Portal의 Data Box Edge 서비스를 사용하여 장치를 활성화합니다.
    
    1. Data Box Edge에 대한 **활성화 키 가져오기**에서 얻은 [활성화 키](data-box-edge-deploy-prep.md#get-the-activation-key)를 입력합니다.

    2. **적용**을 클릭합니다. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. 디바이스가 성공적으로 활성화되면 연결 모드 옵션이 표시됩니다. 부분적으로 연결이 끊어짐 또는 연결 끊김 모드에서 디바이스를 사용해야 하는 경우 이러한 설정이 구성됩니다. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

디바이스 설정이 완료되었습니다. 이제 디바이스에서 공유를 추가할 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Edge 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 물리적 디바이스에 연결
> * 물리적 디바이스 설정 및 활성화


다음 자습서로 넘어가서 Data Box Edge를 사용하여 데이터를 전송하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Data Box Edge를 사용하여 데이터 전송](./data-box-edge-deploy-add-shares.md)