---
title: Kubernetes에 자체 호스팅 게이트웨이 배포 | Microsoft Docs
description: Kubernetes에 Azure API Management의 자체 호스팅 게이트웨이 구성 요소를 배포 하는 방법을 알아봅니다.
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205106"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Kubernetes에 자체 호스팅 게이트웨이 배포

이 문서에서는 Kubernetes 클러스터에 Azure API Management의 자체 호스팅 게이트웨이 구성 요소를 배포 하는 단계를 설명 합니다.

## <a name="prerequisites"></a>전제 조건

- 다음 빠른 시작: [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md) 를 완료 합니다.
- Kubernetes 클러스터를 만듭니다.
> [!TIP]
> [단일 노드 클러스터](https://kubernetes.io/docs/setup/#learning-environment) 는 개발 및 평가 목적으로 잘 작동 합니다. 프로덕션 워크 로드를 위해 온-프레미스 또는 클라우드에서 [Kubernetes 인증](https://kubernetes.io/partners/#conformance) 된 다중 노드 클러스터를 사용 합니다.
- [API Management 인스턴스에서 자체 호스팅 게이트웨이 리소스를 프로 비전](api-management-howto-provision-self-hosted-gateway.md)합니다.

## <a name="deploy-to-kubernetes"></a>Kubernetes에 배포

1. **배포 및 인프라**에서 **게이트웨이** 를 선택 합니다.
2. 배포 하려는 자체 호스팅 게이트웨이 리소스를 선택 합니다.
3. **배포**를 선택 합니다.
4. **토큰** 텍스트 상자의 액세스 토큰은 기본 **만료** 및 **비밀 키** 값을 사용 하 여 자동으로 자동 생성 되었습니다. 필요한 경우 또는 두 컨트롤에서 원하는 값을 선택 하 여 새 토큰을 생성 합니다.
5. **배포 스크립트**아래에서 **Kubernetes** 탭을 선택 합니다.
6. **<게이트웨이 이름> .yml** 파일 링크를 클릭 하 고 yml 파일을 다운로드 합니다.
7. **배포** 텍스트 상자의 오른쪽 아래에 있는 `kubectl` **복사** 아이콘을 선택 하 여 명령을 클립보드에 저장 합니다.
8. 명령을 터미널 (또는 명령) 창에 붙여 넣습니다. 첫 번째 명령은 4 단계에서 생성 된 액세스 토큰을 포함 하는 Kubernetes secret을 만듭니다. 두 번째 명령은 6 단계에서 다운로드 한 구성 파일을 Kubernetes 클러스터에 적용 하 고 파일이 현재 디렉터리에 있다고 예상 합니다.
9. 명령을 실행 하 여 [기본 네임 스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 에 필요한 Kubernetes 개체를 만들고 Microsoft Container Registry에서 다운로드 한 [컨테이너 이미지](https://aka.ms/apim/sputnik/dhub) 에서 자체 호스트 된 게이트웨이 pod를 시작 합니다.
10. 아래 표시 된 명령을 실행 하 여 배포에 성공 했는지 확인 합니다. 모든 개체를 만들고 pod를 초기화 하는 데 약간의 시간이 걸릴 수 있습니다.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. 아래 표시 된 명령을 실행 하 여 서비스가 성공적으로 만들어졌는지 확인 합니다. 서비스 Ip 및 포트는 다를 수 있습니다.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Azure Portal로 돌아가서 **개요**를 선택 합니다.
13. 녹색 확인 표시 아이콘을 표시 하 고, YAML 파일에 지정 된 복제본 수와 일치 하는 노드 수를 표시 하는 **상태** 는 배포 된 자체 호스팅 게이트웨이 pod가 API Management 서비스와 성공적으로 통신 하 고 일반 "하트 비트"를 포함 한다는 것을 확인 합니다.

![게이트웨이 상태](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> 명령을 <code>kubectl logs deployment/<gateway-name></code> 실행 하 여 임의로 선택한 pod에서 로그를 확인 합니다.
> 명령 <code>kubectl logs -h</code> 옵션의 전체 집합 (예: 특정 pod 또는 컨테이너에 대 한 로그를 보는 방법)에 대해를 실행 합니다.

## <a name="production-deployment-considerations"></a>프로덕션 배포 고려 사항

### <a name="access-token"></a>액세스 토큰
유효한 액세스 토큰이 없으면 자체 호스트 된 게이트웨이가 연결 된 API Management 서비스의 구성 데이터 끝점에서 구성에 액세스 하 고이를 다운로드할 수 없습니다. 액세스 토큰은 최대 30 일간 유효 합니다. 이를 다시 생성 해야 하며, 클러스터는 수동으로 또는 자동화를 통해 새 토큰으로 구성 되어야 합니다. 토큰 새로 고침을 자동화 하는 경우이 관리 API [작업](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) 을 사용 하 여 새 토큰을 생성 합니다. Kubernetes 암호 관리에 대 한 자세한 내용은 다음 [링크](https://kubernetes.io/docs/concepts/configuration/secret) 를 참조 하세요.

### <a name="namespace"></a>네임스페이스
Kubernetes [네임 스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 는 단일 클러스터를 여러 팀, 프로젝트 또는 응용 프로그램 간에 분할 하는 데 도움이 됩니다. 네임 스페이스는 리소스 및 이름에 대 한 범위를 제공 하며 리소스 할당량 및 액세스 제어 정책에 연결 될 수 있습니다.
에서 제공 하는 명령은 자동으로 생성 되 고, 모든 클러스터에 존재 하며, 삭제할 수 없는 **기본** 네임 스페이스에 자체 호스트 된 게이트웨이 리소스를 만들 Azure Portal.
자체 호스팅 게이트웨이를 만들어 프로덕션 환경에서 별도의 네임 스페이스에 [배포 하](https://kubernetesbyexample.com/ns/) 는 것이 좋습니다.

### <a name="number-of-replicas"></a>복제본 수
프로덕션에 적합 한 최소 복제본 수는 2입니다.

기본적으로 자체 호스팅 게이트웨이는 **RollingUpdate** 배포 [전략](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)을 사용 하 여 배포 됩니다. 기본값을 검토 하 고, 특히 높은 복제본 수를 사용 하는 경우 [**maxunavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) 및 [**maxunavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) 수 필드를 명시적으로 설정 하는 것이 좋습니다.

### <a name="container-resources"></a>컨테이너 리소스
기본적으로 Azure Portal에 제공 된 YAML 파일은 컨테이너 리소스 요청을 지정 하지 않습니다.

컨테이너 당 CPU 및 메모리 리소스의 양을 안정적으로 예측 하 고 권장 하는 것은 불가능 하며, 다음과 같은 여러 요소로 인해 특정 워크 로드를 지 원하는 데 필요한 복제본 수를 결정 하는 것은 불가능 합니다.

- 클러스터가 실행 중인 특정 하드웨어
- 가상화의 현재 상태 및 유형
- 동시 클라이언트 연결 수 및 요율
- 요청 속도
- 구성 된 정책의 종류 및 수
- 페이로드 크기 및 페이로드가 버퍼링 되거나 스트리밍되는 경우
- 백 엔드 서비스 대기 시간

리소스 요청을 2 개의 코어로 설정 하 고, GiB 2 개를 시작 지점으로 설정 하 고, 부하 테스트를 수행 하 고, 결과를 기준으로 규모를 확장/축소 하거나 축소 하는 것이 좋습니다.

### <a name="container-image-tag"></a>컨테이너 이미지 태그
Azure Portal에 제공 된 YAML 파일은 항상 자체 호스팅 게이트웨이 컨테이너 이미지의 최신 버전을 참조 하는 **최신** 태그를 사용 합니다.

프로덕션에서 특정 버전 태그를 사용 하 여 의도 하지 않은 최신 버전으로의 업그레이드를 방지 하는 것이 좋습니다.

사용 가능한 태그의 전체 목록을 보려면이 [링크](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) 를 따르세요.

### <a name="dns-policy"></a>DNS 정책
DNS 이름 확인은 Azure에서 종속성에 연결 하 고 백 엔드 서비스에 대 한 API 호출을 발송 하는 자체 호스팅 게이트웨이의 중요 한 역할을 합니다.

Azure Portal에 제공 된 YAML 파일은 클러스터 DNS에서 확인 하지 않은 이름 확인 요청이 노드에서 상속 된 업스트림 DNS 서버로 전달 되도록 하는 기본 [**Clusterfirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) 정책을 적용 합니다.

이 [링크](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) 를 따라 Kubernetes에서 이름 확인에 대해 알아보고 설치에 적합 한 [DNS 정책](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) 또는 D[NS 구성을](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) 사용자 지정 하는 것이 좋습니다.

### <a name="configuration-backup"></a>구성 백업
이 [링크](self-hosted-gateway-overview.md#connectivity-to-azure) 를 따라 임시 Azure 연결이 중단 된 상태에서 자체 호스팅 게이트웨이 동작에 대해 알아보세요.
다운로드 한 최신 구성의 백업 복사본을 유지 하 고, 연결이 종료 되 면 다시 시작할 때 사용 하 여 자체 호스팅 게이트웨이 컨테이너에 대 한 로컬 저장소 볼륨을 구성 합니다. 볼륨 탑재 경로는 여야 <code>/apim/config</code>합니다. [여기](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)에서 예제를 참조하세요.
Kubernetes의 저장소에 대 한 자세한 내용은이 [링크](https://kubernetes.io/docs/concepts/storage/volumes/)를 따릅니다.

### <a name="local-logs-and-metrics"></a>로컬 로그 및 메트릭
자체 호스팅 게이트웨이는 연결 된 API Management 서비스의 구성 설정에 따라 [Azure Monitor](api-management-howto-use-azure-monitor.md) 및 [Azure 애플리케이션 정보](api-management-howto-app-insights.md) 에 대 한 원격 분석을 보냅니다.
[Azure에](self-hosted-gateway-overview.md#connectivity-to-azure) 대 한 연결이 일시적으로 끊어진 경우 azure에 대 한 원격 분석 흐름이 중단 되 고 가동 중단 기간 동안 데이터가 손실 됩니다.
Azure 연결 중단 중에 API 트래픽을 관찰 하 고 원격 분석 손실을 방지할 수 있도록 [로컬 모니터링을 설정](how-to-configure-local-metrics-logs.md) 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대해 자세히 알아보려면 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md) 를 참조 하세요.