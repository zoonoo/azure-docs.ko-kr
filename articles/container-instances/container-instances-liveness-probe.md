---
title: 컨테이너 인스턴스에 활기 프로브 설정
description: Azure Container Instances의 비정상 컨테이너를 다시 시작하도록 활동성 프로브를 구성하는 방법을 알아봅니다
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934170"
---
# <a name="configure-liveness-probes"></a>활동성 프로브 구성

컨테이너화된 응용 프로그램은 장기간 실행될 수 있으므로 컨테이너를 다시 시작하여 복구해야 하는 상태가 손상될 수 있습니다. Azure 컨테이너 인스턴스는 라이브 프로브를 지원하므로 중요한 기능이 작동하지 않는 경우 컨테이너 그룹 내에서 컨테이너를 다시 시작하도록 구성할 수 있습니다. 생생도 프로브는 [Kubernetes 생생도 프로브처럼 행동합니다.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

이 문서에서는 활동성 프로브가 포함된 컨테이너 그룹을 배포하는 방법을 설명하고, 시뮬레이션된 비정상 컨테이너를 다시 시작하는 방법을 보여줍니다.

또한 Azure 컨테이너 [인스턴스는 준비 프로브를](container-instances-readiness-probe.md)지원하며, 이 프로브는 트래픽이 준비된 경우에만 컨테이너에 도달하도록 구성할 수 있습니다.

> [!NOTE]
> 현재 가상 네트워크에 배포된 컨테이너 그룹에서는 라이브 프로브를 사용할 수 없습니다.

## <a name="yaml-deployment"></a>YAML 배포

다음 코드 조각이 포함된 `liveness-probe.yaml` 파일을 만듭니다. 이 파일은 결국 비정상 상태가 되는 NGNIX 컨테이너를 구성하는 컨테이너 그룹을 정의합니다.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

다음 명령을 실행하여 위의 YAML 구성으로 이 컨테이너 그룹을 배포합니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>시작 명령

배포에는 컨테이너가 `command` 처음 실행되기 시작할 때 실행되는 시작 명령을 정의하는 속성이 포함됩니다. 이 속성은 문자열의 배열을 허용 합니다. 이 명령은 비정상 상태로 들어가는 컨테이너를 시뮬레이션합니다.

먼저 bash 세션을 시작하고 `healthy` `/tmp` 디렉터리 내에서 호출된 파일을 만듭니다. 그런 다음 파일을 삭제하기 전에 30 초 동안 자든 다음 10 분 절전 모드로 들어갑니다.

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>활동성 명령

이 배포는 `livenessProbe` 라이브 확인 `exec` 역할을 하는 라이브 명령이 지원되는 을 정의합니다. 이 명령이 0이 아닌 값으로 종료되면 컨테이너가 종료되고 다시 `healthy` 시작되어 파일을 찾을 수 없습니다. 이 명령이 종료 코드 0에서 성공적으로 종료되면 아무 작업도 수행되지 않습니다.

`periodSeconds` 속성은 활동성 명령을 5초마다 실행해야 한다고 지정합니다.

## <a name="verify-liveness-output"></a>활동성 출력 확인

처음 30초 안에는 시작 명령에서 만든 `healthy` 파일이 존재합니다. liveness 명령이 `healthy` 파일의 존재를 확인하면 상태 코드가 0을 반환하여 성공을 알리므로 다시 시작하지 않습니다.

30초 후에 `cat /tmp/healthy` 명령이 실패하기 시작하여 비정상 및 죽이는 이벤트가 발생합니다.

이러한 이벤트는 Azure Portal 또는 Azure CLI에서 볼 수 있습니다.

![포털 비정상 이벤트][portal-unhealthy]

Azure 포털에서 이벤트를 확인 하면 형식의 `Unhealthy` 이벤트는 라이브 명령 실패 시 트리거됩니다. 후속 이벤트는 다시 `Killing`시작을 시작할 수 있도록 컨테이너 삭제를 나타내는 형식입니다. 이 이벤트가 발생할 때마다 컨테이너 증분에 대한 다시 시작 카운트입니다.

다시 시작은 공용 IP 주소 및 노드별 콘텐츠와 같은 리소스가 보존되도록 현재 완료됩니다.

![포털 다시 시작 카운터][portal-restart]

수동 프로브가 계속 실패하고 너무 많은 재시작을 트리거하면 컨테이너가 지수 백오프 지연에 들어갑니다.

## <a name="liveness-probes-and-restart-policies"></a>활동성 프로브 및 다시 시작 정책

다시 시작 정책은 활동성 프로브에 의해 트리거되는 다시 시작 동작보다 우선합니다. 예를 들어 a `restartPolicy = Never` *및* 수동 프로브를 설정하면 실패한 수체 확인으로 인해 컨테이너 그룹이 다시 시작되지 않습니다. 대신 컨테이너 그룹은 컨테이너 그룹의 다시 시작 정책을 `Never`준수합니다.

## <a name="next-steps"></a>다음 단계

필수 구성 요소 함수가 제대로 작동하지 않는 경우 자동으로 다시 시작되도록 설정하려면 작업 기반 시나리오는 활동성 프로브가 필요할 수 있습니다. 작업 기반 컨테이너 실행에 대한 자세한 내용은 [Azure Container Instances에서 컨테이너화된 작업 실행](container-instances-restart-policy.md)을 참조하세요.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
