---
title: StorSimple 가상 배열에 대한 액세스 제어 레코드 관리 | Microsoft Docs
description: ACR(액세스 제어 레코드)을 관리하여 어떤 호스트가 StorSimple 가상 배열의 볼륨에 연결할 수 있는지 지정하는 방법에 대해 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2016
ms.author: alkohli

---
# StorSimple 관리자 서비스를 사용하여 StorSimple 가상 배열에 대한 액세스 제어 레코드 관리
## 개요
ACR(액세스 제어 레코드)을 사용하면 어떤 호스트가 StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치라고도 함)장치의 볼륨에 연결할 수 있는지 지정할 수 있습니다. ACR은 특정 볼륨으로 설정되며 호스트의 IQN(iSCSI 정규화된 이름)을 포함합니다. 호스트가 볼륨에 연결하려고 할 때 해당 장치는 IQN 이름에 대한 볼륨과 연결된 ACR을 확인하고 일치하는 경우 이 연결이 확정됩니다. **구성** 페이지의 **액세스 제어 레코드** 섹션은 호스트의 해당 IQN으로 모든 액세스 제어 레코드를 표시합니다.

이 자습서에서는 다음과 같은 일반적인 ACR 관련 작업을 설명합니다.

* IQN 가져오기
* 액세스 제어 레코드 추가 
* 액세스 제어 레코드 편집 
* 액세스 제어 레코드 삭제 

> [!IMPORTANT]
> * 볼륨에 ACR을 할당할 때 이 볼륨을 손상시킬 수 있으므로 하나 이상의 클러스터되지 않은 호스트에서 동시에 액세스하지 않은 볼륨에 주의를 기울여야 합니다. 
> * 볼륨에서 ACR을 삭제할 때 삭제로 인해 읽기-쓰기 중단이 발생할 수 있기 때문에 해당 호스트가 볼륨에 액세스하지 않는지 확인해야 합니다.
> 
> 

## IQN 가져오기
Windows Server 2012를 실행하는 Windows 호스트의 IQN을 가져오려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## ACR 추가
StorSimple 관리자 서비스 **구성** 페이지를 사용하여 ACR을 추가합니다. 일반적으로 하나의 볼륨으로 하나의 ACR을 연결합니다.

볼륨과 ACR을 연결하는 방법에 대한 정보는 [볼륨 추가](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)로 이동합니다.

> [!IMPORTANT]
> 볼륨에 ACR을 할당할 때 이 볼륨을 손상시킬 수 있으므로 하나 이상의 클러스터되지 않은 호스트에서 동시에 액세스하지 않은 볼륨에 주의를 기울여야 합니다.
> 
> 

ACR을 추가하려면 다음 단계를 수행합니다.

#### ACR을 추가하려면
1. 서비스 방문 페이지에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음 **구성** 탭을 클릭합니다.
   
    ![구성 탭](./media/storsimple-ova-manage-acrs/acr1.png)
2. **액세스 제어 레코드** 아래 테이블 형식 목록에서 ACR에 대한 **이름**을 지정합니다.
3. **iSCSI 초기자 이름**에서 Windows 호스트의 IQN 이름을 제공합니다.
4. 새롭게 만든 ACR을 저장하려면 페이지 맨 아래에서 **저장**을 클릭합니다. 다음과 같은 확인 메시지가 표시됩니다.
   
    ![확인 메시지](./media/storsimple-ova-manage-acrs/acr2.png)
5. 확인 아이콘![확인 아이콘](./media/storsimple-ova-manage-acrs/check-icon.png)을 클릭합니다. 테이블 형식 목록이 이 추가 사항을 반영하도록 업데이트됩니다.

## ACR 편집
Azure 클래식 포털의 **구성** 페이지에서 ACR을 편집합니다.

> [!NOTE]
> 현재 사용하지 않고 있는 ACR만 수정해야 합니다. 현재 사용 중인 볼륨과 연관된 ACR을 편집하려면 먼저 볼륨을 오프라인으로 전환해야 합니다.
> 
> 

ACR을 편집하려면 다음 단계를 수행합니다.

#### ACR을 편집하려면
1. 서비스 방문 페이지에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음 **구성** 탭을 클릭합니다.
2. 액세스 제어 레코드의 테이블 형식 목록에서 수정하려는 ACR 위로 커서를 가져갑니다.
3. ACR의 새 이름 및/또는 IQN을 제공합니다.
4. 변경된 ACR을 저장하려면 페이지 맨 아래에서 **저장**을 클릭합니다. 확인 메시지가 표시됩니다.
5. 확인 아이콘![확인 아이콘](./media/storsimple-ova-manage-acrs/check-icon.png)을 클릭합니다. 테이블 형식 목록이 이 변경 내용을 반영하도록 업데이트됩니다.

## 액세스 제어 레코드 삭제
Azure 클래식 포털의 **구성** 페이지에서 ACR을 삭제합니다.

> [!NOTE]
> * 현재 사용하지 않고 있는 ACR만 삭제해야 합니다. 현재 사용 중인 볼륨과 연관된 ACR을 삭제하려면 먼저 볼륨을 오프라인으로 전환해야 합니다.
> * 볼륨에서 ACR을 삭제할 때 삭제로 인해 읽기-쓰기 중단이 발생할 수 있기 때문에 해당 호스트가 볼륨에 액세스하지 않는지 확인해야 합니다.
> 
> 

액세스 제어 레코드를 삭제하려면 다음 단계를 수행합니다.

#### 액세스 제어 레코드를 삭제하려면
1. 서비스 방문 페이지에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음 **구성** 탭을 클릭합니다.
2. ACR(액세스 제어 레코드)의 테이블 형식 목록에서 삭제하려는 ACR 위로 커서를 가져갑니다.
3. 삭제 아이콘(**x**)이 선택한 ACR의 맨 오른쪽 열에 표시됩니다. **x** 아이콘을 클릭하여 ACR을 삭제합니다. 다음과 같은 확인 메시지가 표시됩니다.
   
    ![확인 메시지](./media/storsimple-ova-manage-acrs/acr3.png)
4. 확인 아이콘![확인 아이콘](./media/storsimple-ova-manage-acrs/check-icon.png)을 클릭합니다. 테이블 형식 목록이 삭제를 반영하도록 업데이트됩니다.

## 다음 단계
* [볼륨 추가 및 ACR 구성](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0511_2016-->