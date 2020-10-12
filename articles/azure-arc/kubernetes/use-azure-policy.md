---
title: Azure Policy를 사용하여 대규모로 클러스터 구성 대규모(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy를 사용하여 대규모로 클러스터 구성 적용
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050038"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Azure Policy를 사용하여 대규모로 클러스터 구성 대규모(미리 보기)

## <a name="overview"></a>개요

Azure Policy를 사용 하 여 각 `Microsoft.Kubernetes/connectedclusters` 리소스 Git-Ops 또는 사용 가능한 `Microsoft.ContainerService/managedClusters` 리소스가 특정 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 적용 되도록 적용 합니다. Azure Policy를 사용하려면 기존 정책 정의를 선택하고 정책 할당을 만듭니다. 정책 할당을 만들 때 할당 범위를 설정합니다. 이는 Azure 리소스 그룹 또는 구독이 됩니다. 또한 생성되는 `sourceControlConfiguration`에 대한 매개 변수를 설정합니다. 할당이 만들어지면 정책 엔진에서 범위 내에 있는 모든 `connectedCluster` 또는 `managedCluster` 리소스를 식별하고 각 리소스에 `sourceControlConfiguration`을 적용합니다.

각 클러스터에 대한 진실 공급원으로 여러 Git 리포지토리를 사용하는 경우(예: 중앙 IT/클러스터 운영자용 리포지토리 하나, 그 외 애플리케이션 리포지토리), 여러 정책을 할당하여 이를 사용할 수 있습니다. 각 정책 할당은 다른 Git 리포지토리를 사용하도록 구성되었습니다.

## <a name="prerequisite"></a>필수 조건

`Microsoft.Authorization/policyAssignments/write`이 정책 할당을 만들려는 범위 (구독 또는 리소스 그룹)에 대 한 권한이 있는지 확인 합니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

1. Azure Portal에서 정책으로 이동하여 사이드바의 **제작** 섹션에서 **정의**를 선택합니다.
2. "Kubernetes" 범주의 "Kubernetes 클러스터에 GitOps 배포" 기본 정책을 선택 하 고 **할당**을 클릭 합니다.
3. **범위**를 정책 할당이 적용되는 관리 그룹, 구독 또는 리소스 그룹으로 설정합니다.
4. 정책 범위에서 리소스를 제외하려면 **제외**를 설정합니다.
5. 정책 할당에 쉽게 식별할 수 있도록 **이름**과 **설명**을 지정합니다.
6. **정책 적용**이 *사용됨*으로 설정되어 있는지 확인합니다.
7. **다음**을 선택합니다.
8. `sourceControlConfiguration`을 만드는 동안 사용할 매개 변수 값을 설정합니다.
9. **다음**을 선택합니다.
10. **수정 작업 만들기**를 사용합니다.
11. **관리 ID 만들기**가 선택되어 있는지, ID에 **참여자** 권한이 있는지 확인합니다. 필요한 권한에 대한 자세한 내용은 [이 문서](../../governance/policy/assign-policy-portal.md)와 [이 문서의 설명](../../governance/policy/how-to/remediate-resources.md)을 참조하세요.
12. **검토 + 만들기**를 선택합니다.

정책 할당을 만든 후 할당 범위 내에 있는 새 `connectedCluster` 리소스(또는 GitOps 에이전트가 설치된 `managedCluster` 리소스)에 대해 `sourceControlConfiguration`이 적용됩니다. 기존 클러스터의 경우 수정 작업을 수동으로 실행해야 합니다. 정책 할당이 적용되는 데에는 보통 10~20분이 걸립니다.

## <a name="verify-a-policy-assignment"></a>정책 할당 확인

1. Azure Portal에서 `connectedCluster` 리소스 중 하나로 이동하여 사이드바의 **설정** 섹션에서 **정책**을 선택합니다. AKS 클러스터에 대 한 UX는 아직 구현 되지 않지만 출시 됩니다.
2. 목록에 위에서 만든 정책 할당이 표시되어야 하고, **준수 상태**는 *준수*여야 합니다.
3. 사이드바의 **설정** 섹션에서 **구성**을 선택합니다.
4. 목록에 정책 할당에서 만든 `sourceControlConfiguration`이 표시되어야 합니다.
5. **kubectl**을 사용하여 클러스터를 조사합니다. `sourceControlConfiguration`에서 만든 네임스페이스와 아티팩트가 표시되어야 합니다.
6. 5분 이내에 구성된 Git 리포지토리의 매니페스트에 설명된 아티팩트가 클러스터에 표시되어야 합니다.

## <a name="next-steps"></a>다음 단계

* [Arc에서 Kubernetes 클러스터를 사용하는 컨테이너에 대한 Azure Monitor 설정](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
