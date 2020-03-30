---
title: HPC - Azure 가상 머신에 대한 메시지 전달 인터페이스 설정 | 마이크로 소프트 문서
description: Azure에서 HPC에 대한 MPI를 설정하는 방법에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023993"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC용 메시지 전달 인터페이스 설정

MPI(메시지 전달 인터페이스) 워크로드는 기존 HPC 워크로드의 중요한 부분입니다. Azure에서 SR-IOV를 사용하도록 활성화된 VM 크기를 사용하면 MPI의 거의 모든 맛을 사용할 수 있습니다. 

VM에서 MPI 작업을 실행하려면 테넌트 전체에서 파티션 키(p-키)를 설정해야 합니다. p-키 값 결정에 대한 자세한 내용은 [파티션 키 검색](#discover-partition-keys) 섹션의 단계를 따릅니다.

## <a name="ucx"></a>Ucx

[UCX는](https://github.com/openucx/ucx) IB에서 최고의 성능을 제공하며 MPICH 및 OpenMPI와 함께 작동합니다.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>오픈MPI

앞서 설명한 대로 UCX를 설치합니다.

```bash
sudo yum install –y openmpi
```

OpenMPI를 빌드합니다.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

OpenMPI를 실행합니다.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

위에서 언급 한 대로 파티션 키를 확인하십시오.

## <a name="mpich"></a>MPICH

앞서 설명한 대로 UCX를 설치합니다.

MPICH를 빌드합니다.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH 실행.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

위에서 언급 한 대로 파티션 키를 확인하십시오.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2를 빌드합니다.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2 실행.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>플랫폼 MPI 커뮤니티 에디션

플랫폼 MPI에 필요한 패키지를 설치합니다.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

설치 프로세스를 따릅니다.

## <a name="intel-mpi"></a>인텔 MPI

[인텔 MPI를 다운로드합니다.](https://software.intel.com/mpi-library/choose-download)

버전에 따라 I_MPI_FABRICS 환경 변수를 변경합니다. 인텔 MPI 2018의 `I_MPI_FABRICS=shm:ofa` 경우 2019년용 및 사용 `I_MPI_FABRICS=shm:ofi`하십시오.

프로세스 고정은 기본적으로 15, 30 및 60 PPN에서 올바르게 작동합니다.

## <a name="osu-mpi-benchmarks"></a>OSU MPI 벤치마크

[OSU MPI 벤치 마크및 언타르를 다운로드합니다.](http://mvapich.cse.ohio-state.edu/benchmarks/)

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

특정 MPI 라이브러리를 사용하여 벤치마크를 빌드합니다.

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 벤치마크는 `mpi/` 폴더 아래에 있습니다.


## <a name="discover-partition-keys"></a>파티션 키 검색

동일한 테넌트(가용성 집합 또는 VM 배율 집합) 내에서 다른 VM과 통신하기 위한 파티션 키(p-키)를 검색합니다.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

둘 중 더 큰 것은 MPI와 함께 사용해야 하는 테넌트 키입니다. 예: 다음은 p 키인 경우 MPI와 함께 0x800b를 사용해야 합니다.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

기본(0x7fff) 파티션 키 이외의 파티션을 사용합니다. UCX는 p 키의 MSB를 지워야 합니다. 예를 들어 UCX_IB_PKEY 0x800b에 대해 0x000b로 설정합니다.

또한 테넌트(AVSet 또는 VMSS)가 존재하는 한 PKEYs는 동일하게 유지됩니다. 노드가 추가/삭제된 경우에도 마찬가지입니다. 새 테넌은 다른 PKEY를 얻습니다.


## <a name="set-up-user-limits-for-mpi"></a>MPI에 대한 사용자 제한 설정

MPI에 대한 사용자 제한을 설정합니다.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>MPI용 SSH 키 설정

필요한 MPI 유형에 대해 SSH 키를 설정합니다.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

위의 구문은 공유 홈 디렉터리, 기타 .ssh 디렉터리를 각 노드에 복사해야 한다고 가정합니다.

## <a name="next-steps"></a>다음 단계

Azure에서 [HPC에](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 대해 자세히 알아보세요.
