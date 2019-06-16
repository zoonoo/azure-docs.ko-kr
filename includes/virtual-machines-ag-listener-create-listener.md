---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165489"
---
이 단계에서는 장애 조치(Failover) 클러스터 관리자와 SQL Server Management Studio에서 가용성 그룹 수신기를 수동으로 만듭니다.

1. 주 복제본을 호스팅하는 노드에서 [장애 조치(Failover) 클러스터 관리자]를 엽니다.

2. **네트워크** 노드를 선택한 다음 클러스터 네트워크 이름을 적어둡니다. 이 이름은 PowerShell 스크립트에서 $ClusterNetworkName 변수에 사용됩니다.

3. 클러스터 이름을 확장한 다음 **역할**을 클릭합니다.

4. **역할** 창에서 가용성 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **리소스 추가** > **클라이언트 액세스 지점**을 차례로 선택합니다.
   
    ![가용성 그룹에 대한 클라이언트 액세스 지점 추가](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. **이름** 상자에서 이 새 수신기에 대한 이름을 입력하고, **다음**을 두 번 클릭한 다음, **마침**을 클릭합니다.  
    현재 온라인 상태에서 수신기 또는 리소스를 가져오지 마세요.

6. **리소스** 탭을 클릭한 다음 방금 만든 클라이언트 액세스 지점을 펼칩니다. 
    클러스터의 각 클러스터 네트워크에 대한 IP 주소 리소스가 표시됩니다. Azure 전용 솔루션인 경우 하나의 IP 주소 리소스만 표시됩니다.

7. 다음 중 하나를 수행합니다.
   
   * 하이브리드 솔루션 구성:
     
        a. 온-프레미스 서브넷에 해당하는 IP 주소 리소스를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다. IP 주소 이름과 네트워크 이름을 적어둡니다.
   
        b. **고정 IP 주소**를 선택하고 사용되지 않은 IP 주소를 할당한 다음 **확인**을 클릭합니다.
 
   * Azure 전용 솔루션 구성:

        a. Azure 서브넷에 해당하는 IP 주소 리소스를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.
       
       > [!NOTE]
       > 나중에 DHCP에서 선택된 IP 주소와 충돌하여 수신기가 온라인 상태가 되지 않을 경우 이 [속성] 창에서 유효한 고정 IP 주소를 구성할 수 있습니다.
       > 
       > 

       b. 동일한 **IP 주소** 속성 창에서 **IP 주소 이름**을 변경합니다.  
        이 이름은 PowerShell 스크립트의 $IPResourceName 변수에 사용됩니다. 솔루션이 여러 Azure 가상 네트워크에 걸쳐 있는 경우 각 IP 리소스에 대해 이 단계를 반복합니다.

