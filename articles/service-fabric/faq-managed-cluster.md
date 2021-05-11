---
title: Service Fabric 관리형 클러스터에 관한 일반적인 질문
description: 기능, 사용 사례, 일반적인 시나리오를 포함해 Service Fabric 관리형 클러스터에 대한 질문과 대답입니다.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633090"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Service Fabric 관리형 클러스터 질문과 대답

Service Fabric 관리형 클러스터에 대한 FAQ와 그 답변입니다(미리 보기).

## <a name="general"></a>일반

### <a name="what-are-service-fabric-managed-clusters"></a>Service Fabric 관리형 클러스터란 무엇인가요?

Service Fabric 관리형 클러스터는 클러스터를 보다 쉽게 배포하고 관리할 수 있도록 설계된 Service Fabric 클러스터 리소스 모델의 진화형입니다. Service Fabric 관리형 클러스터는 Azure Resource Manager 캡슐화 모델을 사용하여, 현재 사용자가 배포해야 하는 다수의 독립 리소스(가상 머신 확장 집합, 부하 분산 장치, IP 등)에 비해 단일 클러스터 리소스만 정의하고 배포할 수 있게 해줍니다.

### <a name="what-regions-are-supported-in-the-preview"></a>미리 보기가 지원되는 지역은 어디인가요?

Service Fabric 관리형 클러스터 미리 보기가 지원되는 지역에는 `centraluseuap`, `eastus2euap`, `eastasia`, `northeurope`, `westcentralus`, `eastus2`이 포함됩니다.

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>기존 Service Fabric 클러스터를 관리형 클러스터 리소스로 현재 위치 마이그레이션을 할 수 있나요?

아니요. 이제는 새 Service Fabric 관리형 클러스터 리소스 유형을 사용하기 위해 새 Service Fabric 클러스터 리소스를 만들어야 합니다.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Service Fabric 관리형 클러스터에 추가 비용이 들어가나요?

아니요. 클러스터에 필요한 기본 컴퓨팅, 스토리지, 네트워킹 리소스 비용 외에 Service Fabric 관리형 클러스터에 대한 추가 비용은 없습니다.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Service Fabric 관리형 클러스터 리소스에서 도입된 새로운 SLA(서비스 수준 약정)가 있나요?

SLA는 현재 Service Fabric 리소스 모델에서 변경되지 않았습니다.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>기본과 표준 SKU 클러스터 간의 차이점은 무엇인가요?

기본 SKU 클러스터는 대부분의 구성이 Service Fabric 리소스 공급자에 의해 제공됩니다. 기본 SKU 클러스터는 테스트 및 사전 프로덕션 환경에 사용하기 위한 클러스터입니다. 표준 SKU 클러스터를 사용하면 사용자의 특정한 요구를 충족하는 클러스터를 구성할 수 있습니다. 자세한 내용은 [Service Fabric 관리형 클러스터 SKU](./overview-managed-cluster.md#service-fabric-managed-cluster-skus)를 참조하세요.

## <a name="cluster-deployment-and-management"></a>클러스터 배포 및 관리

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>가상 머신 확장 집합에서 사용자 지정 스크립트 확장을 실행하고 있습니다. 관리형 Service Fabric 리소스로도 계속 그렇게 진행할 수 있나요?

예, 관리형 클러스터 노드 형식에서 VM 확장을 지정할 수 있습니다. 자세한 내용은 [Service Fabric 관리형 클러스터 노드 형식에 확장 집합 확장 추가](how-to-managed-cluster-vmss-extension.md)를 참조하세요.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>내부 전용 부하 분산 장치를 사용할 수 있나요?

아니요. 현재는 내부 전용 부하 분산 장치를 사용할 수 없습니다. 원치 않는 인바운드/아웃바운드 트래픽을 차단하도록 NSG(네트워크 보안 그룹) 규칙을 잠그는 것을 권장합니다.

### <a name="can-i-autoscale-my-cluster"></a>내 클러스터 크기를 자동으로 조정할 수 있나요?

자동 크기 조정은 현재 미리 보기에서 사용할 수 없습니다.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>가용성 영역에서 클러스터를 배포할 수 있나요?

가용성 영역 간 클러스터는 현재 미리 보기에서 사용할 수 없습니다.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>내 클러스터 런타임에 대해 자동 업그레이드와 수동 업그레이드 중에서 선택할 수 있나요?

미리 보기에서는 모든 런타임 업그레이드가 자동으로 완료됩니다.

## <a name="applications"></a>애플리케이션

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Service Fabric 관리형 클러스터에서 사용할 수 있는 로컬 개발 환경이 있나요?

로컬 개발 환경은 기존 Service Fabric 클러스터에서 변경되지 않은 상태로 유지됩니다. 자세한 내용은 [개발 환경 설정](./service-fabric-get-started.md)에서 로컬 개발 환경에 대한 자세한 내용을 참조하세요.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>애플리케이션을 Azure Resource Manager 리소스로 배포할 수 있나요?

예. PowerShell 및 CLI를 사용하는 배포 외에도, 애플리케이션을 Azure Resource Manager 리소스로 배포하기 위한 지원이 추가되었습니다. 시작하려면 [ARM 템플릿을 사용한 Service Fabric 관리형 클러스터 애플리케이션 배포](how-to-managed-cluster-app-deployment-template.md)를 참조하세요.
