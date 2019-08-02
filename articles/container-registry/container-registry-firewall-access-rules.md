---
title: Azure Container Registry에 액세스 하기 위한 방화벽 규칙
description: 방화벽 뒤에서 Azure container registry에 액세스 하는 규칙을 구성 합니다.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/17/2019
ms.author: danlep
ms.openlocfilehash: 285d5592ce667f7b4d84f26d67a00af0698c2967
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620962"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>방화벽 뒤에 있는 Azure container registry에 액세스 하는 규칙 구성

이 문서에서는 Azure container registry에 대 한 액세스를 허용 하도록 방화벽에 대 한 규칙을 구성 하는 방법을 설명 합니다. 예를 들어 방화벽 또는 프록시 서버 뒤에 있는 Azure IoT Edge 장치는 컨테이너 레지스트리에 액세스 하 여 컨테이너 이미지를 가져와야 할 수 있습니다. 또는 온-프레미스 네트워크의 잠긴 서버에서 이미지를 푸시하는 액세스 권한이 필요할 수 있습니다.

대신 Azure virtual network 또는 공용 IP 주소 범위 내 에서만 액세스를 허용 하도록 컨테이너 레지스트리에서 인바운드 네트워크 액세스 규칙을 구성 하려면 [가상 네트워크에서 azure container registry에 대 한 액세스 제한](container-registry-vnet.md)을 참조 하세요.

## <a name="about-registry-endpoints"></a>레지스트리 끝점 정보

이미지 또는 기타 아티팩트를 Azure container registry로 끌어오거나 푸시하는 경우 Docker 데몬 같은 클라이언트는 두 개의 고유한 끝점을 사용 하 여 HTTPS를 통해 상호 작용 해야 합니다.

* **레지스트리 REST API 끝점** -인증 및 레지스트리 관리 작업은 레지스트리의 공용 REST API 끝점을 통해 처리 됩니다. 이 끝점은 레지스트리의 로그인 서버 URL 또는 연결 된 IP 주소 범위입니다. 

* **저장소 끝점** -azure는 컨테이너 이미지 및 기타 아티팩트를 관리 하기 위해 각 레지스트리를 대신 하 여 azure storage 계정에 [blob 저장소를 할당](container-registry-storage.md) 합니다. 클라이언트는 Azure container registry의 이미지 계층에 액세스할 때 레지스트리에서 제공 하는 저장소 계정 끝점을 사용 하 여 요청을 수행 합니다.

레지스트리가 [지역에서 복제](container-registry-geo-replication.md)되는 경우 클라이언트는 특정 지역 또는 여러 복제 된 지역에서 REST 및 storage 끝점과 상호 작용 해야 할 수 있습니다.

## <a name="allow-access-to-rest-and-storage-urls"></a>REST 및 저장소 Url에 대 한 액세스 허용

* **REST 끝점** -레지스트리 서버 URL에 대 한 액세스를 허용 합니다 (예:).`myregistry.azurecr.io`
* **저장소 끝점** -와일드 카드를 사용 하 여 모든 Azure blob Storage 계정에 대 한 액세스를 허용 합니다.`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>IP 주소 범위에의 한 액세스 허용

특정 IP 주소에 대 한 액세스를 허용 해야 하는 경우 [AZURE IP 범위 및 서비스 태그 – 공용 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드 합니다.

ACR REST 끝점 IP 범위를 찾으려면 JSON 파일에서 **AzureContainerRegistry** 를 검색 합니다.

> [!IMPORTANT]
> Azure 서비스에 대 한 IP 주소 범위가 변경 될 수 있으며, 매주 업데이트가 게시 됩니다. JSON 파일을 정기적으로 다운로드 하 고 액세스 규칙에 필요한 업데이트를 수행 합니다. 시나리오에 Azure 가상 네트워크에서 Azure Container Registry 액세스 하기 위해 네트워크 보안 그룹 규칙을 구성 하는 작업이 포함 된 경우 **AzureContainerRegistry** [service 태그](#allow-access-by-service-tag) 를 대신 사용 합니다.
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

## <a name="next-steps"></a>다음 단계

* [네트워크 보안에 대 한 Azure 모범 사례](../security/azure-security-network-security-best-practices.md) 알아보기

* Azure virtual network의 [보안 그룹](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview.md) 에 대 한 자세한 정보



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

