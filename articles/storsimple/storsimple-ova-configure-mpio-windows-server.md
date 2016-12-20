---
title: "StorSimple 가상 배열 호스트에 MPIO 구성| Microsoft Docs"
description: "Windows Server 2012 R2를 실행하는 호스트에 연결된 StorSimple 가상 배열에 대해 MPIO(다중 경로 I/O)를 구성하는 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 57b54af8-ca0d-4a89-bb65-5f4b9eb740ea
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7775214cc187222e97a9168e6e5fd3fbbc8e2006


---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Windows Server 호스트에 StorSimple 가상 배열에 대한 다중 경로 I/O 구성
## <a name="overview"></a>개요
이 문서에서는 Windows Server 호스트에 MPIO(다중 경로 I/O) 기능을 설치하고, StorSimple 전용 볼륨에 대한 특정 구성 설정을 적용한 다음 StorSimple 볼륨의 MPIO를 확인하는 방법을 설명합니다. 이 절차에서는 두 개의 네트워크 인터페이스가 있는 StorSimple 1200 가상 배열이 두 개의 네트워크 인터페이스가 있는 Windows Server 호스트에 연결된 것으로 가정합니다. 이 문서에 포함된 정보는 가상 배열에만 적용됩니다. StorSimple 8000 시리즈 장치에 대한 자세한 내용은 [StorSimple 호스트에 대한 MPIO 구성](storsimple-configure-mpio-windows-server.md)을 참조하세요. 

Windows Server의 MPIO 기능은 가용성이 높고 내결함성을 갖춘 저장소 구성을 빌드하는 데 유용합니다. MPIO는 중복 실제 경로 구성 요소(어댑터, 케이블 및 스위치)를 사용하여 서버 및 저장소 장치 간의 논리 경로를 만듭니다. 논리 경로에 오류를 일으키는 구성 요소 오류가 발생할 경우 응용 프로그램이 데이터에 계속 액세스할 수 있도록 다중 경로 논리가 I/O에 대한 대체 경로를 사용합니다. 또한 구성에 따라 MPIO가 이러한 모든 경로에서 부하를 다시 분산하여 성능을 향상할 수도 있습니다. 자세한 내용은 [MPIO 개요](https://technet.microsoft.com/library/cc725907.aspx "MPIO 개요 and features")을 참조하세요.  

StorSimple 솔루션의 가용성을 높이려면 StorSimple 1200 가상 배열(온-프레미스 가상 장치라고도 함)에 연결된 Windows Server 호스트에 MPIO를 구성하세요. 그러면 호스트 서버가 링크, 네트워크 또는 인터페이스 장애를 허용할 수 있습니다. 

MPIO를 구성하려면 다음 단계를 수행해야 합니다. 

* 필수 구성 요소
* 1단계: Windows Server 호스트에 MPIO 설치
* 2단계: StorSimple 볼륨에 대한 MPIO 구성
* 3단계: 호스트에 StorSimple 볼륨 탑재

위의 각 단계는 다음 섹션에서 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 섹션에서는 Windows Server 호스트 및 가상 배열의 필수 구성 요소를 자세히 설명합니다.

### <a name="on-windows-server-host"></a>Windows Server 호스트
* Windows Server 호스트에 사용 가능한 2개의 네트워크 인터페이스가 있는지 확인합니다.

### <a name="on-storsimple-virtual-array"></a>StorSimple 가상 배열
* 가상 배열이 iSCSI 서버로 구성되어야 합니다. 자세한 내용은 [가상 배열을 iSCSI 서버로 설정](storsimple-ova-deploy3-iscsi-setup.md)을 참조하세요. 배열에서 하나 이상의 네트워크 인터페이스가 활성화되어야 합니다.   
* Windows Server 호스트에서 가상 배열의 네트워크 인터페이스에 도달할 수 있어야 합니다.
* StorSimple 가상 배열에 하나 이상의 볼륨을 만들어야 합니다. 자세한 내용은 StorSimple 1200 가상 배열의 [볼륨 추가](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) 를 참조하세요. 이 절차에서는 가상 배열에 볼륨 3개(아래 그림처럼 2개는 로컬에 고정, 1개는 계층화된 볼륨)를 만들었습니다.
  
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>StorSimple 가상 배열에 대한 하드웨어 구성
아래 그림은 이 절차에 사용되는 Windows Server 호스트 및 StorSimple 가상 배열의 고가용성과 다중 경로 부하 분산을 위한 하드웨어 구성을 보여줍니다.  

![mpio 하드웨어 구성](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

이전 그림에서 표시된 것처럼

* Hyper-V에 프로비전된 StorSimple 가상 배열은 iSCSI 서버로 구성된 단일 노드 활성 장치입니다.
* 배열에 두 개의 가상 네트워크 인터페이스가 활성화되었습니다. 1200 가상 배열의 로컬 웹 UI에서 아래와 같이 **네트워크 설정** 으로 이동하여 두 네트워크 인터페이스가 활성화되었는지 확인하세요.
  
    ![1200에 활성화된 네트워크 인터페이스](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
  
    활성화된 네트워크 인터페이스의 IPv4 주소(기본적으로 이더넷, 이더넷 2)를 확인하고 나중에 호스트에서 사용할 수 있도록 저장해 둡니다.
* Windows Server 호스트에 두 개의 네트워크 인터페이스가 활성화되었습니다. 호스트 및 배열에 대해 연결된 인터페이스가 동일한 서브넷에 있으면 4개 경로를 사용할 수 있습니다. 이 절차의 사례가 바로 그렇습니다. 그러나 배열의 각 네트워크 인터페이스와 호스트 인터페이스가 서로 다른 IP 서브넷에 있으면(따라서 라우팅할 수 없음) 2개 경로만 사용할 수 있습니다.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>1단계: Windows Server 호스트에 MPIO 설치
MPIO는 Windows Server에서 선택적 기능이며 기본적으로 설치되지 않습니다. 서버 관리자를 통해 기능으로 설치해야 합니다. Windows Server 호스트에 이 기능을 설치하려면 다음 절차를 완료합니다.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>2단계: StorSimple 볼륨에 대한 MPIO 구성
MPIO는 StorSimple 볼륨을 식별하도록 구성해야 합니다. StorSimple 볼륨을 인식하도록 MPIO를 구성하려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>3단계: 호스트에 StorSimple 볼륨 탑재
MPIO가 Windows Server에 구성된 후 StorSimple 배열에 생성된 볼륨이 탑재될 수 있으며 중복에 대해 MPIO를 활용할 수 있습니다. 볼륨을 탑재하려면 다음 단계를 수행합니다.

#### <a name="to-mount-volumes-on-the-host"></a>호스트에 볼륨을 탑재 하려면
1. Windows Server 호스트에서 **iSCSI 초기자 속성** 창을 엽니다. **서버 관리자 > 대시보드 > 도구 > iSCSI 초기자**를 클릭합니다.
2. **iSCSI 초기자 속성** 대화 상자에서 검색 탭을 클릭한 다음 **대상 포털 검색**을 클릭합니다.
3. **대상 포털 검색** 대화 상자에서 다음을 수행합니다.
   
   * StorSimple 가상 배열에서 첫 번째로 활성화된 네트워크 인터페이스의 IP 주소를 입력합니다. 기본적으로 **이더넷**이 됩니다. 
   * **확인** 을 클릭하여 **iSCSI 초기자 속성** 대화 상자로 돌아갑니다.
     
     > [!IMPORTANT]
     > **iSCSI 연결에 개인 네트워크를 사용하는 경우 개인 네트워크에 연결된 데이터 포트의 IP 주소를 입력합니다.**
     > 
     > 
4. 배열의 두 번째 네트워크 인터페이스(예: 이더넷 2)에 대해 2~3단계를 반복합니다. 
5. **iSCSI 초기자 속성** 대화 상자에서 **대상** 탭을 선택합니다. 가상 배열의 경우 각 볼륨 서피스가 **검색된 대상**아래에 대상으로 표시될 것입니다. 이 경우 3개 대상(3개의 볼륨에 해당하는)이 검색될 것입니다.
   
    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)
6. **연결** 을 클릭하여 StorSimple 배열에 iSCSI 세션을 설정합니다. **대상에 연결** 대화 상자가 표시됩니다. **다중 경로 사용** 확인란을 선택합니다. **고급**을 클릭합니다.
   
    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)
7. **고급 설정** 대화 상자에서 다음을 수행합니다.                                        
   
   * **로컬 어댑터** 드롭다운 목록에서 **Microsoft iSCSI 초기자**를 선택합니다.
   * **초기자 IP** 드롭다운 목록에서 호스트의 IP 주소를 선택합니다.
   * **대상 포털** IP 드롭다운 목록에서 배열 인터페이스의 IP를 선택합니다.
   * **확인** 을 클릭하여 **iSCSI 초기자 속성** 대화 상자로 돌아갑니다.
     
     ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)
8. **속성**을 클릭합니다. 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
9. **속성** 대화 상자에서 **세션 추가**를 클릭합니다.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. **대상에 연결** 대화 상자에서 **다중 경로 사용** 확인란을 선택합니다. **고급**을 클릭합니다.
11. **고급 설정** 대화 상자에서:                                        
    
    * **로컬 어댑터** 드롭다운 목록에서 Microsoft iSCSI 초기자를 선택합니다.
    * **초기자 IP** 드롭다운 목록에서 호스트에 해당하는 IP 주소를 선택합니다. 이 경우에 해당 배열의 두 네트워크 인터페이스를 호스트의 단일 네트워크 인터페이스에 연결합니다. 따라서이 인터페이스는 첫 번째 세션에 제공된 것과 동일합니다.
    * **대상 포털 IP** 드롭다운 목록에서 해당 배열에서 사용할 수 있는 두 번째 데이터 인터페이스의 IP 주소를 선택합니다.
    * **확인** 을 클릭하여 iSCSI 초기자 속성 대화 상자로 돌아갑니다. 두 번째 세션을 대상에 추가했습니다.
      
       ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)
    * 원하는 세션(경로)을 추가한 후 **iSCSI 초기자 속성** 대화 상자에서 대상을 선택하고 **속성**을 클릭합니다. **속성** 대화 상자의 세션 탭에서 가능한 경로 순열에 해당하는 네 개의 세션 식별자를 기록해 둡니다. 세션을 취소하려면 세션 식별자를 옆에 있는 확인란을 선택하고 **연결 끊기**를 클릭합니다.
    * 세션 내에 표시되는 장치를 보려면 **장치** 탭을 선택합니다. 선택한 장치에 대한 MPIO 정책을 구성하려면 **MPIO**를 클릭합니다. **
    * 세부 정보** 대화 상자가 표시됩니다. **MPIO** 탭에서 적절한 **부하 분산 정책** 설정을 선택할 수 있습니다. **활성** 또는 **대기** 경로 유형도 볼 수 있습니다.
12. 대상에 추가 세션(경로)을 추가하려면 8-11단계를 반복합니다. 호스트의 두 인터페이스 및 가상 배열의 두 인터페이스를 사용하여 각 대상에 대해 총 네 개의 세션을 추가할 수 있습니다. 
    
    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)
13. 각 볼륨에 대해 이러한 단계를 반복해야 합니다(서피스를 대상으로).
    
    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)
14. **서버 관리자 > 대시보드 > 컴퓨터 관리**로 이동하여 **컴퓨터 관리**를 엽니다. 왼쪽 창에서 **저장소 > 디스크 관리**를 클릭합니다. 이 호스트에 표시되는 StorSimple 가상 배열에 만들어진 볼륨이 **디스크 관리** 에 새 디스크로 나타납니다.
15. 디스크를 초기화하고 새 볼륨을 만듭니다. 포맷 프로세스에서 할당 단위 크기(AUS)를 64KB로 선택합니다. 사용 가능한 모든 볼륨에 대해 이 프로세스를 반복합니다.
    
    ![디스크 관리](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)
16. **디스크 관리**에서 **디스크**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
17. **다중 경로 디스크 장치 속성** 대화 상자에서 **MPIO** 탭을 클릭합니다.
    
    ![디스크 속성](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)
18. **DSM 이름** 섹션에서 **세부 정보**를 클릭하고 해당 매개 변수가 기본 매개 변수로 설정되었는지 확인합니다. 기본 매개 변수는 다음과 같습니다.
    
    * 경로 확인 기간 = 30
    * 재시도 횟수 = 3
    * PDO 제거 기간 = 20
    * 재시도 간격 = 1
    * 경로 확인 사용 = 선택하지 않음
    
    > [!NOTE]
    > **기본 매개 변수를 수정하지 마세요.**
    > 
    > 

## <a name="next-steps"></a>다음 단계
[StorSimple Manager 서비스를 사용하여 StorSimple 가상 배열을 관리](storsimple-ova-manager-service-administration.md)하는 방법을 자세히 알아봅니다.




<!--HONumber=Nov16_HO3-->


