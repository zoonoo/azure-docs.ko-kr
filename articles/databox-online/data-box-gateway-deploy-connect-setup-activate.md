---
title: Azure Portal에서 Azure Data Box Gateway에 연결하고, 구성하고, 활성화 | Microsoft Docs
description: Data Box Gateway 배포에 대한 세 번째 자습서에서는 가상 디바이스에 연결하고, 설정하고, 활성화하는 방법을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402338"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>자습서: 연결 설정, Azure 데이터 상자 게이트웨이 활성화 합니다.

## <a name="introduction"></a>소개

이 자습서에 연결을 설정 하 여, 로컬 웹 UI를 사용 하 여 데이터 상자 게이트웨이 장치를 활성화 하는 방법을 설명 합니다. 

설정 및 활성화 프로세스를 완료하는 데 10분 정도가 소요됩니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 장치에 연결
> * 가상 디바이스 설정 및 활성화

## <a name="prerequisites"></a>필수 조건

Data Box Gateway를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* 가상 장치를 프로 비전 하 고에 설명 된 대로 연결 된 URL을 얻은 합니다 [데이터 상자 게이트웨이 Hyper-v에서 프로 비전](data-box-gateway-deploy-provision-hyperv.md) 또는 [VMware에서 데이터 상자 게이트웨이 프로 비전](data-box-gateway-deploy-provision-vmware.md)합니다.
* Data Box Gateway 디바이스를 관리하기 위해 만든 Data Box Gateway 서비스의 활성화 키를 갖고 있습니다. 자세한 내용은 [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)를 참조하세요.


## <a name="connect-to-the-local-web-ui-setup"></a>로컬 웹 UI 설정에 연결 

1. 브라우저 창을 열고 액세스 로컬 웹 UI에서 장치:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   이전 자습서에서 언급한 연결 URL을 사용합니다. 웹 사이트의 보안 인증서에 문제가 있음을 나타내는 오류 또는 경고가 표시됩니다.

2. **이 웹 페이지에서 계속 진행**을 선택합니다. 이러한 단계는 사용 중인 브라우저에 따라 달라질 수 있습니다.
   
    ![웹 사이트 보안 인증서 오류 메시지](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. 가상 디바이스의 웹 UI에 로그인합니다. 기본 암호는 *Password1*입니다. 
   
    ![로컬 웹 UI에 로그인](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. 프롬프트에서 장치 암호를 변경 합니다. 새 암호는 8 ~ 16 자 포함 되어야 합니다. 세 가지를 포함 해야 합니다: 대문자, 소문자, 숫자 및 특수 문자입니다.

    ![디바이스 암호 변경](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

이제 디바이스의 **대시보드**에 있습니다.

## <a name="set-up-and-activate-the-virtual-device"></a>가상 디바이스 설정 및 활성화
 
대시보드를 구성 하 고 데이터 상자 게이트웨이 서비스를 사용 하 여 가상 장치를 등록 하는 데 필요한 다양 한 설정을 표시 합니다. **디바이스 이름**, **네트워크 설정**, **웹 프록시 설정** 및 **시간 설정**은 선택 사항입니다. 유일한 필수 설정은 **클라우드 설정**입니다.
   
![로컬 웹 UI "대시보드" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. 왼쪽 창에서 선택 **장치 이름**, 장치에 대 한 친숙 한 이름을 입력 합니다. 친숙 한 이름은 1에서 15 자까지를 포함 하 고 문자, 숫자 및 하이픈만 포함할 합니다.

    ![로컬 웹 UI "장치 이름" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (선택 사항) 왼쪽 창에서 선택 **네트워크 설정** 다음 설정을 구성 합니다. 가상 장치에서 하나 이상의 네트워크 인터페이스 및 기본 가상 컴퓨터에서 구성한 개수에 따라 표시 됩니다. 아래와 같이 네트워크 인터페이스 하나가 설정된 가상 디바이스의 **네트워크 설정** 페이지가 표시됩니다.
    
    ![로컬 웹 UI "네트워크 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    네트워크 설정을 구성할 때 염두에서에 둡니다.

    - 환경에서 DHCP를 사용하도록 설정하면 네트워크 인터페이스가 자동으로 구성됩니다. IP 주소, 서브넷, 게이트웨이 및 DNS가 자동으로 할당됩니다.
    - DHCP를 사용하지 않는 경우 필요에 따라 고정 IP를 할당할 수 있습니다.
    - 네트워크 인터페이스를 IPv4로 구성할 수 있습니다.

     >[!NOTE] 
     > 디바이스에 연결할 다른 IP 주소가 없다면 네트워크 인터페이스의 로컬 IP 주소를 정적에서 DCHP로 전환하지 않는 것이 좋습니다. 하나의 네트워크 인터페이스를 사용하며 DHCP로 전환하는 경우 DHCP 주소를 확인할 방법이 없습니다. DHCP 주소로 변경하려는 경우 디바이스가 서비스에 등록될 때까지 기다렸다가 변경합니다. 그러면 서비스에 대한 Azure Portal의 **디바이스 속성**에서 모든 어댑터의 IP를 볼 수 있습니다.

3. (선택 사항) 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항, 웹 프록시를 사용 하는 경우에이 페이지에만 구성할 수 있습니다.
   
   ![로컬 웹 UI "웹 프록시 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   에 **웹 프록시** 페이지에서 다음을 수행 합니다.
   
   1. **웹 프록시 URL** 상자에 `http://&lt;host-IP address or FQDN&gt;:Port number` 형식으로 URL을 입력합니다. HTTPS URL은 지원되지 않습니다.
   2. **인증** 아래에서 **없음** 또는 **NTLM**을 선택합니다.
   3. 인증을 사용 하는 경우 입력을 **사용자 이름** 하 고 **암호**합니다.
   4. 유효성을 검사 하 고 구성 된 웹 프록시 설정을 적용 하려면 선택 **적용**합니다.

4. (선택 사항) 왼쪽 창에서 **시간 설정**을 선택하고 디바이스의 표준 시간대와 기본 및 보조 NTP 서버를 구성합니다. 

    클라우드 서비스 공급자와 인증할 수 있도록 디바이스 시간을 동기화해야 하기 때문에 NTP 서버가 필요합니다.
    
    ![로컬 웹 UI "시간 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    에 **시간 설정** 페이지에서 다음을 수행 합니다.
    
    1. 에 **시간대** 드롭 다운 목록에서 장치가 배포 된 지리적 위치에 해당 하는 표준 시간대를 선택 합니다.
        디바이스의 기본 표준 시간대는 PST입니다. 디바이스는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.

    2. 지정는 **기본 NTP 서버** 장치용의 기본값을 그대로 또는 `time.windows.com`합니다.   
        네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다.

    3. 필요에 따라 합니다 **보조 NTP 서버** 상자에 장치에 대 한 보조 서버를 입력 합니다.

    4. 구성된 시간 설정의 유효성을 검사하고 설정을 적용하려면 **적용**을 선택합니다.

6. 왼쪽된 창에서 선택 **클라우드 설정**, 한 다음 Azure portal에서 데이터 상자 게이트웨이 서비스를 사용 하 여 장치를 활성화 합니다.
    
    1. 에 **정품 인증 키** 상자에 입력 합니다는 **정품 인증 키** 가져온 [정품 인증 키 가져오기](data-box-gateway-deploy-prep.md#get-the-activation-key) 데이터 상자 게이트웨이에 대 한 합니다.

    2. **활성화**를 선택합니다.
       
         ![로컬 웹 UI "클라우드 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. 장치가 활성화 되어 및 중요 업데이트, 사용 가능한 경우 자동으로 적용 됩니다. 그 결과 알림이 표시 됩니다. Azure portal 통해 업데이트 진행률을 모니터링 합니다.

        ![로컬 웹 UI "클라우드 설정" 페이지](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **대화 상자에 복사 하 고 안전한 위치에 저장 해야 하는 복구 키를 있습니다. 이 키는 장치가 부팅 될 수 없습니다. 이벤트 데이터를 복구 하려면 사용 됩니다.**


    4. 업데이트를 성공적으로 완료 하려면 몇 분 정도 대기 해야 합니다. 업데이트 후이 완료 된 장치에 로그인 합니다. 합니다 **클라우드 설정** 페이지 업데이트 장치가 성공적으로 활성화 되어 있는지를 나타냅니다.

        ![로컬 웹 UI "클라우드 설정" 페이지 업데이트](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

디바이스 설정이 완료되었습니다. 이제 디바이스에서 공유를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가상 장치에 연결
> * 가상 디바이스 설정 및 활성화

데이터 상자 게이트웨이 사용 하 여 데이터를 전송 하는 방법에 알아보려면 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [Data Box Gateway를 통해 데이터 전송](./data-box-gateway-deploy-add-shares.md).
