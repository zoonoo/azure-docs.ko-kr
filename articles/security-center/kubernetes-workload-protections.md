---
title: Kubernetes 워크 로드에 대 한 워크 로드 보호
description: Azure Security Center의 Kubernetes 워크 로드 보호 보안 권장 사항 집합을 사용 하는 방법에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/12/2020
ms.author: memildin
ms.openlocfilehash: 500131121640026fd3fda5be9eecb376d2db8f0e
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999315"
---
# <a name="protect-your-kubernetes-workloads"></a>Kubernetes 워크로드 보호

이 페이지에서는 Kubernetes 워크 로드 보호 전용의 보안 권장 사항 집합 Azure Security Center 사용 하는 방법을 설명 합니다.

[Kubernetes 허용 제어를 사용 하 여 작업 보호 모범 사례](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control) 에서 이러한 기능에 대해 자세히 알아보세요.

Security Center는 Azure Defender를 사용 하도록 설정 하는 경우 더 많은 컨테이너 보안 기능을 제공 합니다. 구체적으로는 다음과 같습니다.

- 컨테이너 [레지스트리에 대 한 Azure Defender의](defender-for-container-registries-introduction.md) 취약성에 대 한 컨테이너 레지스트리를 검색 합니다.
- [Kubernetes 용 Azure Defender](defender-for-kubernetes-introduction.md) K8s 클러스터에 대 한 실시간 위협 검색 경고 가져오기

> [!TIP]
> Kubernetes 클러스터 및 노드에 대해 나타날 수 있는 *모든* 보안 권장 사항의 목록은 권장 사항 참조 표의 [컨테이너 섹션](recommendations-reference.md#recs-containers) 을 참조 하세요.



## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기|
|가격 책정:|Free|
|필요한 역할 및 권한:|할당을 편집 하기 위한 **소유자** 또는 **보안 관리자**<br>권장 사항을 보려면 **판독기**|
|지원 되는 클러스터:|Kubernetes v 1.14 이상이 필요 합니다.<br>클러스터에 PodSecurityPolicy 리소스 (이전 PSP 모델)가 없음<br>Windows 노드가 지원 되지 않습니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||


## <a name="set-up-your-workload-protection"></a>워크 로드 보호 설정

Azure Security Center **에는 Kubernetes 용 Azure Policy 추가 기능**을 설치한 경우 사용할 수 있는 권장 구성 번들이 포함 되어 있습니다.

1. 권장 사항을 구성 하려면 먼저 추가를 설치 해야 합니다.

    1. 권장 사항 페이지에서 **Kubernetes에 대 한 추가 기능 Azure Policy 이라는 권장 사항을 검색 하 여 클러스터에 설치 하 고 사용 하도록 설정 해야 합니다**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="권장 사항 * * Kubernetes 용 Azure Policy 추가 기능을 클러스터에 설치 하 고 사용 하도록 설정 해야 합니다. * *":::

        > [!TIP]
        > 권장 사항은 다섯 가지 보안 제어에 포함 되며, 다음 단계에서 어떤 것을 선택 하 든 중요 하지 않습니다.

    1. 보안 제어에서 추가를 설치할 수 있는 리소스를 확인 하는 권장 사항을 선택 하 고 **재구성**을 선택 합니다. 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="권장 사항 * * Kubernetes 용 Azure Policy 추가 기능을 클러스터에 설치 하 고 사용 하도록 설정 해야 합니다. * *":::

1. 추가 기능 설치가 완료 Security Center 되 고 약 30 분 후에는 다음과 같은 권장 사항에 대 한 클러스터의 상태를 표시 합니다 .이에 대 한 자세한 내용은 각각 관련 보안 제어에서 다음과 같이 표시 됩니다.

    > [!TIP]
    > 일부 권장 사항에는 Azure Policy를 통해 효율적으로 사용 하기 위해 사용자 지정 해야 하는 매개 변수가 있습니다. 예를 들어 권장 구성 **컨테이너 이미지를 신뢰할 수 있는 레지스트리 에서만 배포 해야**하는 경우 신뢰할 수 있는 레지스트리를 정의 해야 합니다.
    > 
    > 구성이 필요한 권장 구성에 필요한 매개 변수를 입력 하지 않으면 작업은 비정상으로 표시 됩니다.

    | 권장 사항 이름                                                                   | 보안 컨트롤                         | 구성 필요 |
    |---------------------------------------------------------------------------------------|------------------------------------------|------------------------|
    | 컨테이너 CPU 및 메모리 제한 적용 (미리 보기)                          | DDoS 공격 으로부터 응용 프로그램 보호 | 아니요                     |
    | 권한 있는 컨테이너를 피해 야 함 (미리 보기)                                     | 액세스 및 사용 권한 관리            | 아니요                     |
    | 컨테이너 (미리 보기)에 대해 변경할 수 없는 (읽기 전용) 루트 파일 시스템을 적용 해야 함     | 액세스 및 사용 권한 관리            | 아니요                     |
    | 권한 에스컬레이션이 있는 컨테이너를 사용 하지 않아야 함 (미리 보기)                       | 액세스 및 사용 권한 관리            | 아니요                     |
    | 루트 사용자로 컨테이너를 실행 하는 것을 피해 야 함 (미리 보기)                           | 액세스 및 사용 권한 관리            | 아니요                     |
    | 중요 한 호스트 네임 스페이스를 공유 하는 컨테이너를 피해 야 함 (미리 보기)              | 액세스 및 사용 권한 관리            | 아니요                     |
    | 컨테이너 (미리 보기)에 대해 최소 권한 Linux 기능을 적용 해야 함       | 액세스 및 사용 권한 관리            | **예**                |
    | Pod HostPath 볼륨 탑재 사용은 알려진 목록 (미리 보기)으로 제한 되어야 합니다.    | 액세스 및 사용 권한 관리            | **예**                |
    | 컨테이너는 허용 된 포트만 수신 해야 함 (미리 보기)                              | 무단 네트워크 액세스 제한     | **예**                |
    | 서비스는 허용 된 포트만 (미리 보기)에서 수신 해야 합니다.                                | 무단 네트워크 액세스 제한     | **예**                |
    | 호스트 네트워킹 및 포트 사용은 제한 되어야 합니다 (미리 보기).                     | 무단 네트워크 액세스 제한     | **예**                |
    | 컨테이너 AppArmor 프로필을 재정의 하거나 사용 하지 않도록 설정 해야 합니다 (미리 보기). | 보안 구성 수정        | **예**                |
    | 컨테이너 이미지는 신뢰할 수 있는 레지스트리 (미리 보기) 에서만 배포 해야 합니다.            | 취약성 해결                | **예**                |


1. 매개 변수가 포함 된 권장 사항을 사용자 지정 해야 합니다. 매개 변수를 설정 합니다.

    1. Security Center의 메뉴에서 **보안 정책**을 선택 합니다.
    1. 관련 구독을 선택합니다.
    1. **Security Center 기본 정책** 섹션에서 **유효 정책 보기**를 선택 합니다.
    1. "ASC Default"를 선택 합니다.
    1. **매개 변수** 탭을 열고 필요에 따라 값을 수정 합니다.
    1. **검토 후 저장**을 선택합니다.
    1. **저장**을 선택합니다.


1. 권장 사항을 적용 하려면 다음을 수행 합니다. 

    1. 권장 사항 세부 정보 페이지를 열고 **거부**를 선택 합니다.

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="권장 사항 * * Kubernetes 용 Azure Policy 추가 기능을 클러스터에 설치 하 고 사용 하도록 설정 해야 합니다. * *":::

        그러면 범위를 설정 하는 창이 열립니다. 

    1. 범위를 설정 했으면 **거부로 변경**을 선택 합니다.

1. 클러스터에 적용 되는 권장 사항을 확인 하려면 다음을 수행 합니다.

    1. Security Center의 [자산 인벤토리](asset-inventory.md) 페이지를 열고 리소스 종류 필터를 사용 하 여 **서비스를 Kubernetes**.

    1. 사용 가능한 권장 사항을 조사 하 고 검토할 클러스터를 선택 합니다. 

1. 워크 로드 보호 집합에서 권장 사항을 볼 때 클러스터와 함께 나열 된 영향을 받는 pod ("Kubernetes components") 수가 표시 됩니다. 특정 pod 목록을 보려면 클러스터를 선택 하 고 **작업 수행**을 선택 합니다.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="권장 사항 * * Kubernetes 용 Azure Policy 추가 기능을 클러스터에 설치 하 고 사용 하도록 설정 해야 합니다. * *"::: 

1. 적용을 테스트 하려면 아래의 두 Kubernetes 배포를 사용 합니다.

    - 하나는 작업 보호 권장 구성의 번들을 준수 하는 정상적인 배포에 대 한 것입니다.
    - 다른 *하나* 는 권장 사항을 준수 하지 않는 비정상 배포를 위한 것입니다.

    예제. yaml 파일을 있는 그대로 배포 하거나 고유한 워크 로드를 수정 하기 위한 참조로 사용 (step VIII)  


## <a name="healthy-deployment-example-yaml-file"></a>정상적인 배포 예제. yaml 파일

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>비정상 배포 예제. yaml 파일

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 워크 로드 보호를 구성 하는 방법을 배웠습니다. 

기타 관련 자료는 다음 페이지를 참조 하세요. 

- [컨테이너에 대 한 Security Center 권장 사항](recommendations-reference.md#recs-containers)
- [AKS 클러스터 수준에 대 한 경고](alerts-reference.md#alerts-akscluster)
- [컨테이너 호스트 수준에 대 한 경고](alerts-reference.md#alerts-containerhost)