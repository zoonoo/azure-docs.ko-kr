---
title: Azure Data Box 설정| Microsoft Docs
description: 장치에 케이블을 연결하고 Azure Data Box에 연결하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/17/2018
ms.author: alkohli
ms.openlocfilehash: c3766713c87dd721f7e4c9a90624192cdb402be9
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393833"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>자습서: Azure Data Box에 케이블 연결

이 자습서에서는 Azure Data Box Disk에 케이블을 연결하고, 연결하고, 전원을 켜는 방법을 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Data Box 케이블 연결
> * Data Box에 연결

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box 주문](data-box-deploy-ordered.md)을 완료했습니다.
2. Data Box를 받았고 포털의 주문 상태가 **배달됨**입니다. 현재 레이블 아래의 장치에 부착된 투명 주머니에 배송 레이블이 있습니다. 반품 발송 시 사용할 수 있으므로 이 레이블을 안전하게 보관하세요.
3. [Data Box 안전 지침](data-box-safety.md)을 검토했습니다.
4. 100TB 저장소 장치에 사용할 하나의 접지식 전원 코드를 수취했습니다.
5. Data Box에 복사할 데이터가 포함된 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
    - [지원되는 운영 체제](data-box-system-requirements.md)를 실행합니다.
    - 고속 네트워크에 연결되어 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없는 경우 1GbE 데이터 링크를 사용할 수 있지만 복사 속도에 영향을 미칩니다. 
6. Data Box를 놓을 평평한 표면이 있어야 합니다. 표준 랙 선반에 장치를 놓으려면 데이터 센터 랙에 7U 슬롯이 있어야 합니다. 장치는 랙에 평평하게 놓거나 똑바로 세울 수 있습니다.
7. Data Box를 호스트 컴퓨터에 연결하기 위해 다음과 같은 케이블을 준비해 둔 상태여야 합니다.
    - 10GbE SFP+ 2축 동 케이블 2개(DATA 1, DATA 2 네트워크 인터페이스에 사용)
    - RJ-45 CAT 6 네트워크 케이블 1개(MGMT 네트워크 인터페이스에 사용)
    - RJ-45 CAT 6A 하나 또는 RJ-45 CAT 6 네트워크 케이블 하나(각각 10Gbps 또는 1Gbps로 구성된 DATA 3 네트워크 인터페이스에 사용)

## <a name="cable-your-device"></a>장치 케이블 연결

다음 단계를 수행하여 장치에 케이블을 연결합니다.

1. 장치에 변조의 증거 또는 기타 명백한 손상이 있는지 검사합니다. 장치가 변조되거나 심각하게 손상된 경우에는 진행하지 마십시오. 장치의 작동 상태가 양호한지 교체품을 배송해야 하는지 여부를 평가하는 데 도움을 얻으려면 Microsoft 지원에 즉시 문의하세요.
2. 장치의 전원을 켜려는 위치로 장치를 옮깁니다. 표면이 평평한 곳에 장치를 놓습니다. 표준 랙 선반 위에 장치를 놓을 수도 있습니다.
3. 전원 및 네트워크 케이블을 연결합니다. 연결된 장치의 백플레인에 대한 일반적 구성은 아래와 같습니다. 
    
    ![케이블이 연결된 Data Box 장치의 백플레인](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. 레이블이 지정된 전원 입력 위치에 전원 케이블을 연결합니다. 전원 케이블의 다른 쪽 끝은 배전 장치에 연결해야 합니다.
    2. RJ-45 CAT 6 케이블의 한쪽 끝은 MGMT 포트에 연결하고 다른 쪽 끝은 랩톱에 연결합니다.            
    3. RJ-45 CAT 6A 케이블의 한쪽 끝을 DATA 3 포트에 연결합니다. DATA 3은 RJ-45 CAT 6A 케이블을 통해 연결하는 경우 10GbE로, RJ-45 CAT 6 케이블을 통해 연결하는 경우에는 1GbE로 구성됩니다.
    4. 10 GbE SFP + 2축 동 케이블을 사용하여 DATA 1 및 DATA 2 포트 각각에 연결합니다. 
    5. 데이터 포트에 연결된 케이블의 다른 쪽 끝은 10GbE 스위치를 통해 호스트 컴퓨터에 연결됩니다.

4. 장치의 전면 조작 패널에서 전원 단추를 찾습니다. 장치를 켭니다.

    ![Data Box 전원 단추](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>장치에 연결

다음 단계를 수행하여 로컬 웹 UI 및 포털 UI를 사용하여 장치를 설정합니다.

1. 사용 중인 랩톱에서 이더넷 어댑터를 구성하여 고정 IP 주소가 192.168.100.5이고 서브넷이 255.255.255.0인 장치에 연결합니다. 
2. 장치의 MGMT 포트에 연결하고 로컬 웹 UI(https://192.168.100.10)에 액세스합니다. 장치를 켠 후 최대 5분이 소요될 수 있습니다.
3. **세부 정보**를 클릭한 다음, **웹 페이지로 이동**을 클릭합니다.

   ![로컬 웹 UI에 연결](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. 로컬 웹 UI에 대한 **로그인** 페이지가 보입니다. 장치 일련 번호가 포털 UI와 로컬 웹 UI 모두에서 일치하는지 확인합니다. 이 때는 장치가 잠겨있습니다.
5. [Azure Portal](https://portal.azure.com)에 로그인합니다.
6. 포털에서 장치 자격 증명을 다운로드합니다. **일반 > 장치 세부 정보**로 이동합니다. **장치 암호**를 복사합니다. 장치 암호는 포털에서 특정 순서에 연결됩니다. 

    ![장치 자격 증명 얻기](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. 이전 단계에 Azure Portal에서 얻은 장치 암호를 입력하여 장치의 로컬 웹 UI에 로그인합니다. **로그인**을 클릭합니다.
8. **대시보드**에서 네트워크 인터페이스가 구성되어 있는지 확인합니다. 
    - 환경에서 DHCP를 사용하도록 설정하면 네트워크 인터페이스가 자동으로 구성됩니다. 
    - DHCP를 사용하도록 설정하지 않으면 필요한 경우 **집합 네트워크 인터페이스**로 이동하여 정적 IP를 할당합니다.

    ![장치 대시보드](media/data-box-deploy-set-up/data-box-dashboard-1.png)

데이터 네트워크 인터페이스가 구성되면 DATA 1 - DATA 3 인터페이스의 아무 IP 주소를 사용하여 `https://<IP address of a data network interface>`에서 로컬 웹 UI에 액세스할 수 있습니다. 

장치 설정이 완료되면 장치 공유에 연결하여 컴퓨터의 데이터를 장치로 복사할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box 케이블 연결
> * Data Box에 연결

Data Box에서 데이터를 복사하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box에 데이터 복사](./data-box-deploy-copy-data.md)

