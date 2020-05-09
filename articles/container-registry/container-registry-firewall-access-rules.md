---
title: 방화벽 액세스 규칙
description: ("허용 목록") REST API 및 데이터 끝점 도메인 이름이 나 서비스별 IP 주소 범위에 대 한 액세스를 허용 하 여 방화벽 뒤에서 Azure container registry에 액세스 하는 규칙을 구성 합니다.
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: b3560fe769a97c8d3a4e5a3580d42d7c0b3a3f08
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978351"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>방화벽 뒤에 있는 Azure container registry에 액세스 하는 규칙 구성

이 문서에서는 Azure container registry에 대 한 액세스를 허용 하도록 방화벽에 대 한 규칙을 구성 하는 방법을 설명 합니다. 예를 들어 방화벽 또는 프록시 서버 뒤에 있는 Azure IoT Edge 장치는 컨테이너 레지스트리에 액세스 하 여 컨테이너 이미지를 가져와야 할 수 있습니다. 또는 온-프레미스 네트워크의 잠긴 서버에서 이미지를 푸시하는 액세스 권한이 필요할 수 있습니다.

대신 azure virtual network 내에서 컨테이너 레지스트리에 대 한 인바운드 네트워크 액세스를 구성 하려는 경우 [azure container registry에 대 한 Azure 개인 링크 구성](container-registry-private-link.md)을 참조 하세요.

## <a name="about-registry-endpoints"></a>레지스트리 끝점 정보

이미지 또는 기타 아티팩트를 Azure container registry로 끌어오거나 푸시하는 경우 Docker 데몬 같은 클라이언트는 두 개의 고유한 끝점을 사용 하 여 HTTPS를 통해 상호 작용 해야 합니다. 방화벽 뒤에서 레지스트리에 액세스 하는 클라이언트의 경우 두 끝점에 대 한 액세스 규칙을 구성 해야 합니다.

* **레지스트리 REST API 끝점** -인증 및 레지스트리 관리 작업은 레지스트리의 공용 REST API 끝점을 통해 처리 됩니다. 이 끝점은 레지스트리의 로그인 서버 이름입니다. 예: `myregistry.azurecr.io`

* **저장소 (데이터) 끝점** -Azure는 컨테이너 이미지 및 다른 아티팩트에 대 한 데이터를 관리 하기 위해 각 레지스트리를 대신 하 여 Azure Storage 계정에 [blob 저장소를 할당](container-registry-storage.md) 합니다. 클라이언트는 Azure container registry의 이미지 계층에 액세스할 때 레지스트리에서 제공 하는 저장소 계정 끝점을 사용 하 여 요청을 수행 합니다.

레지스트리가 [지역에서 복제](container-registry-geo-replication.md)되는 경우 클라이언트는 특정 지역 또는 여러 복제 된 지역에서 데이터 끝점과 상호 작용 해야 할 수 있습니다.

## <a name="allow-access-to-rest-and-data-endpoints"></a>REST 및 데이터 끝점에 대 한 액세스 허용

* **REST 끝점** -정규화 된 레지스트리 로그인 서버 이름 `<registry-name>.azurecr.io`또는 연결 된 IP 주소 범위에 대 한 액세스를 허용 합니다.
* **저장소 (데이터) 끝점** -와일드 카드 `*.blob.core.windows.net`또는 연결 된 IP 주소 범위를 사용 하 여 모든 Azure blob Storage 계정에 대 한 액세스를 허용 합니다.
> [!NOTE]
> Azure Container Registry는 [전용 데이터 끝점](#enable-dedicated-data-endpoints-preview) (미리 보기)을 도입 하 여 레지스트리 저장소에 대 한 클라이언트 방화벽 규칙의 범위를 엄격 하 게 지정할 수 있습니다. 필요에 따라 형식을 `<registry-name>.<region>.data.azurecr.io`사용 하 여 레지스트리가 있거나 복제 된 모든 지역에서 데이터 끝점을 사용 하도록 설정 합니다.

## <a name="allow-access-by-ip-address-range"></a>IP 주소 범위에의 한 액세스 허용

조직에 특정 IP 주소 또는 주소 범위에 대 한 액세스만 허용 하는 정책이 있는 경우 [AZURE IP 범위 및 서비스 태그 – 공용 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드 합니다.

액세스를 허용 해야 하는 ACR REST 끝점 IP 범위를 찾으려면 JSON 파일에서 **AzureContainerRegistry** 를 검색 합니다.

> [!IMPORTANT]
> Azure 서비스에 대 한 IP 주소 범위가 변경 될 수 있으며, 매주 업데이트가 게시 됩니다. JSON 파일을 정기적으로 다운로드 하 고 액세스 규칙에 필요한 업데이트를 수행 합니다. 시나리오에 Azure 가상 네트워크에서 네트워크 보안 그룹 규칙을 구성 하거나 Azure 방화벽을 사용 하는 경우 **AzureContainerRegistry** [service 태그](#allow-access-by-service-tag) 를 대신 사용 합니다.
>

### <a name="rest-ip-addresses-for-all-regions"></a>모든 지역에 대 한 REST IP 주소

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

### <a name="rest-ip-addresses-for-a-specific-region"></a>특정 지역에 대 한 REST IP 주소

특정 지역 (예: **AzureContainerRegistry. AustraliaEast**)을 검색 합니다.

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

### <a name="storage-ip-addresses-for-all-regions"></a>모든 지역에 대 한 저장소 IP 주소

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

### <a name="storage-ip-addresses-for-specific-regions"></a>특정 지역에 대 한 저장소 IP 주소

특정 지역 (예: **AustraliaCentral**)을 검색 합니다.

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

## <a name="allow-access-by-service-tag"></a>서비스 태그로 액세스 허용

Azure 가상 네트워크에서 네트워크 보안 규칙을 사용 하 여 가상 머신과 같은 리소스에서 컨테이너 레지스트리로의 트래픽을 필터링 합니다. Azure 네트워크 규칙 만들기를 간소화 하려면 **AzureContainerRegistry** [service 태그](../virtual-network/security-overview.md#service-tags)를 사용 합니다. 서비스 태그는 글로벌 또는 Azure 지역에 따라 Azure 서비스에 액세스 하기 위한 IP 주소 접두사 그룹을 나타냅니다. 주소가 변경 되 면 태그가 자동으로 업데이트 됩니다. 

예를 들어 대상 **AzureContainerRegistry** 를 사용 하 여 Azure container registry에 대 한 트래픽을 허용 하는 아웃 바운드 네트워크 보안 그룹 규칙을 만듭니다. 특정 지역의 서비스 태그에만 액세스를 허용 하려면 다음 형식으로 지역을 지정 합니다. **AzureContainerRegistry**. [*지역 이름*].

## <a name="enable-dedicated-data-endpoints-preview"></a>전용 데이터 끝점 사용 (미리 보기)

> [!WARNING]
> 이전에 기존 `*.blob.core.windows.net` 끝점에 대 한 클라이언트 방화벽 액세스를 구성한 경우 전용 데이터 끝점으로 전환 하면 클라이언트 연결에 영향을 주므로 끌어오기 오류가 발생 합니다. 클라이언트에 일관 된 액세스 권한이 있는지 확인 하려면 클라이언트 방화벽 규칙에 새 데이터 끝점 규칙을 추가 합니다. 완료 되 면 Azure CLI 또는 다른 도구를 사용 하 여 레지스트리에 대 한 전용 데이터 끝점을 사용 하도록 설정 합니다.

전용 데이터 끝점은 **프리미엄** 컨테이너 레지스트리 서비스 계층의 선택적 기능입니다. 레지스트리 서비스 계층 및 제한에 대 한 자세한 내용은 [Azure Container Registry 계층](container-registry-skus.md)을 참조 하세요. Azure CLI를 사용 하 여 데이터 끝점을 사용 하도록 설정 하려면 Azure CLI 버전 2.4.0 이상을 사용 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음 [az acr update][az-acr-update] 명령은 레지스트리 *myregistry*에서 전용 데이터 끝점을 사용 하도록 설정 합니다. 데모용으로 다음과 같은 두 지역에서 레지스트리가 복제 된 것으로 가정 합니다.

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

데이터 끝점은 지역 패턴를 `<registry-name>.<region>.data.azurecr.io`사용 합니다. 데이터 끝점을 보려면 [az acr show-endpoints][az-acr-show-endpoints] 명령을 사용 합니다.

```azurecli
az acr show-endpoints --name myregistry
```

출력:

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

레지스트리에 대 한 전용 데이터 끝점을 설정한 후 데이터 끝점에 대 한 클라이언트 방화벽 액세스 규칙을 사용 하도록 설정할 수 있습니다. 필요한 모든 레지스트리 영역에 대해 데이터 끝점 액세스 규칙을 사용 하도록 설정 합니다.

## <a name="configure-client-firewall-rules-for-mcr"></a>MCR에 대 한 클라이언트 방화벽 규칙 구성

방화벽 뒤에서 MCR (Microsoft Container Registry)에 액세스 해야 하는 경우 [mcr 클라이언트 방화벽 규칙](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)을 구성 하는 지침을 참조 하세요. MCR은 Windows Server 이미지와 같은 모든 Microsoft 게시 docker 이미지의 기본 레지스트리입니다.

## <a name="next-steps"></a>다음 단계

* [네트워크 보안에 대 한 Azure 모범 사례](../security/fundamentals/network-best-practices.md) 알아보기

* Azure virtual network의 [보안 그룹](/azure/virtual-network/security-overview) 에 대 한 자세한 정보

* Azure Container Registry의 [전용 데이터 끝점](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) 에 대 한 자세한 정보



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints

