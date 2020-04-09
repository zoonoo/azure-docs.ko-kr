---
title: Azure Red Hat 오픈시프트 클러스터에 프로메테우스 인스턴스 배포
description: Azure Red Hat OpenShift 클러스터에서 프로메테우스 인스턴스를 만들어 응용 프로그램의 메트릭을 모니터링합니다.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: 프로메테우스, 아로, 오픈 시프트, 메트릭, 빨간 모자
ms.openlocfilehash: 7f22df587f51af735e0ea663e53f6eef14d60692
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886891"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat 오픈시프트 클러스터에 독립 실행형 프로메테우스 인스턴스 배포

이 문서에서는 Azure Red Hat OpenShift 클러스터에서 서비스 검색을 사용하는 독립 실행형 Prometheus 인스턴스를 구성하는 방법에 대해 설명합니다.

> [!NOTE]
> Azure Red Hat OpenShift 클러스터에 대한 고객 관리자 액세스가 필요하지 않습니다.

대상 설정:

- 프로메테우스와 경고 관리자를 포함하는 하나의 프로젝트(프로메테우스-프로젝트).
- 모니터링할 응용 프로그램이 포함된 두 프로젝트(앱-project1 및 앱-project2)입니다.

일부 프로메테우스 구성 파일을 로컬에서 준비합니다. 저장할 새 폴더를 만듭니다. Config 파일은 나중에 비밀 토큰이 클러스터에 추가되는 경우에 대비하여 클러스터에 비밀로 저장됩니다.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>OC 도구를 사용하여 클러스터에 로그인

1. 웹 브라우저를 연 다음 클러스터의 웹 콘솔로https://openshift이동합니다.* 랜덤 ID*. 지역 .azmosa.io)을 *참조하십시오.*
2. Azure 자격 증명으로 로그인합니다.
3. 오른쪽 상단 모서리에서 사용자 이름을 선택한 다음 **로그인 명령 복사를**선택합니다.
4. 사용할 터미널에 사용자 이름을 붙여넣습니다.

> [!NOTE]
> 올바른 클러스터에 로그인되어 있는지 확인하려면 명령을 `oc whoami -c` 실행합니다.

## <a name="prepare-the-projects"></a>프로젝트 준비

프로젝트를 만들려면 다음 명령을 실행합니다.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> 또는 `-n` `--namespace` 매개 변수를 사용하거나 명령을 실행하여 활성 `oc project` 프로젝트를 선택할 수 있습니다.

## <a name="prepare-the-prometheus-configuration-file"></a>프로메테우스 구성 파일 준비
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
다음 구성을 입력하여 무도회라는 비밀을 만듭니다.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

prometheus.yml 파일은 기본 프로메테우스 구성 파일입니다. 간격을 설정하고 세 가지 프로젝트(프로메테우스 프로젝트, 앱-project1, 앱-project2)에서 자동 검색을 구성합니다. 이전 구성 파일에서 자동 검색된 끝점은 인증 없이 HTTP를 통해 스크랩됩니다.

끝점을 긁는 것에 대한 자세한 내용은 [Prometheus scape config를](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)참조하십시오.


## <a name="prepare-the-alertmanager-config-file"></a>경고 관리자 구성 파일 준비
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
다음 구성을 입력하여 무도회 경고라는 비밀을 만듭니다.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml은 경고 관리자 구성 파일입니다.

> [!NOTE]
> 이전 두 단계를 확인하려면 `oc get secret -n prometheus-project` 명령을 실행합니다.

## <a name="start-prometheus-and-alertmanager"></a>프로메테우스 및 경고 관리자 시작
오픈 [시프트/오리진 리포지토리로](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) 이동하여 [프로메테우스 독립실행.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) 템플릿을 다운로드합니다. 다음 구성을 입력하여 프로메테우스 프로젝트에 템플릿을 적용합니다.
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
프로메테우스-독립형.yaml 파일은 OpenShift 템플릿입니다. 그것은 그것의 앞에 oauth 프록시와 경고 관리자 인스턴스와 프로메테우스 인스턴스를 만들 것입니다., 또한 oauth 프록시로 보호. 이 템플릿에서 oauth-proxy는 프로메테우스 프로젝트 네임스페이스를 "얻을"수 있는 모든 `-openshift-sar` 사용자를 허용하도록 구성됩니다(플래그 참조).

> [!NOTE]
> 무도회 StatefulSet가 원하는 및 현재 번호 복제본과 `oc get statefulset -n prometheus-project` 동일한지 확인하려면 명령을 실행합니다. 프로젝트의 모든 리소스를 확인하려면 `oc get all -n prometheus-project` 명령을 실행합니다.

## <a name="add-permissions-to-allow-service-discovery"></a>서비스 검색을 허용하는 권한 추가

다음 내용을 입력하여 프로메테우스 sdrole.yml 파일을 만듭니다.
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
> 역할 및 역할 바인딩이 올바르게 만들어졌는지 확인하려면 `oc get role` 및 `oc get rolebinding` 명령을 실행합니다.

## <a name="optional-deploy-example-application"></a>선택 사항: 예제 응용 프로그램 배포

모든 것이 작동하지만 메트릭 소스가 없습니다. 프로메테우스 URL로 이동https://prom-prometheus-project.apps( .* 랜덤 ID*. 지역 .azmosa.io/)을 *참조하십시오.* 다음 명령을 사용하여 찾을 수 있습니다.

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> 호스트 이름의 시작 부분에 https:// 접두사를 추가해야 합니다.

**상태 > 서비스 검색** 페이지에는 0/0 활성 대상이 표시됩니다.

/metrics 끝점 아래에 기본 Python 메트릭을 노출하는 예제 응용 프로그램을 배포하려면 다음 명령을 실행합니다.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
새 응용 프로그램은 배포 후 30초 이내에 서비스 검색 페이지에 유효한 대상으로 표시되어야 합니다.

자세한 내용을 보려면 **상태** > **대상을**선택합니다.

> [!NOTE]
> 성공적으로 스크레이핑된 모든 대상에 대해 Prometheus는 위계 메트릭에 데이터 포인트를 추가합니다. 왼쪽 위 모서리에서 **프로메테우스를** 선택하고 **식으로** 입력한 다음 **실행을**선택합니다.

## <a name="next-steps"></a>다음 단계

애플리케이션에 사용자 지정 프로메테우스 계측을 추가할 수 있습니다. 프로메테우스 메트릭 준비를 간소화하는 Prometheus 클라이언트 라이브러리는 다양한 프로그래밍 언어에 사용할 수 있습니다.

자세한 내용은 다음 GitHub 라이브러리를 참조하십시오.

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [이동](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
