---
title: HPC에 대 한 메시지 전달 인터페이스 설정-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HPC에 대 한 MPI를 설정 하는 방법에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 08d952738a085aa6ed814668ece898f7460b4f33
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963745"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC에 대 한 메시지 전달 인터페이스 설정

[MPI (메시지 전달 인터페이스)](https://en.wikipedia.org/wiki/Message_Passing_Interface) 는 분산 메모리 병렬화를 위한 개방형 라이브러리 및 사실상 표준입니다. 일반적으로 많은 HPC 워크 로드에서 사용 됩니다. [RDMA 지원](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM의 HPC 워크 로드는 MPI를 사용 하 여 짧은 대기 시간 및 높은 대역폭 InfiniBand 네트워크를 통해 통신할 수 있습니다.

Azure에서 SR-IOV를 사용 하도록 설정 된 VM 크기 (HBv2, HB, HC, NCv3, NDv2)를 사용 하면 MPI OFED에서 MPI의 거의 모든 버전을 사용할 수 있습니다. SR-IOV를 사용할 수 없는 Vm에서 지원 되는 MPI 구현은 Microsoft 네트워크 직접 (ND) 인터페이스를 사용 하 여 Vm 간에 통신 합니다. 따라서 Microsoft MPI (MS MPI) 2012 R2 이상 및 Intel MPI 5.x 버전만 지원 됩니다. Intel MPI 런타임 라이브러리의 이후 버전 (2017, 2018)은 Azure RDMA 드라이버와 호환 될 수도 있고 그렇지 않을 수도 있습니다.

SR-IOV를 사용 하도록 설정 된 [rdma 지원 vm](../../sizes-hpc.md#rdma-capable-instances)의 경우 Marketplace의 [CentOS 버전 7.6 이상](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 버전 VM 이미지는 RDMA 용 OFED 드라이버 및 자주 사용 되는 다양 한 MPI 라이브러리 및 과학적 컴퓨팅 패키지를 사용 하 여 최적화 되 고 미리 로드 되며, 시작 하는 가장 쉬운 방법입니다.

여기에 나와 있는 예제는 RHEL/CentOS에 대 한 것 이지만 일반적인 단계 이며 Ubuntu (16.04, 18.04 19.04, 20.04) 및 SLES (12 SP4 및 15)와 같은 호환 되는 Linux 운영 체제에 사용할 수 있습니다. 다른 사용자에 대 한 다른 MPI 구현을 설정 하기 위한 더 많은 예제는 [azhpc 리포지토리](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)에 있습니다.

> [!NOTE]
> SR-IOV를 사용 하도록 설정 된 Vm에서 MPI 작업을 실행 하려면 격리 및 보안을 위해 테 넌 트 전체에 파티션 키 (p-키)를 설정 해야 합니다. P 키 값을 확인 하 고 MPI 작업에 대 한 값을 올바르게 설정 하는 방법에 대 한 자세한 내용은 [파티션 키 검색](#discover-partition-keys) 섹션의 단계를 따르세요.

## <a name="ucx"></a>함께 x

[통합 통신 x ()](https://github.com/openucx/ucx) 는 HPC 용 통신 api의 프레임 워크입니다. InfiniBand를 통한 MPI 통신에 최적화 되며 OpenMPI 및 MPICH와 같은 여러 MPI 구현에서 작동 합니다.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

[HPC x 소프트웨어 도구 키트](https://www.mellanox.com/products/hpc-x-toolkit) 에는 HCOLL x와가 포함 되어 있습니다.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

HPC-X 실행

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

위에 설명 된 대로을 설치 합니다. HCOLL은 [HPC X 소프트웨어 도구 키트](https://www.mellanox.com/products/hpc-x-toolkit) 의 일부 이며 특별 한 설치를 요구 하지 않습니다.

리포지토리에서 사용할 수 있는 패키지에서 OpenMPI를 설치 합니다.

```bash
sudo yum install –y openmpi
```

OpenMPI를 빌드합니다.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

OpenMPI를 실행 합니다.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

위에서 언급 한 대로 파티션 키를 확인 합니다.

## <a name="intel-mpi"></a>Intel MPI

선택한 버전의 [INTEL MPI](https://software.intel.com/mpi-library/choose-download)를 다운로드 합니다. 버전에 따라 I_MPI_FABRICS 환경 변수를 변경 합니다. Intel MPI 2018의 경우 `I_MPI_FABRICS=shm:ofa` 2019에 및를 사용 하 고를 사용 `I_MPI_FABRICS=shm:ofi` 합니다.

### <a name="non-sr-iov-vms"></a>비 sr-iov Vm
SR-IOV를 [사용](https://registrationcenter.intel.com/en/forms/?productid=1740) 하지 않는 vm의 경우에는 다음을 수행 합니다.
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
설치 단계에 대해서는 [Intel MPI Library 설치 가이드](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)를 참조하세요.
필요에 따라 루트가 아닌 비 디버거 프로세스에 대해 ptrace를 사용 하도록 설정할 수 있습니다 (최신 버전의 Intel MPI에 필요).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
SUSE Linux Enterprise Server VM 이미지 버전-HPC 용 12 SP3, hpc 용 SLES 12 SP3 (프리미엄), hpc 용 SLES 12 SP1, hpc 용 SLES 12 SP1 (Premium), SLES 12 SP4 및 SLES 15, RDMA 드라이버가 설치 되 고 Intel MPI 패키지가 VM에 배포 됩니다. 다음 명령을 실행 하 여 Intel MPI를 설치 합니다.
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH

위에 설명 된 대로을 설치 합니다. MPICH를 빌드합니다.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

MPICH를 실행 합니다.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

위에서 언급 한 대로 파티션 키를 확인 합니다.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2를 빌드합니다.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

MVAPICH2를 실행 합니다.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>플랫폼 MPI Community Edition

플랫폼 MPI에 필요한 패키지를 설치 합니다.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

설치 프로세스를 따릅니다.

## <a name="osu-mpi-benchmarks"></a>OSU MPI 벤치 마크

[Osu MPI 벤치 마크](http://mvapich.cse.ohio-state.edu/benchmarks/) 및 untar를 다운로드 합니다.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

특정 MPI 라이브러리를 사용 하 여 벤치 마크 빌드:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 벤치 마크는 폴더 아래에 있습니다 `mpi/` .


## <a name="discover-partition-keys"></a>파티션 키 검색

동일한 테 넌 트 내의 다른 Vm (가용성 집합 또는 가상 머신 확장 집합)과 통신 하기 위한 파티션 키 (p 키)를 검색 합니다.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

둘 중 더 큰 값은 MPI에서 사용 해야 하는 테 넌 트 키입니다. 예: 다음은 p 키입니다. 0x800b는 MPI와 함께 사용 해야 합니다.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

기본 (0x7fff) 파티션 키가 아닌 다른 파티션을 사용 합니다. 키를 사용 하려면 MSB의 키를 지워야 합니다. 예를 들어 0x800b에 대해 UCX_IB_PKEY를 0x000b로 설정 합니다.

또한 테 넌 트 (가용성 집합 또는 가상 머신 확장 집합)가 있는 한 PKEYs는 동일 하 게 유지 됩니다. 노드가 추가 되거나 삭제 되는 경우에도 마찬가지입니다. 새 테 넌 트가 서로 다른 PKEYs를 가져옵니다.


## <a name="set-up-user-limits-for-mpi"></a>MPI에 대 한 사용자 제한 설정

MPI에 대 한 사용자 제한을 설정 합니다.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>MPI에 대 한 SSH 키 설정

필요한 MPI 유형에 대 한 SSH 키를 설정 합니다.

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

위의 구문은 공유 홈 디렉터리를 가정 합니다. ssh 디렉터리를 각 노드에 복사 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [InfiniBand 사용](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) vm에 대해 알아봅니다.
- [HB 시리즈 개요](hb-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토하여 성능 및 확장성을 높일 수 있도록 워크로드를 최적으로 구성하는 방법을 알아보세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항과 HPC 예제 및 결과를 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
