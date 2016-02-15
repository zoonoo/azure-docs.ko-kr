<properties
 pageTitle="MPI 응용 프로그램을 실행하는 Linux RDMA 클러스터 | Microsoft Azure"
 description="RDMA를 사용하여 MPI 응용 프로그램을 실행하기 위해 크기가 A8 또는 A9인 VM의 Linux 클러스터를 만듭니다."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/21/2016"
 ms.author="danlep"/>

# MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


이 문서에서는 Azure에서 [크기가 A8 및 A9인 가상 컴퓨터](virtual-machines-a8-a9-a10-a11-specs.md)를 사용하여 MPI(Message Passing Interface) 응용 프로그램을 병렬로 실행하도록 Linux RDMA를 설정하는 방법을 보여 줍니다. 지원되는 MPI 구현을 실행하도록 크기가 A8 및 A9인 Linux 기반 VM을 구성한 경우 MPI 응용 프로그램은 Azure에서 RDMA(원격 직접 메모리 액세스) 기술을 기반으로 하는 낮은 대기 시간 및 높은 처리량의 네트워크에서 효율적으로 통신합니다.

>[AZURE.NOTE] Azure Linux RDMA는 현재 Azure 마켓플레이스의 SUSE Linux Enterprise Server 12(SLES 12) 이미지에서 실행되는 Intel MPI Library 버전 5에서 지원됩니다. 이 문서는 Intel MPI 5.0.3.048 버전을 기반으로 합니다.
>
> 또한 Azure에서는 A8 및 A9 인스턴스와 처리 성능이 동일하지만 RDMA 백 엔드 네트워크에 연결되지 않는 A10 및 A11 계약 집약적 인스턴스도 제공합니다. Azure에서 MPI 작업을 실행하려는 경우 일반적으로 A8 및 A9 인스턴스에서 최상의 성능을 얻을 수 있습니다.


## Linux 클러스터 배포 옵션

다음은 작업 스케줄러를 사용하거나 사용하지 않고 Linux RDMA 클러스터를 만드는 데 사용할 수 있는 방법입니다.

* **HPC 팩** - Azure에서 Microsoft HPC 팩 클러스터를 만들고 지원되는 Linux 배포판을 실행하는 계산 노드를 추가합니다. 특정 Linux 노드는 RDMA 네트워크에 액세스하도록 구성할 수 있습니다. [Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작](virtual-machines-linux-cluster-hpcpack.md)을 참조하세요.

* **Azure CLI 스크립트** - 이 문서의 나머지 부분의 단계에 나와 있는 설명과 같이 Mac, Linux 및 Windows용 [Azure CLI](../xplat-cli-install.md)(명령줄 인터페이스)를 사용하여Linux 클러스터를 만드는 데 필요한 가상 네트워크 및 기타 구성 요소의 배포를 스크립팅합니다. 클래식(서비스 관리) 배포 모드의 CLI는 계산 노드를 순차적으로 배포하므로 많은 계산 노드를 배포하는 경우 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

* **Azure 리소스 관리자 템플릿** - Azure 리소스 관리자 배포 모델을 사용하여 여러 A8 및 A9 Linux VM을 배포하고 가상 네트워크, 고정 IP 주소, DNS 설정 및 그 밖에 RDMA 네트워크를 활용하여 MPI 작업을 실행할 수 있는 계산 클러스터를 위한 리소스를 정의합니다. [사용자 고유의 템플릿을 만들거나](../resource-group-authoring-templates.md) [Azure 빠른 시작 템플릿 페이지](https://azure.microsoft.com/documentation/templates/)에서 Microsoft 또는 커뮤니티가 참여한 템플릿을 확인하여 원하는 솔루션을 배포할 수 있습니다. 리소스 관리자 템플릿을 사용하면 빠르고 안정적으로 Linux 클러스터를 배포할 수 있습니다.

## Azure 서비스 관리에서 Azure CLI 스크립트를 사용하여 배포

다음 단계에서는 Azure CLI를 사용하여 SUSE Linux Enterprise Server 12 VM을 배포하고, Intel MPI Library 및 기타 사용자 지정 항목을 설치하고, 사용자 지정 VM 이미지를 만들고, A8 또는 A9 VM 클러스터 배포 스크립트를 작성하도록 도와줍니다.

### 필수 조건

*   **클라이언트 컴퓨터** - Azure와 통신할 Mac, Linux 또는 Windows 기반 클라이언트가 필요합니다. 이러한 단계는 Linux 클라이언트를 사용하는 것을 가정합니다.

*   **Azure 구독** - 계정이 없는 경우에는 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

*   **코어 할당량** - A8 또는 A9 VM 클러스터를 배포하려면 코어 할당량을 늘려야 할 수도 있습니다. 예를 들어 이 문서에 설명된 것처럼 8대의 A9 VM을 배포하려는 경우 적어도 128개의 코어가 필요합니다. 할당량을 늘리려면 무료로 [온라인 고객 지원 요청을 개설](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)합니다.

*   **Azure CLI** - Azure CLI를 [설치](../xplat-cli-install.md)하고 클라이언트 컴퓨터에서 Azure 구독을 연결하도록 [구성](../xplat-cli-connect.md)합니다.

*   **Intel MPI** -클러스터에 대한 Linux VM 이미지 사용자 지정의 일부로(이 문서의 뒷부분에 나오는 세부 정보 참조) [Intel.com 사이트](https://software.intel.com/ko-KR/intel-mpi-library/)에서 Intel MPI 라이브러리 5 런타임을 다운로드 및 설치해야 합니다. 이를 준비하려면 Intel에 등록한 후 확인 전자 메일의 링크를 따라 관련 웹 페이지로 이동하여 적절한 Intel MPI 버전의 .tgz 파일에 대한 다운로드 링크를 복사합니다. 이 문서는 Intel MPI 5.0.3.048 버전을 기반으로 합니다.

### SLES 12 VM 프로비전

Azure CLI에서 Azure에 로그인한 후 `azure config list`를 실행하여 출력이 CLI가 Azure 서비스 관리 모드에 있는지를 나타내는 **asm** 모드를 표시하는지 확인합니다. 그렇지 않은 경우 다음 명령을 실행하여 모드를 설정합니다.

```
azure config mode asm
```

다음을 입력하여 사용 권한이 부여된 모든 구독을 나열합니다.

```
azure account list
```

현재 활성 구독은 `Current`가 `true`로 설정되어 있습니다. 클러스터를 만드는 데 사용하려는 구독이 아닌 경우 해당 구독 번호를 활성 구독으로 설정합니다.

```
azure account set <subscription-number>
```

Azure에서 공개적으로 사용할 수 있는 SLES 12 HPC 이미지를 보려면 셸 환경에서 **grep**를 지원하는 경우 다음과 유사한 명령을 실행합니다.

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]SLES 12 HPC 이미지는 Azure에 필요한 Linux RDMA 드라이버로 미리 구성되어 있습니다.

이제 다음과 유사한 명령을 실행하여 사용 가능한 SLES 12 HPC 이미지로 크기가 A9인 VM을 프로비전합니다.

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

여기서,

* 크기(이 예제의 경우 A9)는 A8 또는 A9일 수 있습니다.

* 외부 SSH 포트 번호(이 예제의 경우 SSH 기본인 22)는 유효한 모든 포트 번호이고, 내부 SSH 포트 번호는 22로 설정됩니다.

* 새 클라우드 서비스는 위치로 지정된 Azure 지역에 서 만들어집니다. A8 및 A9 인스턴스를 사용할 수 있는 "West US"와 같은 위치를 지정합니다.

* 이미지 이름은 현재 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708`(무료) 또는 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708`(SUSE 우선 지원의 경우 - 요금이 부과됨)일 수 있습니다.

### VM 사용자 지정

VM 프로비전이 완료되면 VM의 외부 IP 주소(또는 DNS 이름) 및 구성한 외부 포트 번호를 사용하여 VM에 SSH 연결한 후 VM을 사용자 지정합니다. 연결에 대한 자세한 내용은 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)을 참조하세요. 루트 액세스가 단계를 완료하기 위해 필요하지 않은 경우 VM에서 구성된 사용자로 명령을 수행해야 합니다.

>[AZURE.IMPORTANT]Microsoft Azure에서는 Linux VM에 대한 루트 액세스를 제공하지 않습니다. VM에 사용자로 연결된 경우 관리 액세스 권한을 얻으려면 `sudo`을(를) 사용하여 명령을 실행합니다.

*   **업데이트** -**zypper**를 사용하여 업데이트를 설치합니다. NFS 유틸리티를 설치하려고 할 수도 있습니다.  

    >[AZURE.IMPORTANT]이때 Linux RDMA 드라이버에 문제가 발생할 수 있으므로 커널 업데이트를 적용하지 않는 것이 좋습니다.

*   **Intel MPI** - Intel.com 사이트에서 Intel MPI Library 5 런타임을 다운로드하여 설치합니다.

    ```
    sudo wget <download link for your registration>

    sudo tar xvzf <tar-file>

    cd <mpi-directory>

    sudo ./install.sh

    ```

    VM에 Intel MPI를 설치하려면 기본 설정을 수락합니다.

*   **메모리 잠금** - MPI 코드를 사용하여 RDMA에 사용 가능한 메모리를 잠그려면 /etc/security/limits.conf 파일에서 다음 설정을 추가하거나 변경해야 합니다. (이 파일을 편집하려면 루트 액세스가 필요합니다.)

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]테스트를 위해 memlock을 무제한으로 설정할 수도 있습니다. 예: `<User or group name>    hard    memlock unlimited`


*   **SSH 키** - MPI 작업을 실행하는 경우 SSH 키를 생성하여 클러스터의 모든 계산 노드 간의 사용자 계정에 대 한 트러스트를 설정합니다. 다음 명령을 실행하여 SSH 키를 만듭니다. enter 키를 눌러 암호를 설정하지 않고 기본 위치에 키를 생성합니다.

    ```
    ssh-keygen
    ```

    알려진 공용 키에 대한 authorized\_keys 파일에 공개키를 추가합니다.

    ```
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    ```

    ~/.ssh 디렉터리에서 "config" 파일을 편집하거나 만듭니다. Azure에서 사용할 개인 네트워크의 IP 주소 범위를 제공합니다(이 예에서는 10.32.0.0/16).

    ```
    host 10.32.0.*
    StrictHostKeyChecking no
    ```

    또는 다음과 같이 클러스터의 각 VM에 대한 개인 네트워크 IP 주소를 나열합니다.

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]`StrictHostKeyChecking no`를 구성하면 이 예와 같이 특정 IP 주소 또는 범위를 지정하지 않은 경우 잠재적인 보안 위험이 발생할 수 있습니다.

* **응용 프로그램** - 이미지를 캡처하기 전에 이 VM에 필요한 응용 프로그램을 설치하거나 다른 사용자 지정을 수행합니다.

### 이미지 캡처

이미지를 캡처하려면 먼저 Linux VM에서 다음 명령을 실행합니다. VM을 프로비전 취소하지만 설정한 사용자 계정 및 SSH 키를 유지 관리합니다.

```
sudo waagent -deprovision
```

그런 다음 클라이언트 컴퓨터에서 다음 Azure CLI 명령을 실행하여 이미지를 캡처합니다. 자세한 내용은 [클래식 Linux 가상 컴퓨터를 이미지로 캡처하는 방법](virtual-machines-linux-capture-image.md)을 참조하세요.

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

이러한 명령을 실행하면 VM 이미지가 캡처되고 VM이 삭제됩니다. 이제 사용자 지정 이미지를 사용하여 클러스터를 배포할 수 있습니다.

### 이미지를 사용하여 클러스터 배포

다음 Bash 스크립트를 사용자 환경에 적절한 값으로 수정하여 클라이언트 컴퓨터에서 실행합니다. 서비스 관리자 배포 방법에서는 VM을 순차적으로 배포하려면 이 스크립트에 제안된 8대의 A9 VM을 배포하는 데 몇 분 정도 걸립니다.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## SLES 12용 Linux RDMA 드라이버 업데이트

SLES 12 HPC 이미지에 따라 Linux RDMA 클러스터를 만든 후 RDMA 네트워크 연결을 위해 VM에서 RDMA 드라이버를 업데이트해야 할 수 있습니다.

>[AZURE.IMPORTANT]이 단계는 현재 대부분의 Azure 지역에서 Linux RDMA 클러스터 배포에 **필요합니다**. **업데이트하면 안 되는 SLES 12 VM은 미국 서부, 유럽 서부, 일본 동부 등의 Azure 지역에서 만들어진 VM뿐입니다.**

드라이버를 업데이트 하기 전에 **zypper** 프로세스 또는 VM에서 SUSE 리포지토리 데이터베이스를 잠그는 모든 프로세스를 중지합니다. 그렇지 않으면 드라이버가 제대로 업데이트되지 않을 수 있습니다.


클라이언트 컴퓨터에서 다음 Azure CLI 명령 집합 중 하나를 실행하여 각 VM에서 Linux RDMA 드라이버를 업데이트합니다.

**Azure 서비스 관리에서 프로비전된 VM**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Azure 리소스 관리자에서 프로비전된 VM**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]드라이버를 설치하는 데 약간의 시간이 걸릴 수 있으며 명령은 출력 없이 반환됩니다. 업데이트가 수행된 후 VM이 다시 시작하므로 몇 분 후에 사용할 수 있습니다.

클러스터의 모든 노드에서 드라이버 업데이트를 스크립팅할 수 있습니다. 예를 들어 다음 스크립트는 이전 단계의 스크립트에서 만든 8노드 클러스터의 드라이버를 업데이트합니다.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For ASM VMs use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For ARM VMs use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```

## Intel MPI 구성 및 실행

Azure Linux RDMA에서 MPI 응용 프로그램을 실행하려면 Intel MPI에 특정한 특정 환경 변수를 구성해야 합니다. 변수를 구성하고 응용 프로그램을 실행하는 샘플 Bash 스크립트는 다음과 같습니다.

```
#!/bin/bash -x

source /opt/intel/impi_latest/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLEAND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

호스트 파일의 형식은 다음과 같습니다. 클러스터의 각 노드에 대해 한 줄을 추가합니다. DNS 이름이 아니라 이전에 정의된 VNet에서 개인 IP 주소를 지정합니다. 예를 들어 IP 주소가 10.32.0.1 및 10.32.0.2인 두 호스트의 경우 파일은 다음을 포함합니다.

```
10.32.0.1:16
10.32.0.2:16
```

## Intel MPI를 설치한 후 기본 2노드 클러스터를 확인합니다.

아직 그렇게 하지 않은 경우 먼저 Intel MPI에 대한 환경을 설정합니다.

```
source /opt/intel/impi_latest/bin64/mpivars.sh
```

### 간단한 MPI 명령 실행

계산 노드 중 하나에서 간단한 MPI 명령을 실행하여 MPI가 제대로 설치되고 적어도 두 개의 계산 노드 사이에서 통신할 수 있는지 표시합니다. 다음 **mpirun** 명령은 두 개의 노드에서 **hostname** 명령을 실행합니다.

```
/opt/intel/impi_latest/bin64/mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
출력은 `-hosts`에 대한 입력으로 전달한 모든 노드의 이름을 나열해야 합니다. 예를 들어 두 개의 노드가 있는 **mpirun** 명령은 다음과 유사한 출력을 반환합니다.

```
cluster11
cluster12
```

### MPI 벤치마크 실행

다음 Intel MPI 명령은 pingpong 벤치마크를 사용하여 클러스터 구성 및 RDMA 네트워크 연결을 확인합니다.

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
```

두 개의 노드가 있는 작동하는 클러스터에 다음과 유사한 출력이 표시되어야 합니다. Azure의 RDMA 네트워크에서 최대 512바이트의 메시지 크기에 대해 3 마이크로초 이하의 대기 시간이 예상됩니다.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```

## 네트워크 토폴로지 고려 사항

* Linux VM에서 Eth1은 RDMA 네트워크 트래픽용으로 예약됩니다. Eth1 설정 또는 이 네트워크를 참조하는 구성 파일의 정보를 변경하지 마세요.

* Azure IP over Infiniband(IB)는 지원되지 않습니다. RDMA over IB만 지원됩니다.

* Linux VM에서 Eth0은 일반 Azure 네트워크 트래픽용으로 예약됩니다.

## 다음 단계

* Linux 클러스터에서 Linux MPI 응용 프로그램을 배포하고 실행합니다.

* Intel MPI에 대한 지침은 [Intel MPI Library 설명서](https://software.intel.com/ko-KR/articles/intel-mpi-library-documentation/)를 참조하세요.

<!---HONumber=AcomDC_0204_2016-->