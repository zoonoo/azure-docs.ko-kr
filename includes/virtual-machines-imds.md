---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: e18c09130fcbcdbb470abc19d76bdf2ccfef0775
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175716"
---
IMDS (Azure Instance Metadata Service)는 현재 실행 중인 가상 머신 인스턴스에 대 한 정보를 제공 합니다. 가상 컴퓨터를 관리 하 고 구성 하는 데 사용할 수 있습니다.
이 정보에는 SKU, 저장소, 네트워크 구성 및 예정 된 유지 관리 이벤트가 포함 됩니다. 사용할 수 있는 데이터의 전체 목록은 [끝점 범주 요약](#endpoint-categories)을 참조 하세요.

IMDS는 Vm (가상 머신) 및 가상 머신 확장 집합 인스턴스의 인스턴스를 실행 하는 데 사용할 수 있습니다. 모든 끝점은 [Azure Resource Manager](/rest/api/resources/)를 사용 하 여 만들고 관리 하는 vm을 지원 합니다. 인스턴스 범주의 증명 된 범주 및 네트워크 부분만 클래식 배포 모델을 사용 하 여 만든 Vm을 지원 합니다. 증명 된 끝점은 제한 된 범위에만 해당 됩니다.

IMDS는 잘 알려진 라우팅할 수 없는 IP 주소 ()에서 사용할 수 있는 REST API입니다 `169.254.169.254` . VM 내 에서만 액세스할 수 있습니다. VM과 IMDS 간의 통신은 호스트를 유지 하지 않습니다.
IMDS를 쿼리할 때 HTTP 클라이언트가 VM 내에서 웹 프록시를 우회 하도록 하 고와 동일 하 게 처리 `169.254.169.254` [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md) 합니다.

## <a name="usage"></a>사용량

### <a name="access-azure-instance-metadata-service"></a>Azure Instance Metadata Service 액세스

IMDS에 액세스 하려면 [Azure Resource Manager](/rest/api/resources/) 또는 [Azure Portal](https://portal.azure.com)에서 VM을 만들고 다음 샘플을 사용 합니다.
더 많은 예제는 [Azure 인스턴스 메타 데이터 샘플](https://github.com/microsoft/azureimds)을 참조 하세요.

인스턴스의 모든 메타 데이터를 검색 하는 샘플 코드는 다음과 같습니다. 특정 데이터 원본에 액세스 하려면 모든 사용 가능한 기능에 대 한 개요는 [끝점 범주](#endpoint-categories) 를 참조 하세요.

**요청**

> [!IMPORTANT]
> 이 예에서는 프록시를 무시 합니다. IMDS를 쿼리할 때 프록시를 무시 **해야** 합니다. 자세한 내용은 [프록시](#proxies) 를 참조 하세요.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01" | jq
```

---

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>보안 및 인증

Instance Metadata Service는 라우팅할 수 없는 IP 주소에 실행 중인 가상 머신 인스턴스 내 에서만 액세스할 수 있습니다. Vm은 자신에 게 관련 된 메타 데이터/기능과 상호 작용 하는 것으로 제한 됩니다. 이 API는 HTTP 전용 이며 호스트를 유지 하지 않습니다.

요청이 IMDS에 직접 사용 되며 원하지 않거나 원치 않는 요청의 리디렉션을 방지 하기 위해 요청을 요청 합니다.
- 헤더를 포함 **해야 합니다** .`Metadata: true`
- 헤더를 포함 **하지** 않아야 합니다. `X-Forwarded-For`

이러한 요구 사항을 **모두** 충족 하지 않는 요청은 서비스에서 거부 됩니다.

> [!IMPORTANT]
> IMDS는 중요 한 데이터의 채널이 **아닙니다** . API는 인증 되지 않고 VM의 모든 프로세스에 대해 열립니다. 이 서비스를 통해 노출되는 정보는 VM 내에서 실행 중인 모든 애플리케이션에 대한 공유 정보로 간주되어야 합니다.

## <a name="proxies"></a>프록시

IMDS는 프록시 뒤에서 사용 하기 위한 것이 **아니며** 지원 되지 않습니다. 대부분의 HTTP 클라이언트는 요청에 대 한 프록시를 사용 하지 않도록 설정할 수 있는 옵션을 제공 하며, IMDS와 통신할 때이 기능을 사용 해야 합니다. 자세한 내용은 클라이언트의 설명서를 참조 하세요.

> [!IMPORTANT]
> 사용자 환경에서 프록시 구성을 알지 못하는 경우에도 **여전히 기본 클라이언트 프록시 설정을 재정의 해야** 합니다. 프록시 구성은 자동으로 검색 될 수 있으며 이러한 구성을 우회 하지 못하면 나중에 컴퓨터의 구성을 변경 해야 하는 경우 중단 위험을 노출 시킬 수 있습니다.

## <a name="rate-limiting"></a>속도 제한

일반적으로 IMDS에 대 한 요청은 초당 5 개의 요청으로 제한 됩니다. 이 임계값을 초과 하는 요청은 429 응답으로 거부 됩니다. [관리 되는 id](#managed-identity) 범주에 대 한 요청은 초당 20 개의 요청 및 5 개의 동시 요청으로 제한 됩니다.

## <a name="http-verbs"></a>HTTP 동사

현재 지원 되는 HTTP 동사는 다음과 같습니다.

| 동사 | Description |
|------|-------------|
| `GET` | 요청 된 리소스를 검색 합니다.

## <a name="parameters"></a>매개 변수

끝점은 필수 및/또는 선택적 매개 변수를 지원할 수 있습니다. 자세한 내용은 해당 끝점에 대 한 [스키마](#schema) 및 설명서를 참조 하세요.

### <a name="query-parameters"></a>쿼리 매개 변수

IMDS 끝점은 HTTP 쿼리 문자열 매개 변수를 지원 합니다. 예를 들면 다음과 같습니다. 

```
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

매개 변수를 지정 합니다.

| Name | 값 |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

쿼리 매개 변수 이름이 중복 된 요청은 거부 됩니다.

### <a name="route-parameters"></a>경로 매개 변수

더 큰 json blob을 반환 하는 일부 끝점의 경우 요청 끝점에 경로 매개 변수를 추가 하 여 응답의 하위 집합으로 필터링 할 수 있습니다. 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Json 개체를 탐색 하는 데 사용 되는 인덱스/키에 해당 하는 매개 변수는 구문 분석 된 표현과 상호 작용 합니다.

예를 들어 `/metatadata/instance` 는 json 개체를 반환 합니다.
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

계산 속성만으로 응답을 필터링 하려는 경우 요청을 보냅니다. 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

마찬가지로, 중첩 된 속성 또는 특정 배열 요소로 필터링 하려면 키를 추가 합니다. 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
속성의 첫 번째 요소로 필터링 하 `Network.interface` 고를 반환 합니다.

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> 리프 노드로 필터링 할 때는 `format=json` 작동 하지 않습니다. `format=text`기본 형식이 json 이므로 이러한 쿼리를 명시적으로 지정 해야 합니다.

## <a name="schema"></a>스키마

### <a name="data-format"></a>데이터 형식

기본적으로 IMDS는 JSON 형식 ()으로 데이터를 반환 `Content-Type: application/json` 합니다. 그러나 응답 필터링을 지 원하는 끝점 ( [경로 매개 변수](#route-parameters)참조)도 형식을 지원 `text` 합니다.

기본이 아닌 응답 형식에 액세스하려면 요청된 형식을 요청의 쿼리 문자열 매개 변수로 지정합니다. 다음은 그 예입니다.

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

Json 응답에서 모든 기본 형식은 형식이 되며 `string` 없거나 적용할 수 없는 값은 항상 포함 되지만 빈 문자열로 설정 됩니다.

### <a name="versioning"></a>버전 관리

IMDS는 버전이 지정 되며 HTTP 요청에서 API 버전을 지정 하는 것은 필수입니다. 이 요구 사항에 대 한 유일한 예외는 사용 가능한 API 버전을 동적으로 검색 하는 데 사용할 수 있는 [버전](#versions) 끝점입니다.

새로운 버전이 추가되더라도 스크립트가 특정 데이터 형식에 종속성이 있는 경우 이전 버전에 여전히 액세스할 수 있습니다.

버전을 지정 하지 않으면 지원 되는 최신 버전의 목록과 함께 오류가 발생 합니다.
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>지원되는 API 버전
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01

### <a name="swagger"></a>Swagger

IMDS에 대 한 전체 Swagger 정의는 다음 위치에서 제공 됩니다. https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>국가별 가용성

서비스는 일반적으로 모든 Azure 클라우드에서 **사용할 수** 있습니다.

## <a name="root-endpoint"></a>루트 끝점

루트 끝점은 `http://169.254.169.254/metadata` 입니다.

## <a name="endpoint-categories"></a>끝점 범주

IMDS API에는 서로 다른 데이터 소스를 나타내는 여러 끝점 범주가 포함 되어 있으며, 각 끝점에는 하나 이상의 끝점이 포함 되어 있습니다. 자세한 내용은 각 범주를 참조 하세요.

| 범주 루트 | 설명 | 도입된 버전 |
|---------------|-------------|--------------------|
| `/metadata/attested` | [증명된 데이터](#attested-data) 참조 | 2018-10-01
| `/metadata/identity` | [IMDS를 통한 관리 되는 id](#managed-identity) 참조 | 2018-02-01
| `/metadata/instance` | [인스턴스 메타 데이터](#instance-metadata) 를 참조 하세요. | 2017-04-02
| `/metadata/loadbalancer` | [IMDS를 통해 Load Balancer 메타 데이터 검색](#load-balancer-metadata) 을 참조 하세요. | 2020-10-01
| `/metadata/scheduledevents` | [IMDS를 통해 Scheduled Events](#scheduled-events) 를 참조 하세요. | 2017-08-01
| `/metadata/versions` | [버전](#versions) 참조 | N/A

## <a name="versions"></a>버전

> [!NOTE]
> 이 기능은 현재 롤아웃 되 고 있는 모든 지역에서 아직 사용할 수 없는 버전 2020-10-01와 함께 출시 되었습니다.

### <a name="list-api-versions"></a>API 버전 나열

지원 되는 API 버전의 집합을 반환 합니다.

```
GET /metadata/versions
```

#### <a name="parameters"></a>매개 변수

없음 (이 끝점은 버전이 없습니다.)

#### <a name="response"></a>응답

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>인스턴스 메타데이터

### <a name="get-vm-metadata"></a>VM 메타 데이터 가져오기

계산, 네트워크 및 저장소를 포함 하 여 VM 인스턴스에 대 한 중요 한 메타 데이터를 노출 합니다. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>매개 변수

| 속성 | 필수/선택 | 설명 |
|------|-------------------|-------------|
| `api-version` | 필수 | 요청을 처리 하는 데 사용 되는 버전입니다.
| `format` | 필드 | `json`응답의 형식 (또는 `text` )입니다. * 참고: 요청 매개 변수를 사용 하는 경우 필요할 수 있습니다.

이 끝점은 [경로 매개 변수](#route-parameters)를 통한 응답 필터링을 지원 합니다.

#### <a name="response"></a>응답

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

스키마 분석:

**컴퓨팅**

| 데이터 | Description | 도입된 버전 |
|------|-------------|--------------------|
| `azEnvironment` | VM이 실행되는 Azure 환경 | 2018-10-01
| `customData` | 이 기능은 현재 사용할 수 없습니다. 이 설명서를 사용할 수 있게 되 면 업데이트 합니다. | 2019-02-01
| `evictionPolicy` | [지점 VM](../articles/virtual-machines/spot-vms.md) 이 제거 되는 방법을 설정 합니다. | 2020-12-01
| `isHostCompatibilityLayerVm` | VM이 호스트 호환성 계층에서 실행 되는지 확인 합니다. | 2020-06-01
| `licenseType` | [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit)라이선스의 유형입니다. AHB 사용 Vm에 대해서만 제공 됩니다. | 2020-09-01
| `location` | VM을 실행하는 Azure 지역 | 2017-04-02
| `name` | VM의 이름 | 2017-04-02
| `offer` | VM 이미지에 대한 제품 정보이며 Azure 이미지 갤러리에서 배포된 이미지에만 있습니다. | 2017-04-02
| `osProfile.adminUsername` | 관리자 계정의 이름을 지정 합니다. | 2020-07-15
| `osProfile.computerName` | 컴퓨터의 이름을 지정 합니다. | 2020-07-15
| `osProfile.disablePasswordAuthentication` | 암호 인증을 사용할 수 없는지 여부를 지정합니다. Linux Vm에 대해서만 제공 됩니다. | 2020-10-01
| `osType` | Linux 또는or Windows | 2017-04-02
| `placementGroupId` | 가상 머신 확장 집합의 [배치 그룹](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | Azure Marketplace 이미지에 해당하는 VM의 이름, 제품 및 게시자를 포함하는 [계획](/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
| `platformUpdateDomain` |  VM을 실행 중인 [업데이트 도메인](../articles/virtual-machines/manage-availability.md) | 2017-04-02
| `platformFaultDomain` | VM을 실행 중인 [장애 도메인](../articles/virtual-machines/manage-availability.md) | 2017-04-02
| `priority` | VM의 우선 순위입니다. 자세한 내용은 [지점 vm](../articles/virtual-machines/spot-vms.md) 을 참조 하세요. | 2020-12-01
| `provider` | VM의 공급자 | 2018-10-01
| `publicKeys` | VM 및 경로에 할당된 [공개 키 컬렉션](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
| `publisher` | VM 이미지 게시자 | 2017-04-02
| `resourceGroupName` | Virtual Machine에 대한 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | 리소스의 [정규화된](/rest/api/resources/resources/getbyid) ID | 2019-03-11
| `sku` | VM 이미지에 해당하는 SKU | 2017-04-02
| `securityProfile.secureBootEnabled` | UEFI 보안 부팅이 VM에서 사용 되는지 여부를 식별 합니다. | 2020-06-01
| `securityProfile.virtualTpmEnabled` | VM에서 TPM (가상 신뢰할 수 있는 플랫폼 모듈)이 사용 되는지 여부를 식별 합니다. | 2020-06-01
| `storageProfile` | 아래 저장소 프로필을 참조 하세요. | 2019-06-01
| `subscriptionId` | Virtual Machine에 대한 Azure 구독 | 2017-08-01
| `tags` | Virtual Machine에 대한 [태그](../articles/azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | 원활한 프로그래매틱 구문 분석을 위해 JSON 배열로 형식이 지정된 태그  | 2019-06-04
| `version` | VM 이미지의 버전 | 2017-04-02
| `vmId` | VM의 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | 가상 머신 확장 집합의 [가상 머신 확장 집합 이름](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [VM 크기](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | 가상 머신의 [가용성 영역](../articles/availability-zones/az-overview.md) | 2017-12-01

**스토리지 프로필**

VM의 저장소 프로필은 이미지 참조, OS 디스크 및 데이터 디스크의 세 가지 범주로 구분 됩니다.

이미지 참조 개체에는 OS 이미지에 대한 다음 정보가 포함되어 있습니다.

| 데이터 | Description |
|------|-------------|
| `id` | 리소스 ID
| `offer` | 플랫폼의 제품 또는 마켓플레이스 이미지
| `publisher` | 이미지 게시자입니다.
| `sku` | 이미지 SKU
| `version` | 플랫폼의 버전 또는 마켓플레이스 이미지

OS 디스크 개체에는 VM에서 사용하는 OS 디스크에 대한 다음 정보가 포함됩니다.

| 데이터 | Description |
|------|-------------|
| `caching` | 캐싱 요구 사항
| `createOption` | VM이 생성된 방법에 대한 정보
| `diffDiskSettings` | 임시 디스크 설정
| `diskSizeGB` | 디스크 크기(GB)
| `image`   | 원본 사용자 이미지 가상 하드 디스크
| `lun`     | 디스크의 논리 단위 번호
| `managedDisk` | 관리 디스크 매개 변수
| `name`    | 디스크 이름
| `vhd`     | 가상 하드 디스크
| `writeAcceleratorEnabled` | writeAccelerator를 디스크에서 사용할 수 있는지 여부

데이터 디스크 배열에는 VM에 연결된 데이터 디스크 목록이 포함됩니다. 각 데이터 디스크 개체에는 다음 정보가 들어 있습니다.

데이터 | Description |
-----|-------------|
| `caching` | 캐싱 요구 사항
| `createOption` | VM이 생성된 방법에 대한 정보
| `diffDiskSettings` | 임시 디스크 설정
| `diskSizeGB` | 디스크 크기(GB)
| `encryptionSettings` | 디스크의 암호화 설정
| `image` | 원본 사용자 이미지 가상 하드 디스크
| `managedDisk` | 관리 디스크 매개 변수
| `name` | 디스크 이름
| `osType` | 디스크에 포함된 OS 유형
| `vhd` | 가상 하드 디스크
| `writeAcceleratorEnabled` | writeAccelerator를 디스크에서 사용할 수 있는지 여부

**Network**

| 데이터 | Description | 도입된 버전 |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | VM의 로컬 IPv4 주소 | 2017-04-02
| `ipv4.publicIpAddress` | VM의 공용 IPv4 주소 | 2017-04-02
| `subnet.address` | VM의 서브넷 주소 | 2017-04-02
| `subnet.prefix` | 서브넷 접두사, 예:24 | 2017-04-02
| `ipv6.ipAddress` | VM의 로컬 IPv6 주소 | 2017-04-02
| `macAddress` | VM MAC 주소 | 2017-04-02


#### <a name="sample-1-tracking-vm-running-on-azure"></a>샘플 1: Azure에서 실행 중인 VM 추적

서비스 공급자는 소프트웨어를 실행 중인 VM의 수를 추적하거나 VM의 고유성을 추적해야 하는 에이전트가 있어야 합니다. VM의 고유 ID를 가져오려면 Instance Metadata Service의 `vmId` 필드를 사용합니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**응답**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>샘플 2: 여러 데이터 복제본 배치

특정 시나리오의 경우 다양한 데이터 복제본 배치가 매우 중요합니다. 예를 들어, [HDFS 복제본 배치](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) 또는 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 를 통한 컨테이너 배치를 위해서는 `platformFaultDomain` `platformUpdateDomain` VM이 실행 되 고 있는 및를 알고 있어야 합니다.
인스턴스에 대해 [가용성 영역](../articles/availability-zones/az-overview.md)을 사용하여 이러한 결정을 내릴 수도 있습니다.
IMDS를 통해이 데이터를 직접 쿼리할 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**응답**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>샘플 3: VM 태그 가져오기

VM 태그는 인스턴스/컴퓨팅/태그 엔드포인트 아래 인스턴스 API에 포함되어 있습니다.
태그를 특정 분류법에 따라 논리적으로 정리하기 위해 Azure VM에 태그가 적용되었을 수 있습니다. VM에 할당된 태그는 아래 요청을 사용하여 검색할 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**응답**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

`tags` 필드는 태그가 세미콜론으로 구분된 문자열입니다. 이 출력은 태그 자체에서 세미콜론을 사용 하는 경우 문제가 될 수 있습니다. 프로그래밍 방식으로 태그를 추출 하도록 파서를 작성 한 경우에는 필드를 사용 해야 합니다 `tagsList` . `tagsList`필드는 구분 기호가 없는 JSON 배열 이므로 구문 분석 하기가 더 쉽습니다. VM에 할당 된 tagsList는 아래 요청을 사용 하 여 검색할 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**응답**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>샘플 4: 지원 사례에서 VM에 대 한 추가 정보 얻기

서비스 공급자는 지원 요청을 받을 수 있으며 이 때 VM에 대해 자세한 정보를 알아야 하는 경우가 있습니다. 고객에게 컴퓨팅 메타데이터를 공유하도록 요청하면 지원 전문가가 Azure에서 VM의 종류에 대해 알 수 있도록 기본 정보가 제공될 수 있습니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>샘플 5: VM이 실행 되는 Azure 환경 가져오기

Azure에는 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)와 같은 다양한 소버린 클라우드가 있습니다. 경우에 따라 몇 가지 런타임 결정을 내리려면 Azure 환경이 필요합니다. 다음 샘플에서는 이러한 동작을 수행하는 방법을 보여줍니다.

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**응답**

```
AzurePublicCloud
```

클라우드 및 Azure 환경의 값이 여기에 나열 됩니다.

| 클라우드 | Azure 환경 |
|-------|-------------------|
| [일반 공급 되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure 독일](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>샘플 6: 네트워크 정보 검색

**요청**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**응답**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>샘플 7: 공용 IP 주소 검색

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>증명 된 데이터

### <a name="get-attested-data"></a>증명 된 데이터 가져오기

IMDS는 제공 된 데이터가 Azure에서 제공 되도록 보장 하는 데 도움이 됩니다. Microsoft는이 정보의 일부를 서명 하므로 Azure Marketplace의 이미지가 Azure에서 실행 중인 이미지 인지 확인할 수 있습니다.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>매개 변수

| 속성 | 필수/선택 | 설명 |
|------|-------------------|-------------|
| `api-version` | 필수 | 요청을 처리 하는 데 사용 되는 버전입니다.
| `nonce` | Optional | 암호화 nonce 역할을 하는 10 자리 문자열입니다. 값을 제공 하지 않으면 IMDS는 현재 UTC 타임 스탬프를 사용 합니다.

#### <a name="response"></a>응답

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> IMDS의 캐싱 메커니즘으로 인해 이전에 캐시된 nonce 값이 반환될 수 있습니다.

서명 blob은 문서에 대 한 [pkcs7](https://aka.ms/pkcs7)서명 버전입니다. 여기에는 특정 VM 관련 세부 정보와 함께 서명에 사용 되는 인증서가 포함 됩니다.

Azure Resource Manager를 사용 하 여 만든 vm의 경우 문서 `vmId` 를 `sku` `nonce` `subscriptionId` `timeStamp` 만들고 만료 하기 위한,,, 및 이미지에 대 한 계획 정보를 포함 합니다. 요금제 정보는 Azure Marketplace 이미지에 대해서만 채워집니다.

클래식 배포 모델을 사용 하 여 만든 Vm의 경우에만 `vmId` 채워집니다. 응답에서 인증서를 추출 하 고이를 사용 하 여 응답이 유효 하 고 Azure에서 수신 되는지 확인할 수 있습니다.

디코딩된 문서에는 다음 필드가 포함 됩니다.

| 데이터 | Description | 도입된 버전 |
|------|-------------|--------------------|
| `licenseType` | [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit)라이선스의 유형입니다. 이는 AHB 사용 Vm에 대해서만 제공 됩니다. | 2020-09-01
| `nonce` | 요청에 선택적으로 제공할 수 있는 문자열입니다. 가 제공 되지 않은 경우 `nonce` 현재 협정 세계시 타임 스탬프가 사용 됩니다. | 2018-10-01
| `plan` | [Azure Marketplace 이미지 계획](/rest/api/compute/virtualmachines/createorupdate#plan)입니다. 계획 ID (이름), 제품 이미지 또는 제품 (제품) 및 게시자 ID (게시자)를 포함 합니다. | 2018-10-01
| `timestamp.createdOn` | 서명 된 문서를 만든 시간에 대 한 UTC 타임 스탬프입니다. | 2018-20-01
| `timestamp.expiresOn` | 서명 된 문서가 만료 되는 시간에 대 한 UTC 타임 스탬프입니다. | 2018-10-01
| `vmId` | VM의 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | Virtual Machine에 대한 Azure 구독 | 2019-04-30
| `sku` | VM 이미지에 해당하는 SKU | 2019-11-01

> [!NOTE]
> 클래식 (비 Azure Resource Manager) Vm의 경우 vmId만 채워질 수 있습니다.

예제 문서:
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>샘플 1: VM이 Azure에서 실행 되 고 있는지 확인

의 공급 업체는 해당 소프트웨어가 Azure 에서만 실행 되도록 허가를 Azure Marketplace 합니다. 누군가가 VHD를 온-프레미스 환경에 복사할 경우 공급 업체는이를 검색할 수 있어야 합니다. IMDS를 통해 이러한 공급 업체는 Azure 에서만 응답을 보장 하는 서명 된 데이터를 가져올 수 있습니다.

> [!NOTE]
> 이 샘플을 사용 하려면 jq 유틸리티를 설치 해야 합니다.

**유효성 검사**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Proxy $Null -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

서명이 Microsoft Azure 인지 확인 하 고 인증서 체인에서 오류를 확인 합니다.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**결과**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

서명이 Microsoft Azure 인지 확인 하 고 인증서 체인에서 오류를 확인 합니다.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> IMDS의 캐싱 메커니즘으로 인해 이전에 캐시 된 `nonce` 값이 반환 될 수 있습니다.

`nonce` `nonce` 초기 요청에서 매개 변수를 제공한 경우 서명 된 문서의을 비교할 수 있습니다.

> [!NOTE]
> 공용 클라우드와 각 소 버린 클라우드의 인증서가 서로 다릅니다.

| 클라우드 | 인증서 |
|-------|-------------|
| [일반 공급 되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure 독일](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> 인증서가 공용 클라우드와 정확히 일치 하지 않을 수 있습니다 `metadata.azure.com` . 이러한 이유로 인증 유효성 검사는 하위 도메인의 일반 이름을 허용 해야 합니다 `.metadata.azure.com` .

유효성 검사 중 네트워크 제약 조건으로 인해 중간 인증서를 다운로드할 수 없는 경우 중간 인증서를 고정할 수 있습니다. Azure는 표준 PKI 관행 인 인증서를 롤오버 합니다. 롤오버를 수행 하는 경우 고정 된 인증서를 업데이트 해야 합니다. 중간 인증서 업데이트에 대 한 변경 사항이 계획 될 때마다 Azure 블로그는 업데이트 되 고 Azure 고객에 게 알림이 제공 됩니다. 

[PKI 리포지토리에서](https://www.microsoft.com/pki/mscorp/cps/default.htm)중간 인증서를 찾을 수 있습니다. 각 지역의 중간 인증서는 다를 수 있습니다.

> [!NOTE]
> Azure 중국 21Vianet에 대 한 중간 인증서는 Baltimore 대신 DigiCert Global Root CA에서 가져온 것입니다.
루트 체인 인증 기관 변경의 일부로 Azure 중국에 대 한 중간 인증서를 고정 한 경우 중간 인증서를 업데이트 해야 합니다.


## <a name="managed-identity"></a>관리 ID

시스템에서 할당 한 관리 되는 id를 VM에서 사용 하도록 설정할 수 있습니다. 또한 VM에 사용자 할당 관리 id를 하나 이상 할당할 수 있습니다.
그런 다음 IMDS에서 관리 되는 id에 대 한 토큰을 요청할 수 있습니다. 이러한 토큰을 사용 하 여 Azure Key Vault 같은 다른 Azure 서비스를 인증 합니다.

이 기능을 사용하도록 설정하는 자세한 단계는 [액세스 토큰 획득](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요.

## <a name="load-balancer-metadata"></a>메타 데이터 Load Balancer
가상 컴퓨터 또는 가상 컴퓨터 집합 인스턴스를 Azure 표준 Load Balancer 뒤에 두면 IMDS를 사용 하 여 부하 분산 장치 및 인스턴스와 관련 된 메타 데이터를 검색할 수 있습니다. 자세한 내용은 [부하 분산 장치 정보 검색](../articles/load-balancer/instance-metadata-service-load-balancer.md)을 참조 하세요.

## <a name="scheduled-events"></a>예약된 이벤트
IMDS를 사용 하 여 예약 된 이벤트의 상태를 가져올 수 있습니다. 그런 다음 사용자는 이러한 이벤트에 대해 실행할 작업 집합을 지정할 수 있습니다. 자세한 내용은 [Linux의 예약 된 이벤트](../articles/virtual-machines/linux/scheduled-events.md) 또는 [Windows에 대 한 예약 된 이벤트](../articles/virtual-machines/windows/scheduled-events.md)를 참조 하세요.


## <a name="sample-code-in-different-languages"></a>여러 언어의 샘플 코드

다음 표에는 VM 내에서 다른 언어를 사용 하 여 IMDS를 호출 하는 샘플이 나와 있습니다.

| 언어 | 예제 |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>오류 및 디버깅

찾을 수 없는 데이터 요소 또는 형식이 잘못된 요청이 있으면 Instance Metadata Service는 표준 HTTP 오류를 반환합니다. 다음은 그 예입니다.

| HTTP 상태 코드 | 이유 |
|------------------|--------|
| `200 OK` | 요청이 성공했습니다.
| `400 Bad Request` | `Metadata: true` `format=json` 리프 노드를 쿼리할 때 누락 된 헤더 또는 누락 된 매개 변수
| `404 Not Found` | 요청된 요소가 없음
| `405 Method Not Allowed` | HTTP 메서드 (verb)는 끝점에서 지원 되지 않습니다.
| `410 Gone` | 최대 70초 후 다시 시도
| `429 Too Many Requests` | API [Rate 제한을](#rate-limiting) 초과 했습니다.
| `500 Service Error` | 잠시 후 다시 시도하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

**오류가 발생 `400 Bad Request, Required metadata header not specified` 합니다. 이것은 무엇을 의미 하나요?**

IMDS에서 `Metadata: true` 요청에 헤더를 전달 해야 합니다. REST 호출에서이 헤더를 전달 하면 IMDS에 액세스할 수 있습니다.

**VM에 대한 컴퓨팅 정보를 구할 수 없는 이유가 무엇인가요?**

현재 IMDS는 Azure Resource Manager을 사용 하 여 만든 인스턴스만 지원 합니다.

**잠시 전에 Azure Resource Manager을 통해 VM을 만들었습니다. 계산 메타 데이터 정보가 표시 되지 않는 이유는 무엇 인가요?**

9 월 2016 이후에 VM을 만든 경우 [태그](../articles/azure-resource-manager/management/tag-resources.md) 를 추가 하 여 계산 메타 데이터 보기를 시작 합니다. 9 월 2016 일 이전에 VM을 만든 경우 VM 인스턴스에 확장 또는 데이터 디스크를 추가 하거나 제거 하 여 메타 데이터를 새로 고칩니다.

**새 버전에 대해 채워진 모든 데이터가 표시 되지 않는 이유는 무엇 인가요?**

9 월 2016 이후에 VM을 만든 경우 [태그](../articles/azure-resource-manager/management/tag-resources.md) 를 추가 하 여 계산 메타 데이터 보기를 시작 합니다. 9 월 2016 일 이전에 VM을 만든 경우 VM 인스턴스에 확장 또는 데이터 디스크를 추가 하거나 제거 하 여 메타 데이터를 새로 고칩니다.

**오류가 발생 하는 이유는 무엇 인가요 `500 Internal Server Error` `410 Resource Gone` ?**

요청을 다시 시도 하세요. 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조 하세요. 문제가 지속 되 면 VM에 대 한 Azure Portal에서 지원 문제를 만듭니다.

**가상 머신 확장 집합 인스턴스에 대해이 작업을 수행 하나요?**

예, IMDS는 가상 머신 확장 집합 인스턴스에 사용할 수 있습니다.

**가상 머신 확장 집합에서 내 태그를 업데이트 했지만 단일 인스턴스 Vm과 달리 인스턴스에는 표시 되지 않습니다. 문제가 발생 하나요?**

현재 가상 머신 크기 집합에 대 한 태그는 다시 부팅, 이미지로 다시 설치 또는 인스턴스에 대 한 디스크 변경과 같이 VM에만 표시 됩니다.

**내 요청이 서비스 호출에 대해 제한 시간을 초과 하는 이유는 무엇 인가요?**

메타 데이터 호출은 VM의 기본 네트워크 카드에 할당 된 기본 IP 주소에서 이루어져야 합니다. 또한 경로를 변경한 경우 VM의 로컬 라우팅 테이블에 169.254.169.254/32 주소에 대 한 경로가 있어야 합니다.

#### <a name="windows"></a>[Windows](#tab/windows/)

1. 로컬 라우팅 테이블을 덤프 하 고 IMDS 항목을 찾습니다. 예를 들면 다음과 같습니다.
    ```console
    > route print
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
      169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
    ... (continues) ...
    ```
1. 에 대 한 경로가 있는지 확인 하 `169.254.169.254` 고 해당 네트워크 인터페이스 (예:)를 확인 `172.16.69.7` 합니다.
1. 인터페이스 구성을 덤프 하 고, MAC (물리적) 주소를 확인 하 여 라우팅 테이블에서 참조 된 항목에 해당 하는 인터페이스를 찾습니다.
    ```console
    > ipconfig /all
    ... (continues) ...
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
       Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
       Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
       DHCP Enabled. . . . . . . . . . . : Yes
       Autoconfiguration Enabled . . . . : Yes
       Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
       IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
       Subnet Mask . . . . . . . . . . . : 255.255.255.0
    ... (continues) ...
    ```
1. 인터페이스가 VM의 기본 NIC 및 기본 IP에 해당 하는지 확인 합니다. Azure Portal의 네트워크 구성을 확인 하거나 Azure CLI를 조회 하 여 기본 NIC 및 IP를 찾을 수 있습니다. 개인 Ip (및 CLI를 사용 하는 경우 MAC 주소)를 확인 합니다. PowerShell CLI 예제는 다음과 같습니다.
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: wintest767 True 00-0D-3A-E5-1C-C0
    ```
1. 일치 하지 않는 경우 기본 NIC와 IP가 대상으로 지정 되도록 라우팅 테이블을 업데이트 합니다.

#### <a name="linux"></a>[Linux](#tab/linux/)

 1. 와 같은 명령을 사용 하 여 로컬 라우팅 테이블을 덤프 `netstat -r` 하 고 IMDS 항목을 찾습니다 (예:).
    ```console
    ~$ netstat -r
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
    default         _gateway        0.0.0.0         UG        0 0          0 eth0
    168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
    169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
    172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
    ```
1. 에 대 한 경로가 있는지 확인 하 `169.254.169.254` 고 해당 네트워크 인터페이스 (예:)를 확인 합니다. `eth0`
1. 라우팅 테이블의 해당 인터페이스에 대 한 인터페이스 구성을 덤프 합니다 (구성 파일의 정확한 이름은 다를 수 있음).
    ```console
    ~$ cat /etc/netplan/50-cloud-init.yaml
    network:
    ethernets:
        eth0:
            dhcp4: true
            dhcp4-overrides:
                route-metric: 100
            dhcp6: false
            match:
                macaddress: 00:0d:3a:e4:c7:2e
            set-name: eth0
    version: 2
    ```
1. 동적 IP를 사용 하는 경우 MAC 주소를 확인 합니다. 고정 IP를 사용 하는 경우 나열 된 IP 및/또는 MAC 주소를 확인할 수 있습니다.
1. 인터페이스가 VM의 기본 NIC 및 기본 IP에 해당 하는지 확인 합니다. Azure Portal의 네트워크 구성을 확인 하거나 Azure CLI를 조회 하 여 기본 NIC 및 IP를 찾을 수 있습니다. 개인 Ip (및 CLI를 사용 하는 경우 MAC 주소)를 확인 합니다. PowerShell CLI 예제는 다음과 같습니다.
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: ipexample606 True 00-0D-3A-E4-C7-2E
    ```
1. 일치 하지 않는 경우 기본 NIC/i p의 대상이 되도록 라우팅 테이블을 업데이트 합니다.

---

**Windows Server의 장애 조치 (Failover) 클러스터링**

장애 조치 (failover) 클러스터링을 사용 하 여 IMDS를 쿼리 하는 경우 라우팅 테이블에 경로를 추가 해야 하는 경우가 있습니다. 방법은 다음과 같습니다.

1. 관리자 권한으로 명령 프롬프트를 엽니다.

1. 다음 명령을 실행 하 고 `0.0.0.0` IPv4 경로 테이블에서 네트워크 대상 ()의 인터페이스 주소를 확인 합니다.

```bat
route print
```

> [!NOTE]
> 다음 예제 출력은 장애 조치 (failover) 클러스터가 사용 하도록 설정 된 Windows Server VM에서 발생 합니다. 간단히 하기 위해 출력에는 IPv4 경로 테이블만 포함 됩니다.

```
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

다음 명령을 실행 하 고 네트워크 대상 ()의 인터페이스 주소를 사용 `0.0.0.0` `10.0.1.10` 합니다 .이 예제에서는 ()입니다.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="support"></a>지원

여러 번 시도한 후 메타 데이터 응답을 받을 수 없는 경우 Azure Portal에서 지원 문제를 만들 수 있습니다.

## <a name="product-feedback"></a>제품 사용자 의견

Virtual Machines Instance Metadata Service >에서 사용자 피드백 채널에 제품 피드백 및 아이디어를 제공할 수 있습니다. https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>다음 단계

[VM에 대 한 액세스 토큰 획득](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Linux에 대 한 예약 된 이벤트](../articles/virtual-machines/linux/scheduled-events.md)

[Windows에 대 한 예약 된 이벤트](../articles/virtual-machines/windows/scheduled-events.md)
