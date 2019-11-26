---
title: Get container instance logs & events
description: Azure Container Instances를 사용하여 컨테이너 로그 및 이벤트로 디버그하는 방법 알아보기
ms.topic: article
ms.date: 03/21/2019
ms.custom: mvc
ms.openlocfilehash: 57d35b9423fd8c64e5a58ee4d8055aa3b238ba8c
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481756"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Azure Container Instances에서 컨테이너 로그 및 이벤트 검색

When you have a misbehaving container, start by viewing its logs with [az container logs][az-container-logs], and streaming its standard out and standard error with [az container attach][az-container-attach].

## <a name="view-logs"></a>로그 보기

컨테이너 내에서 애플리케이션 코드에서 로그를 보려면 [az 컨테이너 로그][az-container-logs] 명령을 사용할 수 있습니다.

처리할 잘못된 URL을 입력한 후에 [ACI에서 컨테이너화된 작업 실행](container-instances-restart-policy.md)에서 예제 작업 기반 컨테이너의 로그 출력은 다음과 같습니다.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>출력 스트림 연결

The [az container attach][az-container-attach] command provides diagnostic information during container startup. 컨테이너가 시작되면 로컬 콘솔에 STDOUT 및 STDERR을 스트리밍합니다.

예를 들어 처리할 대규모 텍스트 파일의 잘못된 URL을 입력한 후에 [ACI에서 컨테이너화된 작업 실행](container-instances-restart-policy.md)에서 작업 기반 컨테이너의 로그 출력은 다음과 같습니다.

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>진단 이벤트 가져오기

컨테이너가 성공적으로 배포되지 않을 경우 Azure Container Instances 리소스 공급자가 제공하는 진단 정보를 검토해야 합니다. 컨테이너에 대한 이벤트를 보려면 [az container show][az-container-show] 명령을 실행합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

출력에는 배포 이벤트와 함께 컨테이너의 핵심 속성이 포함되어 있습니다(여기에서 잘려서 표시됨).

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>다음 단계
Azure Container Instances의 [컨테이너 및 배포 문제를 해결](container-instances-troubleshooting.md)하는 방법을 알아봅니다.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
