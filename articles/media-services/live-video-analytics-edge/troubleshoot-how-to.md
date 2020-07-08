---
title: IoT Edge에서 라이브 비디오 분석 문제 해결-Azure
description: 이 문서에서는 IoT Edge에서 라이브 비디오 분석에 대 한 문제 해결 단계를 다룹니다.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045582"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>IoT Edge의 Live Video Analytics 문제 해결

이 문서에서는 IoT Edge에서 라이브 비디오 분석에 대 한 문제 해결 단계를 다룹니다.

## <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

### <a name="diagnostics"></a>진단

라이브 비디오 분석 배포의 일부로 IoT Hub 및 IoT Edge 장치와 같은 Azure 리소스를 설정 합니다. 문제를 진단 하는 첫 번째 단계는 다음 지침에 따라 edge가 올바르게 설정 되었는지 확인 하는 것입니다.

1. [' Check ' 명령을 실행 합니다.](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [IoT Edge 버전 확인](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [IoT Edge 보안 관리자 및 해당 로그의 상태를 확인 합니다.](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [IoT Edge 허브를 통과 하는 메시지 보기](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [컨테이너 다시 시작](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [방화벽 및 포트 구성 규칙 확인](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>배포 전 문제

Edge 인프라가 제대로 작동 하는 경우 배포 매니페스트 파일에서 문제를 찾을 수 있습니다. 다른 IoT 모듈과 함께에 지 장치의 IoT Edge 모듈에 라이브 비디오 분석을 배포 하기 위해 edge 허브, edge 에이전트 및 기타 모듈을 포함 하는 배포 매니페스트를 해당 속성과 함께 사용 합니다. JSON의 형식이 잘못 된 경우 다음과 같은 오류가 발생할 수 있습니다. 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

' Content ' 인수에 대 한 ' ' 파일에서 json을 구문 분석 하지 못했습니다 <deployment manifest.json> . 예외: "추가 데이터: 줄 101 열 1 (문자 5325)"

이 오류가 발생 하는 경우 JSON 파일에서 파일의 구조에 누락 된 대괄호 또는 다른 문제가 있는지 확인 하는 것이 좋습니다. [JSON 뷰어 플러그 인에서 메모장 + +](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) 와 같은 클라이언트를 사용 하거나와 같은 온라인 도구를 사용 https://jsonformatter.curiousconcept.com/ 하 여 파일 구조의 유효성을 검사할 수 있습니다.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>배포-미디어 그래프 직접 메서드를 사용 하 여 진단 

IoT Edge 모듈에서 라이브 비디오 분석이 Edge 장치에 올바르게 배포 되 면 [직접 메서드](direct-methods.md)를 호출 하 여 미디어 그래프를 만들고 실행할 수 있습니다. 포털을 사용 하 여 직접 메서드를 통해 미디어 그래프의 진단 작업을 실행할 수 있습니다.

1. 포털을 통해에 지 장치에 연결 된 IoT Hub로 이동 합니다.
    1. IoT Hub 블레이드에서 >IoT Edge 자동 장치 관리를 찾습니다.
    1. IoT Edge 클릭 하면에 지 장치 목록이 표시 됩니다. 진단 하려는 장치를 선택 합니다.
         
        ![Edge 장치](./media/troubleshoot-how-to/lva-sample-device.png)
    1. 응답 코드가 200 인지 확인 합니다. [IoT Edge 런타임에](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) 대 한 다양 한 응답 코드는 다음과 같습니다.
        1. 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
        1. 417-장치에 배포 구성 집합이 없습니다.
        1. 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
        1. 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
        1. 500 - IoT Edge 런타임에서 오류가 발생했습니다.
    1. 장치를 클릭 하면 배포 된 예상 IoT Edge 모듈과 해당 상태가 표시 됩니다.
    1. "배포에서 지정" 및 "장치에서 보고" 열이 "예"를 나타내는 경우 IoT Edge 모듈의 라이브 비디오 분석에서 직접 메서드를 호출할 수 있습니다. 모듈을 클릭 하면 원하는 속성 및 보고 된 속성을 확인 하 고 직접 메서드를 호출할 수 있는 화면으로 이동 됩니다. 
        1. 보고 및 desired 속성을 확인 하면 모듈 속성이 배포와 동기화 되었는지 여부를 파악할 수 있습니다. 그렇지 않은 경우 edge를 다시 시작할 수 있습니다. 
        1. [직접 메서드](direct-methods.md) 가이드를 사용 하 여 몇 가지 메서드 (특히 GraphTopologyList와 같은 간단한 메서드)를 호출 합니다. 이 가이드는 또한 예상 요청 및 응답 페이로드 및 오류 코드를 지정 합니다. 간단한 직접 메서드가 성공적으로 완료 되 면 라이브 비디오 분석에 지 모듈이 제대로 작동 하는지 확인할 수 있습니다.
        
        ![직접 메서드](./media/troubleshoot-how-to/direct-method.png) 
1. 상태 501 코드를 가져오는 경우 직접 메서드 이름이 정확한 지 확인 하세요. 메서드 이름과 요청 페이로드가 정확한 경우 성공 코드 = 200과 함께 결과를 다시 가져와야 합니다. 요청 페이로드가 정확 하지 않은 경우에는 상태 = 400 및 직접 메서드 호출을 통해 문제를 진단 하는 데 도움이 되는 오류 코드 및 메시지를 나타내는 응답 페이로드를 얻게 됩니다. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>배포 후-실행 중 문제에 대 한 로그 진단 

Edge 모듈의 컨테이너 로그에는 진단이 있어야 합니다.<!--<todo:add link to diagnostics doc>--> 모듈 런타임 중에 문제를 디버깅 하는 데 도움이 되는 정보입니다. [컨테이너 로그에서 문제를 확인](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) 하 고 자체 진단을 수행할 수 있습니다. 그러나 위의 모든 검사가 수행 되 고 문제가 계속 발생 하는 경우 Azure 팀에서 추가로 분석할 수 있는 [' support 번들 ' 명령을 사용 하 여](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) IoT Edge 장치에서 로그를 수집 합니다. 지원 센터에 [연락](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 하 여 수집 된 로그를 제출할 수 있습니다.

## <a name="common-error-resolutions"></a>일반적인 오류 해결

라이브 비디오 분석은에 지 장치에 IoT Edge 모듈로 배포 되며, IoT Edge 에이전트 및 허브 모듈과 공동으로 작동 합니다. 라이브 비디오 분석 배포에 직면 하는 일반적인 오류 중 일부는 기본 IoT 인프라의 문제로 인해 발생 합니다. 에이전트 및 허브 IoT Edge 몇 가지 일반적인 오류는 다음과 같습니다.

1. [IoT Edge 에이전트는 약 1 분 후에 중지](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute)됩니다.
1. [IoT Edge 에이전트가 모듈의 이미지 (403)에 액세스할 수 없습니다](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [Edge Agent 모듈이 ' 빈 구성 파일 '을 보고 하 고 장치에서 시작 되는 모듈이 없습니다](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [IoT Edge 허브가 시작 되지 않습니다](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IoT Edge 보안 데몬이 잘못 된 호스트 이름으로 실패](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname)합니다.
1. [Live Video Analytics 또는 기타 사용자 지정 IoT Edge 모듈이 404 오류가 발생 하 여 Edge 허브로 메시지를 보내지 못합니다](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [IoT Edge 모듈 배포는 장치에서 성공적으로 사라집니다](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Edge 설정 스크립트 문제

설명서의 일부로 Live Video Analytics Edge를 시작 하기 위해 Edge 및 클라우드 리소스를 배포 하는 [설정 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) 를 제공 했습니다. 이 섹션에서는 스크립트를 사용 하 여 발생할 수 있는 오류와 스크립트를 디버그 하는 방법을 살펴보았습니다.

스크립트는 일부 리소스를 만드는 작업을 부분적으로 실행 하지만 다음 메시지와 함께 실패 합니다.

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
이 문제를 해결하려면

1. 다음 명령을 실행합니다.

    ```
    az --version
    ```
1. 다음 확장을 설치 했는지 확인 합니다. 이 가이드를 작성할 당시 확장의 버전은 다음과 같습니다.

    | 확장명 | 버전 |
    |---|---|
    |azure-cli   |      2.5.1|
    |명령-모듈-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetry| 1.0.4|
    |확장:    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 확장이 위의 릴리스 번호 보다 오래 된 경우 명령을 사용 하 여 확장을 최신 버전으로 업데이트 합니다.

    ```
    az extension update --name <Extension name>
    ```

    예를 들면 `az extension update --name azure-iot`과 같습니다.

### <a name="sample-app-issues"></a>샘플 앱 문제

Microsoft는이 릴리스의 일부로 개발자 커뮤니티를 시작 하기 위한 몇 가지 .NET 샘플 코드를 제공 했습니다. 이 섹션에서는 샘플 코드를 실행 하는 동안 발생할 수 있는 오류를 캡처되고 이러한 오류를 디버깅 하는 방법을 설명 했습니다.

1. 직접 메서드 호출에서 다음 오류가 발생 하 여 Program.cs가 실패 합니다.

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. VS code 환경에 [Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 를 설치 하 고 IoT Hub 설정에 대 한 연결을 설정 했는지 확인 합니다. (Ctrl + shift + P를 누른 다음 선택 IoT Hub 메서드를 선택 하 여 구독에 연결 하 고 IoT Hub)
1. VS Code를 통해 edge 모듈에서 직접 메서드를 호출할 수 있는지 확인 합니다. 예를 들어 다음 페이로드 {"": "1.0"}를 사용 하 여 GraphToplogyList를 호출 하면 @apiVersion 다음 응답이 반환 됩니다. 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 위의 작업이 실패 하는 경우 다음을 시도 합니다.
    1. Edge 장치의 명령 프롬프트로 이동 하 여을 입력 합니다.
    
    ```
    sudo systemctl restart iotedge
    ```

    Edge 장치와 모든 모듈이 다시 시작 됩니다. 몇 분 정도 기다린 후 다음을 실행 하 여 모듈이 실행 중인지 확인 한 다음 DirectMethod를 다시 사용 합니다.

    ```
    sudo iotedge list
    ```
    1. 또한 위의 오류가 발생할 경우 VM 또는 컴퓨터를 다시 부팅 해 보세요.
    1. 모든 작업이 실패 하는 경우 다음을 실행 하 여 관련 된 모든 [로그](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) 를 해당 [지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에 첨부 하는 ZIP 파일을 가져옵니다.

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. 오류 응답 400 코드를 수신 하는 경우 메서드 호출 페이로드가 [직접 메서드](direct-methods.md) 가이드에 따라 올바른 형식 인지 확인 합니다.
1. 상태 200 코드를 수신 하는 경우 허브는 정상적으로 작동 하 고 모듈 배포가 올바르고 응답성을 나타냅니다. 다음 단계는 앱 구성이 정확한 지 확인 하는 것입니다. 앱 구성은 appsettings.js파일의 다음 필드로 구성 됩니다. DeviceId 및 moduleId이 정확한 지 확인 하세요. 이를 확인 하는 쉬운 방법은 VSCode의 Azure IoT Hub 확장 섹션을 통해 확인 하는 것입니다. 파일 및 IoT Hub 섹션 appsettings.js의 값이 일치 해야 합니다.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)

1. 마지막으로 appsettings.js내에서 IoT Hub 장치 연결 문자열이 아닌 IoT Hub 연결 문자열을 제공 했는지 확인 합니다. [formats](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)

### <a name="live-video-analytics-working-with-external-modules"></a>라이브 비디오 분석 외부 모듈 사용

HTTP 확장 프로세서를 통한 라이브 비디오 분석에서는 REST를 사용 하 여 HTTP를 통해 다른 IoT Edge 모듈에서 데이터를 보내고 받도록 미디어 그래프를 확장할 수 있습니다.  특정 한 [예로](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) 미디어 그래프가 비디오 프레임을 Yolo V3 같은 외부 유추 모듈에 이미지로 보내고 JSON 기반 분석 결과를 받을 수 있습니다. 이러한 토폴로지에서 이벤트의 끝 대상은 대부분 IoT Hub입니다. 허브에 대 한 유추 이벤트가 표시 되지 않는 경우 다음을 확인 하세요.

1. 미디어 그래프가 게시 하는 허브가 vs에 게시 되는지 확인 합니다. 검사 하 고 있는 허브가 동일 해야 합니다. 여러 배포를 만들 때 여러 허브를 사용 하 게 되 고 잘못 된 허브에서 이벤트를 잘못 확인 하는 경우가 있습니다.
1. 외부 모듈이 배포 되 고 실행 되는 경우 VSCode를 통해 확인 합니다. 예제 이미지에서 rtspsim과 cv는 lvaEdge 모듈 외부에서 실행 되는 IoT Edge 모듈입니다.

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)
1. 이벤트를 올바른 URL 끝점으로 전송 하 고 있는지 확인 합니다. 외부 AI 컨테이너는 게시 요청에서 데이터를 수신 하 고 반환 하는 URL 및 포트를 노출 합니다. 이 URL은 Http 확장 프로세서의 끝점: url 속성으로 지정 됩니다. 여기에 표시 된 것 처럼 [토폴로지 url](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) 은 추론 url 매개 변수로 설정 됩니다. 매개 변수의 기본값 ( http://yolov3/score) 또는 전달 된 값이 정확한 지 확인 하 고, 해당 값이 말아 넘기기를 사용 하 여 작동 하는지 테스트할 수 있습니다.  
    1. 예를 들어 로컬 컴퓨터에서 실행 되는 yolo v3 컨테이너와 컨테이너의 IP 주소는 172.17.0.3 (docker 검사를 사용 하 여 IP 주소 찾기).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    반환 된 결과:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Http 확장 프로세서를 활용 하는 그래프의 인스턴스를 하나 또는 여러 개 실행 하는 경우 비디오 피드의 초당 프레임 수 (fps)를 관리 하려면 각 Http 확장 프로세서 앞에 프레임 속도 필터를 포함 해야 합니다. 에 지 컴퓨터의 CPU/메모리가 과도 하 게 활용 되는 특정 상황에서는 특정 유추 이벤트가 손실 될 수 있습니다. 이를 해결 하려면 프레임 속도 필터에서 maximumFps 속성의 낮은 값을 설정 합니다. 그래프의 각 인스턴스에서 0.5 ("maximumFps": 0.5)로 설정 하 고 다시 실행 하 여 허브에 대 한 유추 이벤트를 확인할 수 있습니다.
    1. 또는 더 높은 CPU와 메모리를 사용 하 여 더 강력한 edge 컴퓨터를 가져올 수도 있습니다.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>병렬의 여러 메서드-시간 제한 실패 

IoT Edge의 Live Video Analytics에서는 여러 토폴로지와 여러 그래프 인스턴스를 설정 하는 데 사용할 수 있는 직접 방법 기반 프로그래밍 모델을 제공 합니다. 토폴로지 및 그래프 설정의 일부로 edge 모듈에서 여러 개의 직접 메서드 호출을 호출 합니다. 이러한 여러 메서드 호출을 호출 하는 경우, 특히 그래프를 시작 하 고 중지 하는 경우 다음과 같은 몇 가지 시간 초과 오류가 발생할 수 있습니다. 

어셈블리 초기화 메서드 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync에서 예외를 throw 했습니다. IotHubException: IotHubException: (영문): (영문) (영문):<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

병렬 방식으로 직접 메서드를 호출 하지 않는 것이 좋습니다. 즉,  이전 메서드가 완료 된 후에만 하나의 직접 메서드 호출

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>지원 티켓을 제출 하기 위한 로그 수집

자체 단계별 문제 해결 단계를 수행 해도 문제가 해결 되지 않으면 Azure Portal으로 이동 하 여 [지원 티켓을 열어야](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)합니다.

티켓에 추가 해야 하는 관련 로그를 수집 하려면 다음 단계를 진행 합니다. 지원 요청의 **세부 정보** 탭에서 로그 파일을 업로드할 수 있습니다.

### <a name="support-bundle"></a>지원-번들

IoT Edge 장치에서 로그를 수집 해야 하는 경우 가장 쉬운 방법은 명령을 사용 하는 것입니다 `support-bundle` . 이 명령은 다음을 수집 합니다.

- 모듈 로그
- IoT Edge 보안 관리자 및 컨테이너 엔진 로그
- Iotedge check JSON 출력
- 유용한 디버그 정보

#### <a name="use-the-iot-edge-security-manager"></a>IoT Edge 보안 관리자 사용
 
IoT Edge 보안 관리자는 시작 및 프로 비전 장치에서 IoT Edge 시스템을 초기화 하는 등의 작업을 담당 합니다. IoT Edge를 시작 하지 않는 경우 보안 관리자 로그에서 유용한 정보를 제공할 수 있습니다. IoT Edge 보안 관리자에 대 한 자세한 로그를 보려면 다음을 수행 합니다.

1. IoT Edge 장치에서 IoT Edge 디먼 설정을 편집 합니다.

    ```
    sudo systemctl edit iotedge.service
    ```

1. 다음 줄을 업데이트합니다.

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. 다음 명령을 실행 하 여 IoT Edge 보안 디먼을 다시 시작 합니다.

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. 이전에 `support-bundle` 로그를 가져올 기간을 지정 하려면--이후 플래그를 사용 하 여 명령을 실행 합니다. 예를 들어 2 시간이 지난 2 시간 이후 로그를 가져옵니다. 다른 기간에 대 한 로그를 포함 하도록이 플래그의 값을 변경할 수 있습니다.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>LVA 디버그 로그

디버그 로그를 생성 하도록 IoT Edge 모듈에서 LVA를 구성 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴에서 **IoT Edge**를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. 상단 메뉴에서 **모듈 설정** 링크를 클릭 합니다.

  ![모듈 설정 azure portal](media/troubleshoot-how-to/set-modules.png)

5. IoT Edge 모듈 섹션 아래에서 **lvaEdge**를 찾아 클릭 합니다.
1. **컨테이너 만들기 옵션**을 클릭 합니다.
1. 바인딩 섹션에서 다음 명령을 추가 합니다.

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    그러면에 지 장치와 컨테이너 간에 로그 폴더가 바인딩됩니다.

1. **업데이트** 단추를 클릭 합니다.
1. 페이지 맨 아래에 있는 **검토 + 만들기** 단추를 클릭 합니다. 간단한 유효성 검사가 수행 되 고 녹색 배너 아래에 성공적인 유효성 검사 메시지가 게시 됩니다.
1. **만들기** 단추를 클릭합니다.
1. 다음으로 DebugLogsDirectory 매개 변수가 로그가 수집 되는 디렉터리를 가리키도록 **모듈 id** 쌍을 업데이트 합니다.
    1. **모듈** 테이블 아래에서 **lvaEdge** 을 선택 합니다.
    1. **모듈 id** 쌍 링크를 클릭 합니다. 페이지 맨 위에서이를 찾을 수 있습니다. 그러면 편집 가능한 창이 열립니다.
    1. **원하는 키**아래에 다음 키-값 쌍을 추가 합니다.

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. **Save**를 클릭합니다.

1. 이슈를 재현합니다.
1. 포털의 IoT Hub 페이지에서 가상 컴퓨터에 연결 합니다.
1. 폴더로 이동 하 여 `/var/local/mediaservices/logs` 이 폴더의 bin 콘텐츠를 압축 하 고 us와 공유 합니다. (이러한 로그 파일은 자체 진단을 위한 것이 아닙니다. Azure 엔지니어링은 문제를 분석 하기 위한 것입니다.

1. **모듈 id** 쌍의 해당 값을 다시 *null* 로 설정 하 여 로그 수집을 중지할 수 있습니다. **모듈 id** 쌍 페이지로 돌아가서 다음 매개 변수를로 업데이트 합니다.

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>다음 단계

[자습서: 이벤트 기반 비디오를 클라우드에 녹화하고 클라우드에서 재생](event-based-video-recording-tutorial.md)
