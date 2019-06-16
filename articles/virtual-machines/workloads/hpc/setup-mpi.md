---
title: HPC-Azure Virtual Machines에 대 한 메시지 전달 인터페이스를 설정 합니다. | Microsoft Docs
description: Azure에서 HPC에 대 한 MPI를 설정 하는 방법에 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 1717c0d95c00e13ae0d87ab920bc88cd4f0df978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809844"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC 용 메시지 전달 인터페이스를 설정 합니다.

메시지 전달 인터페이스 (MPI) 작업은 기존 HPC 워크 로드의 중요 한 부분입니다. SR-IOV 사용할 MPI의 거의 모든 버전을 허용 하는 Azure에서 VM 크기를 사용 하도록 설정된 합니다. 

Vm에서 MPI 작업을 실행는 테 넌 트 전체 파티션 키 (p-키) 설정을 해야 합니다. 단계를 수행 합니다 [파티션 키를 검색](#discover-partition-keys) p 키 값은 대 한 자세한 내용은 섹션입니다.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) IB MPICH OpenMPI와 작동에 최상의 성능을 제공 합니다.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

앞에서 설명한 대로 UCX를 설치 합니다.

```bash
sudo yum install –y openmpi
```

OpenMPI를 빌드하십시오.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

OpenMPI를 실행 합니다.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

위에서 설명한 것 처럼 파티션 키를 확인 합니다.

## <a name="mpich"></a>MPICH

앞에서 설명한 대로 UCX를 설치 합니다.

MPICH를 빌드하십시오.

```bash
wget http://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH를 실행 합니다.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

위에서 설명한 것 처럼 파티션 키를 확인 합니다.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2를 빌드하십시오.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2를 실행 합니다.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>플랫폼 MPI Community Edition

플랫폼 MPI에 대 한 필요한 패키지를 설치 합니다.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

설치 프로세스를 따릅니다.

## <a name="intel-mpi"></a>Intel MPI

[Intel MPI를 다운로드](https://software.intel.com/mpi-library/choose-download)합니다.

버전에 따라 I_MPI_FABRICS 환경 변수를 변경 합니다. Intel MPI 2018 년에 대 한 사용 `I_MPI_FABRICS=shm:ofa` 2019, 사용 및 `I_MPI_FABRICS=shm:ofi`합니다.

프로세스 고정 작동 올바르게 15, 30 및 60 PPN 기본적으로 합니다.

## <a name="osu-mpi-benchmarks"></a>OSU MPI 벤치 마크

[OSU MPI 벤치 마크 다운로드] [ http://mvapich.cse.ohio-state.edu/benchmarks/ ](http://mvapich.cse.ohio-state.edu/benchmarks/) 및 여 untar 합니다.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

특정 MPI 라이브러리를 사용 하 여 벤치 마크를 빌드하십시오.

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 벤치 마크는 `mpi/` 폴더입니다.


## <a name="discover-partition-keys"></a>파티션 키를 검색 합니다.

다른 Vm과 통신 하는 것에 대 한 파티션 키 (p-키)를 검색 합니다.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

MPI를 사용 하 여 사용 해야 하는 테 넌 트 키를 둘 중 더 큰 경우 예제: P 키를 다음 인 경우 0x800b MPI를 사용 하 여 사용 해야 합니다.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

기본 (0x7fff) 파티션 키 이외의 파티션을 사용 합니다. UCX 지울 p 키의 MSB 필요 합니다. 예를 들어로 UCX_IB_PKEY 0x000b 0x800b에 대 한 합니다.


## <a name="set-up-user-limits-for-mpi"></a>MPI에 대 한 사용자 제한 설정

MPI에 대 한 사용자의 한도 설정 합니다.

```bash
cat << EOF >> /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>MPI에 대 한 SSH 키를 설정 합니다.

필요로 하는 MPI 형식에 대 한 SSH 키를 설정 합니다.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

위의 구문 가정 공유 홈 디렉터리, 다른.ssh 디렉터리를 각 노드에 복사 해야 합니다.

## <a name="next-steps"></a>다음 단계

에 대해 자세히 알아보세요 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure에서.
