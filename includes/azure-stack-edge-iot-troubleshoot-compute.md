---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 34d0d55ba6eb403055be96758b57b7bd0c2ab704
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104988337"
---
IoT Edge 에이전트 런타임 응답을 사용 하 여 계산 관련 오류 문제를 해결할 수 있습니다. 가능한 응답 목록은 다음과 같습니다.

* 200 - 확인
* 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
* 417-장치에 배포 구성 집합이 없습니다.
* 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
* 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
* 500 - IoT Edge 런타임에서 오류가 발생했습니다.

자세한 내용은 [IoT Edge 에이전트](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)를 참조 하세요.

다음 오류는 Azure Stack Edge Pro 장치의 IoT Edge 서비스와 관련 되어 있습니다.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>계산 모듈은 알 수 없는 상태 이므로 사용할 수 없습니다.

#### <a name="error-description"></a>오류 설명

장치에 있는 모든 모듈은 알 수 없는 상태를 표시 하며 사용할 수 없습니다. 알 수 없음 상태는 다시 부팅을 통해 유지 됩니다.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>추천 솔루션

IoT Edge 서비스를 삭제 한 다음 모듈을 다시 배포 합니다. 자세한 내용은 [IoT Edge 서비스 제거](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service)를 참조 하세요.


### <a name="modules-show-as-running-but-are-not-working"></a>모듈이 실행 중으로 표시 되지만 작동 하지 않습니다.

#### <a name="error-description"></a>오류 설명

모듈의 런타임 상태가 실행 중으로 표시 되지만 예상 된 결과가 표시 되지 않습니다. 

이 문제는 작동 하지 않거나 `edgehub` 예상 대로 메시지를 라우팅 하지 않는 모듈 경로 구성 문제로 인해 발생할 수 있습니다. 로그를 확인할 수 있습니다 `edgehub` . IoT Hub 서비스에 대 한 연결 실패와 같은 오류가 발생 하는 경우 가장 일반적인 이유는 연결 문제입니다. 연결 문제는 IoT Hub 서비스가 통신에 사용 하는 기본 포트로 사용 되는 AMPQ 포트가 차단 되었거나 웹 프록시 서버에서 이러한 메시지를 차단 하기 때문에 발생할 수 있습니다.

#### <a name="suggested-solution"></a>추천 솔루션

다음 단계를 수행합니다.
1. 이 오류를 해결 하려면 장치에 대 한 IoT Hub 리소스로 이동한 후 Edge 장치를 선택 합니다. 
1. **모듈 설정 > 런타임 설정** 으로 이동 합니다. 
1. `Upstream protocol`환경 변수를 추가 하 고 값을 할당 `AMQPWS` 합니다. 이 경우 구성 된 메시지는 포트 443을 통해 Websocket을 통해 전송 됩니다.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>모듈이 실행 중으로 표시 되지만 IP가 할당 되어 있지 않습니다.

#### <a name="error-description"></a>오류 설명

모듈의 런타임 상태가 실행 중으로 표시 되지만 컨테이너 화 된 앱에 할당 된 IP가 없습니다. 

이 상태는 Kubernetes 외부 서비스 Ip에 대해 제공한 Ip 범위가 충분 하지 않기 때문입니다. 배포한 각 컨테이너 또는 VM이 포함 되도록이 범위를 확장 해야 합니다.

#### <a name="suggested-solution"></a>추천 솔루션

장치의 로컬 웹 UI에서 다음 단계를 수행 합니다.
1. **Compute** 페이지로 이동 합니다. 계산 네트워크를 사용 하도록 설정한 포트를 선택 합니다. 
1. **Kubernetes 외부 서비스 ip** 에 대 한 고정 ip 범위를 입력 합니다. 서비스에는 1 개의 IP가 필요 `edgehub` 합니다. 또한 각 IoT Edge 모듈 및 배포할 각 VM에 대해 하나의 IP가 필요 합니다. 
1. **적용** 을 선택합니다. 변경 된 IP 범위는 즉시 적용 됩니다.

자세한 내용은 [컨테이너의 외부 서비스 Ip 변경](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#change-external-service-ips-for-containers)을 참조 하세요.

### <a name="configure-static-ips-for-iot-edge-modules"></a>IoT Edge 모듈의 고정 Ip 구성

#### <a name="problem-description"></a>문제 설명

Kubernetes는 Azure Stack Edge Pro GPU 장치의 각 IoT Edge 모듈에 동적 Ip를 할당 합니다. 모듈에 대 한 고정 Ip를 구성 하려면 메서드가 필요 합니다.

#### <a name="suggested-solution"></a>추천 솔루션

아래에 설명 된 대로 K8s-실험적 섹션을 통해 IoT Edge 모듈에 고정 IP 주소를 지정할 수 있습니다. 

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
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Kubernetes service를 내부 통신용 클러스터 IP 서비스로 노출

#### <a name="problem-description"></a>문제 설명

기본적으로 IoT 서비스 유형은 부하 분산 장치 유형이 며 외부 연결 IP 주소가 할당 됩니다. 응용 프로그램에 대 한 외부 연결 IP 주소를 원하지 않을 수 있습니다. 외부에 노출 된 부하 분산 장치 서비스가 아닌 다른 pod으로 액세스 하려면 KUbernetes 클러스터 내에서 pod를 노출 해야 할 수 있습니다. 

#### <a name="suggested-solution"></a>추천 솔루션

K8s-실험적 섹션을 통해 만들기 옵션을 사용할 수 있습니다. 다음 서비스 옵션은 포트 바인딩에서 작동 해야 합니다.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```
