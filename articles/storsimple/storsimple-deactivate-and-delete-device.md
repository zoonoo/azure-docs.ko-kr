---
title: "StorSimple 장치 비활성화 및 삭제 | Microsoft Docs"
description: "먼저 StorSimple 장치를 비활성화한 후 삭제하여 서비스에서 제거하는 방법을 설명합니다."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: anbacker
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: c000a642aa088ac80cc7077453b87e9a47f96900
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="deactivate-and-delete-a-storsimple-8000-series-device-via-storsimple-manager-service"></a>StorSimple Manager 서비스를 통해 StorSimple 8000 시리즈 장치 비활성화 및 삭제
## <a name="overview"></a>개요
StorSimple 장치 서비스를 중단하고 싶을 수 있습니다(예: 장치를 교체 또는 업그레이드하거나 더 이상 StorSimple을 사용하지 않는 경우). 그런 경우 장치를 비활성화한 다음 삭제해야 합니다. 비활성화하면 장치 및 해당 StorSimple Manager 서비스 간의 연결이 끊깁니다. 이 자습서에서는 StorSimple 장치를 먼저 비활성화한 후 삭제하여 서비스에서 제거하는 방법에 대해 설명합니다. 

장치를 비활성화하면 장치에 로컬로 저장된 데이터에 더 이상 액세스할 수 없게 됩니다. 클라우드에 저장된 장치와 연결된 데이터만 복구할 수 있습니다.  

> [!WARNING]
> 비활성화는 영구 작업이며 실행 취소할 수 없습니다. 먼저 기본 팩터리 설정에서 장치를 재설정해야 비활성화된 장치를 StorSimple Manager 서비스에 등록할 수 있습니다. 
> 
> 공장 재설정 프로세스는 장치에 로컬로 저장된 모든 데이터를 삭제합니다. 따라서 반드시 장치를 비장치를 비활성화하기 전에 모든 데이터의 클라우드 스냅숏을 만들어야 합니다. 이렇게 하면 이후 단계에서 모든 데이터를 복구할 수 있습니다.
> 
> 

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

* 장치 비활성화 및 데이터 삭제
* 장치 비활성화 및 데이터 유지

StorSimple 가상 장치에서 비활성화 및 삭제가 어떻게 작동하는지도 설명합니다.

> [!NOTE]
> StorSimple 물리적 장치 또는 가상 장치를 비활성화하기 전에 장치에 의존하는 클라이언트와 호스트를 중지하거나 삭제했는지 확인합니다.
> 
> 

## <a name="deactivate-and-delete-data"></a>데이터 비활성화 및 삭제
장치를 완전히 삭제하고 장치의 데이터를 보존하지 않으려는 경우 다음 단계를 완료합니다.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>장치를 비활성화하고 데이터를 삭제하려면
1. 장치를 비활성화하기 전에 장치와 연결된 모든 볼륨 컨테이너(및 볼륨)를 삭제해야 합니다. 연결된 백업을 삭제한 후에만 볼륨 컨테이너를 삭제할 수 있습니다.
2. 다음과 같이 장치를 비활성화합니다.
   
   1. StorSimple Manager 서비스 **장치** 페이지에서 비활성화할 장치를 선택하고 페이지 하단에서 **비활성화**를 클릭합니다.
   2. 확인 메시지가 표시됩니다. **예** 를 클릭하여 계속합니다. 비활성화 프로세스가 시작되고 완료하는 데 몇 분이 소요됩니다.
3. 비활성화한 후 장치를 완전히 삭제할 수 있습니다. 장치를 삭제하면 서비스에 연결된 장치 목록에서 제거됩니다. 그러면 서비스에서 삭제된 장치를 더 이상 관리할 수 없습니다. 장치를 삭제하려면 다음 단계를 사용합니다.
   
   1. StorSimple Manager 서비스 **장치** 페이지에서 삭제할 비활성화된 장치를 선택합니다.
   2. 페이지 맨 아래에서 **삭제**를 클릭합니다.
   3. 확인하라는 메시지가 표시됩니다. **예** 를 클릭하여 계속합니다.
      
      장치를 삭제하는 데는 몇 분 정도 걸릴 수 있습니다.

## <a name="deactivate-and-retain-data"></a>데이터 비활성화 및 보존
장치를 삭제하지만 데이터를 보존하고 싶은 경우 다음 단계를 완료합니다.

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>장치를 비활성화하고 데이터를 유지하려면
1. 장치를 비활성화합니다. 장치의 모든 볼륨 컨테이너 및 스냅숏은 유지됩니다.
   
   1. StorSimple Manager 서비스 **장치** 페이지에서 비활성화할 장치를 선택하고 페이지 하단에서 **비활성화**를 클릭합니다.
   2. 확인 메시지가 표시됩니다. **예** 를 클릭하여 계속합니다. 비활성화 프로세스가 시작되고 완료하는 데 몇 분이 소요됩니다.
2. 이제 볼륨 컨테이너와 연결된 스냅숏을 장애 조치(Failover)할 수 있습니다. 이에 대한 절차를 보려면 [StorSimple 장치에 대한 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)로 이동하세요.
3. 비활성화 및 장애 조치(Failover) 후 장치를 완전히 삭제할 수 있습니다. 장치를 삭제하면 서비스에 연결된 장치 목록에서 제거됩니다. 그러면 서비스에서 삭제된 장치를 더 이상 관리할 수 없습니다. 장치를 삭제하려면 다음 단계를 완료합니다.
   
   1. StorSimple Manager 서비스 **장치** 페이지에서 삭제할 비활성화된 장치를 선택합니다.
   2. 페이지 맨 아래에서 **삭제**를 클릭합니다.
   3. 확인하라는 메시지가 표시됩니다. **예** 를 클릭하여 계속합니다.
      
      장치를 삭제하는 데는 몇 분 정도 걸릴 수 있습니다.

## <a name="deactivate-and-delete-a-virtual-device"></a>가상 장치 비활성화 및 삭제
StorSimple 가상 컴퓨터에 대한 비활성화는 가상 장치의 할당을 취소합니다. 그런 다음 가상 컴퓨터가 프로비전되었을 때 만든 리소스와 가상 컴퓨터를 삭제할 수 있습니다. 가상 장치가 비활성화된 후 이전 상태로 복원할 수 없습니다. 

비활성화하면 다음 작업이 진행됩니다.

* StorSimple 가상 장치가 제거됩니다.
* OS 디스크 및 StorSimple 가상 장치에 대해 만든 데이터 디스크가 제거됩니다.
* 프로비전 중 호스티드 서비스 및 가상 네트워크가 보존됩니다. 이러한 엔터티를 사용하지 않는 경우 수동으로 삭제해야 합니다.
* StorSimple 가상 장치에서 만든 클라우드 스냅숏은 보존됩니다.

## <a name="next-steps"></a>다음 단계
* 비활성화된 장치를 공장 기본 설정으로 복원하려면 [장치를 공장 기본 설정으로 초기화](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)로 이동합니다.
* 기술 지원을 받으려면 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md)하세요.
* StorSimple Manager 서비스를 사용하는 방법을 자세히 알아보려면 [StorSimple Manager 서비스를 사용하여 StorSimple 장치 관리](storsimple-manager-service-administration.md)로 이동하세요. 


