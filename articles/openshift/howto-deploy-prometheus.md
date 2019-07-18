---
title: Azure Red Hat OpenShift 클러스터에서 독립 실행형 Prometheus 인스턴스 배포 | Microsoft Docs
description: 응용 프로그램의 메트릭을 모니터링 하는 Azure의 Red Hat OpenShift 클러스터에서 Prometheus 인스턴스를 만듭니다.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro openshift, 메트릭, red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827010"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에서 독립 실행형 Prometheus 인스턴스 배포

이 문서에서는 Azure Red Hat OpenShift 클러스터에서 서비스 검색을 사용 하는 독립 실행형 Prometheus 인스턴스를 구성 하는 방법을 설명 합니다.

> [!NOTE]
> Azure Red Hat OpenShift 클러스터에 대 한 고객 관리 액세스는 필요 하지 않습니다.

대상 설정:

- 하나 (prometheus-프로젝트)를 포함 하는 프로젝트 Prometheus 및 Alertmanager 합니다.
- 두 개의 프로젝트 project1 앱과 앱 project2를 모니터링 하는 응용 프로그램을 포함 하는 합니다.

일부 Prometheus 구성 파일을 로컬로 준비할 수 있습니다. 저장할 새 폴더를 만듭니다. 구성 파일 클러스터에 보안 토큰은 나중에 추가 하는 경우을 비밀로 클러스터에 저장 됩니다.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>OC 도구를 사용 하 여 클러스터에 로그인

1. 웹 브라우저를 열고 클러스터의 웹 콘솔에 이동 (https://openshift. *임의 id*. *지역*. azmosa.io).
2. Azure 자격 증명으로 로그인합니다.
3. 오른쪽 위 모서리에서 사용자 이름을 선택 하 고 선택한 **복사 로그인 명령을**합니다.
4. 사용자 이름을 사용 하는 터미널에 붙여 넣습니다.

> [!NOTE]
> 올바른 클러스터에 로그인 하는 경우를 보려면 실행는 `oc whoami -c` 명령입니다.

## <a name="prepare-the-projects"></a>프로젝트를 준비 합니다.

프로젝트를 만들려면 다음 명령을 실행 합니다.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 사용할 수 있습니다 합니다 `-n` 또는 `--namespace` 매개 변수 또는 실행 하 여 활성 프로젝트를 선택 합니다 `oc project` 명령입니다.

## <a name="prepare-the-prometheus-configuration-file"></a>Prometheus 구성 파일을 준비
다음 콘텐츠를 입력 하 여 prometheus.yml 파일을 만듭니다.
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
다음 구성을 입력 하 여 Prom 이라는 암호를 만듭니다.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus.yml 파일이 기본 Prometheus 구성 파일입니다. 간격을 설정 하 고 세 개의 프로젝트 (prometheus 프로젝트, 앱 project1 project2 앱)에서 자동 검색을 구성 합니다. 이전 구성 파일에서 자동 검색 끝점을 인증 하지 않고 HTTP를 통해 가져오고 됩니다.

끝점을 스크랩 하는 방법에 대 한 자세한 내용은 참조 하세요. [Prometheus scape 구성](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)합니다.


## <a name="prepare-the-alertmanager-config-file"></a>Alertmanager config 파일 준비
다음 콘텐츠를 입력 하 여 alertmanager.yml 파일을 만듭니다.
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
다음 구성을 입력 하 여 Prom 경고 이라는 암호를 만듭니다.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml 경고 관리자 구성 파일입니다.

> [!NOTE]
> 이전 두 단계를 확인 하려면 실행을 `oc get secret -n prometheus-project` 명령입니다.

## <a name="start-prometheus-and-alertmanager"></a>Prometheus 및 Alertmanager 시작
로 이동 [openshift/원본 리포지토리로](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) 하 고 다운로드 합니다 [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) 템플릿. 다음 구성을 입력 하 여 템플릿을 prometheus 프로젝트에 적용 됩니다.
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus standalone.yaml 파일 OpenShift 템플릿입니다. 고도 oauth 프록시를 사용 하 여 보호 Alertmanager 인스턴스를 앞에 oauth 프록시를 사용 하 여 Prometheus 인스턴스를 만듭니다. 이 템플릿에서 oauth 프록시는 모든 사용자가 있습니다 "get" prometheus 프로젝트 네임 스페이스를 허용 하도록 구성 되어 (참조는 `-openshift-sar` 플래그).

> [!NOTE]
> StatefulSet prom 같은 필요한 및 현재 복제본 수 있는지 확인 하려면 실행을 `oc get statefulset -n prometheus-project` 명령입니다. 프로젝트의 모든 리소스를 확인 하려면 실행을 `oc get all -n prometheus-project` 명령입니다.

## <a name="add-permissions-to-allow-service-discovery"></a>서비스 검색을 허용 하도록 사용 권한을 추가 합니다.

다음 콘텐츠를 입력 하 여 prometheus sdrole.yml 파일을 만듭니다.
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
서비스 검색을 허용 하려는 모든 프로젝트에 템플릿을 적용 하려면 다음 명령을 실행 합니다.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
자체에서 메트릭을 수집 하려면 Prometheus가 prometheus 프로젝트에서 사용 권한을 적용 합니다.

> [!NOTE]
> 역할 및 RoleBinding 올바르게 생성 되었는지 확인 하려면 실행 합니다 `oc get role` 및 `oc get rolebinding` 명령입니다.

## <a name="optional-deploy-example-application"></a>선택 사항: 예제 응용 프로그램 배포

모든 것이 작동 하지만 메트릭 원본이 없습니다. Prometheus URL로 이동 (https://prom-prometheus-project.apps. *임의 id*. *지역*.azmosa.io/). 다음 명령을 사용 하 여 찾을 수 있습니다.

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 호스트 이름의 시작 부분에는 https:// 접두사를 추가 해야 합니다.

합니다 **상태 > 서비스 검색** 0/0 active 대상 페이지에 표시 됩니다.

/Metrics 끝점에서 기본 Python 메트릭을 노출 하는 예제 응용 프로그램을 배포 하려면 다음 명령을 실행 합니다.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
새 응용 프로그램 배포 후 30 초 내에 서비스 검색 페이지에서 유효한 대상으로 표시 됩니다.

자세한 내용은 선택 **상태** > **대상을**합니다.

> [!NOTE]
> 성공적으로 직접 달았습니다 모든 대상에 대 한 Prometheus 최신 메트릭으로 데이터 요소를 추가합니다. 선택 **Prometheus** 왼쪽 위 모서리에 있는 입력 **위로** 식 및 선택 **Execute**합니다.

## <a name="next-steps"></a>다음 단계

Prometheus는 사용자 지정 계측 응용 프로그램에 추가할 수 있습니다. Prometheus 메트릭을 준비를 간소화, Prometheus 클라이언트 라이브러리를 다양 한 프로그래밍 언어 준비가 되었습니다.

자세한 내용은 다음 GitHub 라이브러리를 참조 하세요.

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
