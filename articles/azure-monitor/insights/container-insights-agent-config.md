---
title: 컨테이너 에이전트 데이터 수집을 위한 Azure 모니터 구성 | 마이크로 소프트 문서
description: 이 문서에서는 stdout/stderr 및 환경 변수 로그 수집을 제어하도록 컨테이너 에이전트에 대한 Azure Monitor를 구성하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933013"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>컨테이너에 대한 Azure 모니터에 대한 에이전트 데이터 컬렉션 구성

컨테이너용 Azure Monitor는 컨테이너화된 에이전트에서 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드에서 stdout, stderr 및 환경 변수를 수집합니다. 이 환경을 제어하기 위해 사용자 지정 Kubernetes ConfigMaps를 만들어 에이전트 데이터 수집 설정을 구성할 수 있습니다. 

이 문서에서는 ConfigMap을 만들고 요구 사항에 따라 데이터 수집을 구성하는 방법을 보여 줍니다.

>[!NOTE]
>Azure Red Hat OpenShift의 경우 템플릿 ConfigMap 파일이 *오픈시프트-azure 로깅* 네임스페이스에 만들어집니다. 
>

## <a name="configmap-file-settings-overview"></a>구성맵 파일 설정 개요

템플릿 ConfigMap 파일은 처음부터 만들 필요없이 사용자 정의와 쉽게 편집 할 수 있습니다 제공됩니다. 시작하기 전에 [ConfigMaps에](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 대한 Kubernetes 설명서를 검토하고 ConfigMaps를 생성, 구성 및 배포하는 방법을 숙지해야 합니다. 이렇게 하면 네임스페이스당 또는 전체 클러스터에서 stderr 및 stdout을 필터링하고 클러스터의 모든 포드/노드에서 실행되는 모든 컨테이너에 대한 환경 변수를 필터링할 수 있습니다.

>[!IMPORTANT]
>컨테이너 워크로드에서 stdout, stderr 및 환경 변수를 수집하기 위해 지원되는 최소 에이전트 버전은 ciprod06142019 이상입니다. 에이전트 버전을 확인하려면 **노드** 탭에서 노드를 선택하고 속성 창에서 에이전트 **이미지 태그** 속성의 메모 값을 선택합니다. 에이전트 버전 및 각 릴리스에 포함된 내용에 대한 자세한 내용은 [에이전트 릴리스 정보를](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)참조하십시오.

### <a name="data-collection-settings"></a>데이터 수집 설정

다음은 데이터 수집을 제어하도록 구성할 수 있는 설정입니다.

|Key |데이터 형식 |값 |설명 |
|----|----------|------|------------|
|`schema-version` |문자열(대/소문자 구분) |v1 |이 ConfigMap을 구문 분석할 때 에이전트에서 사용하는 스키마 버전입니다. 현재 지원되는 스키마 버전은 v1입니다. 이 값을 수정하는 것은 지원되지 않으며 ConfigMap이 평가될 때 거부됩니다.|
|`config-version` |String | | 소스 제어 시스템/리포지토리에서 이 구성 파일의 버전을 추적할 수 있는 기능을 지원합니다. 허용되는 최대 문자는 10이고 다른 모든 문자는 잘립니다. |
|`[log_collection_settings.stdout] enabled =` |부울 | true 또는 false | stdout 컨테이너 로그 수집이 활성화되어 있는지 확인합니다. stdout `true` 로그 모음(아래`log_collection_settings.stdout.exclude_namespaces` 설정)에 대해 네임스페이스가 제외되지 않도록 설정하면 stdout 로그가 클러스터의 모든 포드/노드에서 모든 컨테이너에서 수집됩니다. ConfigMaps에 지정하지 않은 경우 `enabled = true`기본값은 . |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | 쉼표 로 구분된 배열 |stdout 로그가 수집되지 않는 Kubernetes 네임스페이스 배열입니다. 이 설정은 `log_collection_settings.stdout.enabled` `true`로 설정된 경우에만 적용됩니다. ConfigMap에 지정하지 않으면 기본값은 `exclude_namespaces = ["kube-system"]`입니다.|
|`[log_collection_settings.stderr] enabled =` |부울 | true 또는 false |stderr 컨테이너 로그 수집이 활성화되어 있는지 제어합니다. stdout `true` 로그 모음(설정)에`log_collection_settings.stderr.exclude_namespaces` 대해 네임스페이스가 제외되지 않도록 설정하면 stderr 로그가 클러스터의 모든 포드/노드에서 모든 컨테이너에서 수집됩니다. ConfigMaps에 지정하지 않은 경우 `enabled = true`기본값은 . |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |쉼표 로 구분된 배열 |stderr 로그가 수집되지 않는 Kubernetes 네임스페이스 배열입니다. 이 설정은 `log_collection_settings.stdout.enabled` `true`로 설정된 경우에만 적용됩니다. ConfigMap에 지정하지 않으면 기본값은 `exclude_namespaces = ["kube-system"]`입니다. |
| `[log_collection_settings.env_var] enabled =` |부울 | true 또는 false | 이 설정은 클러스터의 모든 포드/노드에서 환경 변수 컬렉션을 `enabled = true` 제어하고 ConfigMaps에 지정되지 않은 경우 기본값을 제어합니다. 환경 변수의 컬렉션을 전역적으로 사용하도록 설정된 경우 Dockerfile 설정이나 **env:** 섹션아래에서 [Pod의 구성 파일](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)을 사용하여 환경 변수 `AZMON_COLLECT_ENV`을 **False**로 설정하여 특정 컨테이너에 대해 사용하지 않도록 설정할 수 있습니다. 환경 변수 의 수집이 전역적으로 비활성화된 경우 특정 컨테이너에 대한 컬렉션을 활성화할 수 없습니다(즉, 컨테이너 수준에서 적용할 수 있는 유일한 재정의는 이미 전역적으로 활성화된 경우 컬렉션을 사용하지 않도록 설정하는 것입니다.) |
| `[log_collection_settings.enrich_container_logs] enabled =` |부울 | true 또는 false | 이 설정은 컨테이너 로그 보강을 제어하여 클러스터의 모든 컨테이너 로그에 대해 ContainerLog 테이블에 기록된 모든 로그 레코드에 대한 이름 및 이미지 속성 값을 채웁니다. ConfigMap에 `enabled = false` 지정되지 않은 경우 기본값입니다. |

ConfigMaps는 전역 목록이며 에이전트에 하나의 ConfigMap만 적용할 수 있습니다. 컬렉션을 덮는 다른 ConfigMaps를 가질 수 없습니다.

## <a name="configure-and-deploy-configmaps"></a>구성 맵 구성 및 배포

다음 단계를 수행하여 ConfigMap 구성 파일을 구성하고 클러스터에 배포합니다.

1. 템플릿 ConfigMap yaml 파일을 [다운로드하고](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) 컨테이너 azm-ms-agentconfig.yaml로 저장합니다. 

   >[!NOTE]
   >ConfigMap 템플릿이 클러스터에 이미 있으므로 Azure Red Hat OpenShift로 작업할 때이 단계는 필요하지 않습니다.

2. ConfigMap yaml 파일을 사용자 지정으로 편집하여 stdout, stderr 및/또는 환경 변수를 수집합니다. Azure Red Hat OpenShift에 대한 ConfigMap yaml 파일을 편집하는 경우 먼저 명령을 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 실행하여 텍스트 편집기에서 파일을 엽니다.

    - stdout 로그 컬렉션에 대한 특정 네임스페이스를 제외하려면 다음 `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`예제를 사용하여 키/값을 구성합니다.
    
    - 특정 컨테이너에 대한 환경 변수 컬렉션을 사용하지 `[log_collection_settings.env_var] enabled = true` 않도록 설정하려면 키/값을 설정하여 변수 컬렉션을 전역적으로 사용하도록 설정한 다음 [여기에](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) 있는 단계를 수행하여 특정 컨테이너에 대한 구성을 완료합니다.
    
    - stderr 로그 수집 클러스터 전체에서 사용하지 않도록 설정하려면 다음 `[log_collection_settings.stderr] enabled = false`예제를 사용하여 키/값을 구성합니다.

3. Azure Red Hat OpenShift 이외의 클러스터의 경우 Azure Red Hat OpenShift `kubectl apply -f <configmap_yaml_file.yaml>` 이외의 클러스터에서 다음과 같은 kubectl 명령을 실행하여 ConfigMap을 만듭니다. 
    
    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Azure Red Hat OpenShift의 경우 변경 내용을 편집기에서 저장합니다.

구성 변경이 적용되기 전에 완료하는 데 몇 분 정도 걸릴 수 있으며 클러스터의 모든 omsagent 포드가 다시 시작됩니다. 다시 시작은 모든 omsagent 포드에 대한 롤링 다시 시작이며 동시에 다시 시작되지는 않습니다. 다시 시작이 완료되면 다음과 유사한 메시지가 표시되며 결과가 포함됩니다. `configmap "container-azm-ms-agentconfig" created`

## <a name="verify-configuration"></a>구성 확인

구성이 Azure Red Hat OpenShift가 아닌 다른 클러스터에 성공적으로 적용되었는지 확인하려면 다음 명령을 `kubectl logs omsagent-fdf58 -n=kube-system`사용하여 에이전트 포드의 로그를 검토합니다. omsagent 포드의 구성 오류가 있는 경우 출력에 다음과 유사한 오류가 표시됩니다.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

구성 변경 적용과 관련된 오류도 검토할 수 있습니다. 구성 변경에 대한 추가 문제 해결을 수행하는 데 다음 옵션을 사용할 수 있습니다.

- 에이전트 포드에서 동일한 `kubectl logs` 명령을 사용하여 로그입니다. 

    >[!NOTE]
    >이 명령은 Azure Red Hat OpenShift 클러스터에 적용되지 않습니다.
    > 

- 라이브 로그에서. 라이브 로그에는 다음과 유사한 오류가 표시됩니다.

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- 로그 분석 작업 영역의 **KubeMonAgentEvents** 테이블에서. 데이터는 구성 오류에 대한 *오류* 심각도와 함께 매시간 전송됩니다. 오류가 없는 경우 테이블의 항목에는 오류 없음을 보고하는 심각도 *Info가*있는 데이터가 있습니다. **Tags** 속성에는 오류가 발생한 포드 및 컨테이너 ID에 대한 자세한 정보와 마지막 시간의 첫 번째 발생, 마지막 발생 및 개수에 대한 자세한 정보가 포함되어 있습니다.

- Azure Red Hat OpenShift를 사용하면 **ContainerLog** 테이블을 검색하여 옴에이전트 로그를 확인하여 openshift-azure-로깅의 로그 수집이 활성화되어 있는지 확인합니다.

Azure Red Hat OpenShift가 아닌 클러스터의 ConfigMap에서 오류를 수정한 후 yaml 파일을 저장하고 명령을 실행하여 `kubectl apply -f <configmap_yaml_file.yaml`업데이트된 ConfigMaps를 적용합니다. Azure Red Hat OpenShift의 경우 명령을 실행하여 업데이트된 ConfigMaps를 편집하고 저장합니다.

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>업데이트된 구성 맵 적용

Azure Red Hat OpenShift 가 아닌 클러스터에 ConfigMap을 이미 배포한 적이 있고 최신 구성으로 업데이트하려는 경우 이전에 사용했던 ConfigMap 파일을 편집한 다음 `kubectl apply -f <configmap_yaml_file.yaml`이전과 동일한 명령을 사용하여 적용할 수 있습니다. Azure Red Hat OpenShift의 경우 명령을 실행하여 업데이트된 ConfigMaps를 편집하고 저장합니다.

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

구성 변경이 적용되기 전에 완료하는 데 몇 분 정도 걸릴 수 있으며 클러스터의 모든 omsagent 포드가 다시 시작됩니다. 다시 시작은 모든 omsagent 포드에 대한 롤링 다시 시작이며 동시에 다시 시작되지는 않습니다. 다시 시작이 완료되면 다음과 유사한 메시지가 표시되며 결과가 포함됩니다. `configmap "container-azm-ms-agentconfig" updated`

## <a name="verifying-schema-version"></a>스키마 버전 확인

지원되는 구성 스키마 버전은 omsagent 포드에서 포드 별표(스키마 버전)로 사용할 수 있습니다. 다음 kubectl 명령으로 볼 수 있습니다.`kubectl describe pod omsagent-fdf58 -n=kube-system`

출력은 추가 스키마 버전과 유사하게 표시됩니다.

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>다음 단계

- 컨테이너용 Azure Monitor에는 미리 정의된 경고 집합이 포함되어 있지 않습니다. [컨테이너에 대한 Azure Monitor를 사용하여 성능 경고 만들기를](container-insights-alerts.md) 검토하여 DevOps 또는 운영 프로세스 및 절차를 지원하기 위해 높은 CPU 및 메모리 사용률에 대한 권장 경고를 만드는 방법을 알아봅니다.

- 모니터링을 사용하여 AKS 또는 하이브리드 클러스터 및 워크로드에서 실행되는 상태 및 리소스 사용률을 수집할 수 있으므로 컨테이너에 Azure [Monitor를 사용하는 방법을](container-insights-analyze.md) 알아봅니다.

- [로그 쿼리 예제를](container-insights-log-search.md#search-logs-to-analyze-data) 보고 미리 정의된 쿼리 및 예제를 보고 클러스터에 대한 경고, 시각화 또는 분석을 위해 평가하거나 사용자 지정합니다.
