---
title: Azure Arc 지원 Kubernetes 클러스터에 Event Grid 설치
description: 이 문서에서는 Azure Arc 지원 Kubernetes 클러스터에 Event Grid를 설치하는 단계를 제공합니다.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 43879b5124c49ba3d0c4494e2d3a1eba95e617ff
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386562"
---
# <a name="install-event-grid-extension-on-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 지원 Kubernetes 클러스터에 Event Grid 확장 설치
이 문서에서는 [Azure Arc 지원 Kubernetes](../../azure-arc/kubernetes/overview.md) 클러스터에 Event Grid를 설치하는 단계를 안내합니다.

간결함을 위해 이 문서에서는 "Kubernetes의 Event Grid 확장"을 "Kubernetes의 Event Grid" 또는 "Event Grid"로 지칭합니다.

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]


## <a name="supported-kubernetes-distributions"></a>지원되는 Kubernetes 배포
Event Grid를 배포하고 실행할 수 있는 지원되는 Kubernetes 배포는 다음과 같습니다.

1. Azure AKS [지원되는 Kubernetes 배포](../../aks/supported-kubernetes-versions.md).
1. RedHat [OpenShift Container Platform](https://www.openshift.com/products/container-platform).

[사용자 의견](https://feedback.azure.com/forums/909934-azure-event-grid) 및 [Azure Arc 지원 Kubernetes](../../azure-arc/kubernetes/validation-program.md)의 지원에 따라 더 많은 배포가 온보딩됩니다.

## <a name="event-grid-extension"></a>Event Grid 확장
Kubernetes 클러스터에 Event Grid 서비스 인스턴스를 설치하는 작업은 **Event Grid 브로커** 와 **Event Grid 운영자** 를 모두 배포하는 Azure Arc 클러스터 확장을 만드는 것입니다. 브로커 및 운영자의 기능에 대한 자세한 내용은 [Kubernetes의 Event Grid 구성 요소](concepts.md#event-grid-on-kubernetes-components)를 참조하세요. [Azure Arc 클러스터 확장](../../azure-arc/kubernetes/conceptual-extensions.md) 기능은 Azure Arc 지원 Kubernetes 클러스터에 배포된 Event Grid에 ARM(Azure Resource Manager) 컨트롤 플레인 작업을 사용하여 수명 주기 관리를 제공합니다.

> [!NOTE]
> 서비스의 미리 보기 버전은 Event Grid 확장이 현재 클러스터 범위 확장으로 정의되어 있으므로 Kubernetes 클러스터에서 Event Grid 확장의 단일 인스턴스만 지원합니다. 여러 인스턴스를 클러스터에 배포할 수 있도록 하는, Event Grid에 대한 네임스페이스 범위 배포는 아직 지원되지 않습니다.  확장 범위에 대한 자세한 내용은 [확장 인스턴스 만들기](../../azure-arc/kubernetes/extensions.md#create-extensions-instance)를 참조하고 ``scope``를 검색합니다.

## <a name="prerequisites"></a>필수 구성 요소
Event Grid 설치를 계속하기 전에 다음 필수 구성 요소를 충족하는지 확인합니다. 

1. [지원되는 Kubernetes 배포](#supported-kubernetes-distributions) 중 하나에서 실행되는 클러스터.
1. [Azure 구독](https://azure.microsoft.com/en-us/free/).
1. Event Grid 브로커와 HTTPS 연결을 설정하는 데 사용할 [PKI 인증서](#pki-certificate-requirements).
1. [클러스터를 Azure Arc에 연결합니다](../../azure-arc/kubernetes/quickstart-connect-cluster.md).

## <a name="getting-support"></a>지원 받기
문제가 발생하면 [문제 해결](#troubleshooting) 섹션에서 일반적인 조건에 대한 도움말을 참조하세요. 여전히 문제가 있는 경우 [Azure 지원 요청을 만드세요](get-support.md#how-to-create-a-support-request).

## <a name="pki-certificate-requirements"></a>PKI 인증서 요구 사항
Event Grid 브로커(서버)는 두 종류의 클라이언트를 제공합니다. 서버 인증은 인증서를 사용하여 수행됩니다. 클라이언트 인증은 클라이언트 유형에 따라 인증서 또는 SAS 키를 사용하여 수행됩니다.

- Event Grid 브로커에 대한 컨트롤 플레인 요청을 만드는 Event Grid 운영자는 인증서를 사용하여 인증됩니다.
- Event Grid 토픽에 대한 이벤트를 게시하는 Event Grid 게시자는 토픽의 SAS 키로 인증됩니다.

Event Grid 브로커 및 Event Grid 운영자와 보안 HTTPS 통신을 설정하려면 Event Grid 확장을 설치하는 동안 PKI 인증서를 사용합니다. 이러한 PKI 인증서에 대한 일반적인 요구 사항은 다음과 같습니다.

1. 인증서와 키는 [X.509](https://en.wikipedia.org/wiki/X.509) 인증서와 [Privacy-Enhanced Mail](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail) PEM으로 인코딩되어야 합니다.
1. 설치하는 동안 Event Grid 브로커(서버) 인증서를 구성하려면 다음을 제공해야 합니다.
    1. CA 인증서
    1. 공용 인증서
    1. 프라이빗 키
1. Event Grid 운영자(클라이언트) 인증서를 구성하려면 다음을 제공해야 합니다.
    1. CA 인증서
    1. 공용 인증서
    1. 프라이빗 키

    게시 클라이언트는 Event Grid 브로커 CA 인증서를 사용하여 토픽에 이벤트를 게시할 때 서버의 유효성을 검사할 수 있습니다.

    > [!IMPORTANT]
    > 클라이언트에 연결된 도메인에 서로 다른 인증 기관에서 발급한 공용 인증서가 두 개 이상 있을 수 있지만 Kubernetes의 Event Grid는 Event Grid를 설치할 때 클라이언트에 대한 단일 CA 인증서 업로드만 허용합니다. 결과적으로 인증서 체인 유효성 검사가 성공하고 TLS 세션이 성공적으로 설정되기 위해서는 동일한 CA에서 Event Grid 운영자의 인증서를 발급(서명)해야 합니다.
1. 서버 및 클라이언트 인증서에 대한 CN(일반 이름)을 구성하는 경우 인증서 인증 기관 인증서에 대해 제공된 CN과 다른지 확인합니다.

    > [!IMPORTANT] 
    > 초기 개념 증명 단계에서는 자체 서명된 인증서가 옵션이지만 일반적으로 CA(인증 기관)에서 서명한 적절한 PKI 인증서를 조달하고 사용해야 합니다.

## <a name="install-event-grid-on-kubernetes-extension"></a>Kubernetes의 Event Grid 확장 설치

1. Azure Portal에서 **Azure Arc** 를 검색(맨 위 필드)합니다.
1. 왼쪽 메뉴의 **인프라** 섹션에서 **Kubernetes 클러스터** 를 선택합니다.
1. 클러스터 목록에서 Event Grid를 설치하려는 클러스터를 찾아 선택합니다. 클러스터에 대한 **개요** 페이지가 표시됩니다.
1. 왼쪽 메뉴의 **설정** 그룹에서 **확장** 을 선택합니다.
1. **+추가** 를 선택합니다. 사용 가능한 Azure Arc Kubernetes 확장을 표시하는 페이지가 표시됩니다.
1. **Event Grid** 를 선택합니다.
1. Azure Arc를 사용하는 Kubernetes의 Event Grid 페이지에서 **만들기** 를 선택합니다.
1. **Event Grid 설치** 페이지에 **기본 사항** 탭이 표시됩니다. Azure Arc 확장이 설치된 연결 클러스터의 동일한 Azure 구독 및 리소스 그룹 아래에서 Azure Arc 확장이 배포되므로 **프로젝트 세부 정보** 섹션에 읽기 전용 구독 및 리소스 그룹 값이 표시됩니다.
1. **Event Grid 확장 이름** 필드에 이름을 제공합니다. 이 이름은 동일한 Azure Arc 연결 클러스터에 배포된 다른 Azure Arc 확장에서 고유해야 합니다.
1. **릴리스 네임스페이스** 의 경우 Event Grid 구성 요소가 배포될 Kubernetes 네임스페이스의 이름을 제공할 수 있습니다. 기본값은 **eventgrid-system** 입니다. 제공된 네임스페이스가 없으면 자동으로 만들어집니다.
1. **Event Grid 브로커** 세부 정보 섹션에 서비스 유형이 표시됩니다. 이벤트가 전송되는 토픽 엔드포인트를 공개하는 구성 요소인 Event Grid 브로커는 Kubernetes 서비스 유형 **ClusterIP** 로 공개됩니다. 따라서 모든 토픽에 할당된 IP는 클러스터에 대해 구성된 개인 IP 공간을 사용합니다.
1. Kubernetes 배포에서 지원하고 브로커에 사용할 **스토리지 클래스 이름** 을 제공합니다. 예를 들어 AKS를 사용하는 경우 Azure Standard Storage를 사용하는 `azurefile`을 사용할 수 있습니다. AKS에서 지원하는 미리 정의된 스토리지 등급에 대한 자세한 내용은 [AKS의 스토리지 등급](../../aks/concepts-storage.md#storage-classes)을 참조하세요. 다른 Kubernetes 배포를 사용하는 경우 지원되는 미리 정의된 스토리지 클래스 또는 자체 제공 방법은 Kubernetes 배포 설명서를 참조하세요.
1. **스토리지 크기**. 기본값은 1GiB입니다. 스토리지의 크기를 결정할 때는 수집 속도를 고려합니다. 이벤트 크기와 Event Grid 브로커의 모든 토픽에 대한 게시 속도(초당 이벤트 수)를 곱한 값으로 측정된 수집 속도(MiB/초)는 스토리지 할당 시 핵심 요소입니다. 이벤트는 본질적으로 일시적이며, 이벤트가 전달되면 해당 이벤트에 대한 스토리지 소비가 없습니다. 수집 속도는 스토리지 사용에 주로 영향을 미치지만 유일한 것은 아닙니다. 토픽 및 이벤트 구독 구성을 포함하는 메타데이터는 스토리지 공간도 사용하지만 일반적으로 Event Grid에서 수집하고 전달하는 이벤트보다 적은 양의 스토리지 공간이 필요합니다.
1. **메모리 제한**. 기본값은 1GiB입니다. 
1. **메모리 요청**. 기본값은 200MiB입니다. 이 필드는 편집할 수 없습니다.

    :::image type="content" source="./media/install-k8s-extension/basics-page.png" alt-text="Event Grid 확장 설치 - 기본 페이지":::
1. 페이지 아래쪽에서 **다음: 구성** 단추를 선택합니다.
1. **HTTP(안전하지 않음) 통신을 사용합니다**. 클라이언트가 Event Grid 브로커와 통신할 때 비보안 채널을 사용하려면 이 확인란을 선택합니다.

    > [!IMPORTANT]
    > 이 옵션을 사용하도록 설정하면 Event Grid 브로커와의 모든 통신에서 HTTP를 전송으로 사용합니다. 따라서 게시 클라이언트와 Event Grid 운영자는 Event Grid 브로커와 안전하게 통신하지 않습니다. 이 옵션은 개발 초기 단계에서만 사용해야 합니다.
1. HTTP 통신을 사용하도록 설정하지 않은 경우 조달한 각 PKI 인증서 파일을 선택하고 [PKI 인증서 요구 사항](#pki-certificate-requirements)을 충족시킵니다.

    :::image type="content" source="./media/install-k8s-extension/configuration-page.png" alt-text="Event Grid 확장 설치 - 구성 페이지":::
1. 페이지 하단에서 **다음: 모니터링** 을 선택합니다.
1. 이 옵션을 선택하여 **메트릭을 사용하도록 설정** 합니다. Kubernetes의 Event Grid는 [Prometheus 공개 형식](https://prometheus.io/docs/instrumenting/exposition_formats/)을 사용하여 토픽 및 이벤트 구독에 대한 메트릭을 공개합니다.

    :::image type="content" source="./media/install-k8s-extension/monitoring-page.png" alt-text="Event Grid 확장 설치 - 모니터링 페이지":::    
1. **다음: 태그** 를 선택하여 **태그** 페이지로 이동합니다. 필요한 경우 [태그](/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)를 정의합니다.

    :::image type="content" source="./media/install-k8s-extension/tags-page.png" alt-text="Event Grid 확장 설치 - 태그 페이지":::
1. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다.
1. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.
    
    :::image type="content" source="./media/install-k8s-extension/review-create-page.png" alt-text="Event Grid 확장 설치 - 검토 및 만들기 페이지":::   
    
    > [!IMPORTANT]
    > Event Grid 설치는 Azure Portal에서 배포 완료 알림에 표시되는 시간보다 Kubernetes 클러스터에서 더 오래 실행될 수 있는 비동기 작업입니다. "배포 완료"라는 알림이 표시된 후 5분 이상 기다린 다음 사용자 지정 위치 생성(다음 단계)을 시도합니다. Kubernetes 클러스터에 대한 액세스 권한이 있는 경우 bash 세션에서 다음 명령을 실행하여 Event Grid 브로커 및 Event Grid 운영자 Pod가 실행 중(설치가 완료되었음을 나타냄) 상태인지 확인할 수 있습니다.

    ```bash
    kubectl get pods -n \<release-namespace-name\>
    ```
    > [!IMPORTANT]
    > Event Grid 토픽을 배포하기 전에 사용자 지정 위치를 만들어야 합니다. 사용자 지정 위치를 만들려면 "배포 완료" 알림이 표시된 후 5분 후에 하단에 있는 **컨텍스트** 페이지를 선택할 수 있습니다. 또는 [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ExtendedLocation%2FCustomLocations)을 사용하여 사용자 지정 위치를 만들 수 있습니다.
1. 배포가 성공하면 Event Grid 확장에 제공한 이름으로 **확장** 페이지에서 항목을 볼 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="azure-arc-connect-cluster-issues"></a>Azure Arc 연결 클러스터 문제

**문제**: **Azure Arc** 로 이동하여 왼쪽 메뉴에서 **Kubernetes 클러스터** 를 클릭하여 표시된 페이지에 Event Grid를 설치하려고 한 Kubernetes 클러스터가 표시되지 않습니다.

**해결 방법**: Kubernetes 클러스터가 Azure에 등록되지 않았습니다. [기존 Kubernetes 클러스터를 Azure Arc에 연결](../../azure-arc/kubernetes/quickstart-connect-cluster.md) 문서의 단계를 따릅니다. 이 단계에서 문제가 발생하면 Azure Arc 지원 Kubernetes 팀에 [지원 요청](#getting-support)을 제출합니다.

### <a name="event-grid-extension-issues"></a>Event Grid 확장 문제

**문제**: "Event Grid 확장"을 설치하려고 하면 다음 메시지가 표시됩니다. "**잘못된 작업** - Event Grid의 인스턴스가 연결된 이 Kubernetes 클러스터에 이미 설치되어 있습니다. Event Grid 확장은 클러스터 수준에서 범위가 지정됩니다. 즉, 하나의 인스턴스만 클러스터에 설치할 수 있습니다."
    
**설명**: Event Grid가 이미 설치되어 있습니다. 미리 보기 버전의 Event Grid는 클러스터에 배포된 하나의 Event Grid 확장 인스턴스만 지원합니다.


## <a name="next-steps"></a>다음 단계
빠른 시작 [Kubernetes의 Azure Event Grid를 사용하여 클라우드 이벤트를 웹후크로 라우팅](create-topic-subscription.md)을 참조하세요.

