---
title: Service Fabric 관리 되는 클러스터에 대 한 일반적인 질문
description: 기능, 사용 사례 및 일반적인 시나리오를 비롯 하 여 관리 되는 클러스터 Service Fabric에 대 한 질문과 대답입니다.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 73815e793357dd38244dd429f9056c4c6a0bfff1
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410333"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric 관리 클러스터에 대 한 질문과 대답

관리 되는 클러스터 (미리 보기)에서 수행할 수 있는 작업과 사용 방법에 대 한 일반적인 질문 Service Fabric에는 여러 가지가 있습니다. 이 문서에서는 자주 묻는 질문 및 그에 대한 답변을 설명합니다.

## <a name="general"></a>일반

### <a name="what-are-service-fabric-managed-clusters"></a>Service Fabric 관리 되는 클러스터는 무엇 인가요?

Service Fabric 관리 클러스터는 클러스터를 보다 쉽게 배포 하 고 관리할 수 있도록 설계 된 Service Fabric 클러스터 리소스 모델의 진화입니다. Service Fabric 관리 되는 클러스터는 Azure Resource Manager 캡슐화 모델을 사용 하 여 사용자가 현재 배포 해야 하는 여러 독립 리소스 (가상 머신 확장 집합, Load Balancer, IP 등)에 비해 단일 클러스터 리소스만 정의 하 고 배포 해야 합니다.

### <a name="what-regions-are-supported-in-the-preview"></a>미리 보기에서 지원 되는 지역은 무엇 인가요?

관리 되는 클러스터 Service Fabric 미리 보기에 지원 되는 영역에는,,,, 및가 포함 됩니다 `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>기존 Service Fabric 클러스터를 관리 클러스터 리소스로 현재 위치로 마이그레이션할 수 있나요?

이번에는 새 Service Fabric 관리 되는 클러스터 리소스 유형을 사용 하기 위해 새 Service Fabric 클러스터 리소스를 만들어야 합니다.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Service Fabric 관리 되는 클러스터에 대 한 추가 비용이 있나요?

아니요, 클러스터에 필요한 기본 계산, 저장소 및 네트워킹 리소스의 비용 외에도 Service Fabric 관리 되는 클러스터와 관련 된 추가 비용은 없습니다.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Service Fabric 관리 클러스터 리소스에서 도입 된 새로운 SLA가 있나요?

SLA는 현재 Service Fabric 리소스 모델에서 변경 되지 않습니다.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>기본 및 표준 SKU 클러스터 간의 차이점은 무엇 인가요?

기본 SKU 클러스터는 대부분의 구성이 Service Fabric 리소스 공급자에 의해 제공 됨을 의미 합니다. 기본 SKU 클러스터는 테스트 및 사전 프로덕션 환경에 사용 하기 위한 것입니다. 사용자는 표준 SKU 클러스터를 사용 하 여 사용자의 요구를 충족 하도록 클러스터를 구성할 수 있습니다. 자세한 내용은 [관리 되는 클러스터 sku Service Fabric](overview-managed-cluster.md#service-fabric-managed-cluster skus.md) 를 참조 하세요.

## <a name="cluster-deployment-and-management"></a>클러스터 배포 및 관리

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>내 가상 머신 확장 집합에서 사용자 지정 스크립트 확장을 실행 하 고 관리 되는 Service Fabric 리소스로 계속 진행할 수 있나요?

예, 노드 유형에 서 VM 확장을 지정할 수 있습니다. 자세한 내용은 노드 형식 확장 샘플을 참조 하세요.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>내부 전용 부하 분산 장치를 사용할 수 있나요?

현재는 내부 전용 부하 분산 장치를 사용할 수 없습니다. 원치 않는 인바운드/아웃 바운드 트래픽을 차단 하는 네트워크 보안 그룹 규칙을 잠그는 것이 좋습니다.

### <a name="can-i-autoscale-my-cluster"></a>내 클러스터 크기를 자동으로 조정할 수 있나요? 
자동 크기 조정을 현재 미리 보기에서 사용할 수 없습니다.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>가용성 영역에서 클러스터를 배포할 수 있습니까? 
가용성 영역 간 클러스터는 현재 미리 보기에서 사용할 수 없습니다.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>내 클러스터 런타임에 대해 자동 업그레이드와 수동 업그레이드 중에서 선택할 수 있나요? 
미리 보기에서는 모든 런타임 업그레이드가 자동으로 완료 됩니다.

## <a name="applications"></a>애플리케이션

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Service Fabric 관리 되는 클러스터에 대 한 로컬 개발 환경이 있나요?

로컬 개발 환경은 기존 Service Fabric 클러스터에서 변경 되지 않은 상태로 유지 됩니다. 자세한 내용은 [.Net 응용 프로그램 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-quickstart-dotnet) 를 참조 하 여 로컬 개발 환경에 대 한 자세한 내용을 참조 하세요.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>응용 프로그램을 Azure Resource Manager 리소스로 배포할 수 있나요?

미리 보기에서는 응용 프로그램을 Azure Resource Manager 리소스로 배포할 수 없습니다. PowerShell 또는 CLI를 통해 클러스터에 직접 연결 하 여 응용 프로그램을 배포 해야 합니다. 이 기능은 관리 되 Service Fabric 클러스터가 일반 공급으로 전환 되기 전에 추가 될 예정입니다.
