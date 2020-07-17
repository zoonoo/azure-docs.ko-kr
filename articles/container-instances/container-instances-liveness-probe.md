---
title: 컨테이너 인스턴스에서 선거의 프로브 설정
description: Azure Container Instances의 비정상 컨테이너를 다시 시작하도록 활동성 프로브를 구성하는 방법을 알아봅니다
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: befe9693be1413abf455d915814c53aab20db53c
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169700"
---
# <a name="configure-liveness-probes"></a>활동성 프로브 구성

컨테이너 화 된 응용 프로그램은 오랜 시간 동안 실행 될 수 있으며,이로 인해 컨테이너를 다시 시작 하 여 복구 해야 하는 상태가 손상 될 수 있습니다. Azure Container Instances는 선거의 프로브를 지원 하므로 중요 한 기능이 작동 하지 않는 경우 컨테이너 그룹 내에서 컨테이너를 다시 시작 하도록 구성할 수 있습니다. 선거의 프로브는 [Kubernetes 선거의 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)처럼 동작 합니다.

이 문서에서는 활동성 프로브가 포함된 컨테이너 그룹을 배포하는 방법을 설명하고, 시뮬레이션된 비정상 컨테이너를 다시 시작하는 방법을 보여줍니다.

또한 Azure Container Instances은 준비가 된 경우에만 트래픽이 컨테이너에 도달할 수 있도록 구성할 수 있는 [준비 프로브](container-instances-readiness-probe.md)를 지원 합니다.

> [!NOTE]
> 현재는 가상 네트워크에 배포 된 컨테이너 그룹에서 선거의 프로브를 사용할 수 없습니다.

## <a name="yaml-deployment"></a>YAML 배포

다음 코드 조각이 포함된 `liveness-probe.yaml` 파일을 만듭니다. 이 파일은 결국 비정상 상태가 되는 NGNIX 컨테이너를 구성하는 컨테이너 그룹을 정의합니다.

```yaml
apiVersion: 2019-12-01
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

배포에는 `command` 컨테이너가 처음 실행 되기 시작할 때 실행 되는 시작 명령을 정의 하는 속성이 포함 되어 있습니다. 이 속성은 문자열 배열을 허용 합니다. 이 명령은 비정상 상태를 입력 하는 컨테이너를 시뮬레이션 합니다.

먼저 bash 세션을 시작 하 고 디렉터리 내에 라는 파일을 만듭니다 `healthy` `/tmp` . 그런 다음 파일을 삭제 하기 전에 30 초 동안 대기한 다음 10 분의 절전 모드로 전환 합니다.

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>활동성 명령

이 배포는 `livenessProbe` `exec` 선거의 check 역할을 하는 선거의 명령을 지 원하는를 정의 합니다. 이 명령이 0이 아닌 값으로 종료 되 면 컨테이너는 종료 되 고 다시 시작 되며,이 경우 파일을 찾을 수 없습니다 `healthy` . 이 명령이 종료 코드 0으로 성공적으로 종료 되 면 아무 동작도 수행 되지 않습니다.

`periodSeconds` 속성은 활동성 명령을 5초마다 실행해야 한다고 지정합니다.

## <a name="verify-liveness-output"></a>활동성 출력 확인

처음 30초 안에는 시작 명령에서 만든 `healthy` 파일이 존재합니다. 선거의 명령이 `healthy` 파일의 존재 여부를 확인할 때 상태 코드는 0을 반환 하 고, 신호를 다시 시작 하지 않습니다.

30 초 후에 `cat /tmp/healthy` 명령이 실패 하 여 비정상 및 종료 이벤트가 발생 합니다.

이러한 이벤트는 Azure Portal 또는 Azure CLI에서 볼 수 있습니다.

![포털 비정상 이벤트][portal-unhealthy]

Azure Portal 이벤트를 보면 형식의 이벤트는 `Unhealthy` 선거의 명령이 실패 한 경우 트리거됩니다. 후속 이벤트는 형식으로 `Killing` , 다시 시작을 시작 하기 위해 컨테이너 삭제를 나타내는 형식입니다. 컨테이너의 다시 시작 횟수는이 이벤트가 발생할 때마다 증가 합니다.

다시 시작은 내부에서 완료 되므로 공용 IP 주소 및 노드 별 내용과 같은 리소스가 유지 됩니다.

![포털 다시 시작 카운터][portal-restart]

선거의 프로브를 지속적으로 실패 하 고 다시 시작을 트리거하는 경우 컨테이너는 지 수 백오프 지연 시간을 입력 합니다.

## <a name="liveness-probes-and-restart-policies"></a>활동성 프로브 및 다시 시작 정책

다시 시작 정책은 활동성 프로브에 의해 트리거되는 다시 시작 동작보다 우선합니다. 예를 들어 `restartPolicy = Never` *및* 선거의 프로브를 설정 하는 경우 실패 한 선거의 검사로 인해 컨테이너 그룹이 다시 시작 되지 않습니다. 컨테이너 그룹은 대신의 컨테이너 그룹의 다시 시작 정책을 준수 합니다 `Never` .

## <a name="next-steps"></a>다음 단계

필수 구성 요소 함수가 제대로 작동하지 않는 경우 자동으로 다시 시작되도록 설정하려면 작업 기반 시나리오는 활동성 프로브가 필요할 수 있습니다. 작업 기반 컨테이너 실행에 대한 자세한 내용은 [Azure Container Instances에서 컨테이너화된 작업 실행](container-instances-restart-policy.md)을 참조하세요.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
