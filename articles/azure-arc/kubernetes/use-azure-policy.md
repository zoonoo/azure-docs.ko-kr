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
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779957"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Azure Policy를 사용하여 대규모로 클러스터 구성 대규모(미리 보기)

## <a name="overview"></a>개요

Azure Policy를 사용하여 각각의 `Microsoft.Kubernetes/connectedclusters` 또는 Git Ops 사용 `Microsoft.ContainerService/managedClusters` 리소스에 특정 `Microsoft.KubernetesConfiguration/sourceControlConfigurations`가 적용되도록 합니다.  Azure Policy를 사용하려면 기존 정책 정의를 선택하고 정책 할당을 만듭니다.  정책 할당을 만들 때 할당 범위를 설정합니다. 이는 Azure 리소스 그룹 또는 구독이 됩니다.  또한 생성되는 `sourceControlConfiguration`에 대한 매개 변수를 설정합니다.  할당이 만들어지면 정책 엔진에서 범위 내에 있는 모든 `connectedCluster` 또는 `managedCluster` 리소스를 식별하고 각 리소스에 `sourceControlConfiguration`을 적용합니다.

각 클러스터에 대한 진실 공급원으로 여러 Git 리포지토리를 사용하는 경우(예: 중앙 IT/클러스터 운영자용 리포지토리 하나, 그 외 애플리케이션 리포지토리), 여러 정책을 할당하여 이를 사용할 수 있습니다. 각 정책 할당은 다른 Git 리포지토리를 사용하도록 구성되었습니다.

## <a name="create-a-custom-policy-definition"></a>사용자 지정 정책 정의 만들기

1. Azure Portal에서 정책으로 이동하여 사이드바의 **제작** 섹션에서 **정의**를 선택합니다.
2. **+ 정책 정의**를 선택합니다.
3. **정의 위치**를 구독 또는 관리 그룹으로 설정합니다.  이렇게 하면 정책 정의를 사용할 수 있는 가장 넓은 범위가 결정됩니다.
4. 규칙에 **이름**과 **설명**을 지정합니다.
5. 범주에서 **새로 만들기**를 선택하고 *Kubernetes 클러스터 - Azure Arc*를 씁니다.
6. **정책 규칙** 편집 상자에서 이 [예제 정책 정의](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json)의 내용을 복사/붙여넣기 합니다.
7. **저장**합니다.

사용자 지정 정책 정의를 만들기 위한 이 단계는 작업이 완료된 후 이 정책을 기본 제공 정책으로 설정하는 데 필요하지 않습니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

1. Azure Portal에서 정책으로 이동하여 사이드바의 **제작** 섹션에서 **정의**를 선택합니다.
2. 방금 만든 정의를 찾아 선택합니다.
3. 페이지 작업에서 **할당**을 선택합니다.
4. **범위**를 정책 할당이 적용되는 관리 그룹, 구독 또는 리소스 그룹으로 설정합니다.
5. 정책 범위에서 리소스를 제외하려면 **제외**를 설정합니다.
6. 정책 할당에 쉽게 식별할 수 있도록 **이름**과 **설명**을 지정합니다.
7. **정책 적용**이 *사용됨*으로 설정되어 있는지 확인합니다.
8. **다음**을 선택합니다.
9. `sourceControlConfiguration`을 만드는 동안 사용할 매개 변수 값을 설정합니다.
10. **다음**을 선택합니다.
11. **수정 작업 만들기**를 사용합니다.
12. **관리 ID 만들기**가 선택되어 있는지, ID에 **참여자** 권한이 있는지 확인합니다.  필요한 권한에 대한 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal)와 [이 문서의 설명](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)을 참조하세요.
13. **검토 + 만들기**를 선택합니다.

정책 할당을 만든 후 할당 범위 내에 있는 새 `connectedCluster` 리소스(또는 GitOps 에이전트가 설치된 `managedCluster` 리소스)에 대해 `sourceControlConfiguration`이 적용됩니다.  기존 클러스터의 경우 수정 작업을 수동으로 실행해야 합니다.  정책 할당이 적용되는 데에는 보통 10~20분이 걸립니다.

## <a name="verify-a-policy-assignment"></a>정책 할당 확인

1. Azure Portal에서 `connectedCluster` 리소스 중 하나로 이동하여 사이드바의 **설정** 섹션에서 **정책**을 선택합니다. (AKS 관리 클러스터에 대한 UX가 아직 구현되지는 않았지만, 제공될 예정입니다.)
2. 목록에 위에서 만든 정책 할당이 표시되어야 하고, **준수 상태**는 *준수*여야 합니다.
3. 사이드바의 **설정** 섹션에서 **구성**을 선택합니다.
4. 목록에 정책 할당에서 만든 `sourceControlConfiguration`이 표시되어야 합니다.
5. **kubectl**을 사용하여 클러스터를 조사합니다. `sourceControlConfiguration`에서 만든 네임스페이스와 아티팩트가 표시되어야 합니다.
6. 5분 이내에 구성된 Git 리포지토리의 매니페스트에 설명된 아티팩트가 클러스터에 표시되어야 합니다.

## <a name="next-steps"></a>다음 단계

* [Arc에서 Kubernetes 클러스터를 사용하는 컨테이너에 대한 Azure Monitor 설정](./deploy-azure-monitor-for-containers.md)
