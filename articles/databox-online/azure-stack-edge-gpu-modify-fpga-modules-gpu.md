---
title: FPGA 장치에서 IoT Edge 모듈을 수정 하 여 Azure Stack Edge Pro GPU 장치에서 실행
description: Azure Stack Edge Pro GPU 장치에서 실행 되는 기존 FPGA 장치의 기존 IoT Edge 모듈에 필요한 수정 사항을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442998"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Edge Pro GPU 장치의 Azure Stack Edge Pro FPGA Azure Stack 장치에서 기존 IoT Edge 모듈 실행

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro FPGA에서 실행 되는 docker 기반 IoT Edge 모듈에 필요한 변경 사항에 대해 자세히 설명 합니다 .이 모듈을 Azure Stack Edge Pro GPU 장치의 Kubernetes 기반 IoT Edge 플랫폼에서 실행할 수 있습니다. 

## <a name="about-iot-edge-implementation"></a>IoT Edge 구현 정보 

IoT Edge 구현은 edge pro FPGA 장치와 Edge Pro GPU 장치의 Azure Stack에 따라 Azure Stack 다릅니다. GPU 장치의 경우 Kubernetes는 IoT Edge에 대 한 호스팅 플랫폼으로 사용 됩니다. FPGA 장치의 IoT Edge는 docker 기반 플랫폼을 사용 합니다. IoT Edge docker 기반 응용 프로그램 모델은 자동으로 Kubernetes native 응용 프로그램 모델로 변환 됩니다. 그러나 일부 변경 내용은 Kubernetes 응용 프로그램 모델의 작은 하위 집합만 지원 되기 때문에 계속 필요할 수 있습니다.

FPGA 장치에서 GPU 장치로 워크 로드를 마이그레이션하는 경우 Kubernetes 플랫폼에서 성공적으로 실행 되도록 기존 IoT Edge 모듈을 변경 해야 합니다. 저장소, 네트워킹, 리소스 사용량 및 웹 프록시 요구 사항을 다르게 지정 해야 할 수 있습니다. 

## <a name="storage"></a>스토리지

IoT Edge 모듈의 저장소를 지정할 때 다음 정보를 고려 하십시오.

- Kubernetes의 컨테이너에 대 한 저장소는 볼륨 탑재를 사용 하 여 지정 됩니다.
- Kubernetes에 대 한 배포에는 영구 저장소 또는 호스트 경로 연결에 대 한 바인딩을 사용할 수 없습니다.
    - 영구적 저장소의 경우 `Mounts` with type을 사용 `volume` 합니다.
    - 호스트 경로의 경우 `Mounts` with type을 사용 `bind` 합니다.
- Kubernetes에 대 한 IoT Edge의 경우 bind는 파일에 대 한 `Mounts` 것이 아니라 디렉터리만 사용할 수 있습니다.

#### <a name="example---storage-via-volume-mounts"></a>예제-볼륨 탑재를 통한 저장소 

Docker에 대 한 IoT Edge 호스트 경로 바인딩은 장치의 공유를 컨테이너 내의 경로에 매핑하는 데 사용 됩니다. FPGA 장치에서 사용 되는 컨테이너 만들기 옵션은 다음과 같습니다.

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Kubernetes에서 IoT Edge에 대 한 호스트 경로는 다음과 같은 형식으로을 사용 하는 예를 `Mounts` `bind` 보여 줍니다.
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Kubernetes에서 IoT Edge를 실행 하는 GPU 장치의 경우 볼륨 탑재를 사용 하 여 저장소를 지정 합니다. 공유를 사용 하 여 저장소를 프로 비전 하기 위해의 값은 `Mounts.Source` GPU 장치에 프로 비전 된 SMB 또는 NFS 공유의 이름입니다. 는 `/home/input` 컨테이너 내에서 볼륨에 액세스할 수 있는 경로입니다. 다음은 GPU 장치에서 사용 되는 컨테이너 만들기 옵션입니다.

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>네트워크

IoT Edge 모듈에 대해 네트워킹을 지정할 때 다음 정보를 고려 하십시오. 

- `HostPort` 사양은 클러스터 내부와 외부 모두에서 서비스를 노출 하는 데 필요 합니다.
    - K8sExperimental 옵션을 선택 하 여 서비스를 클러스터 전용으로 제한할 수 있습니다.
- 모듈 간 통신에 `HostPort` 는 지정 된 컨테이너 포트를 사용 하지 않고 매핑된 포트를 사용 하 여 사양과 연결 해야 합니다.
- 호스트 네트워킹은와 함께 작동 하며 `dnsPolicy = ClusterFirstWithHostNet` , 모든 컨테이너 (특히 `edgeHub` )는 호스트 네트워크에 있을 필요가 없습니다. <!--Need further clarifications on this one-->
- TCP에 대 한 포트 매핑을 추가 하면 동일한 요청에서 UDP가 작동 하지 않습니다.

#### <a name="example---external-access-to-modules"></a>예: 모듈에 대 한 외부 액세스 

포트 바인딩을 지정 하는 IoT Edge 모듈의 경우 장치의 로컬 UI에 지정 된 Kubernetes 외부 서비스 IP 범위를 사용 하 여 IP 주소가 할당 됩니다. 다음 예에 표시 된 것 처럼 Kubernetes의 docker vs IoT Edge IoT Edge 사이에 컨테이너 만들기 옵션이 변경 되지 않았습니다.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

그러나 모듈에 할당 된 IP 주소를 쿼리하려면 [서비스 또는 모듈에 대 한 ip 주소 가져오기](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)에 설명 된 대로 Kubernetes 대시보드를 사용할 수 있습니다. 

또는 [장치의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 하 고 `iotedge` list 명령을 사용 하 여 장치에서 실행 되는 모든 모듈을 나열할 수 있습니다. 또한 [명령 출력](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) 은 모듈과 연결 된 외부 ip를 표시 합니다.


## <a name="resource-usage"></a>리소스 사용량 

GPU 장치에 대 한 Kubernetes 기반 IoT Edge 설정에서 하드웨어 가속, 메모리 및 CPU 요구 사항과 같은 리소스는 FPGA 장치와 다르게 지정 됩니다. 

#### <a name="compute-acceleration-usage"></a>계산 가속 사용

FPGA에 모듈을 배포 하려면 컨테이너 만들기 옵션을 사용 합니다. <!--with Device Bindings--> 다음 구성에 표시 된 것과 같습니다. <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
GPU의 경우 다음과 같은 최소 구성에 표시 된 것 처럼 장치 바인딩 대신 리소스 요청 사양을 사용 합니다. 끌어올립니다 대신 nvidia 리소스를 요청 하 고 있으므로를 지정 `wireserver` 합니다. 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>메모리 및 CPU 사용량
 
메모리 및 CPU 사용량을 설정 하려면 섹션의 모듈에 대 한 프로세서 제한을 사용 `k8s-experimental` 합니다. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
메모리 및 CPU 사양은 필요 하지 않지만 일반적으로 좋은 방법입니다. `requests`을 지정 하지 않으면 제한 값으로 설정 된 값이 필요한 최소값으로 사용 됩니다. 

또한 모듈에 shared memory를 사용 하려면 다른 방법이 필요 합니다. 예를 들어 [Azure Stack Edge에 라이브 비디오 분석 배포](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal)에 설명 된 대로 라이브 비디오 분석과 유추 솔루션 간의 공유 메모리 액세스에 대해 호스트 IPC 모드를 사용할 수 있습니다.


## <a name="web-proxy"></a>웹 프록시 

웹 프록시를 구성할 때 다음 정보를 고려 하십시오.

네트워크에 웹 프록시가 구성 되어 있는 경우 `edgeHub` FPGA 장치의 docker 기반 IoT Edge 설정에서 배포에 대 한 다음 환경 변수를 구성 합니다.

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (웹 프록시가 트래픽을 허용 하지 않는 경우 `Amqp` )

GPU 장치에 대 한 Kubernetes 기반 IoT Edge 설정의 경우 배포 중에이 추가 변수를 구성 해야 합니다.

- `no_proxy`: localhost

- Kubernetes platform의 IoT Edge proxy는 포트 35000 및 35001을 사용 합니다. 모듈이 이러한 포트에서 실행 되지 않거나 포트 충돌을 일으킬 수 있는지 확인 하십시오. 

## <a name="other-differences"></a>기타 차이점

- **배포 전략**: 모듈에 대 한 업데이트에 대 한 배포 동작을 변경 해야 할 수 있습니다. IoT Edge 모듈의 기본 동작은 롤링 업데이트입니다. 이 동작은 모듈이 하드웨어 가속 또는 네트워크 포트와 같은 리소스를 사용 하는 경우 업데이트 된 모듈을 다시 시작 하지 않도록 합니다. 이 동작은 GPU 장치에 대 한 Kubernetes 플랫폼에서 영구적 볼륨을 처리할 때 발생 하는 예기치 않은 결과를 포함할 수 있습니다. 이 기본 동작을 재정의 하기 위해 `Recreate` 모듈의 섹션에서를 지정할 수 있습니다 `k8s-experimental` .

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **모듈 이름**: 모듈 이름은 Kubernetes 명명 규칙을 따라야 합니다. Kubernetes를 사용 하 여 IoT Edge으로 해당 모듈을 이동할 때 Docker를 사용 하 여 IoT Edge 실행 되는 모듈의 이름을 바꾸어야 할 수 있습니다. 이름 지정에 대 한 자세한 내용은 [Kubernetes 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)을 참조 하세요.
- **기타 옵션**: 
    - FPGA 장치에서 작동 하는 특정 docker 만들기 옵션은 GPU 장치의 Kubernetes 환경에서 작동 하지 않습니다. 예:, like – EntryPoint.<!--can we confirm what exactly is required here-->
    - 와 같은 환경 변수는로 `:` 대체 해야 `__` 합니다.
    - Kubernetes pod의 **컨테이너 만들기** 상태는 IoT Hub 리소스의 모듈에 대 한 **백오프** 상태로 이어집니다. Pod가이 상태로 유지 되는 이유는 여러 가지가 있지만, 일반적으로는 작은 네트워크 대역폭 연결을 통해 대량 컨테이너 이미지를 끌어올 때 발생 합니다. Pod가이 상태 이면 모듈을 시작 하는 경우에도 IOT Hub에서 모듈의 상태가 **백오프** 로 표시 됩니다.


## <a name="next-steps"></a>다음 단계

- [모듈을 사용 하도록 GPU를 구성](azure-stack-edge-j-series-configure-gpu-modules.md)하는 방법에 대해 자세히 알아보세요.
