---
title: AKS(Azure Kubernetes Service)에 대한 모범 사례
description: 클러스터 운영자 및 개발자가 AKS(Azure Kubernetes Service)에서 애플리케이션을 구축 및 관리하는 모범 사례에 대한 모음입니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 12/07/2018
ms.author: iainfou
ms.openlocfilehash: 20922f0fb6932191b8e522e4640b1ba4fecd5ca4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514569"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 애플리케이션을 구축 및 관리하는 클러스터 운영자 및 개발자의 모범 사례

AKS(Azure Kubernetes Service)에서 애플리케이션을 성공적으로 구축하고 실행하기 위해 이해하고 구현해야 할 몇 가지 주요 고려 사항이 있습니다. 이러한 영역에는 다중 테넌트 및 스케줄러 기능, 클러스터 및 Pod 보안, 비즈니스 연속성 및 재해 복구가 포함됩니다. 클러스터 운영자와 개발자가 각 영역에 대한 고려사항을 이해하고 적절한 기능을 구현하는 데 도움이 되도록 모범 사례는 다음과 같이 분류됩니다.

이러한 모범 사례와 개념 문서는 AKS 제품 그룹, 엔지니어링 팀 및 GBB(글로벌 블랙 벨트)를 포함한 현장 팀과 협력하여 작성되었습니다.

## <a name="cluster-operator-best-practices"></a>클러스터 운영자 모범 사례

클러스터 운영자는 애플리케이션 소유자 및 개발자와 협력하여 요구 사항을 파악합니다. 그런 다음, 다음과 같은 모범 사례를 사용하여 필요에 따라 AKS 클러스터를 구성합니다.

**다중 테넌트 지원**

* [클러스터 격리에 대한 모범 사례](operator-best-practices-cluster-isolation.md)
    * 다중 테넌트 핵심 구성 요소 및 네임스페이스를 통한 논리적 격리와 관련이 있습니다.
* [기본 스케줄러 기능에 대한 모범 사례](operator-best-practices-scheduler.md)
    * 리소스 할당량 및 Pod 중단 예산을 사용합니다.
* [고급 스케줄러 기능에 대한 모범 사례](operator-best-practices-advanced-scheduler.md)
    * 흔적 및 허용 오차, 노드 선택기 및 선호도, Pod 간 선호도 및 선호도 방지를 사용합니다.
* [인증 및 권한 부여에 대한 모범 사례](operator-best-practices-identity.md)
    * RBAC(역할 기반 액세스 제어) 및 Pod ID를 사용하여 Azure Active Directory와 통합합니다.

**보안**

* [클러스터 보안 및 업그레이드에 대한 모범 사례](operator-best-practices-cluster-security.md)
    * API 서버에 대한 액세스를 보호하고, 컨테이너 액세스를 제한하며, 업그레이드 및 노드 재부팅을 관리합니다.
* [컨테이너 이미지 관리 및 보안에 대한 모범 사례](operator-best-practices-container-image-management.md)
    * 이미지 및 런타임 및 기본 이미지 업데이트 시 자동화 된 빌드를 보호 하는 포함 되어 있습니다.
* [Pod 보안에 대한 모범 사례](developer-best-practices-pod-security.md)
    * 리소스에 대한 액세스를 보호하고, 자격 증명 노출을 제한하며, Pod ID와 디지털 키 자격 증명 모음을 사용합니다.

**네트워크 및 스토리지**

* [네트워크 연결에 대한 모범 사례](operator-best-practices-network.md)
    * 서로 다른 네트워크 모델이 포함되고, 수신 및 WAF(웹 애플리케이션 방화벽)를 사용하며, 노드 SSH 액세스를 보호합니다.
* [스토리지 및 백업에 대한 모범 사례](operator-best-practices-storage.md)
    * 적절한 스토리지 유형과 노드 크기를 선택하고, 볼륨과 데이터 백업을 동적으로 프로비전합니다.

**엔터프라이즈급 워크로드 실행**

* [비즈니스 연속성 및 재해 복구에 대한 모범 사례](operator-best-practices-multi-region.md)
    * 지역 쌍, Azure Traffic Manager를 포함한 여러 클러스터 및 컨테이너 이미지의 지역 복제를 사용합니다.

## <a name="developer-best-practices"></a>개발자 모범 사례

개발자 또는 애플리케이션 소유자가 개발 환경을 간소화하고, 필요한 애플리케이션 성능 요구 사항을 정의할 수 있습니다.

* [애플리케이션 개발자의 리소스 관리에 대한 모범 사례](developer-best-practices-resource-management.md)
    * Pod 리소스 요청 및 제한을 정의하고, 개발 도구를 구성하며, 애플리케이션 문제를 확인합니다.
* [Pod 보안에 대한 모범 사례](developer-best-practices-pod-security.md)
    * 리소스에 대한 액세스를 보호하고, 자격 증명 노출을 제한하며, Pod ID와 디지털 키 자격 증명 모음을 사용합니다.

## <a name="kubernetes--aks-concepts"></a>Kubernetes/AKS 개념

이러한 모범 사례의 일부 기능과 구성 요소를 이해하기 위해 AKS(Azure Kubernetes Service)의 클러스터에 대한 다음 개념 문서를 참조할 수도 있습니다.

* [Kubernetes 핵심 개념](concepts-clusters-workloads.md)
* [액세스 및 ID](concepts-identity.md)
* [보안 개념](concepts-security.md)
* [네트워크 개념](concepts-network.md)
* [저장소 옵션](concepts-storage.md)
* [크기 조정 옵션](concepts-scale.md)

## <a name="next-steps"></a>다음 단계

AKS를 시작해야 하는 경우 빠른 시작 중 하나에 따라 [Azure CLI](kubernetes-walkthrough.md) 또는 [Azure Portal](kubernetes-walkthrough-portal.md)을 사용하여 AKS(Azure Kubernetes Service) 클러스터를 배포합니다.
