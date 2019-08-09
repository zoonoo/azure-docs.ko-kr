---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3d45defa9ff8e7b2e03d550b76c0e18192c58c4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881605"
---
메모리 최적화 VM 크기는 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합한 높은 메모리 대 CPU 비율을 제공합니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 저장소 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.

* Ev3 시리즈는 하이퍼 스레드 구성에서 E5-2673 v4 2.3GHz(Broadwell) 프로세서를 사용하며, 대부분의 범용 워크로드에 대해 더 나은 가치를 제공하고 Ev3을 다른 대부분 클라우드의 범용 VM과 맞춥니다.  하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(7GiB/vCPU에서 8GiB/vCPU로).  Ev3는 D/Dv2 제품군의 상위 메모리 VM 크기에 대한 후속 시리즈입니다.

* Eav3 시리즈 및 Easv3 시리즈는 최대 256GB L3 캐시가 포함 된 다중 스레드 구성에서 AMD의 2.35 Ghz EPYC<sup>TM</sup> 7452v 프로세서를 활용 하 여 대부분의 메모리 액세스에 최적화 된 워크 로드를 실행 하는 옵션을 늘립니다.  Eav3 시리즈와 Easv3 시리즈는 Ev3 & Esv3 시리즈와 동일한 메모리 및 디스크 구성을 포함 합니다.

* Mv2 시리즈는 클라우드에서 VM의 가장 높은 vCPU 수 (최대 208 Vcpu) 및 가장 큰 메모리 (최대 5.7 TiB)를 제공 합니다. 높은 vCPU 개수 및 많은 양의 메모리를 활용하는 매우 큰 데이터베이스 또는 다른 애플리케이션에 이상적입니다.

* M 시리즈는 높은 vCPU 수 (최대 128 Vcpu) 및 많은 양의 메모리 (최대 3.8 TiB)를 제공 합니다. 매우 큰 데이터베이스 또는 높은 vCPU 수 및 많은 양의 메모리를 활용 하는 다른 응용 프로그램에도 적합 합니다.

* Dv2 시리즈, G 시리즈 및 DSv2/GS 항목은 더 빠른 vCPUs를 요구 하거나 더 나은 임시 저장소 성능을 요구 하거나 더 높은 메모리 요구 사항을 충족 하는 응용 프로그램에 적합 합니다. 이들은 많은 엔터프라이즈급 애플리케이션을 위한 강력한 조합을 제공합니다.

* 원래 D 시리즈의 후속판인 Dv2 시리즈는 더 강력한 CPU가 특징입니다. Dv2 시리즈 CPU는 D 시리즈 CPU보다 약 35% 빠릅니다. 최신 세대 2.4 g h z Intel Xeon® E5-2673 v3 2.4 g h z (Haswell) 또는 E5-2673 v4 2.3 g h z (Broadwell) 프로세서를 기반으로 하며, Intel 터보 부스트 기술 2.0에서 최대 3.1 g h z까지 사용할 수 있습니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

* Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다.  이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다.  고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다.  격리된 VM 옵션은 아래 가상 머신 제품군의 표를 참조하세요.

## <a name="esv3-series"></a>Esv3 시리즈

ACU: 160-190 <sup>1</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

ESv3 시리즈 인스턴스는 2.3GHz Intel XEON® E5-2673 v4(Broadwell) 프로세서를 기반으로 하며, Intel Turbo Boost Technology 2.0을 통해 3.5GHz를 달성하고 Premium Storage를 사용할 수 있습니다. Ev3 시리즈 인스턴스는 메모리 집약적 엔터프라이즈 애플리케이션에 적합합니다.


| 크기             | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 4만/320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 8만/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 8만/1200                             | 8 / 30000                             |


<sup>1</sup> Esv3 시리즈 VM은 Intel® 하이퍼 스레딩 기술 제공

<sup>2</sup> 사용 가능한 코어 크기 제한

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

## <a name="easv3-series"></a>Easv3 시리즈

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨

Easv3 시리즈 크기는 3.35 GHz의 승격 된 Fmax를 실현 하 고 premium storage를 사용할 수 있는 2.35 Ghz AMD EPYC<sup>TM</sup> 7452v 프로세서를 기반으로 합니다. Easv3 시리즈 크기는 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램에 적합 합니다.

| 크기 | vCPU | 메모리: GiB | 임시 저장소 (SSD): GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Ev3 시리즈 

ACU: 160 - 190 <sup>1</sup>

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

Ev3 시리즈 인스턴스는 2.3GHz Intel XEON® E5-2673 v4(Broadwell) 프로세서를 기반으로 하며, Intel Turbo Boost Technology 2.0을 통해 3.5GHz를 달성할 수 있습니다. Ev3 시리즈 인스턴스는 메모리 집약적 엔터프라이즈 애플리케이션에 적합합니다.

데이터 디스크 저장소는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 ESv3 크기를 사용합니다. ESv3 크기의 가격 및 요금 청구 기준은 Ev3 시리즈와 동일합니다. 


| 크기            | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 NIC 수/네트워크 대역폭 |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Ev3 시리즈 VM은 Intel® 하이퍼 스레딩 기술 제공

<sup>2</sup> 사용 가능한 코어 크기 제한

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

## <a name="eav3-series"></a>Eav3 시리즈

Premium Storage: 지원되지 않음

Premium Storage 캐싱: 지원되지 않음

Eav3 시리즈 크기는 3.35 GHz의 승격 된 Fmax를 실현 하 고 premium storage를 사용할 수 있는 2.35 Ghz AMD EPYC<sup>TM</sup> 7452v 프로세서를 기반으로 합니다. Eav3 시리즈 크기는 메모리를 많이 사용 하는 엔터프라이즈 응용 프로그램에 적합 합니다. 데이터 디스크 저장소는 가상 머신과 별도로 비용이 청구됩니다. Premium storage 디스크를 사용 하려면 Easv3 시리즈 크기를 사용 합니다. Easv3 크기에 대 한 가격 책정 및 요금 청구 기준은 Eav3 시리즈와 동일 합니다.

| 크기 | vCPU | 메모리: GiB | 임시 저장소 (SSD): GiB |
|---|---|---|---|---|---|
| Standard_E2a_v3  | 2  | 16  | 50   |
| Standard_E4a_v3  | 4  | 32  | 100  |
| Standard_E8a_v3  | 8  | 64  | 200  |
| Standard_E16a_v3 | 16 | 128 | 400  |
| Standard_E32a_v3 | 32 | 256 | 800  |
| Standard_E48a_v3 | 48 | 384 | 1200 |
| Standard_E64a_v3 | 64 | 432 | 1600 |

## <a name="mv2-series"></a>Mv2 시리즈

Premium Storage: 지원됨

Premium Storage 캐싱: 지원됨

Write Accelerator [지원됨](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2 시리즈는 높은 처리량, 짧은 대기 시간, 하이퍼 스레드 Intel® Xeon® 플래티넘 8180M 2.5 g h z (Skylake) 프로세서에서 실행 되는 로컬 NVMe 저장소를 모두 2.5 GHz의 코어 기본 주파수와 최대 터보 빈도로 3.8 GHz로 제공 합니다. 모든 Mv2 시리즈 가상 머신 크기는 standard 및 premium 영구 디스크를 모두 사용할 수 있습니다. Mv2 시리즈 인스턴스는 메모리 내 데이터베이스 및 워크 로드를 지원 하기 위해 뛰어난 계산 성능을 제공 하는 메모리 액세스에 최적화 된 VM 크기 이며, 관계형 데이터베이스 서버, 큰 캐시 및 메모리 내에서 가장 적합 한 메모리 대 CPU 비율을 제공 합니다. analytics. 

|크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 8만/800 (7040) | 4만/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 8만/800 (7040) | 4만/1000 | 8 / 16000 |

Mv2 시리즈 VM의 기능 Intel® 하이퍼 스레딩 기술  

<sup>1</sup> 이러한 많은 vm에는 다음과 같은 지원 되는 게스트 os 중 하나가 필요 합니다. Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> Mv2 시리즈 vm은 2 세대 전용입니다. Linux를 사용 하는 경우 SUSE Linux 이미지를 찾고 선택 하는 방법에 대해서는 다음 섹션을 참조 하세요.

#### <a name="find-a-suse-image"></a>SUSE 이미지 찾기

Azure Portal에서 적절 한 SUSE Linux 이미지를 선택 하려면 다음을 수행 합니다. 

1. Azure Portal에서 **리소스 만들기** 를 선택 합니다. 
1. "SUSE SAP" 검색 
1. SLES for SAP generation 2 이미지는 종 량 제로 제공 되거나 BYOS (사용자 고유의 구독)를 제공 합니다. 검색 결과에서 원하는 이미지 범주를 확장 합니다.

    * SAP 용 SUSE Linux Enterprise Server (SLES)
    * SAP 용 SUSE Linux Enterprise Server (SLES) (BYOS)
    
1. Mv2 시리즈와 호환 되는 SUSE 이미지에는 이름이 `GEN2:`접두사로 붙습니다. Mv2 시리즈 Vm에 사용할 수 있는 SUSE 이미지는 다음과 같습니다.

    * GEN2 SAP 응용 프로그램용 SUSE Linux Enterprise Server (SLES) 12 SP4
    * GEN2 SAP 응용 프로그램용 SUSE Linux Enterprise Server (SLES) 15
    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SAP 응용 프로그램용 SP4 (BYOS)
    * GEN2 SAP 응용 프로그램용 SUSE Linux Enterprise Server (SLES) 15 (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Azure CLI를 통해 SUSE 이미지를 선택 합니다.

Mv2 시리즈 vm에 대해 현재 사용할 수 있는 SLES for SAP 이미지의 목록을 보려면 다음 [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) 명령을 사용 합니다.

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

명령은 Mv2 시리즈 Vm에 대 한 SUSE에서 사용할 수 있는 현재 사용 가능한 2 세대 Vm을 출력 합니다. 

예제 출력:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M 시리즈 

ACU: 160-180 <sup>1</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

Write Accelerator  [지원됨](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| 크기            | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 1만/100 (793)  | 5000  / 125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 2만/200 (1587) | 1만/250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 4만/400 (3174) | 2만/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 4만/400 (3174) | 2만/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 4만/400 (3174) | 2만/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 8만/800 (6348)| 4만/1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 8만/800 (6348) | 4만/1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 8만/800 (6348)| 4만/1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 16만/1600 (12696) | 8만/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 16만/1600 (12696) | 8만/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 8만/800 (1228) | 4만/1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 8만/800 (1228) | 4만/1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 25만/1600 (2456) | 8만/2000 | 8/32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 25만/1600 (2456) | 8만/2000 | 8/32000 |



<sup>1</sup> M 시리즈 VM은 Intel® 하이퍼 스레딩 기술 제공

<sup>2</sup> 64개를 초과하는 vCPU에는 지원되는 게스트 OS인 Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 및 LIS 4.2.1을 사용하는 Red Hat Enterprise Linux, CentOS 7.3 또는 Oracle Linux 7.3 중 하나가 필요합니다.

<sup>3</sup> 사용 가능한 코어 크기 제한

<sup>4</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.
<br>


## <a name="dsv2-series-11-15"></a>DSv2 시리즈 11-15

ACU: 210 - 250 <sup>1</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |8만/640 (720) |64000/960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> DSv2 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다.  자세한 내용은 [고성능을 위한 디자인](../articles/virtual-machines/windows/premium-storage-performance.md)을 참조하세요.  
<sup>2</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.  
<sup>3</sup> 사용 가능한 코어 크기 제한  
<sup>4</sup> 가속 네트워킹 사용 시 25,000Mbps 

<br>

## <a name="dv2-series-11-15"></a>Dv2 시리즈 11-15

ACU: 210 - 250

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기              | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.  
<sup>2</sup> 가속 네트워킹 사용 시 25,000Mbps 
