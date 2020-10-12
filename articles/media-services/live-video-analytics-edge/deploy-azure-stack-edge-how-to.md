---
title: Azure Stack Edge에 Live Video Analytics 배포
description: 이 문서에서는 Azure Stack Edge에 라이브 비디오 분석을 배포 하는 데 도움이 되는 단계를 나열 합니다.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b13bb779a5a780b21f2d5d96ed8831ef5c26564d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937714"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Azure Stack Edge에 Live Video Analytics 배포

이 문서에서는 Azure Stack Edge에 라이브 비디오 분석을 배포 하는 데 도움이 되는 단계를 나열 합니다. 장치를 설정 하 고 활성화 한 후에는 라이브 비디오 분석 배포를 수행할 준비가 된 것입니다. 

라이브 비디오 분석의 경우 IoT Hub를 통해 배포 하지만, Azure Stack Edge 리소스는 고객이 라이브 비디오 분석과 인터페이스 될 수 있는 추가 IoT Hub 인식 솔루션을 배포할 수 있도록 하는 Kubernetes API를 노출 합니다. 

> [!TIP]
> 사용자 지정 배포에 Kubernetes (K8s) API를 사용 하는 것은 고급 사례입니다. 고객은 Kubernetes API를 사용 하는 대신 edge 모듈을 만들고 각 Azure Stack에 지 리소스에 IoT Hub를 통해 배포 하는 것이 좋습니다. 이 문서에서는 IoT Hub 사용 하 여 라이브 비디오 분석 모듈을 배포 하는 단계를 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* [소유자 권한이](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)있는 Azure 구독입니다.
* [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) 리소스
   
* [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* Live Video Analytics 모듈의 [서비스 사용자](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/create-custom-azure-resource-manager-role-how-to#create-service-principal) 입니다.

   미국 동부 2, 미국 중부, 미국 중 북부, 일본 동부, 미국 서 부 2, 미국 서 부, 캐나다 동부, 영국 남부, 프랑스 중부, 프랑스 남부, 스위스 북부, 스위스 서부, 일본 서 부 중 하나를 사용 하 여 IoT Hub를 사용할 수 있습니다.
* 스토리지 계정

    범용 v2 (GPv2) 저장소 계정을 사용 하는 것이 좋습니다.  
    범용 [v2 저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade?tabs=azure-portal)에 대해 자세히 알아보세요.
* 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/). [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)이 있는지 확인합니다.
* 개발 컴퓨터가 연결된 네트워크에서 5671 포트를 통해 고급 메시지 큐 프로토콜을 허용하는지 확인합니다. 이렇게 설정하면 Azure IoT Tools에서 Azure IoT Hub와 통신할 수 있습니다.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>라이브 비디오 분석을 사용 하기 위한 Azure Stack Edge 구성

Azure Stack Edge는 네트워크 데이터 전송 기능을 사용 하는 서비스 제공 서비스 솔루션 및 AI 지원에 지 컴퓨팅 장치입니다. [Azure Stack Edge 및 자세한 설치 지침을](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)참조 하세요. 시작 하려면 아래 링크의 지침을 따르세요.

* [Azure Stack Edge/Data Box Gateway 리소스 만들기](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)
* [설치 및 설정](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-install)
* [연결 및 활성화](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>Azure Stack에 지에 IoT Hub 연결

1. [Azure Portal](https://ms.portal.azure.com)에서 Azure Stack Edge 리소스로 이동 하 고 개요를 클릭 합니다. 오른쪽에 있는 컴퓨팅 타일에서 시작을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. Edge 컴퓨팅 구성 타일에서 컴퓨팅 구성을 선택합니다.
1. Edge 컴퓨팅 구성 블레이드에서 다음 정보를 입력합니다.
    
    | 필드|값|
    |---|---|
    |IoT Hub|새로 만들기 또는 기존 항목 중에서 선택합니다.<br/>표준 계층(S1)을 사용하여 IoT 리소스를 만드는 것이 기본입니다. 무료 계층 IoT 리소스를 사용하려면 IoT 리소스를 새로 만든 후 기존 리소스를 선택합니다.<br/>각각의 경우 IoT Hub 리소스는 Azure Stack에 지 리소스를 사용 하는 것과 동일한 구독 및 리소스 그룹을 사용 합니다.|
    |Name|IoT Hub 리소스의 이름을 입력합니다.|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Azure Stack Edge":::
1. **만들기**를 선택합니다. IoT Hub 리소스 만들기는 몇 분 정도 걸립니다. IoT Hub 리소스가 만들어지면 컴퓨팅 구성을 표시하도록 **컴퓨팅 구성** 타일이 업데이트됩니다. Edge 컴퓨팅 역할이 구성되었는지 확인하려면 **컴퓨팅 구성** 타일의 **컴퓨팅 보기**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="Azure Stack Edge":::

    > [!NOTE]
    > IoT Hub이 Azure Stack에 지 리소스와 연결 되기 전에 계산 구성 대화 상자가 닫히면 IoT Hub 생성 되지만 계산 구성에는 표시 되지 않습니다. 몇 분 후에 페이지를 다시 로드 하 고 표시 되는지 확인 합니다.
    
    Edge 컴퓨팅 역할이 Edge 디바이스에 설정되면 두 가지 디바이스, 즉 IoT 디바이스와 IoT Edge 디바이스가 만들어집니다. 이 두 디바이스는 모두 IoT Hub 리소스에서 볼 수 있습니다. IoT Edge 런타임은 IoT Edge 장치 에서도 실행 됩니다. 이 시점에서 IoT Edge 디바이스에는 Linux 플랫폼만 사용할 수 있습니다.
    
    모든 정보가 채워지면 Edge 계산 카드 구성이 다음과 같이 표시 됩니다.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="Azure Stack Edge":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge 로컬 UI에서 계산 전제 조건 사용

계속 하기 전에 다음을 확인 합니다.

* Azure Stack Edge 리소스를 활성화 했습니다.
* Azure Stack Edge 리소스에 액세스 하기 위해 PowerShell 5.0 이상을 실행 하는 Windows 클라이언트 시스템에 액세스할 수 있습니다.
* Kubernetes 클러스터를 배포 하려면 [로컬 웹 UI](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate#connect-to-the-local-web-ui-setup)를 통해 Azure Stack Edge 리소스를 구성 해야 합니다. 
    
    * 계산을 사용 하도록 설정 하려면 장치의 로컬 웹 UI에서 계산 페이지로 이동 합니다.
    
        * 계산에 사용할 네트워크 인터페이스를 선택 합니다. 사용을 선택합니다. 계산을 사용 하도록 설정 하면 장치에서 해당 네트워크 인터페이스의 가상 스위치가 생성 됩니다.
        * Kubernetes 테스트 노드 Ip와 Kubernetes external services Ip를 비워 둡니다.
        * 적용 선택-이 작업에는 약 2 분이 소요 됩니다.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text="Azure Stack Edge":::

        * Kubernetes API 및 Azure Stack Edge 리소스에 대해 DNS가 구성 되지 않은 경우 창의 호스트 파일을 업데이트할 수 있습니다.
        
            * 관리자 권한으로 텍스트 편집기 열기
            * ' '에 C:\Windows\System32\drivers\etc\hosts ' 파일 열기
            * Kubernetes API 장치 이름의 IPv4 및 호스트 이름을 파일에 추가 합니다. 이 정보는 장치 섹션의 Azure Stack Edge 포털에서 찾을 수 있습니다.
            * 저장 후 닫기

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Azure Portal를 사용 하 여 라이브 비디오 분석에 지 모듈 배포

이를 위해 [IoT Hub를 통해 라이브 비디오 분석 배포](deploy-iot-edge-device.md)에서 특정 단계만 수행 하면 됩니다.

1. 사용자 및 그룹 만들기 단계를 건너뛰고 [Live Video Analytics Edge 모듈 배포](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) 로 이동 합니다. 여기에 언급 된 단계를 따르세요.
1. 컨테이너 만들기 옵션에서는 환경 변수를 설정할 필요가 없습니다. 따라서이 단계를 건너뜁니다.
1. 컨테이너 만들기 옵션 탭을 엽니다.

   * 다음 JSON을 복사 하 여 상자에 붙여넣어 모듈에서 생성 되는 로그 파일의 크기를 제한 합니다.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > 공유 메모리 전송과 함께 gRPC 프로토콜을 사용 하는 경우 라이브 비디오 분석과 유추 솔루션 간의 공유 메모리 액세스에 대해 호스트 IPC 모드를 사용 합니다.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > JSON의 "바인드" 섹션에는 2 개의 항목이 있습니다. 에 지 장치 바인딩을 자유롭게 업데이트 하 되 해당 디렉터리가 있는지 확인 합니다.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": 컨테이너에서 영구 응용 프로그램 구성 데이터를 바인딩하고에 지 장치에 저장 하는 데 사용 됩니다.
    * "/var/sermedia:/var/sermedia": edge 장치와 컨테이너 사이에 미디어 폴더를 바인딩합니다. Edge 장치에 비디오 클립을 저장할 수 있도록 지 원하는 미디어 그래프 토폴로지를 실행할 때 비디오 기록을 저장 하는 데 사용 됩니다.
        
1. Doc의 단계를 계속 하 고 모듈 쌍 설정을 입력 합니다.
1. **다음**: 경로를 선택 하 여 경로 섹션으로 이동 합니다. 경로를 지정 하십시오.

    기본 경로를 유지 하 고 다음을 선택 합니다. 검토 + 만들기를 선택 하 여 검토 섹션으로 이동 합니다.
1. [배포 검토 및 확인](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>필드 Docker 볼륨 탑재 설정

작업 디렉터리의 데이터를 보려면 다음 단계를 수행 하 여 배포 전에 Docker 볼륨 탑재를 설정 합니다. 

이 단계에서는 게이트웨이 사용자를 만들고 파일 공유를 설정 하 여 라이브 비디오 분석 작업 디렉터리와 라이브 비디오 분석 미디어 폴더의 콘텐츠를 확인 하는 과정을 다룹니다.

> [!NOTE]
> Bind 탑재는 지원 되지만 볼륨 탑재를 통해 데이터를 볼 수 있으며, 원하는 경우 원격으로 복사할 수 있습니다. 바인딩 및 볼륨 탑재를 모두 사용할 수 있지만 동일한 컨테이너 경로를 가리킬 수 없습니다.

1. Azure Portal를 열고 Azure Stack에 지 리소스로 이동 합니다.
1. 공유에 액세스할 수 있는 **게이트웨이 사용자** 를 만듭니다.
    
    1. 왼쪽 탐색 창에서 **게이트웨이 >사용자**를 클릭 합니다.
    1. **+ 사용자 추가** 를 클릭 하 여 사용자 이름과 암호를 설정 합니다. (권장: `lvauser` ).
    1. **추가**를 클릭 합니다.
    
1. 라이브 비디오 분석 지 속성에 대 한 **로컬 공유** 를 만듭니다.

    1. **게이트웨이 >공유**를 클릭 합니다.
    1. **+ 공유 추가**를 클릭 합니다.
    1. 공유 이름을 설정 합니다. (권장: `lva` ).
    1. 공유 유형을 SMB로 유지 합니다.
    1. **Edge 계산에서 공유 사용** 이 선택 되어 있는지 확인 합니다.
    1. **Edge 로컬 공유로 구성** 이 선택 되어 있는지 확인 합니다.
    1. 사용자 세부 정보에서 최근에 만든 사용자에 게 공유에 대 한 액세스 권한을 부여 합니다.
    1. **만들기**를 클릭 합니다.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Azure Stack Edge":::
    
1. 파일 동기화 저장소에 대 한 원격 공유를 만듭니다.

    1. 먼저 동일한 지역에 blob storage 계정을 만듭니다.
    1. **게이트웨이 >공유**를 클릭 합니다.
    1. **+ 공유 추가**를 클릭 합니다.
    1. 공유 이름을 설정 합니다. (권장: 미디어).
    1. 공유 유형을 SMB로 유지 합니다.
    1. **Edge 계산에서 공유 사용** 이 선택 되어 있는지 확인 합니다.
    1. **Edge 로컬 공유로 구성** 이 선택 되어 있지 않은지 확인 합니다.
    1. 최근에 만든 저장소 계정을 선택 합니다.
    1. 컨테이너 이름을 설정 합니다.
    1. 저장소 유형을 Blob 블록으로 설정 합니다.
    1. 사용자 세부 정보에서 최근에 만든 사용자에 게 공유에 대 한 액세스 권한을 부여 합니다.
    1. **만들기**를 클릭 합니다.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Azure Stack Edge"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>모듈이 실행 중인지 확인 합니다.

마지막 단계는 모듈이 예상대로 연결되어 실행되는지 확인하는 것입니다. IoT Hub 리소스에서 IoT Edge 디바이스에 대한 모듈의 런타임 상태가 실행 중이어야 합니다.

모듈이 실행되는지 확인하려면 다음을 수행합니다.

1. Azure Portal에서 Azure Stack에 지 리소스로 돌아갑니다.
1. 모듈 타일을 선택 합니다. 그러면 모듈 블레이드로 이동됩니다. 모듈 목록에서, 배포한 모듈을 찾습니다. 추가한 모듈의 런타임 상태가 실행 중일 것입니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Azure Stack Edge":::

### <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT Tools 확장 구성

다음 지침에 따라 Azure IoT Tools 확장을 사용하여 IoT 허브에 연결합니다.

1. Visual Studio Code에서 보기 > 탐색기를 선택 합니다. 또는 Ctrl+Shift+E를 선택합니다.
1. 탐색기 탭의 왼쪽 아래 모서리에서 Azure IoT Hub를 선택합니다.
1. 기타 옵션 아이콘을 선택하여 상황에 맞는 메뉴를 표시합니다. 그런 다음, IoT Hub 연결 문자열 설정을 선택합니다.
1. 입력 상자가 표시되면 IoT Hub 연결 문자열을 입력합니다. 

   * 연결 문자열을 가져오려면 Azure Portal에서 IoT Hub로 이동 하 여 왼쪽 탐색 창에서 공유 액세스 정책을 클릭 합니다.
   * Iothubowner을 클릭 하 여 공유 액세스 키를 가져옵니다.
   * 연결 문자열-기본 키를 복사 하 여 VSCode의 입력 상자에 붙여넣습니다.

   연결 문자열은 다음과 같습니다.<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   연결에 성공하면 에지 디바이스의 목록이 표시됩니다. Azure Stack Edge가 표시 되어야 합니다. 이제 상황에 맞는 메뉴를 통해 IoT Edge 디바이스를 관리하고 Azure IoT Hub와 상호 작용할 수 있습니다. 에 지 장치에 배포 된 모듈을 보려면 Azure Stack 장치에서 모듈 노드를 확장 합니다.
    
## <a name="troubleshooting"></a>문제 해결

* Kubectl (Kubernetes API Access).

    * 설명서에 따라 [Kubernetes 클러스터에](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues)대 한 액세스를 위해 컴퓨터를 구성 합니다.
    * 배포 된 모든 IoT Edge 모듈은 `iotedge` 네임 스페이스를 사용 합니다. Kubectl를 사용할 때이를 포함 해야 합니다.
* 모듈 로그

    `iotedge`로그를 가져오기 위해 도구에 액세스할 수 없습니다. [Kubectl 로그](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) 를 사용 하 여 파일에 대 한 로그 또는 파이프를 확인 해야 합니다. 예제: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Pod 및 노드 메트릭

    Pod 및 노드 메트릭을 보려면 [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  을 사용 합니다. 이 기능은 다음 Azure Stack Edge 릴리스에서 제공 됩니다. >v2007)<br/>`kubectl top pods -n iotedge`
* 모듈 검색을 위한 모듈 네트워킹 Azure Stack Edge에서 모듈에는 createOptions의 호스트 포트 바인딩이 있어야 합니다. 그러면 모듈에 주소를 지정할 수 `moduleName:hostport` 있습니다.
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* 볼륨 탑재

    컨테이너가 기존 디렉터리와 비어 있지 않은 디렉터리에 볼륨을 탑재 하려고 하면 모듈이 시작 되지 않습니다.
* 공유 메모리

    호스트 IPC를 사용 하 여 모든 네임 스페이스의 pod에서 Azure Stack Edge 리소스에 대 한 공유 메모리를 지원 합니다.
    IoT Hub를 통해 배포에 대 한 edge 모듈에서 공유 메모리를 구성 합니다.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* 고급 Pod 공동 배치

    K8s를 사용 하 여 gRPC를 통해 라이브 비디오 분석과 통신 하는 사용자 지정 유추 솔루션을 배포 하는 경우 pod가 라이브 비디오 분석 모듈과 동일한 노드에 배포 되었는지 확인 해야 합니다.

    * 옵션 1-공동 배치를 위해 노드 선호도 및 기본 제공 노드 레이블을 사용 합니다.

    사용자가 노드에 레이블을 설정할 수 있는 액세스 권한이 없기 때문에 현재 NodeSelector 사용자 지정 구성은 옵션으로 표시 되지 않습니다. 그러나 고객의 토폴로지 및 명명 규칙에 따라 [기본 제공 노드 레이블을](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)사용할 수 있습니다. Live Video Analytics를 사용 하 여 Azure Stack Edge 리소스를 참조 하는 nodeAffinity 섹션을 유추 pod 매니페스트에 추가 하 여 공동 배치할 수 있습니다.
    * 옵션 2-공동 배치에 Pod 선호도 사용 (권장)
Kubernetes는 동일한 노드에서 pod을 예약할 수 있는 [Pod 선호도](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  를 지원 합니다. 라이브 비디오 분석 모듈을 참조 하는 podAffinity 섹션을 유추 pod 매니페스트에 추가 하 여 공동 배치를 달성할 수 있습니다.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>다음 단계

이 모듈을 통해 직접 메서드를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다. 모듈에서 [직접 메서드를 호출](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) 합니다.
