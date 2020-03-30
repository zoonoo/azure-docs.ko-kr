---
title: 방화벽 액세스 규칙
description: REST API 및 저장소 끝점 도메인 이름 또는 서비스별 IP 주소 범위에 대한 액세스를 허용하여 방화벽 뒤에서 Azure 컨테이너 레지스트리에 액세스하도록 규칙을 구성합니다.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168010"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>방화벽 뒤의 Azure 컨테이너 레지스트리에 액세스하도록 규칙을 구성합니다.

이 문서에서는 Azure 컨테이너 레지스트리에 대한 액세스를 허용하도록 방화벽에서 규칙을 구성하는 방법을 설명합니다. 예를 들어 방화벽 또는 프록시 서버 뒤에 있는 Azure IoT Edge 장치는 컨테이너 이미지를 가져오기 위해 컨테이너 레지스트리에 액세스해야 할 수 있습니다. 또는 온-프레미스 네트워크의 잠긴 서버에서 이미지를 푸시하기 위해 액세스해야 할 수 있습니다.

대신 Azure 가상 네트워크 또는 공용 IP 주소 범위 내에서만 컨테이너 레지스트리에 인바운드 네트워크 액세스 규칙을 구성하려면 [가상 네트워크에서 Azure 컨테이너 레지스트리에 대한 액세스 제한을](container-registry-vnet.md)참조하십시오.

## <a name="about-registry-endpoints"></a>레지스트리 끝점 소개

이미지 또는 기타 아티팩트를 Azure 컨테이너 레지스트리로 가져오거나 푸시하려면 Docker 데몬과 같은 클라이언트가 두 개의 고유한 끝점을 사용하여 HTTPS를 통해 상호 작용해야 합니다.

* **레지스트리 REST API 끝점** - 인증 및 레지스트리 관리 작업은 레지스트리의 공용 REST API 끝점을 통해 처리됩니다. 이 끝점은 레지스트리의 로그인 서버 이름 또는 연결된 IP 주소 범위입니다. 

* **저장소 끝점** - Azure는 각 레지스트리를 대신하여 Azure Storage 계정의 [Blob 저장소를 할당하여](container-registry-storage.md) 컨테이너 이미지 및 기타 아티팩트에 대한 데이터를 관리합니다. 클라이언트가 Azure 컨테이너 레지스트리의 이미지 계층에 액세스하면 레지스트리에서 제공하는 저장소 계정 끝점을 사용하여 요청을 합니다.

레지스트리가 [지리적으로 복제된](container-registry-geo-replication.md)경우 클라이언트는 특정 지역 또는 여러 복제된 리전에서 REST 및 저장소 끝점과 상호 작용해야 할 수 있습니다.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>REST 및 저장소 도메인 이름에 대한 액세스 허용

* **REST 엔드포인트** - 정규화된 레지스트리 로그인 서버 이름에 대한 액세스 허용(예:`myregistry.azurecr.io`
* **저장소(데이터) 끝점** - 와일드카드를 사용하여 모든 Azure Blob 저장소 계정에 대한 액세스 허용`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>IP 주소 범위에서 액세스 허용

조직에 특정 IP 주소 또는 주소 범위에만 액세스를 허용하는 정책이 있는 경우 [Azure IP 범위 및 서비스 태그( 퍼블릭 클라우드)를](https://www.microsoft.com/download/details.aspx?id=56519)다운로드합니다.

액세스를 허용해야 하는 ACR REST 엔드포인트 IP 범위를 찾으려면 JSON 파일에서 **AzureContainerRegistry를** 검색합니다.

> [!IMPORTANT]
> Azure 서비스에 대한 IP 주소 범위가 변경될 수 있으며 업데이트는 매주 게시됩니다. JSON 파일을 정기적으로 다운로드하고 액세스 규칙에 필요한 업데이트를 합니다. 시나리오에서 Azure 가상 네트워크에서 네트워크 보안 그룹 규칙을 구성하여 Azure 컨테이너 레지스트리에 액세스하는 경우 대신 **AzureContainerRegistry** [서비스 태그를](#allow-access-by-service-tag) 사용합니다.
>

### <a name="rest-ip-addresses-for-all-regions"></a>모든 리전의 REST IP 주소

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

**AzureContainer레지스트리.오스트레일리아 동부**와 같은 특정 지역을 검색합니다.

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

### <a name="storage-ip-addresses-for-all-regions"></a>모든 리전의 스토리지 IP 주소

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

**Storage.AustraliaCentral 과**같은 특정 지역을 검색합니다.

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

Azure 가상 네트워크에서 네트워크 보안 규칙을 사용하여 가상 컴퓨터와 같은 리소스에서 컨테이너 레지스트리로 트래픽을 필터링합니다. Azure 네트워크 규칙 의 생성을 단순화하려면 **AzureContainerRegistry** [서비스 태그를](../virtual-network/security-overview.md#service-tags)사용합니다. 서비스 태그는 전역또는 Azure 리전에 따라 Azure 서비스에 액세스하는 IP 주소 접두사 그룹을 나타냅니다. 주소가 변경되면 태그가 자동으로 업데이트됩니다. 

예를 들어 대상 **AzureContainerRegistry를** 사용하여 아웃바운드 네트워크 보안 그룹 규칙을 만들어 Azure 컨테이너 레지스트리에 대한 트래픽을 허용합니다. 특정 지역에서만 서비스 태그에 대한 액세스를 허용하려면 다음 형식으로 영역을 **지정합니다.** [*지역 이름].*

## <a name="configure-client-firewall-rules-for-mcr"></a>MCR에 대한 클라이언트 방화벽 규칙 구성

방화벽 뒤에서 MCR(Microsoft 컨테이너 레지스트리)에 액세스해야 하는 경우 [MCR 클라이언트 방화벽 규칙을](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)구성하는 지침을 참조하세요. MCR은 Windows 서버 이미지와 같이 Microsoft에서 게시한 모든 도커 이미지의 기본 레지스트리입니다.

## <a name="next-steps"></a>다음 단계

* 네트워크 [보안을 위한 Azure 모범 사례에](../security/fundamentals/network-best-practices.md) 대해 자세히 알아보기

* Azure 가상 네트워크의 [보안 그룹에](/azure/virtual-network/security-overview) 대해 자세히 알아보기



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

