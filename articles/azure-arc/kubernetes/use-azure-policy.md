---
title: Azure Policy를 사용하여 대규모로 클러스터 구성 적용
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy를 사용하여 대규모로 클러스터 구성 적용
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121459"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Azure Policy를 사용하여 대규모로 클러스터 구성 적용

Azure Policy를 사용 하 여 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure Arc 사용 Kubernetes 클러스터 ()에서 대규모로 구성 (리소스 유형)을 적용할 수 있습니다 `Microsoft.Kubernetes/connectedclusters` .

Azure Policy를 사용 하려면 기존 정책 정의를 선택 하 고 정책 할당을 만듭니다. 정책 할당을 만들 때:
1. 할당에 대 한 범위를 설정 합니다.
    * 범위는 Azure 리소스 그룹 또는 구독이 됩니다. 
2. 생성 될 구성에 대 한 매개 변수를 설정 합니다. 

할당을 만들면 Azure Policy 엔진은 범위 내에 있는 모든 Azure Arc 사용 가능 Kubernetes 클러스터를 식별 하 고 각 클러스터에 구성을 적용 합니다.

여러 정책 할당을 사용 하 여 각각 서로 다른 Git 리포지토리를 가리키는 여러 구성을 만들 수 있습니다. 예를 들어 중앙 IT/클러스터 운영자를 위한 리포지토리와 응용 프로그램 팀을 위한 다른 리포지토리가 있습니다.

## <a name="prerequisite"></a>필수 조건

`Microsoft.Authorization/policyAssignments/write`이 정책 할당을 만들 범위 (구독 또는 리소스 그룹)에 대 한 권한이 있는지 확인 합니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

1. Azure Portal에서 **정책** 으로 이동 합니다.
1. 세로 막대의 **제작** 섹션에서 **정의** 를 선택 합니다.
1. "Kubernetes" 범주에서 "Kubernetes 클러스터에 GitOps 배포" 기본 제공 정책을 선택 합니다. 
1. **할당** 을 클릭 합니다.
1. **범위** 를 정책 할당이 적용 되는 관리 그룹, 구독 또는 리소스 그룹으로 설정 합니다.
    * 정책 범위에서 리소스를 제외 하려면 **제외** 를 설정 합니다.
1. 정책 할당에 쉽게 식별할 **이름** 및 **설명을** 제공 합니다.
1. **정책 적용** 이 **사용** 으로 설정 되어 있는지 확인 합니다.
1. **다음** 을 선택합니다.
1. 를 만드는 동안 사용할 매개 변수 값을 설정 합니다 `sourceControlConfiguration` .
1. **다음** 을 선택합니다.
1. **수정 작업 만들기** 를 사용합니다.
1. **관리 Id 만들기** 가 선택 되어 있고 Id에 **참가자** 권한이 있는지 확인 합니다. 
    * 자세한 내용은 [정책 할당 만들기 빠른](../../governance/policy/assign-policy-portal.md) 시작 및 [Azure Policy 사용 하 여 비준수 리소스 재구성 문서](../../governance/policy/how-to/remediate-resources.md)를 참조 하세요.
1. **검토 + 만들기** 를 선택합니다.

정책 할당을 만든 후에는 정책 할당 범위 내에서 생성 된 새 Azure Arc 사용 Kubernetes 클러스터에 구성이 적용 됩니다.

기존 클러스터의 경우 수동으로 재구성 작업을 실행 해야 합니다. 이 태스크는 일반적으로 정책 할당을 적용 하는 데 10 ~ 20 분이 걸립니다.

## <a name="verify-a-policy-assignment"></a>정책 할당 확인

1. Azure Portal에서 Azure Arc 사용 Kubernetes 클러스터 중 하나로 이동 합니다.
1. 세로 막대의 **설정** 섹션에서 **정책** 을 선택 합니다. 
    * 정책 목록에서 **준수 상태** 를 *규격* 으로 설정 하 여 이전에 만든 정책 할당을 확인 해야 합니다.
1. 사이드바의 **설정** 섹션에서 **구성** 을 선택합니다.
    * 구성 목록에서 정책 할당에 의해 생성 된 구성이 표시 됩니다.
1. `kubectl`를 사용 하 여 클러스터를 검사 합니다. 
    * 구성 리소스에서 만든 네임 스페이스와 아티팩트가 표시 됩니다.
    * 5 분 이내 (클러스터가 Azure에 네트워크로 연결 된 것으로 가정), Git 리포지토리의 매니페스트에서 설명 하는 개체가 클러스터에서 생성 되는 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Arc Enabled Kubernetes 클러스터를 사용 하 여 컨테이너에 대 한 Azure Monitor를 설정](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)합니다.
