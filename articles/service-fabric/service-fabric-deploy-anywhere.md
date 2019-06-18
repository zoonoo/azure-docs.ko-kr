---
title: Azure 및 독립 실행형 Service Fabric 클러스터 개요 | Microsoft Docs
description: Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다. 다시 말해 온-프레미스든 Microsoft Azure든 클라우드 공급자든 Windows Server 또는 Linux 컴퓨터가 서로 연결된 모든 환경에 Service Fabric 애플리케이션을 배포하고 실행할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 6d5169d8ea4480e95e09228f9eb02bd78fdd0be8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393506"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Windows Server 및 Linux의 Azure 및 독립 실행형 Service Fabric 클러스터 비교
Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 컴퓨터나 VM을 클러스터 노드라고 합니다. 클러스터의 규모를 수천 개의 노드로 확장할 수 있습니다. 새 노드를 클러스터에 추가하면 Service Fabric이 증가된 수의 노드에서 서비스 파티션 복제본 및 인스턴스의 균형을 조정합니다. 전반적인 애플리케이션 성능이 향상되고 메모리 액세스에 대한 경합이 감소합니다. 클러스터의 노드가 효율적으로 사용되지 않는 경우 클러스터의 노드 수를 줄일 수 있습니다. Service Fabric은 각 노드의 하드웨어를 보다 효율적으로 사용할 수 있도록 감소된 노드 수에 맞게 파티션 복제본 및 인스턴스의 균형을 다시 조정합니다.

Azure Service Fabric을 사용하면 Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다. 다시 말해 온-프레미스든 Microsoft Azure든 클라우드 공급자든 Windows Server 또는 Linux 컴퓨터가 서로 연결된 모든 환경에 Service Fabric 애플리케이션을 배포하고 실행할 수 있습니다.

## <a name="benefits-of-clusters-on-azure"></a>Azure에서 클러스터의 이점
Azure에서는 클러스터의 작업 및 관리를 더 용이하고 안정적으로 해주는 다음과 같은 다른 Azure 기능 및 서비스와의 통합을 제공합니다.

* **Azure Portal:** Azure Portal을 사용하면 쉽게 클러스터를 만들고 관리할 수 있습니다.
* **Azure Resource Manager:** Azure Resource Manager를 사용하면 클러스터에서 사용하는 모든 리소스를 하나의 단위로 쉽게 관리할 수 있기 때문에 비용 추적 및 청구가 간소화됩니다.
* **Azure 리소스로서의 Service Fabric 클러스터** Service Fabric 클러스터는 Azure 리소스이므로 Azure의 다른 리소스와 같이 모델링할 수 있습니다.
* **Azure 인프라와의 통합** 서비스 패브릭은 OS, 네트워크 및 그 밖에 애플리케이션의 가용성 및 안정성을 개선하기 위한 업그레이드에 맞게 기본 Azure 인프라를 조정합니다.  
* **진단:** Azure에서 Azure 진단 사용 하 여 통합을 제공 하 고 Azure Monitor 기록 합니다.
* **자동 크기 조정:** Azure의 클러스터는 Virtual Machine 확장 집합으로 인해 자동 크기 조정 기능이 기본적으로 제공됩니다. 온-프레미스 및 다른 클라우드 환경에서는 사용자 고유 자동 크기 조정 기능을 빌드하거나 서비스 패브릭에서 클러스터 크기 조정을 위해 제공하는 API를 사용하여 수동으로 조정해야 합니다.

## <a name="benefits-of-standalone-clusters"></a>독립 실행형 클러스터의 이점
* 클러스터를 호스트할 클라우드 공급자를 자유롭게 선택할 수 있습니다.
* 서비스 패브릭 애플리케이션을 한 번 작성한 다음 변경 없이 또는 최소한의 변경으로 여러 호스팅 환경에서 실행할 수 있습니다.
* 호스팅 환경 간에 서비스 패브릭 애플리케이션 빌드에 대한 지식이 전달됩니다.
* 환경 간에 서비스 패브릭 클러스터 실행 및 관리에 대한 운영 경험이 전달됩니다.
* 호스팅 환경 제약을 받지 않아 고객 도달 범위가 넓습니다.
* 데이터 센터 또는 클라우드에 블랙아웃이 발생하면 서비스를 다른 배포 환경으로 이동할 수 있으므로 안정성이 강화되고 대규모로 가동이 중단되는 일이 없습니다.

## <a name="next-steps"></a>다음 단계

* [Azure의 Service Fabric 클러스터](service-fabric-azure-clusters-overview.md) 개요 읽기
* [Service Fabric 클러스터](service-fabric-standalone-clusters-overview.md) 개요 읽기
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기