---
title: "Azure Linux VM 및 Azure Storage | Microsoft Docs"
description: "Linux 가상 컴퓨터와 Azure Standard 및 Premium Storage와 Managed Disks 및 관리되지 않는 디스크를 설명합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0151e188fde38c7a617cf2070939c6498142dd71
ms.lasthandoff: 04/03/2017


---
# <a name="azure-and-linux-vm-storage"></a>Azure 및 Linux VM 저장소
Azure Storage는 내구성, 가용성, 확장성을 활용하여 고객의 요구 사항을 충족하는 최신 응용 프로그램을 위한 클라우드 저장소 솔루션입니다.  Azure Storage는 개발자가 새로운 시나리오를 지원할 대규모 응용 프로그램을 빌드할 수 있게 할 뿐만 아니라 Azure 가상 컴퓨터의 저장소 기반을 제공합니다.

## <a name="managed-disks"></a>Managed Disks

Azure VM은 이제 [Azure Storage 계정](../../storage/storage-introduction.md)을 직접 만들거나 관리하지 않고 VM을 만들 수 있도록 하는 [Azure Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용할 수 있습니다. 프리미엄 또는 표준 저장소를 원하는지 여부와 디스크의 크기를 지정하고 Azure는 VM 디스크를 만듭니다. Managed Disks가 있는 VM에는 다음을 포함한 여러 가지 중요한 기능이 있습니다.

- 자동 확장성 지원. Azure는 디스크를 만들고 기본 저장소를 관리하여 구독당 최대 10,000개의 디스크를 지원합니다.
- 가용성 집합으로 향상된 안정성. Azure는 가용성 집합 내에서 자동으로 VM 디스크를 서로 분리합니다.
- 향상된 액세스 제어. Managed Disks는 [Azure RBAC(역할 기반 액세스 제어)](../../active-directory/role-based-access-control-what-is.md)로 제어되는 다양한 작업을 노출합니다.

Managed Disks의 가격 책정은 관리되지 않는 디스크의 가격 책정과 다릅니다. 해당 정보는 [Managed Disks에 대한 가격 책정 및 요금 청구](../../storage/storage-managed-disks-overview.md#pricing-and-billing)를 참조하세요.

관리되지 않는 디스크를 사용하는 기존 VM을 [az vm convert](/cli/azure/vm#convert)로 관리되는 디스크를 사용하도록 변환할 수 있습니다. 자세한 내용은 [Linux VM을 관리되지 않는 디스크에서 Azure Managed Disks로 변환하는 방법](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 현재 또는 이전에 [Azure SSE(Storage Service Encryption)](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용하여 암호화된 저장소 계정에 관리되지 않는 디스크가 있는 경우 관리되지 않는 디스크를 관리되는 디스크로 변환할 수 없습니다. 다음은 현재 암호화되었거나 이전에 암호화된 저장소 계정에 있는 관리되지 않는 디스크를 변환하는 자세한 단계입니다.

- [az storage blob copy start](/cli/azure/storage/blob/copy#start)를 사용하여 Azure Storage 서비스 암호화를 사용하도록 설정되지 않은 저장소 계정으로 VHD(가상 하드 디스크)를 복사합니다.
- 관리되는 디스크를 사용하는 VM을 만들고 [az vm create](/cli/azure/vm#create)로 생성되는 동안 해당 VHD 파일을 지정합니다. 또는
- [az vm disk attach](/cli/azure/vm/disk#attach)로 복사된 VHD를 관리되는 디스크가 있는 실행 중인 VM에 연결합니다.


## <a name="azure-storage-standard-and-premium"></a>Azure Storage: Standard 및 Premium
Managed Disks 또는 관리되지 않는 디스크를 사용하는지 여부에 따라 Azure VM을 표준 저장소 디스크 또는 프리미엄 저장소 디스크에 빌드할 수 있습니다. VM을 선택하도록 포털을 사용하는 경우 **기본** 사항 화면의 드롭다운을 토글하여 표준 및 프리미엄 디스크를 확인해야 합니다. SSD에 토글될 때 SSD 드라이브에서 지원하는 Premium Storage만 사용하도록 설정한 VM이 표시됩니다.  HDD로 토글되면 SSD에서 지원하는 프리미엄 저장소 VM과 함께 회전 디스크 드라이브에서 지원하는 표준 저장소를 사용하도록 설정한 VM이 표시됩니다.

`azure-cli`에서 VM을 만들 경우 `-z` 또는 `--vm-size` CLI 플래그를 통해 선택하는 경우 표준 및 프리미엄 간에 VM 크기를 선택할 수 있습니다.

## <a name="creating-a-vm-with-a-managed-disk"></a>Managed Disk를 사용하여 VM 만들기

다음 예제에는 Azure CLI 2.0이 필요합니다. [여기에서 설치]할 수 있습니다.

먼저 리소스를 관리하는 리소스 그룹을 만듭니다.

```azurecli
az group create --location westus --name myResourceGroup
```

그런 다음 아래 예제와 같이 `az vm create` 명령을 사용하여 VM을 만듭니다. `manageddisks`는 이미 사용 중일 가능성이 있으므로 고유한 `--public-ip-address-dns-name` 인수를 지정해야 합니다.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```

앞의 예제는 표준 저장소 계정에 관리되는 디스크로 VM을 만듭니다. 프리미엄 저장소 계정을 사용하려면 다음 예제와 같이 `--storage-sku Premium_LRS` 인수를 추가합니다.

```azurecli
az vm create \
--storage-sku Premium_LRS
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


### <a name="create-a-vm-with-an-unmanaged-standard-disk-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 관리되지 않는, 표준 디스크로 VM 만들기

물론 Azure CLI 1.0을 사용하여 표준 및 프리미엄 디스크 VM을 만들 수도 있지만 이번에는 Azure CLI 1.0을 사용하여 Managed Disks에서 지원하는 VM을 만들 수 없습니다.

`-z` 옵션은 표준 저장소 기반 Linux VM인 Standard_A1을 선택합니다.

```azurecli
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 프리미엄 저장소로 VM 만들기
`-z` 옵션은 프리미엄 저장소 기반 Linux VM인 Standard_DS1을 선택합니다.

```azurecli
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Standard Storage
Azure Standard Storage는 저장소의 기본 형식입니다.  Standard Storage는 영구적이면서 동시에 비용 효율적입니다.  

## <a name="premium-storage"></a>Premium Storage
Azure 프리미엄 저장소는 I/O 사용량이 많은 작업을 실행하는 가상 컴퓨터에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 프리미엄 저장소를 사용하는 가상 컴퓨터(VM) 디스크는 솔리드 스테이트 드라이브(SSD)에 데이터를 저장합니다. 이 디스크의 속도와 성능 혜택을 활용하여 응용 프로그램의 VM 디스크를 Azure 프리미엄 저장소로 마이그레이션할 수 있습니다.

Premium Storage 기능은 다음과 같습니다.

* Premium Storage 디스크: Azure Premium Storage는DS, DSv2 또는 GS 시리즈 Azure VM에 연결할 수 있는 VM 디스크를 지원합니다.
* 프리미엄 페이지 Blob: Premium Storage는 Virtual VM(가상 컴퓨터)에 대한 영구 디스크를 보존하는 데 사용되는 Azure 페이지 Blob를 지원합니다.
* 프리미엄 로컬 중복 저장소: Premium Storage 계정은 복제 옵션으로 LRS(로컬 중복 저장소)만 지원하며, 단일 지역 내에 데이터 복사본 3개를 유지합니다.
* [Premium Storage](../../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>프리미엄 저장소 지원 VM
프리미엄 저장소는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 Fs 시리즈 Azure VM(가상 컴퓨터)을 지원합니다. 프리미엄 저장소 지원 VM에서 표준 및 프리미엄 저장소 디스크를 모두 사용할 수 있습니다. 하지만 Premium Storage와 호환되지 않는 VM 시리즈에서는 Premium Storage 디스크를 사용할 수 없습니다.

다음은 프리미엄 저장소로 유효성을 검사한 Linux 배포판입니다.

| 배포 | 버전 | 지원되는 커널 |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x, 8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="file-storage"></a>File Storage
Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다. Azure Files을 사용하여 파일 서버를 사용하는 엔터프라이즈 응용 프로그램을 Azure로 마이그레이션할 수 있습니다. Azure에서 실행 중인 응용 프로그램은 Linux를 실행 중인 Azure 가상 컴퓨터에서 파일 공유를 쉽게 탑재할 수 있습니다. 최신 릴리스 파일 저장소를 사용하면 SMB 3.0을 지원하는 온-프레미스 응용 프로그램에서 파일 공유를 탑재할 수도 있습니다.  파일 공유는 SMB 공유이므로 표준 파일 시스템 API를 통해 파일 공유에 액세스할 수 있습니다.

파일 저장소는 Blob, 테이블 및 큐 저장소와 동일한 기술을 토대로 만들어졌으므로 파일 저장소는 Azure 저장소 플랫폼에 기본 제공되는 기존 가용성, 내구성, 확장성 및 지리적 중복을 활용할 수 있습니다. File Storage 성능 목표 및 제한에 대한 자세한 내용은 Azure Storage 확장성 및 성능 목표를 참조하세요.

* [Linux에서 Azure 파일 저장소 사용 방법](../../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>핫 저장소
Azure 핫 저장소 계층은 자주 액세스하는 데이터 저장에 최적화되어 있습니다.  핫 저장소는 Blob 저장소에 대한 기본 저장소 유형입니다.

## <a name="cool-storage"></a>쿨 저장소
Azure 쿨 저장소 계층은 드물게 액세스하는 오래 지속되는 데이터 저장에 최적화되어 있습니다. 쿨 저장소에 대한 사용 사례 예제는 백업, 미디어 콘텐츠, 과학적 데이터, 규정 준수 및 보관 데이터를 포함합니다. 일반적으로 자주 액세스하지 않는 모든 데이터는 쿨 저장소에 적합합니다.

|  | 핫 저장소 계층 | 쿨 저장소 계층 |
|:--- |:---:|:---:|
| Availability |99.9% |99% |
| 가용성(RA-GRS 읽기) |99.99% |99.9% |
| 사용 요금 |저장소 비용 더 높음 |저장소 비용 더 낮음 |
| 낮은 액세스 |많은 액세스 | |
| 및 트랜잭션 비용 |및 트랜잭션 비용 | |

## <a name="redundancy"></a>중복
Microsoft Azure 저장소 계정의 데이터는 항상 내구성 및 고가용성을 보증하도록 복제되며 일시적인 하드웨어 오류가 발생 하는 경우에도 Azure 저장소 SLA을 충족합니다.

저장소 계정을 만들면 다음 복제 옵션 중 하나를 선택해야 합니다.

* LRS(로컬 중복 저장소)
* ZRS(영역 중복 저장소)
* GRS(지역 중복 저장소)
* RA-GRS(읽기 액세스 지역 중복 저장소)

### <a name="locally-redundant-storage"></a>로컬 중복 저장소
로컬 중복 저장소(LRS)는 저장소 계정을 만든 지역 내의 데이터를 복제합니다. 지속성을 최대화하려면 저장소 계정의 데이터에 대해 작성된 모든 요청에는 세 번 복제됩니다. 이러한 3개의 복제본은 각기 별도 오류 도메인 및 업그레이드 도메인에 상주합니다.  3개의 복제본 모두에 쓰여진 경우에만 요청은 성공적으로 반환합니다.

### <a name="zone-redundant-storage"></a>영역 중복 저장소
영역 중복 저장소(ZRS)가 LRS보다 더 나은 경우, 단일 지역 내 또는 두 개 지역에 걸쳐 2~3개 시설에서 데이터를 복제하며 높은 영속성을 제공합니다. 저장소 계정에서 ZRS를 사용하도록 설정된 경우, 데이터가 시설 중 하나에서 장애가 발생 하더라도 지속됩니다.

### <a name="geo-redundant-storage"></a>지역 중복 저장소
지역 중복 저장소(GRS)는 주 지역에서 수백 마일 떨어져 있는 보조 지역에 데이터를 복제합니다. 저장소 계정에서 GRS를 활성화하면, 전체 지역 가동 중단 또는 기본 지역을 복구할 수 없는 재해이더라도 데이터는 지속됩니다.

### <a name="read-access-geo-redundant-storage"></a>읽기 액세스 지역 중복 저장소
읽기 액세스 지역 중복 저장소(RA-GRS)는 GRS에서 제공한 두 지역에 걸쳐 복제하는 것 외에도 보조 위치에서 데이터에 대한 읽기 전용 액세스를 제공하여 저장소 계정의 가용성을 최대화합니다. 데이터를 사용할 수 없는 기본 지역에서 응용 프로그램은 보조 지역에서 데이터를 읽을 수 있습니다.

Azure Storage 중복에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure 저장소 복제](../../storage/storage-redundancy.md)

## <a name="scalability"></a>확장성
Azure 저장소는 대규모로 확장할 수 있으므로, 수백 테라바이트의 데이터를 저장 및 처리함으로써 과학, 재무 분석 및 미디어 응용 프로그램에 필요한 빅 데이터 시나리오를 지원할 수 있습니다. 또는 소규모 비즈니스 웹 사이트에 필요한 소량의 데이터를 저장할 수도 있습니다. 저장소 요구량이 줄면 저장하는 데이터에 대해서만 비용을 지불하면 됩니다. Azure 저장소는 현재 수십조에 달하는 고유한 고객 개체를 저장하고 초당 평균 수백만 건의 요청을 처리합니다.

Standard Storage 계정: Standard Storage 계정의 최대 총 요청 속도는 20,000IOPS입니다. 표준 저장소 계정에서 모든 가상 컴퓨터 디스크의 총 IOPS는 이 제한을 초과할 수 없습니다.

Premium Storage 계정: Premium Storage 계정의 최대 총 처리량 속도는 50Gbps입니다. 모든 VM 디스크의 총 처리량은 이 제한을 초과할 수 없습니다.

## <a name="availability"></a>Availability
적어도 99.99%(쿨 액세스 계층의 경우 99.9%)의 시간 동안, 주 지역에서 실패한 데이터 읽기 시도를 보조 지역에서 재시도할 경우 RA-GRS(읽기 권한 지역 중복 저장소) 계정에서 데이터 읽기 요청을 성공적으로 처리할 수 있도록 보장합니다.

최소 99.9%(쿨 액세스 계층의 경우 99%)의 시간 동안 LRS(로컬 중복 저장소), ZRS(영역 중복 저장소) 및 GRS(지역 중복 저장소) 계정에 대해 데이터 읽기 요청을 성공적으로 처리할 수 있도록 보장합니다.

최소 99.9%(쿨 액세스 계층의 경우 99%)의 시간 동안 LRS(로컬 중복 저장소), ZRS(영역 중복 저장소) 및 GRS(지역 중복 저장소) 계정 및 RA-GRS(읽기 권한 지역 중복 저장소) 계정에 대해 데이터 읽기 요청을 성공적으로 처리할 수 있도록 보장합니다.

* [저장소에 대한 Azure SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>영역
Azure는 전 세계 30개 지역에서 일반 공급되며 추가로 4개 지역에 대한 계획을 발표했습니다. Azure를 통해 고객은 더 높은 성능을 얻을 수 있으며 Azure는 데이터 위치와 관련하여 고객 요구 사항 및 기본 설정을 지원하기 때문에 지역 확장은 Azure의 우선 사항입니다.  Azure 서비스를 시작할 최신 지역은 독일입니다.

Microsoft Cloud 독일은 유럽에 이미 사용할 수 있는 Microsoft Cloud 서비스 옵션을 제공하는 동시에 독일, EU(유럽 연합) 및 EFTA(유럽 자유 무역 연합)에서 규제하는 파트너 및 고객에게 혁신 및 경제 성장을 위한 더 많은 기회를 제공합니다.

마그데부르크 및 프랑크푸르트에 있는 이러한 새 데이터 센터의 고객 데이터는 데이터 트러스티인 T-Systems International, 독립적인 독일 회사 및 Deutsche Telekom의 자회사의 제어 하에 관리됩니다. 이러한 데이터 센터에서 Microsoft의 상용 클라우드 서비스는 독일 데이터 처리 규정을 준수하고 고객에게 데이터를 처리하는 방법과 위치에 대한 추가 선택 항목을 제공합니다.

* [Azure 지역 맵](https://azure.microsoft.com/regions/)

## <a name="security"></a>보안
Azure 저장소는 여러 개발자가 보안 응용 프로그램을 빌드하도록 지원하는 포괄적인 보안 기능을 제공합니다. 역할 기반 액세스 제어 및 Azure Active Directory를 사용하여 저장소 계정 자체의 보안을 유지할 수 있습니다. 클라이언트 쪽 암호화, HTTP 또는 SMB 3.0을 사용하여 응용 프로그램과 Azure 간에 전송 중인 데이터의 보안을 유지할 수 있습니다. SSE(저장소 서비스 암호화)를 사용하여 데이터가 Azure Storage에 기록될 때 자동으로 암호화되도록 설정할 수 있습니다. 가상 컴퓨터에서 사용되는 OS 및 데이터 디스크는 Azure 디스크 암호화를 사용하여 암호화되도록 설정할 수 있습니다. 공유 액세스 서명을 사용하여 Azure 저장소의 데이터 개체에 대한 위임된 액세스 권한을 부여할 수 있습니다.

### <a name="management-plane-security"></a>관리 평면 보안
관리 평면은 저장소 계정 관리에 사용되는 리소스로 구성됩니다. 이 섹션에서는 Azure Resource Manager 배포 모델 및 RBAC(역할 기반 액세스 제어)를 사용하여 저장소 계정에 대한 액세스를 제어하는 방법에 대해 설명합니다. 또한 저장소 계정 키의 관리와 이러한 키를 다시 생성하는 방법에 대해서도 설명합니다.

### <a name="data-plane-security"></a>데이터 평면 보안
이 섹션에서는 공유 액세스 서명 및 저장된 액세스 정책을 사용하여 저장소 계정에 있는 Blob, 파일, 큐, 테이블 등의 실제 데이터 개체에 대한 액세스를 허용하는 방법을 살펴볼 것입니다. 서비스 수준 SAS 및 계정 수준 SAS에 대한 설명이 제공됩니다. 또한 특정 IP 주소(또는 IP 주소 범위)에 대한 액세스를 제한하는 방법, 사용되는 프로토콜을 HTTPS로 제한하는 방법 및 만료를 기다리지 않고 공유 액세스 서명을 해지하는 방법도 알아봅니다.

## <a name="encryption-in-transit"></a>전송 중 암호화
이 섹션에서는 Azure 저장소 간에 전송하는 데이터를 보호하는 방법을 설명합니다. 그뿐 아니라 권장되는 HTTPS 사용과 Azure 파일 공유를 위해 SMB 3.0에서 사용되는 암호화에 대해 설명합니다. 또한 클라이언트 응용 프로그램의 저장소로 데이터가 전송되기 전에 암호화하고 저장소 외부로 전송된 후에 암호를 해독할 수 있도록 하는 클라이언트 쪽 암호화도 살펴봅니다.

## <a name="encryption-at-rest"></a>휴지 상태의 암호화
SSE(저장소 서비스 암호화)와 이 암호화 방법을 저장소 계정에 사용하도록 설정하는 방법을 알아보고, 이로 인해 블록 Blob, 페이지 Blob 및 추가 Blob을 Azure 저장소에 쓸 때 자동으로 암호화되는 과정을 설명합니다. Azure 디스크 암호화를 사용하는 방법을 살펴보고, 디스크 암호화, SSE 및 클라이언트 쪽 암호화의 사례와 기본적인 차이점을 알아봅니다. 미국 정부 컴퓨터의 FIPS 준수에 대해서도 간단히 살펴봅니다.

* [Azure Storage 보안 가이드](../../storage/storage-security-guide.md)

## <a name="temporary-disk"></a>임시 디스크
각 VM에는 임시 디스크가 포함되어 있습니다. 이러한 임시 디스크는 응용 프로그램 및 프로세스에 대한 단기 저장소를 제공하며 페이지 또는 스왑 파일과 같은 데이터 저장에 사용됩니다. 임시 디스크의 데이터는 [유지 관리 이벤트](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-planned-vs-unplanned-maintenance) 또는 [VM을 다시 배포](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)할 때 손실될 수 있습니다. VM의 표준 다시 부팅 동안 임시 드라이브의 데이터가 유지되어야 합니다.

Linux 가상 컴퓨터에서 디스크는 일반적으로 **/dev/sdb**이며, Azure Linux 에이전트에 의해 **/mnt**로 포맷되고 마운트됩니다. 임시 디스크의 크기는 가상 컴퓨터의 크기에 따라 달라집니다. 자세한 내용은 [Linux 가상 컴퓨터의 크기](sizes.md)를 참조하세요.

Azure에서 임시 디스크를 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 가상 컴퓨터에서의 임시 드라이브 이해](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>비용 절감
* [저장소 비용](https://azure.microsoft.com/pricing/details/storage/)
* [저장소 비용 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>저장소 제한
* [저장소 서비스 제한](../../azure-subscription-service-limits.md#storage-limits)

