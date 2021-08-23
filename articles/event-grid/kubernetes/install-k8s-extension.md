---
title: Azure Arc 지원 Kubernetes 클러스터에 Event Grid 설치
description: 이 문서에서는 Azure Arc 지원 Kubernetes 클러스터에 Event Grid를 설치하는 단계를 제공합니다.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 149f46d96e7e723c89eb473aa5faea2301bc9d5f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567613"
---
# <a name="install-event-grid-extension-on-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 지원 Kubernetes 클러스터에 Event Grid 확장 설치
이 문서에서는 [Azure Arc 지원 Kubernetes](../../azure-arc/kubernetes/overview.md) 클러스터에 Event Grid를 설치하는 단계를 안내합니다.

간결함을 위해 이 문서에서는 "Kubernetes의 Event Grid 확장"을 "Kubernetes의 Event Grid" 또는 "Event Grid"로 지칭합니다.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="supported-kubernetes-distributions"></a>지원되는 Kubernetes 배포
Event Grid를 배포하고 실행할 수 있는 지원되는 Kubernetes 배포는 다음과 같습니다.

1. Azure AKS [지원되는 Kubernetes 배포](../../aks/supported-kubernetes-versions.md).
1. RedHat [OpenShift Container Platform](https://www.openshift.com/products/container-platform).


## <a name="event-grid-extension"></a>Event Grid 확장
Kubernetes 클러스터에 Event Grid 서비스 인스턴스를 설치하는 작업은 **Event Grid 브로커** 와 **Event Grid 운영자** 를 모두 배포하는 Azure Arc 클러스터 확장을 만드는 것입니다. 브로커 및 운영자의 기능에 대한 자세한 내용은 [Kubernetes의 Event Grid 구성 요소](concepts.md#event-grid-on-kubernetes-components)를 참조하세요. [Azure Arc 클러스터 확장](../../azure-arc/kubernetes/conceptual-extensions.md) 기능은 Azure Arc 지원 Kubernetes 클러스터에 배포된 Event Grid에 ARM(Azure Resource Manager) 컨트롤 플레인 작업을 사용하여 수명 주기 관리를 제공합니다.

> [!NOTE]
> 서비스의 미리 보기 버전은 Event Grid 확장이 현재 클러스터 범위 확장으로 정의되어 있으므로 Kubernetes 클러스터에서 Event Grid 확장의 단일 인스턴스만 지원합니다. 여러 인스턴스를 클러스터에 배포할 수 있도록 하는, Event Grid에 대한 네임스페이스 범위 배포는 아직 지원되지 않습니다.  확장 범위에 대한 자세한 내용은 [확장 인스턴스 만들기](../../azure-arc/kubernetes/extensions.md#create-extensions-instance)를 참조하고 ``scope``를 검색합니다.

## <a name="prerequisites"></a>필수 구성 요소
Event Grid 설치를 계속하기 전에 다음 필수 구성 요소를 충족하는지 확인합니다. 

1. [지원되는 Kubernetes 배포](#supported-kubernetes-distributions) 중 하나에서 실행되는 클러스터.
1. [Azure 구독](https://azure.microsoft.com/free/).
1. Event Grid 브로커와 HTTPS 연결을 설정하는 데 사용할 [PKI 인증서](#pki-certificate-requirements).
1. [클러스터를 Azure Arc에 연결합니다](../../azure-arc/kubernetes/quickstart-connect-cluster.md).

## <a name="getting-support"></a>지원 받기
문제가 발생하면 [문제 해결](#troubleshooting) 섹션에서 일반적인 조건에 대한 도움말을 참조하세요. 여전히 문제가 있는 경우 [Azure 지원 요청을 만드세요](get-support.md#how-to-create-a-support-request).

## <a name="pki-certificate-requirements"></a>PKI 인증서 요구 사항
Event Grid 브로커(서버)는 두 종류의 클라이언트를 제공합니다. 서버 인증은 인증서를 사용하여 수행됩니다. 클라이언트 인증은 클라이언트 유형에 따라 인증서 또는 SAS 키를 사용하여 수행됩니다.

- Event Grid 브로커에 대한 컨트롤 플레인 요청을 만드는 Event Grid 운영자는 인증서를 사용하여 인증됩니다.
- Event Grid 토픽에 대한 이벤트를 게시하는 Event Grid 게시자는 토픽의 SAS 키로 인증됩니다.

Event Grid 브로커 및 Event Grid 운영자와 보안 HTTPS 통신을 설정하기 위해 Event Grid 확장을 설치하는 동안 PKI 인증서를 사용합니다. 이러한 PKI 인증서에 대한 일반적인 요구 사항은 다음과 같습니다.

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
1. 서버 및 클라이언트 인증서에 대한 CN(일반 이름)을 구성하는 경우 인증서 인증 기관 인증서에 대해 제공된 CN과 다른지 확인하세요.

    > [!IMPORTANT] 
    > 초기 개념 증명 단계에서는 자체 서명된 인증서가 옵션이지만 일반적으로 CA(인증 기관)에서 서명한 적절한 PKI 인증서를 조달하고 사용해야 합니다.

## <a name="install-using-azure-portal"></a>Azure Portal을 사용하여 설치

1. Azure Portal에서 **Azure Arc** 를 검색(맨 위 필드)합니다.
1. 왼쪽 메뉴의 **인프라** 섹션에서 **Kubernetes 클러스터** 를 선택합니다.
1. 클러스터 목록에서 Event Grid를 설치하려는 클러스터를 찾아 선택합니다. 클러스터에 대한 **개요** 페이지가 표시됩니다.

    :::image type="content" source="./media/install-k8s-extension/select-kubernetes-cluster.png" alt-text="Kubernetes 클러스터 선택":::
1. 왼쪽 메뉴의 **설정** 그룹에서 **확장** 을 선택합니다.
1. **+추가** 를 선택합니다. 사용 가능한 Azure Arc Kubernetes 확장을 표시하는 페이지가 표시됩니다.

    :::image type="content" source="./media/install-k8s-extension/cluster-extensions-add.png" alt-text="클러스터 확장 - 추가 단추":::    
1. **새 리소스** 페이지에서 **Kubernetes Extension의 Event Grid** 를 선택합니다.

    :::image type="content" source="./media/install-k8s-extension/select-event-grid-extension.png" alt-text="Kubernetes 확장에서 Event Grid 선택":::        
1. **Kubernetes 확장의 Event Grid** 페이지에서 **만들기를** 선택합니다.

    :::image type="content" source="./media/install-k8s-extension/select-create-extension.png" alt-text="Kubernetes 확장 만들기 선택":::            
1. **Event Grid 설치** 페이지의 **기본 사항** 탭에서 다음 단계를 수행합니다. 
    1. Azure Arc 확장이 설치된 연결 클러스터의 동일한 Azure 구독 및 리소스 그룹 아래에서 Azure Arc 확장이 배포되므로 **프로젝트 세부 정보** 섹션에 읽기 전용 구독 및 리소스 그룹 값이 표시됩니다.
    1. **Event Grid 확장 이름** 필드에 이름을 제공합니다. 이 이름은 동일한 Azure Arc 연결 클러스터에 배포된 다른 Azure Arc 확장에서 고유해야 합니다.
    1. **릴리스 네임스페이스** 의 경우 Event Grid 구성 요소가 배포될 Kubernetes 네임스페이스의 이름을 제공할 수 있습니다. 예를 들어 클러스터에 배포된 모든 Azure Arc 지원 서비스에 대한 단일 네임스페이스를 사용할 수 있습니다. 기본값은 **eventgrid-system** 입니다. 제공된 네임스페이스가 없으면 자동으로 만들어집니다.
    1. **Event Grid 브로커** 세부 정보 섹션에 서비스 유형이 표시됩니다. 이벤트가 전송되는 토픽 엔드포인트를 공개하는 구성 요소인 Event Grid 브로커는 Kubernetes 서비스 유형 **ClusterIP** 로 공개됩니다. 따라서 모든 토픽에 할당된 IP는 클러스터에 대해 구성된 개인 IP 공간을 사용합니다.
    1. Kubernetes 배포에서 지원하고 브로커에 사용할 **스토리지 클래스 이름** 을 제공합니다. 예를 들어 AKS를 사용하는 경우 Azure Standard Storage를 사용하는 `azurefile`을 사용할 수 있습니다. AKS에서 지원하는 미리 정의된 스토리지 등급에 대한 자세한 내용은 [AKS의 스토리지 등급](../../aks/concepts-storage.md#storage-classes)을 참조하세요. 다른 Kubernetes 배포를 사용하는 경우 지원되는 미리 정의된 저장소 클래스 또는 자체 제공 방법은 Kubernetes 배포 설명서를 참조하세요.
    1. **스토리지 크기**. 기본값은 1GiB입니다. 스토리지의 크기를 결정할 때는 수집 속도를 고려합니다. 이벤트 크기와 Event Grid 브로커의 모든 토픽에 대한 게시 속도(초당 이벤트 수)를 곱한 값으로 측정된 수집 속도(MiB/초)는 스토리지 할당 시 핵심 요소입니다. 이벤트는 기본적으로 일시적이며, 이벤트가 전달되면 해당 이벤트에 대한 저장소 사용이 없습니다. 수집 속도는 저장소 사용에 주로 영향을 미치지만 유일한 것은 아닙니다. 토픽 및 이벤트 구독 구성을 포함하는 메타데이터는 스토리지 공간도 사용하지만 일반적으로 Event Grid에서 수집하고 전달하는 이벤트보다 적은 양의 스토리지 공간이 필요합니다.
    1. **메모리 제한**. 기본값은 1GiB입니다. 
    1. **메모리 요청**. 기본값은 200MiB입니다. 이 필드는 편집할 수 없습니다.

        :::image type="content" source="./media/install-k8s-extension/basics-page.png" alt-text="Event Grid 확장 설치 - 기본 페이지":::
    1. 페이지 아래쪽에서 **다음: 구성** 단추를 선택합니다.
1. **설치 Event Grid** 페이지의 **구성** 탭에서 다음 단계를 수행합니다. 
    1. **HTTP(안전하지 않음) 통신을 사용합니다**. 클라이언트가 Event Grid 브로커와 통신할 때 비보안 채널을 사용하려면 이 확인란을 선택합니다.

        > [!IMPORTANT]
        > 이 옵션을 사용하도록 설정하면 Event Grid 브로커와의 모든 통신에서 HTTP를 전송으로 사용합니다. 따라서 게시 클라이언트와 Event Grid 운영자는 Event Grid 브로커와 안전하게 통신하지 않습니다. 이 옵션은 개발 초기 단계에서만 사용해야 합니다.
    1. HTTP 통신을 사용하도록 설정하지 않은 경우 조달한 각 PKI 인증서 파일을 선택하고 [PKI 인증서 요구 사항](#pki-certificate-requirements)을 충족시킵니다.

        :::image type="content" source="./media/install-k8s-extension/configuration-page.png" alt-text="Event Grid 확장 설치 - 구성 페이지":::
    1. 페이지 하단에서 **다음: 모니터링** 을 선택합니다.
1. **Event Grid 설치** 페이지의 **모니터링** 탭에서 다음 단계를 수행 합니다.
    1. **요금제 사용**(선택 사항)을 선택합니다. 이 옵션을 선택하는 경우 Kubernetes의 Event Grid는 [Prometheus 공개 형식](https://prometheus.io/docs/instrumenting/exposition_formats/)을 사용하여 토픽 및 이벤트 구독에 대한 요금제를 표시합니다.

        :::image type="content" source="./media/install-k8s-extension/monitoring-page.png" alt-text="Event Grid 확장 설치 - 모니터링 페이지":::    
    1. **다음: 태그** 를 선택하여 **태그** 페이지로 이동합니다. 
1. **태그** 페이지에서 다음 단계를 수행 합니다.
    1. 필요한 경우 [태그](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)를 정의합니다.

        :::image type="content" source="./media/install-k8s-extension/tags-page.png" alt-text="Event Grid 확장 설치 - 태그 페이지":::
    1. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다.
1. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.
    
    :::image type="content" source="./media/install-k8s-extension/review-create-page.png" alt-text="Event Grid 확장 설치 - 검토 및 만들기 페이지":::   
    
    > [!IMPORTANT]
    > Event Grid 설치는 Azure Portal에서 배포 완료 알림에 표시되는 시간보다 Kubernetes 클러스터에서 더 오래 실행될 수 있는 비동기 작업입니다. "배포 완료"라는 알림이 표시된 후 5분 이상 기다린 다음 사용자 지정 위치 생성(다음 단계)을 시도합니다. Kubernetes 클러스터에 대한 액세스 권한이 있는 경우 bash 세션에서 다음 명령을 실행하여 Event Grid 브로커 및 Event Grid 운영자 Pod가 실행 중(설치가 완료되었음을 나타냄) 상태인지 확인할 수 있습니다.

    ```bash
    kubectl get pods -n \<release-namespace-name\>
    ```

    샘플 출력은 다음과 같습니다.

    ```bash
    NAME                                  READY   STATUS    RESTARTS   AGE
    eventgrid-broker-568f75976-wxkd2      1/1     Running   0          2m28s
    eventgrid-operator-6c4c6c675d-ttjv5   1/1     Running   0          2m28s    
    ```

    > [!IMPORTANT]
    > Event Grid 토픽을 배포하기 전에 사용자 지정 위치를 만들어야 합니다. 사용자 지정 위치를 만들려면 "배포 완료" 알림이 표시된 후 5분 후에 하단에 있는 **컨텍스트** 페이지를 선택할 수 있습니다. 또는 [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ExtendedLocation%2FCustomLocations)을 사용하여 사용자 지정 위치를 만들 수 있습니다. 자세한 내용은 [사용자 지정 위치 설명서](../../azure-arc/kubernetes/custom-locations.md)를 참조하세요.
1. 배포가 성공하면 Event Grid 확장에 제공한 이름으로 **확장** 페이지에서 항목을 볼 수 있습니다. **설치 상태가** **보류 중** 으로 표시되면 몇 분 정도 기다린 후 도구 모음에서 **새로 고침** 을 선택합니다. 

    :::image type="content" source="./media/install-k8s-extension/extension-installed.png" alt-text="Event Grid 확장 - 설치됨":::   

## <a name="install-using-azure-cli"></a>Azure CLI를 사용하여 설치

1. 셸 세션을 시작합니다. 컴퓨터에서 세션을 시작하거나 [https://shell.azure.com](https://shell.azure.com)에 대한 브라우저를 열 수 있습니다.
1. 구성 파일 ``protected-settings-extension.json``을 만듭니다. 이 파일은 Event Grid 확장을 만들 때 매개 변수로 전달됩니다.

   다음 명령 및 각 구성 줄에서 ``filename`` 파일을 공용 인증서, CA 인증서 또는 운영자(클라이언트)나 브로커(서버)에 대한 키가 포함된 이름으로 바꿉니다. 제공된 모든 인증서는 줄 바꿈 없이 base64로 인코딩되어야 합니다. 따라서 ``base64 --wrap=0`` 명령을 사용합니다. 

    ```bash
    echo "{ 
        \"eventgridoperator.identityCert.base64EncodedIdentityCert\":\"$(base64 <filename> --wrap=0)\",
        \"eventgridoperator.identityCert.base64EncodedIdentityKey\":\"$(base64 <filename> --wrap=0)\",
        \"eventgridoperator.identityCert.base64EncodedIdentityCaCert\":\"$(base64 <filename> --wrap=0)\",
        \"eventgridbroker.service.tls.base64EncodedServerCert\":  \"$(base64 <filename> --wrap=0)\" ,
        \"eventgridbroker.service.tls.base64EncodedServerKey\":  \"$(base64 <filename> --wrap=0)\" ,
        \"eventgridbroker.service.tls.base64EncodedServerCaCert\":  \"$(base64 <filename> --wrap=0)\" 
    }" > protected-settings-extension.json 
    ```
    
    예를 들어 브로커의 공용 인증서(위의 첫 번째 구성 항목)가 ``client.cer``인 경우 첫 번째 구성 행은 다음과 같아야 합니다.

    ```bash
    \"eventgridoperator.identityCert.base64EncodedIdentityCert\":\"$(base64 client.cer --wrap=0)\",    
    ```

1. 구성 파일 ``settings-extension.json``을 만듭니다. 이 파일은 Event Grid 확장을 만들 때 매개 변수로 전달됩니다.
    > [!IMPORTANT]
    > ``ServiceAccount`` 및 ``serviceType``에 대한 값은 변경할 수 없습니다. 미리 보기 버전에서 Kubernetes 서비스 유형은 ``ClusterIP``만 지원됩니다.

    ``storageClassName``의 경우, 브로커에 사용할 저장소 클래스를 제공하고 Kubernetes 배포에서 지원합니다. 예를 들어 AKS를 사용하는 경우 Azure Standard Storage를 사용하는 `azurefile        `을 사용할 수 있습니다. AKS에서 지원하는 미리 정의된 스토리지 등급에 대한 자세한 내용은 [AKS의 스토리지 등급](../../aks/concepts-storage.md#storage-classes)을 참조하세요. 다른 Kubernetes 배포를 사용하는 경우 지원되는 미리 정의된 저장소 클래스 또는 자체 제공 방법은 Kubernetes 배포 설명서를 참조하세요.

    [Prometheus 표시 형식](https://prometheus.io/docs/instrumenting/exposition_formats/)으로 토픽 및 이벤트 구독에 대한 메트릭 항목을 사용하려면 ``reporterType``을 ``prometheus``로 설정합니다.  

    > [!IMPORTANT] 
    > 미리 보기 버전에서 Prometheus 클라이언트를 사용하는 것은 메트릭을 가져오는 데 지원되는 유일한 메커니즘입니다. 

    ```bash
    echo "{
        \"Microsoft.CustomLocation.ServiceAccount\":\"eventgrid-operator\",
        \"eventgridbroker.service.serviceType\": \"ClusterIP\",
        \"eventgridbroker.dataStorage.storageClassName\": \"<storage_class_name>\",
        \"eventgridbroker.diagnostics.metrics.reporterType\":\"prometheus\"
    }" > settings-extension.json
    ```
    
1. 클러스터에 Event Grid 구성 요소를 설치하는 Kubernetes 확장을 만듭니다. 

   매개 변수 ``cluster-name`` 및 ``resource-group``의 경우 [클러스터를 Azure Arc에 연결](../../azure-arc/kubernetes/quickstart-connect-cluster.md)했을 때 제공한 이름을 동일하게 사용해야 합니다.

   ``release-namespace``는 Event Grid 구성 요소가 배포될 네임스페이스입니다. 기본값은 **eventgrid-system** 입니다. 기본값을 재정의하는 값을 제공할 수 있습니다. 예를 들어 클러스터에 배포된 모든 Azure Arc 지원 서비스에 대한 단일 네임스페이스를 사용할 수 있습니다. 제공된 네임스페이스가 없으면 자동으로 만들어집니다.

    > [!IMPORTANT]
    > 미리 보기 버전에서 ``cluster``는 Event Grid 확장을 만들거나 업데이트할 때 지원되는 유일한 범위입니다. 즉, 서비스는 Kubernetes 클러스터에서 Event Grid 확장의 단일 인스턴스만 지원합니다. 네임스페이스 범위 배포는 아직 지원되지 않습니다. 확장 범위에 대한 자세한 내용은 [확장 인스턴스 만들기](../../azure-arc/kubernetes/extensions.md#create-extensions-instance)를 참조하고 ``scope``를 검색합니다.

    ```azurecli-interactive
    az k8s-extension create --cluster-type connectedClusters --cluster-name <connected_cluster_name> --resource-group <resource_group_of_connected_cluster> --name <event_grid_extension_name> --extension-type Microsoft.EventGrid --scope cluster --auto-upgrade-minor-version true --release-train Stable --release-namespace <namespace_name> --configuration-protected-settings-file protected-settings-extension.json --configuration-settings-file settings-extension.json    
    ```
1. Event Grid 확장이 설치되었는지 확인합니다.

    ```azurecli-interactive
    az k8s-extension show  --cluster-type connectedClusters --cluster-name <connected_cluster_name> --resource-group <resource_group_of_connected_cluster> --name <event_grid_extension_name>
    ```

    Event Grid 확장 구성 요소가 배포된 경우 ``installedState`` 속성은 ``Installed``여야 합니다. 

### <a name="custom-location"></a>사용자 지정 위치

> [!IMPORTANT]
> Event Grid 토픽을 배포하기 전에 사용자 지정 위치를 만들어야 합니다. [Azure Portal](../../azure-arc/kubernetes/custom-locations.md#create-custom-location)을 사용하여 사용자 지정 위치를 만들 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="azure-arc-connect-cluster-issues"></a>Azure Arc 연결 클러스터 문제

**문제**: **Azure Arc** 로 이동하고 왼쪽 메뉴에서 **Kubernetes 클러스터** 를 클릭하면 표시된 페이지에 Event Grid를 설치할 Kubernetes 클러스터가 표시되지 않습니다.

**해결 방법**: Kubernetes 클러스터가 Azure에 등록되지 않았습니다. [기존 Kubernetes 클러스터를 Azure Arc에 연결](../../azure-arc/kubernetes/quickstart-connect-cluster.md) 문서의 단계를 따릅니다. 이 단계에서 문제가 발생하면 Azure Arc 지원 Kubernetes 팀에 [지원 요청](#getting-support)을 제출합니다.

### <a name="event-grid-extension-issues"></a>Event Grid 확장 문제

**문제**: "Event Grid 확장"을 설치하려고 하면 다음 메시지가 표시됩니다. "**잘못된 작업** - Event Grid의 인스턴스가 연결된 이 Kubernetes 클러스터에 이미 설치되어 있습니다. Event Grid 확장은 클러스터 수준에서 범위가 지정됩니다. 즉, 하나의 인스턴스만 클러스터에 설치할 수 있습니다."
    
**설명**: Event Grid가 이미 설치되어 있습니다. 미리 보기 버전의 Event Grid는 클러스터에 배포된 하나의 Event Grid 확장 인스턴스만 지원합니다.


## <a name="next-steps"></a>다음 단계
[사용자 지정 위치를 만든](../../azure-arc/kubernetes/custom-locations.md) 다음, 빠른 시작 [Kubernetes에서 Azure Event Grid를 사용하여 클라우드 이벤트를 웹후크로 라우팅](create-topic-subscription.md)의 지침을 따릅니다.
