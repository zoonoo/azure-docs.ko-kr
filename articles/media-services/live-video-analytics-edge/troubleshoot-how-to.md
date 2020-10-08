---
title: IoT Edge에서 라이브 비디오 분석 문제 해결-Azure
description: 이 문서에서는 IoT Edge에서 라이브 비디오 분석에 대 한 문제 해결 단계를 다룹니다.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823194"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>IoT Edge의 Live Video Analytics 문제 해결

이 문서에서는 Azure IoT Edge의 LVA (Live Video Analytics)에 대 한 문제 해결 단계를 다룹니다.

## <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

### <a name="diagnostics"></a>진단

라이브 비디오 분석 배포의 일부로 IoT Hub 및 IoT Edge 장치와 같은 Azure 리소스를 설정 합니다. 문제를 진단 하는 첫 번째 단계로, 다음 지침에 따라 Edge 장치가 올바르게 설정 되었는지 확인 합니다.

1. [ `check` 명령을 실행](../../iot-edge/troubleshoot.md#run-the-check-command)합니다.
1. [IoT Edge 버전을 확인](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)합니다.
1. [IoT Edge 보안 관리자 및 해당 로그의 상태를 확인](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)합니다.
1. [IoT Edge 허브를 통해 이동 하는 메시지를 봅니다](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [컨테이너를 다시 시작](../../iot-edge/troubleshoot.md#restart-containers)합니다.
1. [방화벽 및 포트 구성 규칙을 확인](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)합니다.

### <a name="pre-deployment-issues"></a>배포 전 문제

Edge 인프라가 충분 한 경우 배포 매니페스트 파일에서 문제를 찾을 수 있습니다. IoT Edge 모듈의 Live Video Analytics를 다른 IoT 모듈과 함께 IoT Edge 장치에 배포 하려면 IoT Edge 허브, IoT Edge 에이전트 및 기타 모듈 및 해당 속성을 포함 하는 배포 매니페스트를 사용 합니다. JSON 코드가 제대로 구성 되지 않은 경우 다음 오류가 표시 될 수 있습니다. 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

' Content ' 인수에 대 한 ' ' 파일에서 JSON을 구문 분석 하지 못했습니다 <deployment manifest.json> . 예외: "추가 데이터: 줄 101 열 1 (문자 5325)"

이 오류가 발생 하면 JSON에서 파일의 구조에 누락 된 대괄호 또는 기타 문제를 확인 하는 것이 좋습니다. 파일 구조의 유효성을 검사 하려면 json [뷰어 플러그 인에서 메모장 + +](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) 와 같은 클라이언트 또는 [Json 포맷터 & 유효성 검사기](https://jsonformatter.curiousconcept.com/)와 같은 온라인 도구를 사용할 수 있습니다.

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>배포 하는 동안: 미디어 그래프 직접 메서드를 사용 하 여 진단 

IoT Edge 모듈에서 라이브 비디오 분석이 IoT Edge 장치에 올바르게 배포 된 후 [직접 메서드](direct-methods.md)를 호출 하 여 미디어 그래프를 만들고 실행할 수 있습니다. Azure Portal를 사용 하 여 직접 메서드를 통해 미디어 그래프의 진단 작업을 실행할 수 있습니다.

1. Azure Portal에서 IoT Edge 장치에 연결 된 IoT hub로 이동 합니다.

1. **자동 장치 관리**를 찾은 다음 **IoT Edge**를 선택 합니다.  

1. 에 지 장치 목록에서 진단 하려는 장치를 선택 합니다.  
         
    ![에 지 장치 목록을 표시 하는 Azure Portal의 스크린샷](./media/troubleshoot-how-to/lva-sample-device.png)

1. 응답 코드가 *200*인지 확인 합니다. [IoT Edge 런타임에](../../iot-edge/iot-edge-runtime.md) 대 한 다른 응답 코드는 다음과 같습니다.
    * 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
    * 417-장치에 배포 구성 집합이 없습니다.
    * 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
    * 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
    * 500 - IoT Edge 런타임에서 오류가 발생했습니다.

1. 상태 501 코드를 가져오는 경우 직접 메서드 이름이 정확한 지 확인 합니다. 메서드 이름과 요청 페이로드가 정확한 경우 성공 코드 = 200과 함께 결과를 가져와야 합니다. 요청 페이로드가 정확 하지 않은 경우에는 상태 = 400 및 직접 메서드 호출을 통해 문제를 진단 하는 데 도움이 되는 오류 코드 및 메시지를 나타내는 응답 페이로드를 얻게 됩니다.
    * 보고 및 desired 속성을 확인 하면 모듈 속성이 배포와 동기화 되었는지 여부를 파악할 수 있습니다. 그렇지 않은 경우 IoT Edge 장치를 다시 시작할 수 있습니다. 
    * [직접 메서드](direct-methods.md) 가이드를 사용 하 여 몇 가지 메서드 (특히 GraphTopologyList와 같은 간단한 메서드)를 호출 합니다. 이 가이드는 또한 예상 요청 및 응답 페이로드 및 오류 코드를 지정 합니다. 간단한 직접 메서드를 성공적으로 실행 한 후에는 Live Video Analytics IoT Edge 모듈이 기능적으로 정상 임을 확신할 수 있습니다.
        
       ![IoT Edge 모듈에 대 한 "직접 메서드" 창의 스크린샷](./media/troubleshoot-how-to/direct-method.png) 

1. **배포에 지정** **되 고 장치 열에서 보고 되** 는가 *예*를 나타내는 경우 IoT Edge 모듈의 라이브 비디오 분석에서 직접 메서드를 호출할 수 있습니다. 원하는 속성 및 보고 된 속성을 확인 하 고 직접 메서드를 호출할 수 있는 페이지로 이동 하려면 모듈을 선택 합니다. 고려할 사항은 다음과 같습니다. 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>배포 후: 실행 중 문제에 대 한 로그 진단 

모듈 런타임 중에 문제를 디버그 하는 데 도움이 되는 진단 정보는 IoT Edge 모듈의 컨테이너 로그에 포함 되어 있어야 합니다. [컨테이너 로그에서 문제를 확인](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) 하 고 문제를 스스로 진단할 수 있습니다. 

위의 모든 검사를 실행 하 여 문제가 계속 발생 하는 경우 Azure 팀에서 추가 분석을 위해 [ `support bundle` 명령을 사용 하](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) 여 IoT Edge 장치에서 로그를 수집 합니다. 지원 [서비스에 문의](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 하 고 수집 된 로그를 제출할 수 있습니다.

## <a name="common-error-resolutions"></a>일반적인 오류 해결

라이브 비디오 분석은 IoT Edge 장치에 IoT Edge 모듈로 배포 되며, IoT Edge 에이전트 및 허브 모듈과 긴밀 하 게 연동 됩니다. 라이브 비디오 분석 배포에서 발생 하는 일반적인 오류 중 일부는 기본 IoT 인프라 문제로 인해 발생 합니다. 오류는 다음과 같습니다.

* [IoT Edge 에이전트는 약 1 분 후에 중지](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute)됩니다.
* [IoT Edge 에이전트가 모듈의 이미지 (403)에 액세스할 수 없습니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [IoT Edge agent 모듈은 "빈 구성 파일"을 보고 하 고 장치에서 시작 하는 모듈은 없습니다](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [IoT Edge 허브가 시작 되지 않습니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [IoT Edge 보안 데몬이 잘못 된 호스트 이름으로 인해 실패](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname)합니다.
* [Live Video Analytics 또는 기타 사용자 지정 IoT Edge 모듈이 404 오류가 발생 하 여 Edge 허브로 메시지를 보내지 못합니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [IoT Edge 모듈이 성공적으로 배포 된 후 장치에서 사라집니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>Edge 설치 스크립트 문제

설명서의 일부로 edge 및 클라우드 리소스를 배포 하 고 라이브 비디오 분석에 지를 시작 하는 데 사용할 수 있는 [설치 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) 를 제공 했습니다. 이 섹션에서는 디버깅을 위한 솔루션과 함께 발생할 수 있는 몇 가지 스크립트 오류를 제공 합니다.

문제: 스크립트가 실행 되 고 일부 리소스는 생성 되지 않지만 다음 메시지와 함께 실패 합니다.

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
1. 다음 확장을 설치 했는지 확인 합니다. 이 문서의 게시를 기준으로 확장 및 해당 버전은 다음과 같습니다.

    | 내선 번호 | 버전 |
    |---|---|
    |azure-cli   |      2.5.1|
    |명령-모듈-nspkg         |   2.0.3|
    |core  |    2.5.1|
    |nspkg    | 3.0.4|
    |원격 분석| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. 해당 버전이 여기에 나열 된 릴리스 번호 보다 이전인 확장을 설치한 경우 다음 명령을 사용 하 여 확장을 업데이트 합니다.

    ```
    az extension update --name <Extension name>
    ```

    예를 들어를 실행할 수 있습니다 `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>샘플 앱 문제

Microsoft는이 릴리스의 일부로 개발자 커뮤니티를 시작 하는 데 도움이 되는 몇 가지 .NET 샘플 코드를 제공 했습니다. 이 섹션에서는 샘플 코드를 실행할 때 발생할 수 있는 몇 가지 오류와 이러한 오류를 디버깅 하기 위한 솔루션을 제공 합니다.

문제: 직접 메서드 호출에서 다음 오류가 발생 하 여 Program.cs가 실패 합니다.

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Visual Studio Code 환경에 [Azure Iot 도구가](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 설치 되어 있고 iot hub에 대 한 연결을 설정 했는지 확인 합니다. 이렇게 하려면 Ctrl + Shift + P를 선택한 다음 **IoT Hub 방법 선택**을 선택 합니다.

1. Visual Studio Code를 통해 IoT Edge 모듈에서 직접 메서드를 호출할 수 있는지 여부를 확인 합니다. 예를 들어 다음 페이로드 { &nbsp; " @apiVersion ": "1.0"}를 사용 하 여 GraphTopologyList를 호출 합니다. 다음 응답이 표시 됩니다. 

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

    ![Visual Studio Code 응답의 스크린샷](./media/troubleshoot-how-to/visual-studio-code1.png)
1. 이전 솔루션이 실패 한 경우 다음을 시도 합니다.

    a. IoT Edge 장치의 명령 프롬프트로 이동 하 여 다음 명령을 실행 합니다.
    
      ```
      sudo systemctl restart iotedge
      ```

      이 명령은 IoT Edge 장치와 모든 모듈을 다시 시작 합니다. 몇 분 정도 기다린 후 직접 메서드를 다시 사용 하기 전에 다음 명령을 실행 하 여 모듈이 실행 중인지 확인 합니다.

      ```
      sudo iotedge list
      ```

    b. 위의 방법으로도 오류가 발생 하면 가상 컴퓨터 또는 컴퓨터를 다시 부팅 합니다.

    다. 모든 방법이 실패 하는 경우 다음 명령을 실행 하 여 [관련 로그가](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)모두 포함 된 압축 된 파일을 가져오고 [지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에 연결 합니다.

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. 오류 응답 *400* 코드를 수신 하는 경우 [직접 메서드](direct-methods.md) 가이드에 따라 메서드 호출 페이로드가 제대로 구성 되어 있는지 확인 합니다.
1. 상태 *200* 코드를 수신 하는 경우 허브는 정상적으로 작동 하 고 모듈 배포가 올바르고 응답성을 나타냅니다. 

1. 앱 구성이 정확한 지 확인 합니다. 앱 구성은 *appsettings.js* 파일의 다음 필드로 구성 됩니다. 두 번 확인 하 여 deviceId 및 moduleId이 정확한 지 확인 합니다. 확인 하는 쉬운 방법은 Visual Studio Code의 Azure IoT Hub 확장 섹션으로 이동 하는 것입니다. 파일의 *appsettings.js* 및 IoT Hub 섹션의 값이 일치 해야 합니다.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. *appsettings.js* 파일에서 [연결 문자열 형식이 다르기](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)때문에 IoT Hub 장치 연결 문자열이 *아닌* IoT Hub 연결 문자열을 제공 했는지 확인 합니다.

### <a name="live-video-analytics-working-with-external-modules"></a>라이브 비디오 분석 외부 모듈 사용

HTTP 확장 프로세서를 통한 라이브 비디오 분석에서는 REST를 사용 하 여 HTTP를 통해 다른 IoT Edge 모듈에서 데이터를 보내고 받도록 미디어 그래프를 확장할 수 있습니다. 특정 한 [예로](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)미디어 그래프가 비디오 프레임을 yolo v3과 같은 외부 유추 모듈에 이미지로 보내고 JSON 기반 분석 결과를 받을 수 있습니다. 이러한 토폴로지에서 이벤트의 대상은 대부분 IoT hub입니다. 허브에 대 한 유추 이벤트가 표시 되지 않는 경우 다음을 확인 합니다.

* 미디어 그래프가 게시 하는 허브와 검사 중인 허브가 동일한 지 확인 합니다. 여러 배포를 만들 때 여러 허브를 종료 하 고 잘못 된 허브에서 이벤트를 잘못 확인할 수 있습니다.
* Visual Studio Code에서 외부 모듈이 배포 되 고 실행 중인지 확인 합니다. 예제 이미지에서 rtspsim과 cv는 lvaEdge 모듈 외부에서 실행 되는 모듈 IoT Edge 합니다.

    ![Azure IoT Hub에서 모듈의 실행 상태를 표시 하는 스크린샷](./media/troubleshoot-how-to/iot-hub.png)

* 올바른 URL 끝점에 이벤트를 전송 하 고 있는지 확인 하십시오. 외부 AI 컨테이너는 게시 요청에서 데이터를 수신 하 고 반환 하는 데 사용할 수 있는 URL 및 포트를 제공 합니다. 이 URL은 `endpoint: url` HTTP 확장 프로세서에 대 한 속성으로 지정 됩니다. [토폴로지 url](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)에 표시 된 대로 끝점은 추론 url 매개 변수로 설정 됩니다. 매개 변수의 기본값이 나 전달 된 값이 정확한 지 확인 합니다. 클라이언트 URL (말아)을 사용 하 여 작동 하는지 테스트할 수 있습니다.  

    예를 들어, 172.17.0.3 IP 주소를 사용 하 여 로컬 컴퓨터에서 실행 되는 Yolo v3 컨테이너는 다음과 같습니다. Docker 검사를 사용 하 여 IP 주소를 찾습니다.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    반환 된 결과:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* HTTP 확장 프로세서를 사용 하는 그래프의 인스턴스를 하나 이상 실행 하는 경우 비디오 피드의 초당 프레임 수 (fps) 속도를 관리 하려면 각 HTTP 확장 프로세서 보다 먼저 프레임 속도 필터를 사용 해야 합니다. 

   에 지 컴퓨터의 CPU 또는 메모리가 과도 하 게 활용 되는 특정 상황에서는 특정 유추 이벤트가 손실 될 수 있습니다. 이 문제를 해결 하려면 프레임 속도 필터에서 maximumFps 속성의 낮은 값을 설정 합니다. 그래프의 각 인스턴스에서 0.5 ("maximumFps": 0.5)로 설정한 다음 인스턴스를 다시 실행 하 여 허브에 대 한 유추 이벤트를 확인할 수 있습니다.

   또는 더 높은 CPU와 메모리를 사용 하 여 더 강력한 edge 컴퓨터를 얻을 수 있습니다.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>병렬의 여러 메서드-시간 제한 실패 

IoT Edge의 Live Video Analytics에서는 여러 토폴로지와 여러 그래프 인스턴스를 설정 하는 데 사용할 수 있는 직접 방법 기반 프로그래밍 모델을 제공 합니다. 토폴로지 및 그래프 설정의 일부로 IoT Edge 모듈에서 직접 메서드 호출을 여러 개 호출 합니다. 이러한 여러 메서드 호출을 병렬로 호출 하는 경우, 특히 그래프를 시작 및 중지 하는 경우 다음과 같은 시간 초과 오류가 발생할 수 있습니다. 

어셈블리 초기화 메서드 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync에서 예외를 throw 했습니다. IotHubException: IotHubException: (영문): (영문) (영문):<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

직접 메서드를 병렬로 호출 *하지* 않는 것이 좋습니다. 이러한 메서드를 순차적으로 호출 합니다. 즉, 이전 메서드를 완료 한 후에만 하나의 직접 메서드를 호출 합니다.

### <a name="collect-logs-for-submitting-a-support-ticket"></a>지원 티켓을 제출 하기 위한 로그 수집

자체 단계별 문제 해결 단계에서 문제가 해결 되지 않으면 Azure Portal으로 이동 하 여 [지원 티켓을 엽니다](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> 로그에는 사용자의 IP 주소와 같은 PII (개인적으로 식별이 가능한 정보)가 포함 될 수 있습니다. 로그의 모든 로컬 복사본을 검토 하 고 지원 티켓을 닫으면 즉시 삭제 됩니다.  

티켓에 추가 해야 하는 관련 로그를 수집 하려면 다음 섹션의 지침을 따르세요. 지원 요청의 **세부 정보** 창에서 로그 파일을 업로드할 수 있습니다.

### <a name="use-the-support-bundle-command"></a>지원-번들 명령을 사용 합니다.

IoT Edge 장치에서 로그를 수집 해야 하는 경우 가장 쉬운 방법은 명령을 사용 하는 것입니다 `support-bundle` . 이 명령은 다음을 수집 합니다.

- 모듈 로그
- IoT Edge 보안 관리자 및 컨테이너 엔진 로그
- Iotedge check JSON 출력
- 유용한 디버그 정보

1. 플래그를 포함 하 여 명령을 실행 하 여 로그에 포함할 `support-bundle` 시간을 지정 합니다. *--since* 예를 들어 2h는 지난 2 시간 동안 로그를 가져옵니다. 다른 기간에 대 한 로그를 포함 하도록이 플래그의 값을 변경할 수 있습니다.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   이 명령은 명령을 실행 한 디렉터리에 *support_bundle.zip* 라는 파일을 만듭니다. 
   
1. 지원 티켓에 *support_bundle.zip* 파일을 연결 합니다.

### <a name="live-video-analytics-debug-logs"></a>라이브 비디오 분석 디버그 로그

디버그 로그를 생성 하도록 IoT Edge 모듈에서 Live Video Analytics를 구성 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 IoT hub로 이동 합니다.
1. 왼쪽 창에서 **IoT Edge**을 선택 합니다.
1. 장치 목록에서 대상 장치의 ID를 선택 합니다.
1. 창 위쪽에서 **모듈 설정**을 선택 합니다.

   ![Azure Portal에서 "모듈 설정" 단추의 스크린샷](media/troubleshoot-how-to/set-modules.png)

1. **IoT Edge 모듈** 섹션에서을 찾아 **lvaEdge**를 선택 합니다.
1. **컨테이너 만들기 옵션**을 선택 합니다.
1. **바인딩** 섹션에서 다음 명령을 추가 합니다.

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > 이 명령은 Edge 장치와 컨테이너 간에 로그 폴더를 바인딩합니다. 다른 위치에 있는 로그를 수집 하려면 다음 명령을 사용 하 여 **$LOG _LOCATION_ON_EDGE_DEVICE** 를 사용 하려는 위치로 바꿉니다. `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. **업데이트**를 선택합니다.
1. **검토 + 만들기**를 선택합니다. 성공적인 유효성 검사 메시지는 녹색 배너 아래에 게시 됩니다.
1. **만들기**를 선택합니다.
1. DebugLogsDirectory 매개 변수를 가리키도록 **모듈 id** 쌍을 업데이트 합니다 .이 매개 변수는 로그가 수집 되는 디렉터리를 가리킵니다.

    a. **모듈** 테이블 아래에서 **lvaEdge**을 선택 합니다.  
    b. 창 위쪽에서 **모듈 id**쌍을 선택 합니다. 편집 가능한 창이 열립니다.  
    다. **원하는 키**아래에 다음 키/값 쌍을 추가 합니다.  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > 이 명령은 Edge 장치와 컨테이너 간에 로그 폴더를 바인딩합니다. 장치의 다른 위치에 있는 로그를 수집 하려면:
    > 1. **바인딩 섹션에서** 디버그 로그 위치에 대 한 바인딩을 만들고 **$DEBUG $DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 를 바꾸고 **_LOG_LOCATION** 를 원하는 위치로 바꿉니다.`/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. 다음 명령을 사용 하 여 **$DEBUG _LOG_LOCATION** 을 이전 단계에서 사용한 위치로 바꿉니다.  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. **저장**을 선택합니다.

1. 문제를 재현합니다.
1. 포털의 **IoT Hub** 페이지에서 가상 컴퓨터에 연결 합니다.
1. *Debuglogs* 폴더의 모든 파일을 압축 합니다.

   > [!NOTE]
   > 이러한 로그 파일은 자체 진단을 위한 것이 아닙니다. Azure 엔지니어링 팀은 문제를 분석 하기 위한 것입니다.

   a. 다음 명령에서 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 를 이전에 설정한 EDGE 장치에 있는 디버그 로그의 위치로 바꾸어야 합니다.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. 지원 티켓에 *debugLogs.zip* 파일을 연결 합니다.

1. **모듈 id** 쌍의 값을 *null*로 설정 하 여 로그 수집을 중지할 수 있습니다. **모듈 id** 쌍 페이지로 돌아가서 다음 매개 변수를로 업데이트 합니다.

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>다음 단계

[자습서: 이벤트 기반 비디오를 클라우드에 녹화하고 클라우드에서 재생](event-based-video-recording-tutorial.md)
