---
title: Azure 지역 간 SAP HANA 가용성 | Microsoft Docs
description: Azure VM에서 SPA HANA를 실행하는 경우 가용성 고려 사항의 개요를 설명합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Azure 지역 간 SAP HANA 가용성
이 아티클에서는 다른 Azure 지역에서 SAP HANA 가용성과 관련된 시나리오를 설명합니다. 별도 Azure 지역 간의 간격이 더 크다는 사실을 고려하여 이 아티클에 나열된 특별한 주의 사항이 있습니다.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>여러 Azure 지역에서 배포하는 동기
다른 Azure 지역은 더 큰 간격으로 구분됩니다. 지정학적 지역에 따라 미국에서처럼 수백 마일이나 수천 마일일 수도 있습니다. 다른 Azure 지역 간의 간격으로 인해 다른 Azure 지역에 배포된 자산 간의 네트워크 트래픽에는 상당한 네트워크 왕복 대기 시간이 발생하게 됩니다. 일반적인 SAP 워크로드 아래에서 두 개의 SAP HANA 인스턴스 간의 동기 데이터 교환을 제외할 만큼 중요합니다. 반면 더 넓은 영역에 도달하는 자연 재해가 발생한 경우에 가용성을 제공하기 위해 기본 데이터 센터와 보조 데이터 센터 간의 간격에 정의된 요구 사항이 있다는 사실을 알게 됩니다. 예: 2017년 9월과 10월, 카리브 해 및 플로리다 영역에서 발생한 허리케인 또는 최소 거리 요구 사항 대부분의 고객은 이 최소 거리 정의로 인해 [Azure 지역](https://azure.microsoft.com/regions/) 간에 가용성을 설계해야 합니다. HANA의 동기 복제 모드를 사용하는 두 Azure 지역 간에 거리가 너무 멀기 때문에 RTO 및 RPO의 요구 사항에서는 한 지역 내의 가용성 구성을 배포한 다음, 두 번째 지역에서 추가 배포로 보완하도록 강제합니다.

이러한 구성에서 고려할 또 다른 측면은 장애 조치 및 클라이언트 리디렉션입니다. 다른 두 Azure 지역에 있는 SAP HANA 인스턴스 간의 장애 조치가 항상 수동 장애 조치라고 가정합니다. SAP HANA 시스템 복제의 복제 모드를 비동기로 설정되었지만 기본 HANA 인스턴스에서 커밋된 데이터가 아직 보조 HANA 인스턴스가 되지 못했을 가능성이 있습니다. 따라서 복제가 비동기인 구성에서 자동 장애 조치를 허용할 수 없습니다. 장애 조치 연습처럼 수동 제어된 장애 조치를 사용하여 기본 측에 커밋된 모든 데이터가 다른 Azure 지역으로 수동으로 이동하기 전에 보조 인스턴스가 되었는지 확인하는 조치를 수행해야 합니다.
 
두 번째 Azure 지역에서 배포된 Azure VNet에서 다른 IP 주소 범위로 작업하더라도 SAP HANA 클라이언트는 해당 구성을 변경하거나 이름 확인을 변경하는 단계를 수행해야 합니다. 따라서 클라이언트는 새 보조 서버 IP 주소로 리디렉션됩니다. [인수 후 클라이언트 연결 복구](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)에 대한 SAP 아티클에서는 더 자세히 설명합니다.   

## <a name="simple-availability-between-two-azure-regions"></a>두 Azure 지역 간의 간단한 가용성
이 시나리오에서는 단일 지역 내에서 가용성 구성을 설정하지 않으려고 했습니다. 하지만 재해 발생 시 워크로드를 제공하도록 요청받았습니다. 이와 같은 시스템에 대한 일반적인 사례는 비프로덕션 시스템입니다. 시스템을 반나절 또는 하루 동안이라도 중단한 생태로 유지할 수 있지만 48시간 이상 사용할 수 없도록 허용할 수는 없습니다. 설정의 비용을 줄이기 위해 대상으로 작동하는 VM에서 덜 중요한 다른 시스템을 실행하거나 보조 지역에서 VM의 크기를 작게 조정하고 데이터를 사전 로드하지 않도록 합니다. 장애 조치가 수동이고 전체 응용 프로그램 스택을 장애 조치하는 데 더 많은 단계가 있으며 VM을 종료하는 데 추가 시간이 필요하므로 가능하면 CM의 크기를 조정하고 다시 시작하는 것이 좋습니다.

> [!NOTE]
> HANA 시스템 복제 대상에서 데이터를 사전 로드하지 않아도, 최소 64GB 메모리이며 대상 인스턴스의 메모리에 rowstore 데이터를 유지하기에 충분한 메모리 이상이 필요합니다.

![두 지역에 두 개의 VM](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> 이 구성에서는 HANA 시스템 복제 모드가 비동기이므로 RPO=0을 제공할 수 없습니다. RPO=0을 제공해야 하는 경우 이 구성은 선택되지 않습니다.

구성을 조금 변경하면 데이터를 사전 로드하도록 구성할 수 있습니다. 그러나 장애 조치의 수동 환경 및 응용 프로그램 계층이 두 번째 지역으로 이동해야 한다는 사실을 고려하면 데이터를 사전 로드하는 것이 적합하지 않을 수 있습니다. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>하나의 지역 내 및 지역 간에 가용성을 결합합니다. 
지역 내 및 지역 간 가용성의 조합은 다음으로 결정될 수 있습니다.

- Azure 지역 내의 RPO=0에 대한 요구 사항
- 더 큰 지역에 영향을 주는 주요 자연 재해의 영향을 받는 회사의 전역 운영 의지 또는 능력 부재 지난 몇 년 동안 카리브 해를 강타한 일부 허리케인의 경우와 같습니다.
- Azure 가용성 영역이 제공할 수 있는 범위를 명확히 벗어나는 기본 사이트와 보조 사이트 간에 거리를 요청하는 규정

 
이러한 경우에 HANA 시스템 복제를 사용하여 SAP가 호출하는 [SAP HANA 다계층 시스템 복제 구성](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)을 구성합니다. 아키텍처는 다음과 같습니다.

![두 지역에 세 개의 VM](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

이 구성은 제공 기본 지역 내에서 RTO 시간이 작은 RPO=0을 제공하고 두 번째 지역으로 이동하는 경우에 내림차순 RPO를 제공합니다. 두 번째 지역의 RTO 시간은 데이터 사전 로드를 구성할지 여부에 따라 다릅니다. 대부분 고객은 보조 지역의 VM을 사용하여 테스트 시스템을 실행합니다. 이런 사용으로 인해 데이터를 사전 로드할 수 없습니다.

> [!NOTE]
> 계층 1에서 계층 2로 이동(기본 지역에서 동기 복제)하는 HANA 시스템 복제에 로그 재생 작업 모드를 사용하므로 계층 2와 3 계층 간의 복제(보조 사이트에 복제)는 delta_datashipping 작업 모드일 수 없습니다. 작업 모드 세부 정보 및 몇 가지 제한 사항은 [SAP HANA 시스템 복제에 대한 작업 모드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)에서 SAP별로 설명됩니다. 

## <a name="next-steps"></a>다음 단계
Azure에서 그러한 구성을 설정하는 방법에 대한 단계별 지침이 필요한 경우 다음 아티클을 읽어보세요.

- [Azure VM에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
- [Azure의 SAP - 4부 - 시스템 복제를 사용한 SAP HANA에 대한 고가용성](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
