---
title: "StorSimple 가상 배열에 대한 재해 복구 및 장치 장애 조치(failover)"
description: "StorSimple 가상 배열 장애 조치 방법에 대해 자세히 알아봅니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 94a8c3db-8e47-4e9a-917a-29b14a9263aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 67cdf4e3d8e81d2ea419603918a3204dc6860ae7


---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>StorSimple 가상 배열에 대한 재해 복구 및 장치 장애 조치(failover)
## <a name="overview"></a>개요
이 문서는 재해 발생 시 다른 가상 장치로 장애 조치를 취하는 데 필요한 자세한 단계를 비롯하여 Microsoft Azure StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치라고도 함)에 대한 재해 복구를 설명합니다. 장애 조치를 통해 데이터 센터에 있는 *원본* 장치의 데이터를 동일한 또는 다른 지리적 위치에 있는 별개의 *대상* 장치로 마이그레이션할 수 있습니다. 장치 장애 조치는 전체 장치를 대상으로 합니다. 장애 조치를 수행하는 동안 원본 장치의 클라우드 데이터는 대상 장치의 그것으로 소유권이 변경됩니다.

장치 장애 조치는 재해 복구(DR) 기능을 통해 조정되며 **장치** 페이지에서 시작됩니다. 이 페이지는 StorSimple 관리자 서비스에 연결된 모든 StorSimple 장치를 표로 작성합니다. 각 장치에 대해 친숙한 이름, 상태, 프로비전된 용량 및 최대 용량, 유형 및 모델이 표시됩니다.

![](./media/storsimple-ova-failover-dr/image15.png)

이 문서는 StorSimple 가상 배열에만 적용할 수 있습니다. 8000 시리즈 장치에 장애 조치를 하려면 [StorSimple 장치에 대한 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)로 이동합니다.

## <a name="what-is-disaster-recovery"></a>재해 복구란 무엇인가요?
DR(재해 복구) 시나리오에서 기본 장치가 작동을 중지합니다. 이 상황에서 기본 장치를 *원본*으로 사용하고 다른 장치를 *대상*으로 지정하여 실패한 장치와 연결된 클라우드 데이터를 다른 장치로 옮길 수 있습니다. 이 프로세스는 *장애 조치*라고 합니다. 장애 조지 중에는 원본 장치의 모든 볼륨 또는 공유가 소유권을 변경하고 대상 장치로 전송됩니다. 데이터에 대한 필터링은 허용되지 않습니다.

DR은 열 지도 기반 계층화 및 추적을 사용하여 전체 장치 복원으로 모델링됩니다. 열 지도는 읽기 및 쓰기 패턴을 기반으로 데이터에 열 값을 할당하여 정의됩니다. 이 열 지도는 높은 열(가장 많이 사용되는) 데이터 청크는 로컬 계층에 유지하면서, 최저 열 데이터 청크를 클라우드에 우선 계층화합니다. DR 중에, 열 지도는 클라우드로부터 데이터를 복원하고 리하이드레이션하는 데 사용됩니다. 장치는 최신의 마지막 백업(내부적으로 확인된)에서 모든 볼륨/공유를 가져와서 해당 백업으로부터 복원을 수행합니다. 전체 DR 프로세스는 장치에 의해 오케스트레이션됩니다.

## <a name="prerequisites-for-device-failover"></a>장치 장애 조치에 대한 필수 조건
### <a name="prerequisites"></a>필수 조건
모든 장치 장애 조치에 대해 다음과 같은 필수 조건을 충족해야 합니다.

* 원본 장치는 **비활성화됨** 상태여야 합니다.
* 대상 장치는 Azure 클래식 포털에 **활성** 으로 표시되어야 합니다. 용량이 동일하거나 더 높은 대상 가상 장치를 프로비전해야 합니다. 그 후 로컬 웹 UI를 사용하여 가상 장치를 구성하고 등록을 완료해야 합니다.
  
  > [!IMPORTANT]
  > **장치 설치 완료**를 클릭하여 서비스를 통해 등록된 가상 장치를 구성하려고 시도하지 말아야 합니다. 이 서비스를 통해 장치 구성을 수행하지 말아야 합니다.
  > 
  > 
* 원본 및 대상 장치는 같은 형식이어야 합니다. 파일 서버로 구성된 가장 장치에서는 또 다른 파일 서버로만 장애 조치를 할 수 있습니다. iSCSI 서버에도 같은 내용이 적용됩니다.
* 파일 서버 DR의 경우, 공유 권한이 자동으로 해결되도록 대상 장치를 원본과 같은 도메인에 가입하는 것이 좋습니다. 이 릴리스에서는 같은 도메인에 있는 대상 장치에 대한 장애 조치만 지원됩니다.

### <a name="other-considerations"></a>기타 고려 사항
* 원본 장치의 모든 볼륨 또는 공유를 오프라인으로 전환하는 것이 좋습니다.
* 계획된 장애 조치의 경우, 데이터 손실을 최소화하기 위해 장치의 백업을 수행한 후에 장애 조치를 진행하는 것이 좋습니다. 계획되지 않은 장애 조치의 경우, 가장 최근의 백업이 장치를 복원하는 데 사용됩니다.
* DR에 사용할 수 있는 대상 장치는 원본 장치와 용량이 같거나 더 큰 장치입니다. 서비스에 연결되어 있지만 충분한 공간 조건을 충족하지 않는 장치는 대상 장치로 사용할 수 없습니다.

### <a name="dr-prechecks"></a>DR 사전 검사
DR이 시작되기 전에 장치에서 사전 검사가 수행됩니다. 이러한 검사는 DR이 시작된 후 오류가 발생하지 않도록 하는 데 도움이 됩니다. 사전 검사에는 다음 내용이 포함됩니다.

* 저장소 계정 유효성 검사
* Azure에 대한 클라우드 연결 확인
* 대상 장치의 사용 가능한 공간 확인
* iSCSI 서버 장치에 볼륨에 연결된 유효한 ACR 이름, IQN(길이가 220자를 넘지 않는), CHAP 암호(길이가 12자 및 16자)가 있는지 확인

위의 사전 검사 중 하나라도 실패하면 DR을 진행할 수 없습니다. 문제를 해결한 후 DR을 다시 시도해야 합니다.

DR이 성공적으로 완료된 후에는, 원본 장치에 있는 클라우드 데이터의 소유권이 대상 장치로 이전됩니다. 그 후 원본 장치는 포털에서 더 이상 사용할 수 없게 됩니다. 원본 장치의 모든 볼륨/공유에 대한 액세스가 차단되고 대상 장치가 활성이 됩니다.

> [!IMPORTANT]
> 장치를 더 이상 사용할 수 없지만, 호스트 시스템에서 프로비전한 가상 컴퓨터는 여전히 자원을 소비합니다. DR이 성공적으로 완료되고 나면 이 가상 컴퓨터를 호스트 시스템에서 제거할 수 있습니다.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>가상 배열에 대한 장애 조치
로컬 웹 UI를 통해 다른 StorSimple 가상 배열을 프로비전하고, 구성하고, 이 절차를 실행하기 전에 StorSimple Manager 서비스에 등록하는 것이 좋습니다.

> [!IMPORTANT]
> * StorSimple 8000 시리즈 장치에서 1200 가상 장치로 장애 조치(failover)할 수 없습니다.
> * 정부 포털에 배포된 FIPS(Federal Information Processing Standard) 사용 가상 장치에서 Azure 클래식 포털의 가상 장치로 장애 조치(failover)할 수 있습니다. 그 반대의 경우도 마찬가지입니다.
> 
> 

대상 StorSimple 가상 장치에 장치를 복원하려면 다음 단계를 수행합니다.

1. 호스트에서 볼륨/공유를 오프라인으로 전환합니다. 호스트에 관한 운영 체제별 지침을 참고하여 볼륨/공유를 오프라인으로 전환합니다. 오프라인이 아니면, **장치> 공유**(또는 **장치 > 볼륨**)로 이동하여 장치에서 모든 볼륨/공유를 오프라인으로 전환해야 합니다. 볼륨/공유를 선택하고 페이지 맨 아래에 있는 **오프라인 상태로 전환** 을 클릭합니다. 확인하라는 메시지가 표시되면 **예**를 클릭합니다. 장치의 모든 공유/볼륨에 이 프로세스를 반복합니다.
2. **장치** 페이지에서 장애 조치(failover)에 대한 원본 장치를 선택하고 **비활성화**를 클릭합니다. 
    ![](./media/storsimple-ova-failover-dr/image16.png)
3. 확인하라는 메시지가 표시됩니다. 장치 비활성화는 영구적인 프로세스이며 취소할 수 없습니다. 호스트의 공유/볼륨을 오프라인으로 전환하라는 알림이 표시됩니다.
   
    ![](./media/storsimple-ova-failover-dr/image18.png)
4. 확인이 끝나면 비활성화가 시작됩니다. 비활성화가 성공적으로 완료되면 알림이 표시됩니다.
   
    ![](./media/storsimple-ova-failover-dr/image19.png)
5. **장치** 페이지의 장치 상태가 이제 **비활성화됨**으로 변경됩니다.
   
    ![](./media/storsimple-ova-failover-dr/image20.png)
6. 비활성화된 장치를 선택하고 페이지 맨 아래에서 **장애 조치(failover)**를 클릭합니다.
7. 장애 조치(Failover) 확인 마법사가 열리면 다음을 수행합니다.
   
   1. 사용 가능한 장치 드롭다운 목록에서 **대상 장치**를 선택합니다. 충분한 용량이 있는 장치만 드롭다운 목록에 표시됩니다.
   2. 장치 이름, 총 용량, 장애 조치를 수행할 공유의 이름을 비롯한 원본 장치와 관련된 세부 정보를 검토합니다.
      
       ![](./media/storsimple-ova-failover-dr/image21.png)
8. **장애 조치(failover)는 영구 작업이며 장애 조치(failover)가 완료되면 원본 장치가 삭제되는 데 동의합니다.**확인란을 선택합니다.
9. 확인 아이콘 ![](./media/storsimple-ova-failover-dr/image1.png)을 클릭합니다.
10. 장애 조치 작업이 시작되고 알림이 표시됩니다. **작업 보기** 를 클릭하여 장애 조치(failover)를 모니터링합니다.
    
     ![](./media/storsimple-ova-failover-dr/image22.png)
11. **작업** 페이지에 원본 장치에 대해 만들어진 장애 조치(failover) 작업이 표시됩니다. 이 작업이 DR 사전 검사를 수행합니다.
    
    ![](./media/storsimple-ova-failover-dr/image23.png)
    
     DR 사전 검사가 성공적으로 완료된 후에, 장애 조치 작업은 원본 장치에 존재하는 각 공유/볼륨에 대한 복원 작업을 생성합니다.
    
    ![](./media/storsimple-ova-failover-dr/image24.png)
12. 장애 조치를 완료한 후 **장치** 페이지로 이동합니다.
    
    a. 장애 조치 프로세스에 대한 대상 장치로 사용된 StorSimple 가상 장치를 선택합니다.
    
    b. **공유** 페이지(또는 iSCSI 서버의 경우 **볼륨**)로 이동합니다. 이전 장치의 모든 공유(볼륨)가 목록으로 표시됩니다.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **동영상 사용 가능**

이 동영상은 StorSimple 온-프레미스 가상 장치를 또 다른 가상 장치에 장애 조치하는 방법을 보여줍니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/StorSimple-Virtual-Array-Disaster-Recovery/player]
> 
> 

## <a name="business-continuity-disaster-recovery-bcdr"></a>비즈니스 연속성 재해 복구(BCDR)
비즈니스 연속성 재해 복구(BCDR) 시나리오는 전체 Azure 데이터 센터의 작동이 중지되는 경우 발생합니다. StorSimple 관리자 서비스 및 연결된 StorSimple 장치에 영향을 줄 수 있습니다.

재해가 발생하기 직전에 등록된 StorSimple 장치가 있으면 해당 StorSimple 장치를 삭제해야 할 수도 있습니다. 재해가 끝난 후에 해당 장치를 다시 만들고 구성할 수 있습니다.

## <a name="errors-during-dr"></a>DR 중 오류
**DR 중 클라우드 연결 중단**

DR이 시작된 후와 장치 복원이 완료되기 전에 클라우드 연결이 중단되면, DR이 실패하고 알림이 제공됩니다. 곧이어 DR에 사용된 대상 장치가 *사용할 수 없음*으로 표시됩니다. 동일한 대상 장치는 향후 DR에 사용될 수 없습니다.

**호환되는 대상 장치 없음**

사용 가능한 대상 장치에 충분한 공간이 없으면 호환되는 대상 장치가 없다는 내용의 오류가 표시됩니다.

**사전 검사 실패**

사전 검사 중 하나가 충족되지 않으면 사전 검사 실패가 표시됩니다.

## <a name="next-steps"></a>다음 단계
[로컬 웹 UI를 사용하여 StorSimple 가상 배열을 관리](storsimple-ova-web-ui-admin.md)하는 방법에 대해 자세히 알아봅니다.




<!--HONumber=Nov16_HO3-->


