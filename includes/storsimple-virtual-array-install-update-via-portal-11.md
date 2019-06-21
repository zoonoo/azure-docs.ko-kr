---
title: 포함 파일
description: 포함 파일
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182727"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure Portal을 통해 업데이트를 설치하려면

1. StorSimple 디바이스 관리자를 이동하고 **디바이스**를 선택합니다. 서비스에 연결된 디바이스 목록에서 업데이트하려는 디바이스를 선택하고 클릭합니다.

2. **설정**에서 **장치 업데이트**를 클릭합니다.  

3. 소프트웨어 업데이트를 사용할 수 있는 경우 메시지가 나타납니다. 업데이트를 확인하려면 **스캔**을 클릭할 수도 있습니다. 실행 중인 소프트웨어 버전을 기록해 둡니다. 

    ![디바이스 업데이트](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    스캔이 시작되고 성공적으로 완료되면 알림이 표시됩니다.
 
4. 업데이트가 스캔되면 **업데이트 다운로드**를 클릭합니다. **새 업데이트**에서 릴리스 정보를 검토합니다. 또한 업데이트가 다운로드된 후에는 설치를 확인해야 합니다. **확인**을 클릭합니다.

    ![디바이스 업데이트](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    업로드가 시작되고 성공적으로 완료되면 알림이 표시됩니다.

5. **장치 업데이트**에서 **설치**를 클릭합니다.

     ![디바이스 업데이트](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. **새 업데이트** 아래에 업데이트가 중단되었다는 경고가 나타납니다. 가상 배열이 단일 노드 디바이스인 경우 디바이스가 업데이트된 후에 다시 시작됩니다. 따라서 진행 중인 모든 IO가 중단됩니다. **확인**을 클릭하여 업데이트를 설치합니다.

    ![디바이스 업데이트](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    설치 작업이 시작될 때 알림을 받습니다.

7.  설치 작업이 완료되면 **작업 보기** 링크를 클릭합니다. 이 작업을 수행하면 **업데이트 설치** 블레이드로 이동합니다. 이 작업에 대한 자세한 정보가 표시됩니다. 

    ![디바이스 업데이트](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. 소프트웨어 버전 업데이트 1(10.0.10296.0)을 실행하는 가상 배열로 시작한 경우 이제 업데이트 1.1을 실행하여 완료됩니다. 나머지 단계를 건너뛸 수 있습니다. 

    소프트웨어 버전 업데이트 0.6(10.0.10293.0)을 실행하는 가상 배열로 시작한 경우 이제 업데이트 1.0으로 업데이트됩니다. 업데이트를 사용할 수 있음을 나타내는 다른 메시지가 표시됩니다. 4-7단계를 반복하여 업데이트 1.1을 설치합니다.

    

