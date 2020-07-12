---
title: 방화벽 액세스 규칙
description: (“허용 목록 작성”) REST API 및 데이터 엔드포인트 도메인 이름 또는 서비스별 IP 주소 범위에 대한 액세스를 허용하여 방화벽 뒤에서 Azure 컨테이너 레지스트리에 액세스하기 위한 규칙을 구성합니다.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 679dbcaf30653b855d35825f94e93f87ac68c322
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246982"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>방화벽 뒤에서 Azure 컨테이너 레지스트리에 액세스하기 위한 규칙 구성

이 문서에서는 Azure 컨테이너 레지스트리에 대한 액세스를 허용하도록 방화벽 규칙을 구성하는 방법을 설명합니다. 예를 들어 방화벽 또는 프록시 서버 뒤에 있는 Azure IoT Edge 디바이스가 컨테이너 레지스트리에 액세스하여 컨테이너 이미지를 풀해야 할 수 있습니다. 또는 온-프레미스 네트워크의 잠긴 서버에서 이미지를 푸시하기 위한 액세스 권한이 필요할 수 있습니다.

Azure 가상 네트워크 내에서만 컨테이너 레지스트리에 대한 인바운드 네트워크 액세스를 구성하려는 경우 [Azure 컨테이너 레지스트리에 대한 Azure Private Link 구성](container-registry-private-link.md)을 참조하세요.

## <a name="about-registry-endpoints"></a>레지스트리 엔드포인트 정보

이미지 또는 기타 아티팩트를 Azure 컨테이너 레지스트리로 풀하거나 푸시하려면 Docker 디먼 같은 클라이언트는 HTTPS를 통해 두 개의 고유한 엔드포인트와 상호 작용해야 합니다. 방화벽 뒤에서 레지스트리에 액세스하는 클라이언트의 경우 두 엔드포인트에 대한 액세스 규칙을 구성해야 합니다. 두 엔드포인트 모두 포트 443을 통해 연결됩니다.

* **레지스트리 REST API 엔드포인트** - 인증 및 레지스트리 관리 작업은 레지스트리의 공용 REST API 엔드포인트를 통해 처리됩니다. 이 엔드포인트는 레지스트리의 로그인 서버 이름입니다. 예: `myregistry.azurecr.io`

* **스토리지(데이터) 엔드포인트** - Azure는 컨테이너 이미지 및 다른 아티팩트의 데이터를 관리하기 위해 각 레지스트리를 대신하여 Azure Storage 계정에 [Blob Storage를 할당](container-registry-storage.md)합니다. 클라이언트는 Azure 컨테이너 레지스트리의 이미지 계층에 액세스할 때 레지스트리에서 제공하는 스토리지 계정 엔드포인트를 사용하여 요청을 수행합니다.

레지스트리가 [지역에서 복제](container-registry-geo-replication.md)된 경우 클라이언트는 특정 지역 또는 여러 복제된 지역에서 데이터 엔드포인트와 상호 작용해야 할 수 있습니다.

## <a name="allow-access-to-rest-and-data-endpoints"></a>REST 및 데이터 엔드포인트에 대한 액세스 허용

* **REST 엔드포인트** - 정규화된 레지스트리 로그인 서버 이름 `<registry-name>.azurecr.io` 또는 연결된 IP 주소 범위에 대한 액세스를 허용합니다.
* **스토리지(데이터) 엔드포인트** - 와일드카드 `*.blob.core.windows.net` 또는 연결된 IP 주소 범위를 사용하여 모든 Azure Blob Storage 계정에 대한 액세스를 허용합니다.
> [!NOTE]
> Azure Container Registry는 [전용 데이터 엔드포인트](#enable-dedicated-data-endpoints)를 도입하여 레지스트리 스토리지에 대한 클라이언트 방화벽 규칙의 범위를 확고하게 지정할 수 있습니다. 필요에 따라 `<registry-name>.<region>.data.azurecr.io` 폼을 사용하여 레지스트리가 위치하거나 복제된 모든 지역에서 데이터 엔드포인트를 사용하도록 설정합니다.

## <a name="allow-access-by-ip-address-range"></a>IP 주소 범위를 기준으로 액세스 허용

조직에 특정 IP 주소 또는 주소 범위에 대한 액세스만 허용하는 정책이 있는 경우 [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드합니다.

액세스를 허용해야 하는 ACR REST 엔드포인트 IP 범위를 찾으려면 JSON 파일에서 **AzureContainerRegistry**를 검색합니다.

> [!IMPORTANT]
> Azure 서비스에 대한 IP 주소 범위가 변경될 수 있으며, 매주 업데이트가 게시됩니다. JSON 파일을 정기적으로 다운로드하고 액세스 규칙에 필요한 업데이트를 수행합니다. Azure 가상 네트워크에서 네트워크 보안 그룹 규칙을 구성하는 것이 시나리오에 포함되거나 Azure Firewall을 사용하는 경우 **AzureContainerRegistry** [서비스 태그](#allow-access-by-service-tag)를 대신 사용합니다.
>

### <a name="rest-ip-addresses-for-all-regions"></a>모든 지역의 REST IP 주소

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>특정 지역의 REST IP 주소

**AzureContainerRegistry.AustraliaEast**와 같은 특정 지역을 검색합니다.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>모든 지역의 스토리지 IP 주소

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>특정 지역의 스토리지 IP 주소

**Storage.AustraliaCentral**과 같은 특정 지역을 검색합니다.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>서비스 태그를 기준으로 액세스 허용

Azure 가상 네트워크에서 네트워크 보안 규칙을 사용하여 가상 머신과 같은 리소스에서 컨테이너 레지스트리로의 트래픽을 필터링합니다. Azure 네트워크 규칙 만들기를 간소화하려면 **AzureContainerRegistry** [서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용합니다. 서비스 태그는 전역으로 또는 Azure 지역별로 Azure 서비스에 액세스하기 위한 IP 주소 접두사 그룹을 나타냅니다. 주소가 변경되면 태그가 자동으로 업데이트됩니다. 

예를 들어 대상 **AzureContainerRegistry**를 사용하여 Azure 컨테이너 레지스트리로의 트래픽을 허용하는 아웃바운드 네트워크 보안 그룹 규칙을 만듭니다. 특정 지역에서만 서비스 태그에 대한 액세스를 허용하려면 **AzureContainerRegistry**.[*지역 이름*] 형식으로 지역을 지정합니다.

## <a name="enable-dedicated-data-endpoints"></a>전용 데이터 엔드포인트 사용 

> [!WARNING]
> 이전에 기존 `*.blob.core.windows.net` 엔드포인트에 대한 클라이언트 방화벽 액세스를 구성한 경우에는 전용 데이터 엔드포인트로 전환하면 클라이언트 연결에 영향을 주어 풀 오류가 발생합니다. 클라이언트에 일관된 액세스 권한이 있는지 확인하려면 클라이언트 방화벽 규칙에 새 데이터 엔드포인트 규칙을 추가합니다. 추가를 완료하면 Azure CLI 또는 다른 도구를 사용하여 레지스트리에 대한 전용 데이터 엔드포인트를 사용하도록 설정합니다.

전용 데이터 엔드포인트는 **프리미엄** 컨테이너 레지스트리 서비스 계층의 선택적 기능입니다. 레지스트리 서비스 계층 및 제한에 대한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요. 

Azure Portal 또는 Azure CLI를 사용하여 전용 데이터 엔드포인트를 사용하도록 설정할 수 있습니다. 데이터 엔드포인트는 `<registry-name>.<region>.data.azurecr.io` 지역 패턴을 따릅니다. 지역에서 복제된 레지스트리에서 데이터 엔드포인트를 사용하도록 설정하면 모든 복제본 지역에서 엔드포인트를 사용할 수 있습니다.

### <a name="portal"></a>포털

포털을 사용하여 데이터 엔드포인트를 사용하도록 설정하려면 다음을 수행합니다.

1. 컨테이너 레지스트리로 이동합니다.
1. **네트워킹** > **공용 액세스**를 선택합니다.
1. **전용 데이터 엔드포인트 사용** 확인란을 선택합니다.
1. **저장**을 선택합니다.

데이터 엔드포인트 또는 엔드포인트가 포털에 표시됩니다.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="포털의 전용 데이터 엔드포인트":::

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 데이터 엔드포인트를 사용하도록 설정하려면 Azure CLI 버전 2.4.0 이상을 사용합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음 [az acr update][az-acr-update] 명령은 레지스트리 *myregistry*에서 전용 데이터 엔드포인트를 사용하도록 설정합니다. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

데이터 엔드포인트를 보려면 [az acr show-endpoints][az-acr-show-endpoints] 명령을 사용합니다.

```azurecli
az acr show-endpoints --name myregistry
```

데모용 출력에서는 두 개의 지역 엔드포인트가 표시됩니다.

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

레지스트리에 대한 전용 데이터 엔드포인트를 설정한 후 데이터 엔드포인트에 대한 클라이언트 방화벽 액세스 규칙을 사용하도록 설정할 수 있습니다. 모든 필수 레지스트리 지역에 대해 데이터 엔드포인트 액세스 규칙을 사용하도록 설정합니다.

## <a name="configure-client-firewall-rules-for-mcr"></a>MCR에 대한 클라이언트 방화벽 규칙 구성

방화벽 뒤에서 MCR(Microsoft Container Registry)에 액세스해야 하는 경우 [MCR 클라이언트 방화벽 규칙](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)을 구성하는 방법에 대한 지침을 참조하세요. MCR은 Windows Server 이미지와 같은 Microsoft에 게시된 모든 Docker 이미지의 기본 레지스트리입니다.

## <a name="next-steps"></a>다음 단계

* [네트워크 보안에 대한 Azure 모범 사례](../security/fundamentals/network-best-practices.md)에 대해 알아보기

* Azure 가상 네트워크의 [보안 그룹](../virtual-network/security-overview.md)에 대해 자세히 알아보기

* 컨테이너 레지스트리에 대한 [Private Link](container-registry-private-link.md) 설정에 대해 자세히 알아보기

* Azure Container Registry의 [전용 데이터 엔드포인트](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/)에 대해 자세히 알아보기



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints
