---
title: IoT Edge에서 라이브 비디오 분석 문제 해결-Azure
description: 이 문서에서는 IoT Edge에서 라이브 비디오 분석에 대 한 문제 해결 단계를 다룹니다.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d49f048df7a624dc490acf7cb4c8e5f33aa5f1c6
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060234"
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

Edge 인프라가 충분 한 경우 배포 매니페스트 파일에서 문제를 찾을 수 있습니다. IoT Edge 모듈의 Live Video Analytics를 다른 IoT 모듈과 함께 IoT Edge 장치에 배포 하려면 IoT Edge 허브, IoT Edge 에이전트 및 기타 모듈 및 해당 속성을 포함 하는 배포 매니페스트를 사용 합니다. 다음 명령을 사용 하 여 매니페스트 파일을 배포할 수 있습니다.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
JSON 코드가 제대로 구성 되지 않은 경우 다음 오류가 표시 될 수 있습니다.   
&nbsp;&nbsp;&nbsp;' **Content ' 인수에 대 한 ' ' 파일에서 JSON을 구문 분석 하지 못했습니다 <deployment manifest.json> . 예외: "추가 데이터: 줄 101 열 1 (문자 5325)"**

이 오류가 발생 하면 JSON에서 파일의 구조에 누락 된 대괄호 또는 기타 문제를 확인 하는 것이 좋습니다. 파일 구조의 유효성을 검사 하려면 json [뷰어 플러그 인에서 메모장 + +](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) 와 같은 클라이언트 또는 [Json 포맷터 & 유효성 검사기](https://jsonformatter.curiousconcept.com/)와 같은 온라인 도구를 사용할 수 있습니다.

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>배포 하는 동안: 미디어 그래프 직접 메서드를 사용 하 여 진단 

IoT Edge 모듈에서 라이브 비디오 분석이 IoT Edge 장치에 올바르게 배포 된 후 [직접 메서드](direct-methods.md)를 호출 하 여 미디어 그래프를 만들고 실행할 수 있습니다.  
>[!NOTE]
>  직접 메서드 호출은 모듈에만 이루어져야 합니다 **`lvaEdge`** .

직접 메서드를 사용 하 여 미디어 그래프의 진단을 실행 하는 데 Azure Portal를 사용할 수 있습니다.

1. Azure Portal에서 IoT Edge 장치에 연결 된 IoT hub로 이동 합니다.

1. **자동 장치 관리** 를 찾은 다음 **IoT Edge** 를 선택 합니다.  

1. 에 지 장치 목록에서 진단 하려는 장치를 선택 합니다.  
         
    ![에 지 장치 목록을 표시 하는 Azure Portal의 스크린샷](./media/troubleshoot-how-to/lva-sample-device.png)


1. 응답 코드가 *200* 인지 확인 합니다. [IoT Edge 런타임에](../../iot-edge/iot-edge-runtime.md) 대 한 다른 응답 코드는 다음과 같습니다.
    * 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
    * 417-장치에 배포 구성 집합이 없습니다.
    * 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
    * 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
    * 500 - IoT Edge 런타임에서 오류가 발생했습니다.

    > [!TIP]
    > 사용자 환경에서 Azure IoT Edge 모듈을 실행 하는 데 문제가 발생 하는 경우 **[Azure IoT Edge 표준 진단 단계](https://docs.microsoft.com/azure/iot-edge/troubleshoot?view=iotedge-2018-06&preserve-view=true)** 를 문제 해결 및 진단에 대 한 지침으로 사용 합니다.
### <a name="post-deployment-direct-method-error-code"></a>배포 후: 직접 메서드 오류 코드
1. 상태가 표시 되는 경우 `501 code` 직접 메서드 이름이 정확한 지 확인 합니다. 메서드 이름과 요청 페이로드가 정확한 경우 성공 코드 = 200과 함께 결과를 가져와야 합니다. 
1. 요청 페이로드가 정확 하지 않은 경우 `400 code` 직접 메서드 호출을 통해 문제를 진단 하는 데 도움이 되는 오류 코드와 메시지를 나타내는 상태 및 응답 페이로드를 가져옵니다.
    * 보고 및 desired 속성을 확인 하면 모듈 속성이 배포와 동기화 되었는지 여부를 파악할 수 있습니다. 그렇지 않은 경우 IoT Edge 장치를 다시 시작할 수 있습니다. 
    * [직접 메서드](direct-methods.md) 가이드를 사용 하 여 몇 가지 메서드 (특히 GraphTopologyList와 같은 간단한 메서드)를 호출 합니다. 이 가이드는 또한 예상 요청 및 응답 페이로드 및 오류 코드를 지정 합니다. 간단한 직접 메서드를 성공적으로 실행 한 후에는 Live Video Analytics IoT Edge 모듈이 기능적으로 정상 임을 확신할 수 있습니다.
        
       ![IoT Edge 모듈에 대 한 "직접 메서드" 창의 스크린샷](./media/troubleshoot-how-to/direct-method.png) 

1. **배포에 지정** **되 고 장치 열에서 보고 되** 는가 *예* 를 나타내는 경우 IoT Edge 모듈의 라이브 비디오 분석에서 직접 메서드를 호출할 수 있습니다. 원하는 속성 및 보고 된 속성을 확인 하 고 직접 메서드를 호출할 수 있는 페이지로 이동 하려면 모듈을 선택 합니다. 고려할 사항은 다음과 같습니다. 

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

    > [!TIP]
    > 사용자 환경에서 Azure IoT Edge 모듈을 실행 하는 데 문제가 발생 하는 경우 **[Azure IoT Edge 표준 진단 단계](https://docs.microsoft.com/azure/iot-edge/troubleshoot?view=iotedge-2018-06&preserve-view=true)** 를 문제 해결 및 진단에 대 한 지침으로 사용 합니다.
### <a name="live-video-analytics-working-with-external-modules"></a>라이브 비디오 분석 외부 모듈 사용

미디어 그래프 확장 프로세서를 통한 라이브 비디오 분석에서는 HTTP 또는 gRPC 프로토콜을 사용 하 여 다른 IoT Edge 모듈에서 데이터를 보내고 받도록 미디어 그래프를 확장할 수 있습니다. 특정 한 [예로](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension),이 미디어 그래프는 비디오 프레임을 yolo v3과 같은 외부 유추 모듈에 이미지로 보내고 HTTP 프로토콜을 사용 하 여 JSON 기반 분석 결과를 받을 수 있습니다. 이러한 토폴로지에서 이벤트의 대상은 대부분 IoT hub입니다. 허브에 대 한 유추 이벤트가 표시 되지 않는 경우 다음을 확인 합니다.

* 미디어 그래프가 게시 하는 허브와 검사 중인 허브가 동일한 지 확인 합니다. 여러 배포를 만들 때 여러 허브를 종료 하 고 잘못 된 허브에서 이벤트를 잘못 확인할 수 있습니다.
* Azure Portal에서 외부 모듈이 배포 되 고 실행 중인지 확인 합니다. 예제 이미지에서 rtspsim, yolov3, tinyyolov3 및 logAnalyticsAgent는 lvaEdge 모듈 외부에서 실행 되는 IoT Edge 모듈입니다.

    [![Azure IoT Hub ](./media/troubleshoot-how-to/iot-hub-azure.png) 에서 모듈의 실행 상태를 표시 하는 스크린샷](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* 올바른 URL 끝점에 이벤트를 전송 하 고 있는지 확인 하십시오. 외부 AI 컨테이너는 게시 요청에서 데이터를 수신 하 고 반환 하는 데 사용할 수 있는 URL 및 포트를 제공 합니다. 이 URL은 `endpoint: url` HTTP 확장 프로세서에 대 한 속성으로 지정 됩니다. [토폴로지 url](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json)에 표시 된 대로 끝점은 추론 url 매개 변수로 설정 됩니다. 매개 변수의 기본값이 나 전달 된 값이 정확한 지 확인 합니다. 클라이언트 URL (말아)을 사용 하 여 작동 하는지 테스트할 수 있습니다.  

    예를 들어, 172.17.0.3 IP 주소를 사용 하 여 로컬 컴퓨터에서 실행 되는 Yolo v3 컨테이너는 다음과 같습니다.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    반환 된 결과:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > **[Docker 검사 명령을](https://docs.docker.com/engine/reference/commandline/inspect/)** 사용 하 여 컴퓨터의 IP 주소를 찾습니다.
    
* 미디어 그래프 확장 프로세서를 사용 하는 그래프 인스턴스를 하나 이상 실행 하는 경우 필드를 사용 `samplingOptions` 하 여 비디오 피드의 초당 프레임 수 (fps) 속도를 관리 해야 합니다. 

   * 에 지 컴퓨터의 CPU 또는 메모리가 과도 하 게 활용 되는 특정 상황에서는 특정 유추 이벤트가 손실 될 수 있습니다. 이 문제를 해결 하려면 필드의 속성에 대해 낮은 값을 설정 합니다 `maximumSamplesPerSecond` `samplingOptions` . 그래프의 각 인스턴스에서 0.5 ("maximumSamplesPerSecond": "0.5")로 설정 하 고 인스턴스를 다시 실행 하 여 허브에 대 한 유추 이벤트를 확인할 수 있습니다.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>병렬의 여러 메서드-시간 제한 실패 

IoT Edge의 Live Video Analytics에서는 여러 토폴로지와 여러 그래프 인스턴스를 설정 하는 데 사용할 수 있는 직접 방법 기반 프로그래밍 모델을 제공 합니다. 토폴로지 및 그래프 설정의 일부로 IoT Edge 모듈에서 직접 메서드 호출을 여러 개 호출 합니다. 이러한 여러 메서드 호출을 병렬로 호출 하는 경우, 특히 그래프를 시작 및 중지 하는 경우 다음과 같은 시간 초과 오류가 발생할 수 있습니다. 

어셈블리 초기화 메서드 Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync에서 예외를 throw 했습니다. IotHubException: IotHubException: (영문): (영문) (영문):<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

직접 메서드를 병렬로 호출 *하지* 않는 것이 좋습니다. 이러한 메서드를 순차적으로 호출 합니다. 즉, 이전 메서드를 완료 한 후에만 하나의 직접 메서드를 호출 합니다.

### <a name="collect-logs-for-submitting-a-support-ticket"></a>지원 티켓을 제출 하기 위한 로그 수집

자체 단계별 문제 해결 단계에서 문제가 해결 되지 않으면 Azure Portal으로 이동 하 여 [지원 티켓을 엽니다](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> 로그에는 사용자의 IP 주소와 같은 PII (개인적으로 식별이 가능한 정보)가 포함 될 수 있습니다. 로그의 모든 로컬 복사본을 검토 하 고 지원 티켓을 닫으면 즉시 삭제 됩니다.  

티켓에 추가 해야 하는 관련 로그를 수집 하려면 아래 지침에 따라 지원 요청의 **세부 정보** 창에 로그 파일을 순서 대로 수집 하 고 업로드 합니다.  
1. [자세한 로그를 수집 하도록 라이브 비디오 분석 모듈 구성](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [디버그 로그 설정](#live-video-analytics-debug-logs)
1. 문제 재현
1. 포털의 **IoT Hub** 페이지에서 가상 컴퓨터에 연결 합니다.
    1. *Debuglogs* 폴더의 모든 파일을 압축 합니다.

       > [!NOTE]
       > 이러한 로그 파일은 자체 진단을 위한 것이 아닙니다. Azure 엔지니어링 팀은 문제를 분석 하기 위한 것입니다.

       * 다음 명령에서 **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 를 **2 단계** 에서 설정한 EDGE 장치에 있는 디버그 로그의 위치로 바꾸어야 합니다.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. 지원 티켓에 *debugLogs.zip* 파일을 연결 합니다.
1. [Support 번들 명령을](#use-the-support-bundle-command)실행 하 고 로그를 수집 하 여 지원 티켓에 연결 합니다.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>자세한 로그를 수집 하도록 Live Video Analytics 모듈 구성
다음과 같이 및를 설정 하 여 자세한 로그를 수집 하도록 라이브 비디오 분석 모듈을 구성 합니다 `logLevel` `logCategories` .
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

다음 중 하나에서이 작업을 수행할 수 있습니다.
* **Azure Portal** 에서 라이브 비디오 분석 모듈 모듈 Id 쌍 속성의 모듈 id 쌍 속성을 업데이트 [ ![ 합니다. ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* 또는 **배포 매니페스트** 파일에서 Live Video Analytics 모듈의 속성 노드에 이러한 항목을 추가할 수 있습니다.

### <a name="use-the-support-bundle-command"></a>지원-번들 명령을 사용 합니다.

IoT Edge 장치에서 로그를 수집 해야 하는 경우 가장 쉬운 방법은 명령을 사용 하는 것입니다 `support-bundle` . 이 명령은 다음을 수집 합니다.

- 모듈 로그
- IoT Edge 보안 관리자 및 컨테이너 엔진 로그
- JSON 출력 IoT Edge 확인
- 유용한 디버그 정보

1. 플래그를 포함 하 여 명령을 실행 하 여 로그에 포함할 `support-bundle` 시간을 지정 합니다.  예를 들어 2h는 지난 2 시간 동안 로그를 가져옵니다. 다른 기간에 대 한 로그를 포함 하도록이 플래그의 값을 변경할 수 있습니다.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   이 명령은 명령을 실행 한 디렉터리에 *support_bundle.zip* 라는 파일을 만듭니다. 
   
1. 지원 티켓에 *support_bundle.zip* 파일을 연결 합니다.

### <a name="live-video-analytics-debug-logs"></a>라이브 비디오 분석 디버그 로그

디버그 로그를 생성 하도록 IoT Edge 모듈에서 Live Video Analytics를 구성 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 IoT hub로 이동 합니다.
1. 왼쪽 창에서 **IoT Edge** 을 선택 합니다.
1. 장치 목록에서 대상 장치의 ID를 선택 합니다.
1. 창 위쪽에서 **모듈 설정** 을 선택 합니다.

   ![Azure Portal에서 "모듈 설정" 단추의 스크린샷](media/troubleshoot-how-to/set-modules.png)

1. **IoT Edge 모듈** 섹션에서을 찾아 **lvaEdge** 를 선택 합니다.
1. **컨테이너 만들기 옵션** 을 선택 합니다.
1. **바인딩** 섹션에서 다음 명령을 추가 합니다.

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > 이 명령은 Edge 장치와 컨테이너 간에 로그 폴더를 바인딩합니다. 다른 위치에 있는 로그를 수집 하려면 다음 명령을 사용 하 여 **$LOG _LOCATION_ON_EDGE_DEVICE** 를 사용 하려는 위치로 바꿉니다. `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. **업데이트** 를 선택합니다.
1. **검토 + 만들기** 를 선택합니다. 성공적인 유효성 검사 메시지는 녹색 배너 아래에 게시 됩니다.
1. **만들기** 를 선택합니다.
1. DebugLogsDirectory 매개 변수를 가리키도록 **모듈 id** 쌍을 업데이트 합니다 .이 매개 변수는 로그가 수집 되는 디렉터리를 가리킵니다.

    a. **모듈** 테이블 아래에서 **lvaEdge** 을 선택 합니다.  
    b. 창 위쪽에서 **모듈 id** 쌍을 선택 합니다. 편집 가능한 창이 열립니다.  
    다. **원하는 키** 아래에 다음 키/값 쌍을 추가 합니다.  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > 이 명령은 Edge 장치와 컨테이너 간에 로그 폴더를 바인딩합니다. 장치의 다른 위치에 있는 로그를 수집 하려면:
    > 1. **바인딩 섹션에서** 디버그 로그 위치에 대 한 바인딩을 만들고 **$DEBUG $DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** 를 바꾸고 **_LOG_LOCATION** 를 원하는 위치로 바꿉니다.`/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. 다음 명령을 사용 하 여 **$DEBUG _LOG_LOCATION** 을 이전 단계에서 사용한 위치로 바꿉니다.  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. **저장** 을 선택합니다.


1. **모듈 id** 쌍의 값을 *null* 로 설정 하 여 로그 수집을 중지할 수 있습니다. **모듈 id** 쌍 페이지로 돌아가서 다음 매개 변수를로 업데이트 합니다.

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>로깅에 대 한 모범 사례

[모니터링 및 로깅은](monitoring-logging.md) 분류를 이해 하 고 lva 문제를 디버깅 하는 데 도움이 되는 로그를 생성 하는 데 도움이 됩니다. 

GRPC 서버 구현은 언어에 따라 다르지만 서버 내에서 로깅을 추가 하는 표준 방법은 없습니다.  

예를 들어 .NET core를 사용 하 여 gRPC 서버를 빌드하는 경우 grpc 서비스는 **grpc** 범주에 로그를 추가 합니다. GRPC의 자세한 로그를 사용 하도록 설정 하려면 로깅의 LogLevel 하위 섹션에 다음 항목을 추가 하 여 파일에 대 한 appsettings.js에서 Grpc 접두사를 디버그 수준으로 구성 합니다. 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

ConfigureLogging를 사용 하 여 Startup.cs 파일에서이를 구성할 수도 있습니다. 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[.Net의 gRPC에서 로깅 및 진단은](https://docs.microsoft.com/aspnet/core/grpc/diagnostics?view=aspnetcore-3.1&preserve-view=true) grpc 서버에서 일부 진단 로그를 수집 하기 위한 몇 가지 지침을 제공 합니다. 

### <a name="a-failed-grpc-connection"></a>실패 한 gRPC 연결 

그래프가 활성 상태이 고 카메라에서 스트리밍하는 경우 라이브 비디오 분석에서 연결이 유지 됩니다. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>이러한 리소스가 병목 상태가 될 때 CPU 및 GPU 리소스의 부하를 모니터링 하 고 분산

Live Video Analytics는 하드웨어 리소스 모니터링을 모니터링 하거나 제공 하지 않습니다. 개발자는 하드웨어 제조업체의 모니터링 솔루션을 사용 해야 합니다. 그러나 Kubernetes 컨테이너를 사용 하는 경우 [Kubernetes 대시보드](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)를 사용 하 여 장치를 모니터링할 수 있습니다. 

또한 .NET core 문서의 gRPC는 [성능 모범 사례](https://docs.microsoft.com/aspnet/core/grpc/performance?view=aspnetcore-3.1&preserve-view=true) 및 [부하 분산](https://docs.microsoft.com/aspnet/core/grpc/performance?view=aspnetcore-3.1#load-balancing&preserve-view=true)에 대 한 몇 가지 중요 한 정보를 공유 합니다.  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>유추 서버에서 프레임을 받지 않고 수신 하 고 있는 경우 "알 수 없는" 프로토콜 오류가 발생 하면 문제 해결 

문제에 대 한 자세한 정보를 얻기 위해 수행할 수 있는 몇 가지 작업이 있습니다.  

* 라이브 비디오 분석 모듈의 desired 속성에 "**ediaPipeline** log 범주를 포함 하 고 로그 수준이로 설정 되었는지 확인 `Information` 합니다.  
* 네트워크 연결을 테스트 하기 위해 edge 장치에서 다음 명령을 실행할 수 있습니다. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   명령이 jumbled text의 짧은 문자열을 출력 하는 경우 텔넷은 유추 서버에 대 한 연결을 열고 이진 gRPC 채널을 열 수 있습니다. 이 메시지가 표시 되지 않으면 텔넷에서 네트워크 오류를 보고 합니다. 
* 유추 서버에서 gRPC 라이브러리의 추가 로깅을 사용 하도록 설정할 수 있습니다. 이는 gRPC 채널 자체에 대 한 추가 정보를 제공할 수 있습니다. 언어에 따라 달라 지 며, 다음은 [c #](https://docs.microsoft.com/aspnet/core/grpc/diagnostics?view=aspnetcore-3.1&preserve-view=true)에 대 한 지침입니다. 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>첫 번째 버퍼에 대 한 결과를 보내지 않고 gRPC의 버퍼에서 추가 이미지를 선택 합니다.

GRPC 데이터 전송 계약의 일부로 Live Video Analytics에서 gRPC 추론 서버로 보내는 모든 메시지를 승인 해야 합니다. 이미지 프레임 수신을 승인 하지 않으면 데이터 계약이 중단 되 고 원치 않는 상황이 발생할 수 있습니다.  

Live Video Analytics에서 gRPC 서버를 사용 하려면 최상의 성능을 위해 공유 메모리를 사용할 수 있습니다. 이렇게 하려면 프로그래밍 언어/환경에서 노출 하는 Linux 공유 메모리 기능을 사용 해야 합니다. 

1. Linux 공유 메모리 핸들을 엽니다.
1. 프레임이 수신 되 면 공유 메모리 내의 주소 오프셋에 액세스 합니다.
1. 라이브 비디오 분석을 통해 메모리를 회수할 수 있도록 프레임 처리 완료를 승인 합니다.

   > [!NOTE]
   > 오랜 시간 동안 라이브 비디오 분석에 대 한 프레임 수신을 승인 하는 데 지연이 발생 하는 경우 공유 메모리가 가득 차서 데이터가 삭제 될 수 있습니다.
1. 추론 서버에서 각 프레임을 원하는 데이터 구조 (목록, 배열 등)에 저장 합니다.
1. 그런 다음 원하는 수의 이미지 프레임이 있을 때 처리 논리를 실행할 수 있습니다.
1. 준비가 되 면 추론 결과를 라이브 비디오 분석으로 다시 반환 합니다.

## <a name="next-steps"></a>다음 단계

[자습서: 이벤트 기반 비디오를 클라우드에 녹화하고 클라우드에서 재생](event-based-video-recording-tutorial.md)
