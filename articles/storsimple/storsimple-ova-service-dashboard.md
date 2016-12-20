---
title: "StorSimple Manager 서비스 대시보드 - 가상 배열 | Microsoft Docs"
description: "StorSimple Manager 서비스 대시보드 및 이 대시보드를 사용하여 StorSimple 가상 배열의 상태를 모니터링하는 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 36ac6eb4-a616-4bb1-8163-6862ac33da63
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 58aa9508e28a00efe69d0a9cbc8adb6837d085c4


---
# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>StorSimple 가상 배열을 위한 StorSimple Manager 서비스 대시보드 사용
## <a name="overview"></a>개요
StorSimple Manager 서비스 대시보드 페이지는 StorSimple Manager 서비스에 연결된 StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치 또는 가상 장치라고도 함)에 대한 요약 보기를 통해 시스템 관리자의 주의가 필요한 정보를 중점적으로 제공합니다. 이 자습서에서는 대시보드 페이지를 소개하고, 대시보드의 콘텐츠와 기능 및 이 페이지에서 수행할 수 있는 작업에 대해 설명합니다.

![서비스 대시보드](./media/storsimple-ova-service-dashboard/dashboard1.png)

StorSimple 관리자 서비스 대시보드에 다음 정보가 표시됩니다.

* 페이지 상단 **차트** 영역에서 가상 장치에 대한 관련 메트릭을 볼 수 있습니다. 일정 시간 동안 가상 장치에서 사용된 클라우드 저장소는 물론, 모든 가상 장치에 걸쳐 사용되는 기본 저장소도 볼 수 있습니다. 상대 또는 절대 사용을 지정하거나 1주, 1개월, 3개월 또는 1년 단위를 보려면 차트 오른쪽 위 모퉁이의 컨트롤을 사용하세요. 새로 고침 컨트롤 ![새로 고침 컨트롤](./media/storsimple-ova-service-dashboard/refresh-control.png) 을 사용하여 차트를 새로 고칩니다.
* **사용 개요**에서는 모든 가상 장치에서 사용 가능한 총 저장소를 기준으로 모든 가상 장치에서 프로비전하여 사용하는 기본 저장소를 보여줍니다. **프로비전됨**은 사용을 위해 준비 및 할당된 저장소의 용량을 나타내며 **사용됨**은 가상 장치에 연결된 초기자에 의해 표시된 볼륨 또는 공유의 사용량을 나타냅니다. **최대 용량**은 모든 가상 장치의 최대 용량을 보여 줍니다.
* **경고** 영역에서는 경고 심각도별로 그룹화된 모든 가상 장치 간의 모든 활성 경고에 대한 스냅숏을 제공합니다. 심각도 수준을 클릭하면 이러한 경고를 표시하는 **경고** 페이지가 열립니다. **경고** 페이지에서 개별 경고를 클릭하면 권장 조치를 포함하여 해당 경고에 대한 추가적인 정보를 자세히 볼 수 있습니다. 또한 문제가 해결된 경고를 지울 수도 있습니다.
* **작업** 영역에서는 서비스에 연결된 모든 가상 장치의 최근 작업에 대한 스냅숏을 제공합니다. 현재 진행 중인 작업, 지난 24시간 동안 성공 또는 실패한 작업을 확인하는 데 사용할 수 있는 링크가 있습니다. 
* 페이지 오른쪽의 **간략 상태** 영역에서는 서비스 상태, 서비스에 연결된 총 가상 장치 수, 서비스의 위치, 서비스와 연결된 구독의 세부 정보 등과 같은 유용한 정보를 제공합니다. 또한 작업 로그에 대한 링크도 있습니다. 완료된 모든 StorSimple 관리자 서비스 작업 목록을 보려면 이 링크를 클릭하세요. 

다음 작업을 수행하는 데 StorSimple 관리자 서비스 대시보드 페이지를 사용할 수 있습니다.

* 서비스 등록 키 받기로 이동하세요.
* 작업 로그를 봅니다.

## <a name="get-the-service-registration-key"></a>서비스 등록 키 가져오기
서비스 등록 키는 장치가 추가적인 관리 작업을 할 수 있는 Azure 클래식 포털에 표시되도록 StorSimple Manager 서비스에 StorSimple 가상 장치를 등록하는 데 사용됩니다. 이 키는 첫 번째 가상 장치에서 만들어지고 나머지 가상 장치와 공유됩니다. 

**등록 키**(페이지의 맨 아래)를 클릭하면 **서비스 등록 키** 대화 상자가 열리고, 여기서 현재 서비스 등록 키를 클립보드에 복사하거나 서비스 등록 키를 다시 생성할 수 있습니다.

![등록 키](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

키를 다시 생성해도 이전에 등록된 가상 장치에는 영향을 주지 않으며 해당 키를 다시 생성한 후 서비스에 등록한 가상 장치에만 영향을 줍니다.

서비스 등록 키를 가져오는 방법에 대한 자세한 내용은 [서비스 등록 키 가져오기](storsimple-ova-manage-service.md#get-the-service-registration-key)를 참조하세요.

## <a name="view-the-operations-logs"></a>작업 로그 보기
대시보드의 **간략 상태** 창에 있는 작업 로그 링크를 클릭하여 해당 작업 로그를 볼 수 있습니다. 링크를 클릭하면 관리 서비스 페이지로 이동하므로 여기서 StorSimple 관리자 서비스 관련 로그를 필터링하여 볼 수 있습니다.

![작업 로그](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>다음 단계
로컬 웹 UI를 사용하여 [StorSimple 가상 배열을 관리](storsimple-ova-web-ui-admin.md)하는 방법을 알아봅니다.




<!--HONumber=Nov16_HO3-->


