---
title: Azure NetApp Files 볼륨의 지역 간 복제 | Microsoft Docs
description: 지역 간 복제의 기능, 지원 되는 지역 쌍, 서비스 수준 목표, 데이터 내구성 및 비용 모델 Azure NetApp Files에 대해 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: e7d424435c29eb3e7a6779a7036816a7bedd2085
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708805"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨의 지역 간 복제

Azure NetApp Files 복제 기능은 지역 간 볼륨 복제를 통해 데이터 보호 기능을 제공 합니다. 한 지역의 Azure NetApp Files 볼륨 (원본)에서 다른 지역의 다른 Azure NetApp Files 볼륨 (대상)으로 데이터를 비동기식으로 복제할 수 있습니다.  이 기능을 사용 하면 지역 전체의 가동 중단 또는 재해가 발생할 경우 중요 한 응용 프로그램을 장애 조치 (failover) 할 수 있습니다.

> [!IMPORTANT]
> 지역 간 복제 기능은 현재 공개 미리 보기로 제공 됩니다. [Azure NetApp Files 지역 간 복제 waitlist 제출 페이지](https://aka.ms/anfcrrpreviewsignup)를 통해 기능에 액세스 하기 위한 waitlist 요청을 제출 해야 합니다. 지역 간 복제 기능을 사용 하기 전에 Azure NetApp Files 팀의 공식 확인 전자 메일을 기다리십시오.

## <a name="supported-region-pairs"></a>지원 되는 지역 쌍

볼륨 복제는 현재 다음 고정 지역 쌍에서 사용할 수 Azure NetApp Files.  

* 미국 서 부 및 미국 동부
* 미국 서 부 2 및 미국 동부 
* 미국 중 남부 중부 및 미국 중부 
* 미국 남부 중부 및 미국 동부
* 미국 남부 중부 및 미국 동부 2 
* 미국 동부 2와 미국 중부 
* 북유럽 및 서유럽
* 영국 남부 및 영국 서부
* 오스트레일리아 동부 및 오스트레일리아 남동쪽
* 캐나다 중부 및 캐나다 동부
* 일본 동부 및 일본 서 부
* 영국 남부 및 독일 중서부
* 동남 아시아 및 오스트레일리아 동부

## <a name="service-level-objectives"></a>서비스 수준 목표

RPO (복구 지점 목표) 또는 최대 지속할 데이터 손실은 복제 일정의 두 배로 정의 됩니다.  관찰 되는 실제 RPO는 총 데이터 집합 크기와 같은 요소, 변경 비율, 데이터 덮어쓰기 비율 및 전송에 사용할 수 있는 복제 대역폭 등의 요소에 따라 달라질 수 있습니다.   

* 10 분의 복제 일정의 경우 최대 RPO는 20 분입니다.  
* 매시간 복제 일정의 경우 최대 RPO는 2 시간입니다.  
* 일별 복제 일정의 경우 최대 RPO는 이틀입니다.  

RTO (복구 시간 목표) 또는 최대 지속할 비즈니스 응용 프로그램 가동 중지 시간은 응용 프로그램을 가져오고 두 번째 사이트에서 데이터에 대 한 액세스를 제공 하는 요인에 의해 결정 됩니다. 피어 링 관계를 분리 하 여 대상 볼륨을 활성화 하 고 두 번째 사이트의 읽기 및 쓰기 데이터 액세스를 제공 하는 RTO의 저장소 부분은 1 분 내에 완료 될 것으로 예상 됩니다.

## <a name="cost-model-for-cross-region-replication"></a>지역 간 복제에 대 한 비용 모델  

지역 간 복제를 Azure NetApp Files 사용 하면 복제 하는 데이터의 양에 대해서만 비용을 지불 하 게 됩니다. 설정 요금 또는 최소 사용 요금은 없습니다. 복제 가격은 초기 복제 구성 중에 선택한 *대상* 볼륨의 복제 빈도와 영역을 기준으로 합니다. 자세한 내용은 [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) 페이지를 참조 하세요.  

일반 Azure NetApp Files 저장소 용량 요금은 복제 대상 볼륨 ( *데이터 보호* 볼륨이 라고도 함)에 적용 됩니다. 

### <a name="pricing-examples"></a>가격 책정 예제

한 달에 청구 되는 지역 간 복제는 해당 월에 지역 간 복제 기능을 통해 복제 된 데이터의 양을 기준으로 합니다. 복제 되는 데이터의 양은 GiB로 측정 됩니다. 원본 볼륨에서 대상 볼륨으로, 대상 볼륨에서 원본 볼륨으로의 모든 다시 동기화 복제 중에 두 지역에 걸쳐 복제 되는 데이터의 합계를 나타냅니다.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>예제 1:1 월 1 일 기준 복제 및 증분 복제

다음과 같은 상황을 가정 합니다.

* *원본* 볼륨은 Azure NetApp Files *Premium* 서비스 수준에서 가져온 것입니다. 볼륨 할당량 크기가 1000 GiB이 고, 한 달의 첫 번째 날부터 500 GiB의 볼륨이 사용 됩니다. 볼륨이 *미국 남부 중부* 지역에 있습니다.
* *대상* 볼륨은 Azure NetApp Files *Standard* 서비스 수준에서 가져온 것입니다. *미국 동부 2* 지역에 있습니다.
* 위의 두 볼륨 간에 *매시간* 기반 지역 간 복제를 구성 했습니다. 따라서 복제 가격은 GiB 당 $0.12입니다.
* 간단히 하기 위해 원본 볼륨에는 1 시간 마다 0.5-GiB 데이터가 변경 되는 것으로 가정 하지만 사용 된 총 볼륨 크기는 증가 하지 않습니다 (500 GiB에 남아 있음). 

초기 설치 후에는 기준 복제가 즉시 수행 됩니다.  

* 기준 복제 중에 복제 된 데이터 양: `500 GiB`
* 기준 복제 요금: `500 GiB * $0.12 = $60`

기준 복제 후에는 변경 된 블록만 복제 됩니다. 따라서 0.5 GiB 데이터는 이후 증분 복제에서 1 시간 마다 복제 됩니다.

* 30 일 동안 증분 복제를 통해 복제 된 데이터 양 합계: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* 증분 복제 요금: `360 GiB * $0.12 = $43.2`

1 월 말까지 총 지역 간 복제 요금은 다음과 같습니다.  

*  월 1의 총 지역 간 복제 요금: `$60 + $43.2 = $103.2`

일반 Azure NetApp Files 저장소 용량 요금은 대상 볼륨에 적용 됩니다. 그러나 대상 볼륨은 원본 볼륨 계층과 다른 (및 저렴 한) 저장소 계층을 사용할 수 있습니다.

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>예 2: 월 2 증분 복제 및 다시 동기화 복제  

예 1의 설명에 따라 원본 볼륨, 대상 볼륨 및 두 설정 간의 복제 관계가 있다고 가정 합니다. 두 번째 달 (30 일 월)의 29 일의 경우 매시간 복제가 예상 대로 발생 합니다.

* 29 일 동안 증분 복제를 통해 복제 된 데이터 양 합계: `0.5 GiB * 24 hours * 29 days = 348 GiB`

해당 월의 마지막 날에 계획 되지 않은 중단이 원본 지역에서 발생 하 고 대상 볼륨으로 장애 조치 (failover) 된 경우를 가정 합니다. 2 시간 후에 원본 지역이 복구 되 고 대상 볼륨에서 원본 볼륨으로 다시 동기화 복제가 수행 되었습니다. 2 시간 동안 0.8 GiB는 대상 볼륨에서 데이터 변경이 발생 했으며 원본에 resynced 해야 합니다.

* 최근 날짜의 22 시간 동안 일반 복제를 통해 복제 된 데이터 양 합계: `0.5 GiB * 22 hours = 11 GiB`
* 하나의 다시 동기화 복제 중에 복제 된 데이터 양: `0.8 GiB`

따라서 2 월 말까지 총 지역 간 복제 요금은 다음과 같습니다.  

* 2 월의 총 지역 간 복제 요금: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

월 2에 대 한 일반 Azure NetApp Files 저장소 용량 요금은 대상 볼륨에 적용 됩니다.

## <a name="next-steps"></a>다음 단계
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [복제 피어 링 만들기](cross-region-replication-create-peering.md)
* [복제 관계의 상태를 표시 합니다.](cross-region-replication-display-health-status.md)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)


