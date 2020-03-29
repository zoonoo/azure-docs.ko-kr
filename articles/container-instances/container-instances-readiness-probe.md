---
title: 컨테이너 인스턴스에 준비 프로브 설정
description: Azure 컨테이너 인스턴스의 컨테이너가 준비된 경우에만 요청을 수신하도록 프로브를 구성하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935690"
---
# <a name="configure-readiness-probes"></a>준비 상태 프로브 구성

트래픽을 처리하는 컨테이너화된 응용 프로그램의 경우 컨테이너가 들어오는 요청을 처리할 준비가 되었는지 확인할 수 있습니다. Azure 컨테이너 인스턴스는 특정 조건에서 컨테이너에 액세스할 수 없도록 구성을 포함하도록 준비 프로브를 지원합니다. 준비 프로브는 [Kubernetes 준비 프로브처럼](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)실행됩니다. 예를 들어 컨테이너 앱은 시작 하는 동안 큰 데이터 집합을 로드 해야 하 고이 시간 동안 요청을 수신 하지 않으려는 수 있습니다.

이 문서에서는 준비 프로브가 포함된 컨테이너 그룹을 배포하여 프로브가 성공할 때만 컨테이너가 트래픽을 수신하도록 하는 방법을 설명합니다.

Azure 컨테이너 인스턴스는 비정상 컨테이너가 자동으로 다시 시작하도록 구성할 수 있는 [누수 프로브도](container-instances-liveness-probe.md)지원합니다.

> [!NOTE]
> 현재 가상 네트워크에 배포된 컨테이너 그룹에서 준비 프로브를 사용할 수 없습니다.

## <a name="yaml-configuration"></a>YAML 구성

예를 들어 준비 `readiness-probe.yaml` 프로브가 포함된 다음 코드 조각이 있는 파일을 만듭니다. 이 파일은 작은 웹 앱을 실행하는 컨테이너로 구성된 컨테이너 그룹을 정의합니다. 앱은 공용 `mcr.microsoft.com/azuredocs/aci-helloworld` 이미지에서 배포됩니다. 이 컨테이너화된 앱은 Azure CLI 및 기타 빠른 시작을 [사용하여 Azure의 컨테이너 인스턴스 배포에서도](container-instances-quickstart.md) 보여 주며, Azure에서 설명합니다.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>시작 명령

배포에는 컨테이너가 `command` 처음 실행되기 시작할 때 실행되는 시작 명령을 정의하는 속성이 포함됩니다. 이 속성은 문자열의 배열을 허용 합니다. 이 명령은 웹 앱이 실행되지만 컨테이너가 준비되지 않은 시간을 시뮬레이션합니다. 

먼저 셸 세션을 시작하고 웹 `node` 앱을 시작하는 명령을 실행합니다. 또한 240초 동안 절전 명령을 시작하여 `ready` `/tmp` 디렉터리 내에서 호출된 파일을 만듭니다.

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>준비 명령

이 YAML 파일은 `readinessProbe` `exec` 준비 검사 역할을 하는 준비 명령을 지원하는 을 정의합니다. 이 예제 준비 명령은 디렉터리에서 파일의 존재에 `ready` 대 한 `/tmp` 테스트합니다.

파일이 `ready` 없으면 준비 명령은 0이 아닌 값으로 종료됩니다. 컨테이너가 계속 실행되지만 액세스할 수 없습니다. 종료 코드 0으로 명령이 성공적으로 종료되면 컨테이너에 액세스할 준비가 된 것입니다. 

속성은 `periodSeconds` 준비 명령을 5초마다 실행해야 한다고 지정합니다. 준비 프로브는 컨테이너 그룹의 수명 동안 실행됩니다.

## <a name="example-deployment"></a>예제 배포

다음 명령을 실행하여 앞의 YAML 구성을 사용하여 컨테이너 그룹을 배포합니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>준비 상태 확인 보기

이 예제에서는 처음 240초 동안 파일의 존재를 검사할 `ready` 때 준비 명령이 실패합니다. 반환된 상태 코드는 컨테이너가 준비되지 않았다는 신호를 보신다.

이러한 이벤트는 Azure Portal 또는 Azure CLI에서 볼 수 있습니다. 예를 들어 포털은 준비 `Unhealthy` 명령이 실패할 때 형식 이벤트가 트리거된다는 것을 보여줍니다. 

![포털 비정상 이벤트][portal-unhealthy]

## <a name="verify-container-readiness"></a>컨테이너 준비 성 확인

컨테이너를 시작한 후 처음에는 액세스할 수 없는지 확인할 수 있습니다. 프로비저닝 후 컨테이너 그룹의 IP 주소를 가져옵니다.

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

준비 검색이 실패하는 동안 사이트에 액세스해 보십시오.

```bash
wget <ipAddress>
```

출력은 사이트에 처음에 액세스할 수 없음을 보여 주며,
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240초 후 준비 명령이 성공하여 컨테이너가 준비되었음을 알리는 신호가 표시됩니다. 이제 `wget` 명령을 실행하면 다음이 성공합니다.

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

컨테이너가 준비되면 웹 브라우저를 사용하여 IP 주소로 이동하여 웹 앱에 액세스할 수도 있습니다.

> [!NOTE]
> 준비 프로브는 컨테이너 그룹의 수명 동안 계속 실행됩니다. 나중에 준비 명령이 실패하면 컨테이너에 다시 액세스할 수 없게 됩니다. 
> 

## <a name="next-steps"></a>다음 단계

준비 프로브는 종속 컨테이너로 구성된 다중 컨테이너 그룹과 관련된 시나리오에서 유용할 수 있습니다. 다중 컨테이너 시나리오에 대한 자세한 내용은 [Azure 컨테이너 인스턴스의 컨테이너 그룹을](container-instances-container-groups.md)참조하십시오.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
