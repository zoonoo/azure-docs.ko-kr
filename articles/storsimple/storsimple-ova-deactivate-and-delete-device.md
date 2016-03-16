<properties 
   pageTitle="StorSimple 가상 배열 비활성화 및 삭제 | Microsoft Azure"
   description="먼저 StorSimple 장치를 비활성화한 후 삭제하여 서비스에서 제거하는 방법을 설명합니다."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/08/2016"
   ms.author="alkohli" />

# StorSimple 가상 배열 비활성화 및 삭제

## 개요

StorSimple 가상 배열을 비활성화하면 장치 및 해당 StorSimple Manager 서비스 간의 연결이 끊깁니다. 비활성화는 영구 작업이며 실행 취소할 수 없습니다. 비활성화된 장치는 StorSimple Manager 서비스에 다시 등록할 수 없습니다.

다음과 같은 시나리오에서 StorSimple 가상 장치를 비활성화 및 삭제해야 합니다.


- 장치가 온라인 상태이고 이 장치를 장애 조치할 계획입니다. 더 큰 장치로 업그레이드하려는 경우 이 작업을 수행해야 합니다. 장치 데이터를 전송하고 장애 조치가 완료된 후 장치를 삭제할 수 있습니다.

- 장치가 오프라인 상태이고 이 장치를 장애 조치할 계획입니다. 데이터 센터 가동 중단으로 인해 재해가 발생한 경우 발생할 수 있으며 기본 장치가 종료됩니다. 장치를 보조 장치로 장애 조치하려고 합니다. 장치 데이터를 전송하고 장애 조치가 완료된 후 장치를 삭제할 수 있습니다.

- 장치를 서비스 해제한 다음 삭제하려고 합니다.
 

장치를 비활성화하면 로컬로 저장된 데이터에 더 이상 액세스할 수 없게 됩니다. 클라우드에 저장된 데이터만 복구할 수 있습니다. 비활성화 후 장치 데이터를 유지하려는 경우 장치를 비활성화하기 전에 모든 데이터의 클라우드 스냅숏을 만들어야 합니다. 이렇게 하면 이후 단계에서 모든 데이터를 복구할 수 있습니다.


이 자습서에서는 다음을 수행하는 방법을 설명합니다.

- 장치 비활성화 
- 비활성화된 장치 삭제


## 장치 비활성화

다음 단계를 수행하여 장치를 비활성화합니다.

#### 장치를 비활성화하려면   

1. **장치 페이지** 페이지로 이동합니다. 비활성화하려는 장치를 선택합니다.

	![비활성화할 장치 선택](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)

3. 페이지 맨 아래에서 **비활성화**를 클릭합니다.

	![비활성화 클릭](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)

4. 확인 메시지가 표시됩니다. **예**를 클릭하여 계속합니다.

	![비활성화 확인](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)

	비활성화 프로세스가 시작되고 완료하는 데 몇 분이 소요됩니다.

	![진행 중인 비활성화](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)

3. 비활성화 후 장치 목록이 새로 고쳐집니다.

	![비활성화 완료](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

	이제 이 장치를 삭제할 수 있습니다.

## 장치 삭제

삭제하려면 먼저 장치를 비활성화해야 합니다. 장치를 삭제하면 서비스에 연결된 장치 목록에서 제거됩니다. 그러면 서비스에서 삭제된 장치를 더 이상 관리할 수 없습니다. 그러나 장치와 연결된 데이터는 클라우드에 유지됩니다. 이 데이터는 요금이 부과됩니다.

장치를 삭제하려면 다음 단계를 완료합니다.

#### 장치를 삭제하려면 

 1. StorSimple Manager 서비스 **장치**페이지에서 삭제할 비활성화된 장치를 선택합니다.

	![삭제할 장치 선택](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

 2. 페이지 맨 아래에서 **삭제**를 클릭합니다.
 
	![삭제 클릭](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)

 3. 확인하라는 메시지가 표시됩니다. 장치 이름을 입력하여 장치 삭제를 확인합니다. 장치 삭제는 장치와 연결된 클라우드 데이터를 삭제하지 않습니다. 확인 아이콘을 클릭하여 계속합니다.
 
	![삭제 확인](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png)

 5. 장치를 삭제하는 데는 몇 분 정도 걸릴 수 있습니다.

	![진행 중인 삭제](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)

 	장치를 삭제한 후 장치 목록이 새로 고쳐집니다.

	![삭제 완료](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)


## 다음 단계

- StorSimple Manager 서비스를 사용하는 방법을 자세히 알아보려면 [StorSimple Manager 서비스를 사용하여 StorSimple 가상 배열 관리](storsimple-ova-manager-service-administration.md)로 이동하세요. 

<!---HONumber=AcomDC_0218_2016-->