---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: 85429e67c5e02ef6eb9fe4ef76a5c7e1abaa3d69
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485507"
---
범용 VM 크기는 적당한 CPU 대 메모리 비율을 제공합니다. 테스트 및 개발, 중소 규모 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다. 이 문서에서는 이 그룹화에서 크기에 대한 스토리지 처리량 뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.

- The [DC-series](#dc-series) is a family of virtual machines in Azure that can help protect the confidentiality and integrity of your data and code while it’s processed in the public cloud. 이러한 머신은 SGX 기술을 포함한 최신 세대의 3.7GHz Intel XEON E-2176G 프로세서로 지원됩니다. Intel Turbo Boost Technology를 통해 이러한 머신은 최대 4.7GHz에 도달할 수 있습니다. DC 시리즈 인스턴스를 사용하여 고객은 사용 중인 코드 및 데이터를 보호하는 보안 Enclave 기반 애플리케이션을 빌드할 수 있습니다.

- 다양한 하드웨어 유형 및 프로세서에 Av2 시리즈 VM을 배포할 수 있습니다. A 시리즈 VM은 개발과 테스트 등 항목별 작업에 가장 적합한 CPU 성능 및 메모리 구성을 갖추고 있습니다. 배포된 하드웨어에 관계없이 인스턴스 실행 시 일관된 프로세서 성능을 제공하기 위해 하드웨어에 따라 크기가 제한됩니다. 이 크기가 배포되는 실제 하드웨어를 확인하려면 Virtual Machine 내에서 가상 하드웨어를 쿼리합니다.

  사용 사례로는 개발 및 테스트 서버, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스, 개념 증명, 코드 리포지토리 등이 있습니다.

- 원래 D 시리즈의 후속인 Dv2 시리즈는 더 강력한 CPU와 최적의 CPU-메모리 구성을 갖추고 있어 대부분 프로덕션 워크로드에 적합합니다. The Dv2-series is about 35% faster than the D-series. Dv2-series runs on the Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors with the Intel Turbo Boost Technology 2.0. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

- The Dv3-series runs on the Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors in a hyper-threaded configuration, providing a better value proposition for most general purpose workloads.  하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(~ 3.5GiB/vCPU에서 4GiB/vCPU로).  The Dv3-series no longer has the high memory VM sizes of the D/Dv2-series, those have been moved to the memory optimized Ev3-series for [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#ev3-series) and [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#ev3-series).

  Example D-series use cases include enterprise-grade applications, relational databases, in-memory caching, and analytics.

- The Dav4-series and Dasv4-series are new sizes utilizing AMD’s 2.35Ghz EPYC<sup>TM</sup> 7452 processor in a multi-threaded configuration with up to 256 GB L3 cache dedicating 8 GB of that L3 cache to every 8 cores increasing customer options for running their general purpose workloads. The Dav4-series and Dasv4-series have the same memory and disk configurations as the D & Dsv3-series.
  
## <a name="b-series"></a>B 시리즈

Premium Storage: 지원됨

Premium Storage caching:  Not Supported

버스트 가능한 B 시리즈 VM은 웹 서버, 소규모 데이터베이스 및 개발 및 테스트 환경과 같이 CPU의 전체 성능이 지속적으로 필요하지 않은 작업에 적합합니다. 이러한 작업에는 일반적으로 버스트 가능한 성능 요구 사항이 있습니다. B 시리즈는 이러한 고객들에게 VM에서 기준 성능보다 적게 사용할 때 VM 인스턴스에서 크레딧을 축적할 수 있는 양심적인 가격 위주의 기준 성능을 갖춘 VM 크기를 구입할 수 있는 기능을 제공합니다. VM에 대한 크레딧이 적립되면 애플리케이션에 더 높은 CPU 성능이 필요할 때 VM이 CPU의 최대 100%까지 사용하여 VM의 기준 이상으로 버스트할 수 있습니다.

사용 사례로는 개발 및 테스트 서버, 트래픽이 적은 웹 서버, 작은 데이터베이스, 마이크로 서비스, 개념 증명용 서버, 빌드 서버 등이 있습니다.


| 크기             | vCPU  | 메모리: GiB | 임시 스토리지(SSD) GiB | VM의 CPU 기준 성능 | VM의 CPU 최대 성능 | Initial Credits | 적립 크레딧/시간 | 최대 적립 크레딧 | 최대 데이터 디스크 수 | Max cached and temp storage throughput: IOPS / MBps | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0.5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320/50                                 | 4320/50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320/50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320/50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320/50                                 | 8  |

<sup>1</sup> B1ls is supported only on Linux

## <a name="dsv3-series-sup1sup"></a>Dsv3 시리즈 <sup>1</sup>

ACU: 160-190

Premium Storage: 지원됨

Premium Storage caching:  Supported

Dsv3-series sizes run on the Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors with Intel Turbo Boost Technology 2.0 and use premium storage. Dsv3 시리즈 크기는 대부분의 프로덕션 워크로드에 사용할 수 있는 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.


| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000 / 768 (1200)                                                    | 76800 / 1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000 / 1200                              | 8 / 30000                                               |

<sup>1</sup> Dsv3 시리즈 VM 기능 Intel® 하이퍼 스레딩 기술

## <a name="dasv4-series"></a>Dasv4 시리즈

ACU:  230-260

Premium Storage: 지원됨

Premium Storage caching: Supported

Dasv4-series sizes are based on the 2.35Ghz AMD EPYC<sup>TM</sup> 7452 processor that can achieve a boosted maximum frequency of 3.35GHz and use premium SSD. The Dasv4-series sizes offer a combination of vCPU, memory and temporary storage for most production workloads.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | Max NICs / Expected network bandwidth (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup> These sizes are in Preview.  If you are interested in trying out these larger sizes, sign up at [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dv3-series-sup1sup"></a>Dv3 시리즈 <sup>1</sup>

ACU: 160-190

Premium Storage: 지원되지 않음

Premium Storage caching:  Not Supported

Dv3-series sizes run on the Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors with Intel Turbo Boost Technology 2.0. Dv3 시리즈 크기는 대부분의 프로덕션 워크로드에 적합한 vCPU, 메모리 및 임시 스토리지의 조합을 제공합니다.

데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium Storage 디스크를 사용하려면 Dsv3 크기를 사용합니다. Dsv3 크기의 가격 및 요금 청구 기준은 Dv3 시리즈와 같습니다. 


| 크기            | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | Max NICs / Network bandwidth (Mbps) |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> Dv3 시리즈 VM 기능 Intel® 하이퍼 스레딩 기술

## <a name="dav4-series"></a>Dav4 시리즈

ACU: 230-260

Premium Storage: Not Supported

Premium Storage caching: Not Supported

Dav4-series sizes are based on the 2.35Ghz AMD EPYC<sup>TM</sup> 7452 processor that can achieve a boosted maximum frequency of 3.35GHz. The Dav4-series sizes offer a combination of vCPU, memory and temporary storage for most production workloads. 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. To use premium SSD, use the Dasv4 sizes. The pricing and billing meters for Dasv4 sizes are the same as the Dav4-series.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | Max NICs / Expected network bandwidth (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup> These sizes are in Preview.  If you are interested in trying out these larger sizes, sign up at [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dsv2-series"></a>DSv2 시리즈

ACU: 210-250

Premium Storage: 지원됨

Premium Storage caching:  Supported

DSv2-Series sizes run on the Intel® Xeon® 8171M 2.1GHz (Skylake) or the the Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors with Intel Turbo Boost Technology 2.0 and use premium storage.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000 / 32(43) |3200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000 / 512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-시리즈

ACU: 210-250

Premium Storage: 지원되지 않음

Premium Storage caching:  Not Supported

DSv2-Series sizes run on the Intel® Xeon® 8171M 2.1GHz (Skylake) or the the Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors with Intel Turbo Boost Technology 2.0.

| 크기           | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크 수 | 처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2 시리즈

ACU: 100

Premium Storage: 지원되지 않음

Premium Storage caching:  Not Supported

Av2-Series sizes run on the Intel® Xeon® 8171M 2.1GHz (Skylake) or the the Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors with Intel Turbo Boost Technology 2.0 and use premium storage.

| 크기            | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |

## <a name="dc-series"></a>DC 시리즈

Premium Storage: 지원됨

Premium Storage caching: Supported



| 크기          | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32(43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |
