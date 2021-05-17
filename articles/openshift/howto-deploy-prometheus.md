---
title: Azure Red Hat OpenShift 클러스터에 Prometheus 인스턴스 배포
description: 애플리케이션의 메트릭을 모니터링하려면 Azure Red Hat OpenShift 클러스터에 Prometheus 인스턴스를 만듭니다.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, 메트릭, red hat
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633447"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에 독립 실행형 Prometheus 인스턴스 배포

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11은 2022년 6월 30일부터 사용이 중지됩니다. 새 Azure Red Hat OpenShift 3.11 클러스터 만들기 지원은 2020년 11월 30일까지 계속됩니다. 사용 중지 후에는 보안 취약성을 방지하기 위해 남아 있는 Azure Red Hat OpenShift 3.11 클러스터가 종료됩니다.
> 
> 이 가이드를 따라 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md).
> 궁금한 점은 [문의하세요](mailto:arofeedback@microsoft.com).

이 문서에서는 Azure Red Hat OpenShift 클러스터에서 서비스 검색을 사용하는 독립 실행형 Prometheus 인스턴스를 구성하는 방법을 설명합니다.

> [!NOTE]
> Azure Red Hat OpenShift 클러스터에 대한 고객 관리 액세스는 필요하지 않습니다.

대상 설정:

- Prometheus 및 Alertmanager를 포함하는 하나의 프로젝트(prometheus-project)
- 모니터링할 애플리케이션을 포함하는 두 개의 프로젝트(project1 및 project2)

일부 Prometheus 구성 파일을 로컬로 준비합니다. 구성 파일을 저장할 새 폴더를 만듭니다. 나중에 비밀 토큰이 클러스터에 추가되는 경우를 위해 구성 파일은 클러스터에 비밀로 저장됩니다.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>OC 도구를 사용하여 클러스터에 로그인

1. 웹 브라우저를 연 다음 클러스터의 웹 콘솔로 이동합니다(https://openshift.*random-id*.*region*.azmosa.io).
2. Azure 자격 증명으로 로그인합니다.
3. 오른쪽 위 모서리에서 사용자 이름을 선택한 다음 **로그인 명령 복사** 를 선택합니다.
4. 사용할 터미널에 사용자 이름을 붙여넣습니다.

> [!NOTE]
> 올바른 클러스터에 로그인했는지 확인하려면 `oc whoami -c` 명령을 실행합니다.

## <a name="prepare-the-projects"></a>프로젝트 준비

프로젝트를 만들려면 다음 명령을 실행합니다.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> `-n` 또는 `--namespace` 매개 변수를 사용하거나 `oc project` 명령을 실행하여 활성 프로젝트를 선택할 수 있습니다.

## <a name="prepare-the-prometheus-configuration-file"></a>Prometheus 구성 파일 준비
다음 내용을 입력하여 prometheus.yml 파일을 만듭니다.
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
다음 구성을 입력하여 Prom이라는 비밀을 만듭니다.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

prometheus.yml 파일은 기본 Prometheus 구성 파일입니다. 이 파일은 세 프로젝트(prometheus-project, app-project1, app-project2)에서 간격을 설정하고 자동 검색을 구성합니다. 이전 구성 파일에서 자동 검색된 엔드포인트는 인증 없이 HTTP를 통해 스크래핑됩니다.

엔드포인트 스크래핑에 대한 자세한 내용은 [Prometheus scrape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)를 참조하세요.


## <a name="prepare-the-alertmanager-config-file"></a>Alertmanager 구성 파일 준비
다음 내용을 입력하여 alertmanager.yml 파일을 만듭니다.
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
다음 구성을 입력하여 Prom-Alerts라는 비밀을 만듭니다.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml은 Alert Manager 구성 파일입니다.

> [!NOTE]
> 이전 두 단계를 확인하려면 `oc get secret -n prometheus-project` 명령을 실행합니다.

## <a name="start-prometheus-and-alertmanager"></a>Prometheus 및 Alertmanager 시작
[openshift/origin repository](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)로 이동하여 [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) 템플릿을 다운로드합니다. 다음 구성을 입력하여 해당 템플릿을 prometheus-project에 적용합니다.
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
prometheus-standalone.yaml 파일은 OpenShift 템플릿입니다. 이 도구는 앞에 oauth-proxy가 있는 Prometheus 인스턴스와 역시 oauth-proxy로 보안이 유지되는 Alertmanager 인스턴스를 만듭니다. 이 템플릿에서 oauth-proxy는 prometheus-project 네임스페이스를 ‘가져올’ 수 있는 모든 사용자를 허용하도록 구성됩니다(`-openshift-sar` 플래그 참조).

> [!NOTE]
> prom StatefulSet의 복제본 DESIRED 수와 CURRENT 수가 같은지 확인하려면 `oc get statefulset -n prometheus-project` 명령을 실행합니다. 프로젝트의 모든 리소스를 확인하려면 `oc get all -n prometheus-project` 명령을 실행합니다.

## <a name="add-permissions-to-allow-service-discovery"></a>서비스 검색을 허용하는 권한 추가

다음 내용을 입력하여 prometheus-sdrole.yml 파일을 만듭니다.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
서비스 검색을 허용하려는 모든 프로젝트에 템플릿을 적용하려면 다음 명령을 실행합니다.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Role 및 RoleBinding이 올바르게 만들어졌는지 확인하려면 `oc get role` 명령과 `oc get rolebinding` 명령을 실행합니다.

## <a name="optional-deploy-example-application"></a>선택 사항: 예제 애플리케이션 배포

모든 것이 정상이지만 메트릭 원본이 없습니다. Prometheus URL(https://prom-prometheus-project.apps.*random-id*.*region*.azmosa.io/)로 이동합니다. 다음 명령을 사용하면 찾을 수 있습니다.

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 호스트 이름 앞에 https:// 접두사를 추가해야 합니다.

**상태 > 서비스 검색** 페이지에 0/0개의 활성 대상이 표시됩니다.

/metrics endpoint에서 기본 Python 메트릭을 공개하는 예제 애플리케이션을 배포하려면 다음 명령을 실행합니다.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
새 애플리케이션은 배포 후 30초 내에 서비스 검색 페이지에 유효한 대상으로 표시되어야 합니다.

자세한 내용을 보려면 **상태** > **대상** 을 선택하세요.

> [!NOTE]
> Prometheus는 성공적으로 스크래핑된 대상마다 up 메트릭에 데이터 요소를 추가합니다. 왼쪽 위 모서리에서 **Prometheus** 를 선택하고 식으로 **up** 을 입력한 다음 **실행** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

애플리케이션에 사용자 지정 Prometheus 계측을 추가할 수 있습니다. Prometheus 메트릭 준비를 간소화하는 Prometheus Client 라이브러리에 다양한 프로그래밍 언어를 사용할 수 있습니다.

자세한 내용은 다음 GitHub 라이브러리를 참조하세요.

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
