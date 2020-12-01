---
title: Windows 용 Azure Instance Metadata Service
description: Azure Instance Metadata Service 및 Windows에서 현재 실행 중인 가상 머신 인스턴스에 대 한 정보를 제공 하는 방법에 대해 알아봅니다.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435232"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

IMDS (Azure Instance Metadata Service)는 현재 실행 중인 가상 머신 인스턴스에 대 한 정보를 제공 합니다. 가상 컴퓨터를 관리 하 고 구성 하는 데 사용할 수 있습니다.
이 정보에는 SKU, 저장소, 네트워크 구성 및 예정 된 유지 관리 이벤트가 포함 됩니다. 사용할 수 있는 데이터의 전체 목록은 [메타 데이터 api](#metadata-apis)를 참조 하세요.


IMDS는 Vm (가상 머신) 및 가상 머신 확장 집합 인스턴스의 인스턴스를 실행 하는 데 사용할 수 있습니다. 모든 Api는 [Azure Resource Manager](/rest/api/resources/)을 사용 하 여 만들고 관리 하는 vm을 지원 합니다. 증명 된 및 네트워크 끝점만 클래식 배포 모델을 사용 하 여 만든 Vm을 지원 합니다. 증명 된 끝점은 제한 된 범위에만 해당 됩니다.

IMDS는 잘 알려진 라우팅할 수 없는 IP 주소 ()에서 사용할 수 있는 REST 끝점입니다 `169.254.169.254` . VM 내 에서만 액세스할 수 있습니다. VM과 IMDS 간의 통신은 호스트를 유지 하지 않습니다.
IMDS를 쿼리할 때 HTTP 클라이언트가 VM 내에서 웹 프록시를 우회 하도록 하 고와 동일 하 게 처리 `169.254.169.254` [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) 합니다.

## <a name="security"></a>보안

IMDS 끝점은 라우팅할 수 없는 IP 주소에 실행 중인 가상 머신 인스턴스 내 에서만 액세스할 수 있습니다. 또한 헤더가 포함 된 모든 요청 `X-Forwarded-For` 은 서비스에서 거부 됩니다.
또한 요청에는 `Metadata: true` 실제 요청이 직접 의도 한 것이 아니며 의도 하지 않은 리디렉션의 일부가 되도록 하기 위해 헤더가 포함 되어야 합니다.

> [!IMPORTANT]
> IMDS는 중요 한 데이터의 채널이 아닙니다. 끝점이 VM의 모든 프로세스에 대해 열려 있습니다. VM 내부에서 실행 중인 모든 응용 프로그램에 대 한 공유 정보로이 서비스를 통해 노출 되는 정보를 고려 합니다.

## <a name="usage"></a>사용량

### <a name="access-azure-instance-metadata-service"></a>Azure Instance Metadata Service 액세스

IMDS에 액세스 하려면 [Azure Resource Manager](/rest/api/resources/) 또는 [Azure Portal](https://portal.azure.com)에서 VM을 만들고 다음 샘플을 사용 합니다.
더 많은 예제는 [Azure 인스턴스 메타 데이터 샘플](https://github.com/microsoft/azureimds)을 참조 하세요.

인스턴스의 모든 메타 데이터를 검색 하는 샘플 코드는 다음과 같습니다. 특정 데이터 원본에 액세스 하려면 [METADATA API](#metadata-apis) 섹션을 참조 하세요. 

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy`플래그는 PowerShell 6 이상 에서만 사용할 수 있습니다. 프록시를 설정 하지 않은 경우 플래그를 생략할 수 있습니다.

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 실시간 인쇄를 위해 cmdlet을 통해 REST 쿼리를 파이프 `ConvertTo-Json` 합니다.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
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
        "sku": "Windows-Server-2012-R2-Datacenter",
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
                "osType": "Linux",
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
    },
    "network": {
        "interface": [{
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
        }]
    }
}
```

### <a name="data-output"></a>데이터 출력

기본적으로 IMDS는 JSON 형식 ()으로 데이터를 반환 `Content-Type: application/json` 합니다. 그러나 일부 Api는 요청 된 경우 다른 형식으로 데이터를 반환할 수 있습니다.
다음 표에서는 Api에서 지원할 수 있는 기타 데이터 형식을 보여 줍니다.

API | 기본 데이터 형식 | 다른 형식
--------|---------------------|--------------
/attested | json : | none
/identity | json : | none
/instance | json : | text
/scheduledevents | json : | none

기본이 아닌 응답 형식에 액세스하려면 요청된 형식을 요청의 쿼리 문자열 매개 변수로 지정합니다. 다음은 그 예입니다.

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 에서 리프 노드의 경우 `/metadata/instance` 는 `format=json` 작동 하지 않습니다. 이러한 쿼리의 경우 `format=text` 기본 형식이 JSON 이므로를 명시적으로 지정 해야 합니다.

### <a name="version"></a>Version

IMDS는 버전이 지정 되며 HTTP 요청에서 API 버전을 지정 하는 것은 필수입니다.

지원 되는 API 버전은 다음과 같습니다. 
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

> [!NOTE]
> 버전 2020-10-01은 모든 지역에서 아직 사용할 수 없습니다.

새 버전이 추가 되 면 스크립트에 특정 데이터 형식에 대 한 종속성이 있는 경우 이전 버전의 호환성에 계속 액세스할 수 있습니다.

버전을 지정 하지 않으면 지원 되는 최신 버전의 목록과 함께 오류가 발생 합니다.

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예에서는 버전을 지정 하지 않은 경우의 오류 조건을 나타냅니다. 읽기 쉽도록 응답이 매우 인쇄 됩니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**응답**

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

## <a name="metadata-apis"></a>메타데이터 API

IMDS에는 서로 다른 데이터 원본을 나타내는 여러 Api가 포함 되어 있습니다.

API | 설명 | 도입된 버전
----|-------------|-----------------------
/attested | [증명 된 data](#attested-data) 를 참조 하세요. | 2018-10-01
/identity | [액세스 토큰 획득](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) 참조 | 2018-02-01
/instance | [인스턴스 API](#instance-api) 참조 | 2017-04-02
/scheduledevents | [예약 된 이벤트](scheduled-events.md) 를 참조 하세요. | 2017-08-01

## <a name="instance-api"></a>인스턴스 API

인스턴스 API는 VM, 네트워크 및 저장소를 포함 하 여 VM 인스턴스에 대 한 중요 한 메타 데이터를 제공 합니다. 다음 범주에 액세스할 수 있습니다 `instance/compute` .

데이터 | Description | 도입된 버전
-----|-------------|-----------------------
azEnvironment | VM이 실행 되는 Azure 환경입니다. | 2018-10-01
customData | 이 기능은 현재 사용할 수 없습니다. | 2019-02-01
isHostCompatibilityLayerVm | VM이 호스트 호환성 계층에서 실행 되는지 여부를 식별 합니다. | 2020-06-01
licenseType | [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit)라이선스의 유형입니다. 이는 AHB 사용 Vm에 대해서만 존재 합니다. | 2020-09-01
위치 | VM이 실행 되는 Azure 지역입니다. | 2017-04-02
name | VM의 이름입니다. | 2017-04-02
제품 | VM 이미지에 대한 정보를 제공합니다. Azure 이미지 갤러리에서 배포 된 이미지에 대해서만 제공 됩니다. | 2017-04-02
osProfile.adminUsername | 관리자 계정의 이름을 지정 합니다. | 2020-07-15
osProfile | 컴퓨터의 이름을 지정 합니다. | 2020-07-15
osProfile. disablePasswordAuthentication | 암호 인증을 사용할 수 없는지 여부를 지정합니다. Linux Vm에 대해서만 존재 합니다. | 2020-10-01
osType | Linux 또는 Windows. | 2017-04-02
placementGroupId | 가상 머신 확장 집합의 [배치 그룹](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) 입니다. | 2017-08-01
계획 | Azure Marketplace 이미지인 경우 VM에 대 한 이름, 제품 및 게시자를 포함 하는 [계획](/rest/api/compute/virtualmachines/createorupdate#plan) 입니다. | 2018-04-02
platformUpdateDomain |  VM이 실행 되는 [도메인을 업데이트](../manage-availability.md) 합니다. | 2017-04-02
platformFaultDomain | VM이 실행 되 고 있는 [장애 도메인](../manage-availability.md) 입니다. | 2017-04-02
provider | VM의 공급자입니다. | 2018-10-01
publicKeys | VM 및 경로에 할당 된 [공개 키의 컬렉션](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) 입니다. | 2018-04-02
publisher | VM 이미지의 게시자입니다. | 2017-04-02
resourceGroupName | VM에 대 한 [리소스 그룹](../../azure-resource-manager/management/overview.md) 입니다. | 2017-08-01
resourceId | 리소스 [의 정규화 된 ID입니다](/rest/api/resources/resources/getbyid) . | 2019-03-11
sku | VM 이미지에 대 한 특정 SKU입니다. | 2017-04-02
securityProfile. secureBootEnabled | UEFI 보안 부팅이 VM에서 사용 되는지 여부를 식별 합니다. | 2020-06-01
securityProfile. virtualTpmEnabled | VM에서 TPM (가상 신뢰할 수 있는 플랫폼 모듈)이 사용 되는지 여부를 식별 합니다. | 2020-06-01
storageProfile | [저장소 프로필](#storage-metadata)을 참조 하세요. | 2019-06-01
subscriptionId | VM에 대 한 Azure 구독. | 2017-08-01
tags | VM에 대 한 [태그](../../azure-resource-manager/management/tag-resources.md) 입니다.  | 2017-08-01
tagsList | 프로그래밍 방식의 구문 분석을 용이 하 게 하기 위해 JSON 배열로 형식이 지정 된 태그입니다.  | 2019-06-04
버전 | VM 이미지의 버전입니다. | 2017-04-02
vmId | VM에 대 한 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) 입니다. | 2017-04-02
vmScaleSetName | 가상 머신 확장 집합의 [가상 머신 확장 집합 이름](../../virtual-machine-scale-sets/overview.md) 입니다. | 2017-12-01
vmSize | [VM 크기](../sizes.md)를 참조 하세요. | 2017-04-02
영역 | VM의 [가용성 영역](../../availability-zones/az-overview.md) 입니다. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>샘플 1: Azure에서 실행 되는 VM 추적

서비스 공급자는 소프트웨어를 실행 하는 Vm의 수를 추적 하거나 VM의 고유성을 추적 해야 하는 에이전트가 있어야 할 수 있습니다. VM에 대 한 고유 ID를 가져오려면 `vmId` IMDS의 필드를 사용 합니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**응답**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>샘플 2: 여러 데이터 복제본 배치

특정 시나리오의 경우 다양한 데이터 복제본 배치가 매우 중요합니다. 예를 들어, [HDFS 복제본 배치](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) 또는 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 를 통한 컨테이너 배치를 위해서는 `platformFaultDomain` `platformUpdateDomain` VM이 실행 되 고 있는 및를 알고 있어야 합니다.
인스턴스에 대해 [가용성 영역](../../availability-zones/az-overview.md)을 사용하여 이러한 결정을 내릴 수도 있습니다.
IMDS를 통해이 데이터를 직접 쿼리할 수 있습니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**응답**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>샘플 3: 지원 사례에서 VM에 대 한 추가 정보 가져오기

서비스 공급자는 VM에 대 한 자세한 정보를 알고 싶은 지원 전화를 받을 수 있습니다. 이 경우 고객에 게 계산 메타 데이터를 공유 하도록 요청 하는 것이 유용할 수 있습니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
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
    "sku": "Windows-Server-2012-R2-Datacenter",
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
            "osType": "Linux",
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

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>샘플 4: VM이 실행 되는 Azure 환경 가져오기

Azure는 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)와 같은 다양 한 소 버린 클라우드를 포함 합니다. 런타임 결정을 내리는 데 Azure 환경이 필요할 수도 있습니다. 다음 샘플에서는 이러한 동작을 수행하는 방법을 보여줍니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**응답**

```text
AzurePublicCloud
```

클라우드 및 Azure 환경의 값이 여기에 나열 됩니다.

 클라우드   | Azure 환경
---------|-----------------
[일반 공급 되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure 독일](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>네트워크 메타 데이터 

네트워크 메타데이터는 인스턴스 API의 일부입니다. 다음 네트워크 범주는 끝점을 통해 사용할 수 있습니다 `instance/network` .

데이터 | Description | 도입된 버전
-----|-------------|-----------------------
ipv4/privateIpAddress | VM의 로컬 IPv4 주소입니다. | 2017-04-02
ipv4/publicIpAddress | VM의 공용 IPv4 주소입니다. | 2017-04-02
subnet/address | VM의 서브넷 주소입니다. | 2017-04-02
subnet/prefix | 서브넷 접두사입니다. 예: 24 | 2017-04-02
ipv6/ipaddress | VM의 로컬 IPv6 주소입니다. | 2017-04-02
macAddress | VM mac 주소입니다. | 2017-04-02

> [!NOTE]
> 모든 API 응답은 JSON 문자열입니다. 다음 예제 응답은 모두 가독성을 높이기 위해 적절히 인쇄되었습니다.

#### <a name="sample-1-retrieve-network-information"></a>샘플 1: 네트워크 정보 검색

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

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

#### <a name="sample-2-retrieve-public-ip-address"></a>샘플 2: 공용 IP 주소 검색

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>저장소 메타 데이터

저장소 메타 데이터는 끝점 아래에 있는 인스턴스 API의 일부입니다 `instance/compute/storageProfile` .
VM에 연결된 스토리지 디스크에 대한 세부 정보를 제공합니다. 

VM의 저장소 프로필은 이미지 참조, 운영 체제 디스크 및 데이터 디스크의 세 가지 범주로 구분 됩니다.

이미지 참조 개체에는 운영 체제 이미지에 대 한 다음 정보가 포함 되어 있습니다.

데이터    | Description
--------|-----------------
id      | 리소스 ID
제품   | 플랫폼 또는 이미지의 제안
publisher | 이미지 게시자입니다.
sku     | 이미지 SKU입니다.
버전 | 플랫폼 또는 이미지의 버전

운영 체제 디스크 개체에는 VM에서 사용 하는 운영 체제 디스크에 대 한 다음 정보가 포함 됩니다.

데이터    | Description
--------|-----------------
캐싱 | 캐싱 요구 사항
createOption | VM이 생성된 방법에 대한 정보
diffDiskSettings | 임시 디스크 설정
diskSizeGB | 디스크 크기(GB)
encryptionSettings | 디스크의 암호화 설정
이미지   | 원본 사용자 이미지 가상 하드 디스크
managedDisk | 관리 디스크 매개 변수
name    | 디스크 이름
osType  | 디스크에 포함 된 운영 체제의 유형
VHD     | 가상 하드 디스크
writeAcceleratorEnabled | `writeAccelerator`디스크에서를 사용 하도록 설정할지 여부

데이터 디스크 배열에는 VM에 연결된 데이터 디스크 목록이 포함됩니다. 각 데이터 디스크 개체에는 다음 정보가 들어 있습니다.

데이터    | Description
--------|-----------------
캐싱 | 캐싱 요구 사항
createOption | VM이 생성된 방법에 대한 정보
diffDiskSettings | 임시 디스크 설정
diskSizeGB | 디스크 크기(GB)
이미지   | 원본 사용자 이미지 가상 하드 디스크
lun     | 디스크의 논리 단위 번호
managedDisk | 관리 디스크 매개 변수
name    | 디스크 이름
VHD     | 가상 하드 디스크
writeAcceleratorEnabled | `writeAccelerator`디스크에서를 사용 하도록 설정할지 여부

다음 예제에서는 VM의 저장소 정보를 쿼리 하는 방법을 보여 줍니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**응답**

> [!NOTE]
> 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 인쇄되었습니다.

```json
{
    "dataDisks": [
      {
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
      }
    ],
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
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM 태그

VM 태그는 인스턴스 API의 끝점 아래에 포함 됩니다 `instance/compute/tags` .
태그는 Azure VM에 적용 되어 논리적으로 분류로 구성할 수 있습니다. 다음 요청을 사용 하 여 VM에 할당 된 태그를 검색할 수 있습니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**응답**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` 필드는 태그가 세미콜론으로 구분된 문자열입니다. 이 출력은 태그 자체에서 세미콜론을 사용 하는 경우 문제가 될 수 있습니다. 프로그래밍 방식으로 태그를 추출 하도록 파서를 작성 한 경우에는 필드를 사용 해야 합니다 `tagsList` . `tagsList`필드는 구분 기호가 없는 JSON 배열 이므로 구문 분석 하기가 더 쉽습니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**응답**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>증명 된 데이터

IMDS는 제공 된 데이터가 Azure에서 제공 되도록 보장 하는 데 도움이 됩니다. Microsoft는이 정보의 일부를 서명 하므로 Azure Marketplace의 이미지가 Azure에서 실행 중인 이미지 인지 확인할 수 있습니다.

### <a name="sample-1-get-attested-data"></a>샘플 1: 증명 된 데이터 가져오기

> [!NOTE]
> 모든 API 응답은 JSON 문자열입니다. 다음 예제 응답은 가독성을 높이기 위해 적절히 출력되었습니다.

**요청**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> IMDS의 캐싱 메커니즘으로 인해 이전에 캐시 된 `nonce` 값이 반환 될 수 있습니다.

`Api-version` 는 필수 필드입니다. 지원되는 API 버전은 [사용 섹션](#usage)을 참조하세요.
`Nonce` 은 (는) 선택적 10 진수 문자열입니다. 제공 되지 않은 경우 IMDS는 현재 협정 세계시 타임 스탬프를 대신 반환 합니다.

**Response**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

서명 blob은 문서에 대 한 [pkcs7](https://aka.ms/pkcs7)서명 버전입니다. 여기에는 특정 VM 관련 세부 정보와 함께 서명에 사용 되는 인증서가 포함 됩니다. 

Azure Resource Manager를 사용 하 여 만든 vm의 경우 문서를 만들고 만료 하는,,, `vmId` `sku` `nonce` `subscriptionId` `timeStamp` 및 이미지에 대 한 계획 정보를 포함 합니다. 요금제 정보는 Azure Marketplace 이미지에 대해서만 채워집니다. 

클래식 배포 모델을 사용 하 여 만든 Vm의 경우에만 `vmId` 채워집니다. 응답에서 인증서를 추출 하 고이를 사용 하 여 응답이 유효 하 고 Azure에서 수신 되는지 확인할 수 있습니다.

문서에는 다음 필드가 포함 되어 있습니다.

데이터 | Description | 도입된 버전
-----|-------------|-----------------------
licenseType | [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit)라이선스의 유형입니다. 이는 AHB 사용 Vm에 대해서만 존재 합니다. | 2020-09-01
nonce | 요청에 선택적으로 제공할 수 있는 문자열입니다. 가 제공 되지 않은 경우 `nonce` 현재 협정 세계시 타임 스탬프가 사용 됩니다. | 2018-10-01
계획 | [Azure Marketplace 이미지 계획](/rest/api/compute/virtualmachines/createorupdate#plan)입니다. 계획 ID (이름), 제품 이미지 또는 제품 (제품) 및 게시자 ID (게시자)를 포함 합니다. | 2018-10-01
타임스탬프/createdOn | 서명 된 문서를 만든 시간에 대 한 협정 세계시 타임 스탬프입니다. | 2018-20-01
timestamp/expiresOn | 서명 된 문서가 만료 되는 경우에 대 한 협정 세계시 타임 스탬프입니다. | 2018-10-01
vmId |  VM에 대 한 [고유 식별자](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) 입니다. | 2018-10-01
subscriptionId | VM에 대 한 Azure 구독입니다. | 2019-04-30
sku | VM 이미지에 대 한 특정 SKU입니다. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>샘플 2: VM이 Azure에서 실행 되 고 있는지 확인

의 공급 업체는 해당 소프트웨어가 Azure 에서만 실행 되도록 허가를 Azure Marketplace 합니다. 누군가가 VHD를 온-프레미스 환경에 복사할 경우 공급 업체는이를 검색할 수 있어야 합니다. IMDS를 통해 이러한 공급 업체는 Azure 에서만 응답을 보장 하는 서명 된 데이터를 가져올 수 있습니다.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
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

> [!NOTE]
> IMDS의 캐싱 메커니즘으로 인해 이전에 캐시 된 `nonce` 값이 반환 될 수 있습니다.

`nonce` `nonce` 초기 요청에서 매개 변수를 제공한 경우 서명 된 문서의을 비교할 수 있습니다.

> [!NOTE]
> 공용 클라우드와 각 소 버린 클라우드의 인증서가 서로 다릅니다.

클라우드 | 인증서
------|------------
[일반 공급 되는 모든 글로벌 Azure 지역](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure 독일](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 인증서가 공용 클라우드와 정확히 일치 하지 않을 수 있습니다 `metadata.azure.com` . 이러한 이유로 인증 유효성 검사는 하위 도메인의 일반 이름을 허용 해야 합니다 `.metadata.azure.com` .

유효성 검사 중 네트워크 제약 조건으로 인해 중간 인증서를 다운로드할 수 없는 경우 중간 인증서를 고정할 수 있습니다. Azure는 표준 PKI 관행 인 인증서를 롤오버 합니다. 롤오버가 발생할 때 고정 된 인증서를 업데이트 해야 합니다. 중간 인증서 업데이트에 대 한 변경 사항이 계획 될 때마다 Azure 블로그는 업데이트 되 고 Azure 고객에 게 알림이 제공 됩니다. 

[PKI 리포지토리에서](https://www.microsoft.com/pki/mscorp/cps/default.htm)중간 인증서를 찾을 수 있습니다. 각 지역의 중간 인증서는 다를 수 있습니다.

> [!NOTE]
> Azure 중국 21Vianet에 대 한 중간 인증서는 Baltimore가 아닌 DigiCert Global Root CA입니다.
루트 체인 인증 기관 변경의 일부로 Azure 중국에 대 한 중간 인증서를 고정 한 경우 중간 인증서를 업데이트 해야 합니다.

## <a name="failover-clustering-in-windows-server"></a>Windows Server의 장애 조치 (Failover) 클러스터링

장애 조치 (failover) 클러스터링을 사용 하 여 IMDS를 쿼리 하는 경우 라우팅 테이블에 경로를 추가 해야 하는 경우가 있습니다. 방법은 다음과 같습니다.

1. 관리자 권한으로 명령 프롬프트를 엽니다.

1. 다음 명령을 실행 하 고 `0.0.0.0` IPv4 경로 테이블에서 네트워크 대상 ()의 인터페이스 주소를 확인 합니다.

```bat
route print
```

> [!NOTE]
> 다음 예제 출력은 장애 조치 (failover) 클러스터가 사용 하도록 설정 된 Windows Server VM에서 발생 합니다. 간단히 하기 위해 출력에는 IPv4 경로 테이블만 포함 됩니다.

```text
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

## <a name="managed-identity"></a>관리 ID

시스템에서 할당 한 관리 되는 id를 VM에서 사용 하도록 설정할 수 있습니다. 또한 VM에 사용자 할당 관리 id를 하나 이상 할당할 수 있습니다.
그런 다음 IMDS에서 관리 되는 id에 대 한 토큰을 요청할 수 있습니다. 이러한 토큰을 사용 하 여 Azure Key Vault 같은 다른 Azure 서비스를 인증 합니다.

이 기능을 사용하도록 설정하는 자세한 단계는 [액세스 토큰 획득](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요.

## <a name="scheduled-events"></a>예약된 이벤트
IMDS를 사용 하 여 예약 된 이벤트의 상태를 가져올 수 있습니다. 그런 다음 사용자는 이러한 이벤트에 대해 실행할 작업 집합을 지정할 수 있습니다. 자세한 내용은 [예약 된 이벤트](scheduled-events.md)를 참조 하세요. 

## <a name="regional-availability"></a>국가별 가용성

서비스는 일반적으로 모든 Azure 클라우드에서 사용할 수 있습니다.

## <a name="sample-code-in-different-languages"></a>여러 언어의 샘플 코드

다음 표에는 VM 내에서 다른 언어를 사용 하 여 IMDS를 호출 하는 샘플이 나와 있습니다.

언어      | 예제
--------------|----------------
C++(Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>오류 및 디버깅

찾을 수 없는 데이터 요소 또는 형식이 잘못 된 요청이 있는 경우 IMDS는 표준 HTTP 오류를 반환 합니다. 다음은 그 예입니다. 

HTTP 상태 코드 | 이유
-----------------|-------
200 정상 |
400 잘못된 요청 | `Metadata: true`리프 노드를 쿼리할 때 누락 된 헤더 또는 누락 된 매개 변수 `format=json` 입니다.
404 찾을 수 없음  | 요청 된 요소가 존재 하지 않습니다.
405 메서드를 사용할 수 없음 | `GET`요청만 지원 됩니다.
410 없음 | 최대 70 초 동안 잠시 후 다시 시도 하세요.
429 요청이 너무 많음 | API는 현재 초당 최대 5 개의 쿼리를 지원 합니다.
500 서비스 오류     | 잠시 후 다시 시도 하세요.

### <a name="frequently-asked-questions"></a>질문과 대답

**오류가 발생 `400 Bad Request, Required metadata header not specified` 합니다. 이것은 무엇을 의미 하나요?**

IMDS에서 `Metadata: true` 요청에 헤더를 전달 해야 합니다. REST 호출에서이 헤더를 전달 하면 IMDS에 액세스할 수 있습니다.

**VM에 대한 컴퓨팅 정보를 구할 수 없는 이유가 무엇인가요?**

현재 IMDS는 Azure Resource Manager을 사용 하 여 만든 인스턴스만 지원 합니다.

**잠시 전에 Azure Resource Manager을 통해 VM을 만들었습니다. 계산 메타 데이터 정보가 표시 되지 않는 이유는 무엇 인가요?**

9 월 2016 이후에 VM을 만든 경우 [태그](../../azure-resource-manager/management/tag-resources.md) 를 추가 하 여 계산 메타 데이터 보기를 시작 합니다. 9 월 2016 일 이전에 VM을 만든 경우 VM 인스턴스에 확장 또는 데이터 디스크를 추가 하거나 제거 하 여 메타 데이터를 새로 고칩니다.

**새 버전에 대해 채워진 모든 데이터가 표시 되지 않는 이유는 무엇 인가요?**

9 월 2016 이후에 VM을 만든 경우 [태그](../../azure-resource-manager/management/tag-resources.md) 를 추가 하 여 계산 메타 데이터 보기를 시작 합니다. 9 월 2016 일 이전에 VM을 만든 경우 VM 인스턴스에 확장 또는 데이터 디스크를 추가 하거나 제거 하 여 메타 데이터를 새로 고칩니다.

**오류가 발생 하는 이유는 무엇 인가요 `500 Internal Server Error` `410 Resource Gone` ?**

요청을 다시 시도 하세요. 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조 하세요. 문제가 지속 되 면 VM에 대 한 Azure Portal에서 지원 문제를 만듭니다.

**가상 머신 확장 집합 인스턴스에 대해이 작업을 수행 하나요?**

예, IMDS는 가상 머신 확장 집합 인스턴스에 사용할 수 있습니다.

**가상 머신 확장 집합에서 내 태그를 업데이트 했지만 단일 인스턴스 Vm과 달리 인스턴스에는 표시 되지 않습니다. 문제가 발생 하나요?**

현재 가상 머신 크기 집합에 대 한 태그는 다시 부팅, 이미지로 다시 설치 또는 인스턴스에 대 한 디스크 변경과 같이 VM에만 표시 됩니다.

**내 요청이 서비스 호출에 대해 제한 시간을 초과 하는 이유는 무엇 인가요?**

메타 데이터 호출은 VM의 기본 네트워크 카드에 할당 된 기본 IP 주소에서 이루어져야 합니다. 또한 경로를 변경한 경우 VM의 로컬 라우팅 테이블에 169.254.169.254/32 주소에 대 한 경로가 있어야 합니다.
   * <details>
        <summary>라우팅 테이블을 확인 하는 중</summary>

        1. 로컬 라우팅 테이블을 덤프 하 고 IMDS 항목을 찾습니다. 다음은 그 예입니다. 
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
        1. 인터페이스가 VM의 기본 NIC 및 기본 IP에 해당 하는지 확인 합니다. Azure Portal의 네트워크 구성을 확인 하거나 Azure CLI를 조회 하 여 기본 NIC 및 IP를 찾을 수 있습니다. 공용 및 개인 Ip (및 CLI를 사용 하는 경우 MAC 주소)를 확인 합니다. PowerShell CLI 예제는 다음과 같습니다.
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
    </details>

## <a name="support"></a>지원

여러 번 시도한 후 메타 데이터 응답을 받을 수 없는 경우 Azure Portal에서 지원 문제를 만들 수 있습니다.
**문제 유형** 으로 **관리** 를 선택 합니다. **범주** 에 대해 **Instance Metadata Service** 를 선택 합니다.

![Instance Metadata Service 지원 스크린샷](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>다음 단계

[VM에 대 한 액세스 토큰 획득](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[예약 된 이벤트](scheduled-events.md)
