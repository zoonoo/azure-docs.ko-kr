---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094631"
---
## <a name="warm-up-the-cache"></a>캐시 준비

읽기 전용 호스트 캐싱이 포함 된 디스크는 디스크 제한 보다 더 높은 IOPS를 제공할 수 있습니다. 호스트 캐시에서 이 최대 읽기 성능을 얻으려면 먼저 이 디스크의 캐시를 준비해야 합니다. 이렇게 하면 벤치마킹 도구에서 CacheReads 볼륨을 구동하는 읽기 IO는 직접적인 디스크가 아니라 실제 캐시에 적중합니다. 캐시 적중은 단일 캐시를 사용 하는 디스크에서 더 많은 IOPS를 발생 합니다.

> [!IMPORTANT]
> VM을 다시 부팅할 때마다 벤치 마크를 실행 하기 전에 캐시를 준비 해야 합니다.

## <a name="diskspd"></a>DISKSPD

VM에서 [DISKSP 도구를 다운로드](https://github.com/Microsoft/diskspd) 합니다. DISKSPD는 고유한 가상 워크 로드를 만들기 위해 사용자 지정할 수 있는 도구입니다. 위에서 설명한 것과 동일한 설정을 사용 하 여 벤치마킹 테스트를 실행 합니다. 사양을 변경 하 여 다른 워크 로드를 테스트할 수 있습니다.

이 예에서는 다음과 같은 기준 매개 변수 집합을 사용 합니다.

- -c200G: 테스트에 사용 된 샘플 파일을 만들거나 다시 만듭니다. 바이트, KiB, MiB, GiB 또는 블록으로 설정할 수 있습니다. 이 경우 메모리 캐싱을 최소화 하기 위해 200-GiB 대상 파일의 용량이 많이 사용 됩니다.
- -w100: 쓰기 요청 작업의 백분율을 지정 합니다.-w0는 100% 읽기와 동일 합니다.
- -b4K: 블록 크기 (바이트, KiB, MiB 또는 GiB)를 나타냅니다. 이 경우 4K 블록 크기는 임의의 i/o 테스트를 시뮬레이션 하는 데 사용 됩니다.
- -F4: 총 4 개의 스레드를 설정 합니다.
- -r: 임의의 i/o 테스트를 나타냅니다.-s 매개 변수를 재정의 합니다.
- -o128: 인스턴스당 대상 당 미해결 i/o 요청 수를 나타냅니다. 이를 큐 수준이 라고도 합니다. 이 경우 128을 사용 하 여 CPU를 스트레스 합니다.
- -W7200: 측정을 시작 하기 전 준비 시간의 기간을 지정 합니다.
- -d30: 준비를 포함 하지 않고 테스트의 기간을 지정 합니다.
- -Sh: 소프트웨어 및 하드웨어 쓰기 캐싱을 사용 하지 않도록 설정 합니다 (-Suw와 동일).

매개 변수의 전체 목록은 [GitHub 리포지토리](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters)를 참조 하세요.

### <a name="maximum-write-iops"></a>최대 쓰기 IOPS
128의 높은 큐 크기, 8kb의 작은 블록 크기 및 4 개의 작업자 스레드를 사용 하 여 쓰기 작업을 구동 합니다. 쓰기 작업자는 "None"으로 설정 된 캐시가 있는 3 개의 디스크가 있는 "NoCacheWrites" 볼륨에서 트래픽을 구동 합니다.

30 초 동안 30 초 동안 후 다음 명령을 실행 합니다.

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

결과 Standard_D8ds_v4 VM이 최대 쓰기 IOPS 제한인 12800을 제공 하 고 있음을 보여 줍니다.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="3208642560 총 바이트, 총 391680의 총 i/o 수, 총 101.97 MiB/s, 초당 총 13052.65 i/o":::

### <a name="maximum-read-iops"></a>최대 읽기 IOPS

높은 큐 깊이 128을 사용 하 고, 작은 블록 크기의 경우 4kb, 4 개의 작업자 스레드를 사용 하 여 읽기 작업을 구동 합니다. 읽기 작업 자가 "CacheReads" 볼륨에서 트래픽을 구동 합니다. 여기에는 캐시가 "ReadOnly"로 설정 된 디스크가 하나 있습니다.

두 시간 동안 준비 시간 30 초 동안 다음 명령을 실행 합니다.

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

결과 Standard_D8ds_v4 VM이 최대 읽기 IOPS 제한인 77000을 제공 하 고 있음을 보여 줍니다.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="총 9652785152 바이트에 대 한 2356637 총 i/o, 306.72 총 MiB/s, 초당 총 78521.23 i/o 수가 있습니다.":::

### <a name="maximum-throughput"></a>최대 처리량

최대 읽기 및 쓰기 처리량을 얻으려면 더 큰 블록 크기인 64 KB로 변경할 수 있습니다.
## <a name="fio"></a>FIO

FIO는 Linux VM의 벤치마크 스토리지에 널리 사용되는 도구입니다. 다른 IO 크기, 순차 또는 임의 읽기 및 쓰기를 선택하는 유연성을 가집니다. 지정된 I/O 작업을 수행하는 작업자 스레드 또는 프로세스를 생성합니다. 각 작업자 스레드가 작업 파일을 사용하여 수행해야 하는 I/O 작업의 유형을 지정할 수 있습니다. 아래 예에 나와 있는 시나리오 당 하나의 작업 파일을 만들었습니다. Premium Storage에서 실행되는 다른 작업을 벤치마크하도록 이러한 작업 파일의 사양을 변경할 수 있습니다. 예제에서는 **Ubuntu** 를 실행 하는 Standard_D8ds_v4을 사용 합니다. 벤치 마크 섹션의 시작 부분에 설명 된 것과 동일한 설정을 사용 하 여 벤치 마크 테스트를 실행 하기 전에 캐시를 준비 합니다.

시작하기 전에 [FIO를 다운로드](https://github.com/axboe/fio) 하고 가상 머신에 설치합니다.

Ubuntu에 대해 다음 명령을 실행합니다.

```
apt-get install fio
```

디스크에서 쓰기 작업 구동에 대해 4개의 작업자 스레드를, 읽기 작업 구동에 대해 4개의 작업자 스레드를 사용합니다. 쓰기 작업자는 "None"으로 설정 된 캐시가 있는 3 개의 디스크가 있는 "nocache" 볼륨에서 트래픽을 구동 합니다. 읽기 작업자는 "ReadOnly"로 설정 된 캐시가 있는 디스크 하나를 포함 하는 "readcache" 볼륨에서 트래픽을 구동 합니다.

### <a name="maximum-write-iops"></a>최대 쓰기 IOPS

 최대 쓰기 IOPS를 얻으려면 다음 사양을 가진 작업 파일을 만듭니다. “fiowrite.ini”로 이름을 지정합니다.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

이전 섹션에서 설명한 설계 지침을 따라 핵심 사항을 기록해 둡니다. 이러한 사양은 최대 IOPS 구동에 필수적입니다.  

* 256의 높은 큐 크기.  
* 4KB의 작은 블록 크기  
* 임의 쓰기를 수행하는 다중 스레드.

다음 명령을 실행하여 30초 동안 FIO 테스트를 시작합니다.  

```
sudo fio --runtime 30 fiowrite.ini
```

테스트가 실행되는 동안 VM 및 프리미엄 디스크가 제공하는 쓰기 IOPS 수를 볼 수 있습니다. 아래 샘플에 표시 된 것 처럼 Standard_D8ds_v4 VM은 최대 쓰기 IOPS 제한인 12800 IOPS를 제공 합니다.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="쓰기 IOPS VM 및 프리미엄 Ssd에서 제공 되는 수는 쓰기가 13.1 k IOPS 임을 보여 줍니다.":::

### <a name="maximum-read-iops"></a>최대 읽기 IOPS

 최대 읽기 IOPS를 얻으려면 다음 사양을 가진 작업 파일을 만듭니다. "fioread.ini"로 이름을 지정합니다.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

이전 섹션에서 설명한 설계 지침을 따라 핵심 사항을 기록해 둡니다. 이러한 사양은 최대 IOPS 구동에 필수적입니다.

* 256의 높은 큐 크기.  
* 4KB의 작은 블록 크기  
* 임의 쓰기를 수행하는 다중 스레드.

다음 명령을 실행하여 30초 동안 FIO 테스트를 시작합니다.

```
sudo fio --runtime 30 fioread.ini
```

테스트가 실행되는 동안 VM 및 프리미엄 디스크가 제공하는 읽기 IOPS 수를 볼 수 있습니다. 아래 샘플에 표시 된 것 처럼 Standard_D8ds_v4 VM은 77000 보다 많은 읽기 IOPS를 제공 합니다. 이는 디스크와 캐시 성능의 조합입니다.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="쓰기 IOPS VM 및 프리미엄 Ssd의 수를 제공 하는 스크린샷은 읽기가 78.6 k 임을 보여 줍니다.":::

### <a name="maximum-read-and-write-iops"></a>최대 읽기 및 쓰기 IOPS

 결합된 최대 읽기 및 쓰기 IOPS를 얻으려면 다음과 같은 사양의 작업 파일을 만듭니다. "fioreadwrite.ini"로 이름을 지정합니다.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

이전 섹션에서 설명한 설계 지침을 따라 핵심 사항을 기록해 둡니다. 이러한 사양은 최대 IOPS 구동에 필수적입니다.

* 128의 높은 큐 크기  
* 4KB의 작은 블록 크기  
* 임의 읽기 및 쓰기를 수행하는 다중 스레드

다음 명령을 실행하여 30초 동안 FIO 테스트를 시작합니다.

```
sudo fio --runtime 30 fioreadwrite.ini
```

테스트가 실행되는 동안 VM 및 프리미엄 디스크가 제공하는 결합된 읽기 및 쓰기 IOPS 수를 볼 수 있습니다. 아래 샘플에 표시 된 것 처럼 Standard_D8ds_v4 VM은 9만 개 이상의 결합 된 읽기 및 쓰기 IOPS를 제공 합니다. 이는 디스크와 캐시 성능의 조합입니다.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="읽기 및 쓰기 IOPS를 결합 하 여 읽기는 78.3 k이 고 쓰기는 12.6 k IOPS 임을 보여 줍니다.":::

### <a name="maximum-combined-throughput"></a>결합된 최대 처리량

 결합된 최대 읽기 및 쓰기 처리량을 얻으려면 읽기 및 쓰기를 수행하는 다중 스레드로 더 큰 블록 크기 및 큰 큐 크기를 사용합니다. 64KB의 블록 크기와 128의 큐 깊이를 사용할 수 있습니다.