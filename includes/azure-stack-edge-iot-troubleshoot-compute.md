---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 0d912d0ac3f0fcf4c52116e67909038a1973304b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105785"
---
IoT Edge 에이전트 런타임 응답을 사용하여 컴퓨팅 관련 오류를 해결할 수 있습니다. 가능한 응답 목록은 다음과 같습니다.

* 200 - 확인
* 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
* 417 - 디바이스에 배포 구성 집합이 없습니다.
* 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
* 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
* 500 - IoT Edge 런타임에서 오류가 발생했습니다.

자세한 내용은 [IoT Edge 에이전트](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)를 참조하세요.

다음 오류는 Azure Stack Edge Pro 디바이스의 IoT Edge 서비스와 관련되어 있습니다.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>컴퓨팅 모듈은 알 수 없음 상태이며 사용할 수 없습니다.

#### <a name="error-description"></a>오류 설명

디바이스에 있는 모든 모듈은 알 수 없음 상태를 표시하며 사용할 수 없습니다. 알 수 없음 상태는 재부팅을 해도 유지됩니다.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>추천 솔루션

IoT Edge 서비스를 삭제한 다음, 모듈을 다시 배포합니다. 자세한 내용은 [IoT Edge 서비스 삭제하기](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service)를 참조하세요.


### <a name="modules-show-as-running-but-are-not-working"></a>모듈이 실행 중으로 표시되지만 작동하지 않습니다.

#### <a name="error-description"></a>오류 설명

모듈의 런타임 상태가 실행 중으로 표시되지만 예상 결과가 표시되지 않습니다. 

이 문제는 모듈 경로 구성이 작동하지 않거나 `edgehub`가 예상한 대로 메시지를 라우팅하지 않기 때문에 발생할 수 있습니다. `edgehub` 로그를 확인할 수 있습니다. IoT Hub 서비스에 대한 연결 실패와 같은 오류가 발생하는 경우 가장 일반적인 이유는 연결 문제입니다. 연결 문제는 IoT Hub 서비스 통신에 사용하는 기본 포트로 사용되는 AMPQ 포트가 차단되었거나 웹 프록시 서버에서 이러한 메시지를 차단하기 때문에 발생할 수 있습니다.

#### <a name="suggested-solution"></a>추천 솔루션

다음과 같은 단계를 수행합니다.
1. 이 오류를 해결하려면 디바이스에 대한 IoT Hub 리소스로 이동한 후 Edge 디바이스를 선택합니다. 
1. **모듈 설정 > 런타임 설정** 으로 이동합니다. 
1. `Upstream protocol` 환경 변수를 추가하고 `AMQPWS` 값을 할당합니다. 이 경우 구성된 메시지는 포트 443으로 WebSockets를 통해 전송됩니다.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>모듈이 실행 중으로 표시되지만 IP가 할당되어 있지 않습니다.

#### <a name="error-description"></a>오류 설명

모듈의 런타임 상태가 실행 중으로 표시되지만 컨테이너화된 앱에 할당된 IP가 없습니다. 

이 상태는 Kubernetes 외부 서비스 IP에 대해 제공한 IP 범위가 충분하지 않기 때문입니다. 배포한 각 컨테이너 또는 VM을 포함할 수 있도록 범위를 확장해야 합니다.

#### <a name="suggested-solution"></a>추천 솔루션

디바이스의 로컬 웹 UI에서 다음 단계를 수행합니다.
1. **컴퓨팅** 페이지로 이동합니다. 컴퓨팅 네트워크를 사용하도록 설정한 포트를 선택합니다. 
1. **Kubernetes 외부 서비스 IP** 에 대한 고정 IP 범위를 입력합니다. `edgehub` 서비스에는 1개의 IP가 필요합니다. 또한 배포하고자 하는 각 IoT Edge 모듈 및 VM에 대해 하나의 IP가 필요합니다. 
1. **적용** 을 선택합니다. 변경된 IP 범위는 즉시 적용됩니다.

자세한 내용은 [컨테이너의 외부 서비스 IP 변경](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers)을 참조하세요.

### <a name="configure-static-ips-for-iot-edge-modules"></a>IoT Edge 모듈의 고정 IP 구성

#### <a name="problem-description"></a>문제 설명

Kubernetes는 Azure Stack Edge Pro GPU 디바이스의 각 IoT Edge 모듈에 동적 IP를 할당합니다. 모듈에 대한 고정 IP를 구성해야 합니다.

#### <a name="suggested-solution"></a>추천 솔루션

아래에 설명된 대로 K8s-실험적 섹션을 통해 IoT Edge 모듈에 고정 IP 주소를 지정할 수 있습니다. 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Kubernetes 서비스를 내부 통신용 클러스터 IP 서비스로 노출

#### <a name="problem-description"></a>문제 설명

기본적으로 IoT 서비스 유형은 부하 분산 장치 유형이며 외부 연결 IP 주소가 할당됩니다. 애플리케이션에 대한 외부 연결 IP 주소를 원하지 않을 수 있습니다. 외부에 노출된 부하 분산 장치 서비스가 아닌 다른 Pod로 액세스하려면 Kubernetes 클러스터 내에서 Pod를 노출해야 할 수 있습니다. 

#### <a name="suggested-solution"></a>추천 솔루션

K8s-실험적 섹션을 통해 만들기 옵션을 사용할 수 있습니다. 다음 서비스 옵션은 포트 바인딩에서 작동해야 합니다.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```