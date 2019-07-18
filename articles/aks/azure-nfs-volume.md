---
title: Pod Azure Kubernetes Service (AKS)의 사용에 대 한 NFS (네트워크 파일 시스템) Ubuntu 서버 만들기
description: 수동으로 Azure Kubernetes Service (AKS)에서 pod를 사용 하 여 사용에 대 한 Ubuntu Linux Server NFS 볼륨을 만드는 방법 알아보기
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65468501"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>수동으로 만들고 NFS (네트워크 파일 시스템) Linux Server 볼륨을 사용 하 여 Azure Kubernetes Service (AKS) 사용
컨테이너 기반 서비스 및 응용 프로그램의 필요한 구성 요소 경우가 컨테이너 간에 데이터를 공유 합니다. 일반적으로 외부 영구 볼륨에 같은 정보에 액세스 해야 하는 다양 한 pod 해야 합니다.    
Azure files은 옵션 이지만 다른 형태의 영구 공유 저장소는 Azure VM에서 NFS 서버 만들기. 

이 문서에서는 Ubuntu 가상 머신에서 NFS 서버를 만드는 방법을 보여 줍니다. 고도이 공유 파일 시스템에 AKS 컨테이너 액세스를 제공 합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 기존 AKS 클러스터가 있다고 가정 합니다. AKS 클러스터에 필요한 경우 AKS 빠른 시작을 참조 하세요 [Azure CLI를 사용 하 여] [ aks-quickstart-cli] 하거나 [Azure portal을 사용 하 여][aks-quickstart-portal]합니다.

AKS 클러스터 NFS 서버와 같거나 피어 링 된 가상 네트워크에 거주 해야 합니다. 클러스터 VM과 동일한 VNET 수 있는 기존 VNET에서 생성 되어야 합니다.

기존 VNET을 사용 하 여 구성 하는 단계는 설명서에 설명 된: [기존 VNET에 AKS 클러스터를 만들] [ aks-virtual-network] 고 [VNET 피어 링 된 가상 네트워크에 연결][peer-virtual-networks]

또한 Ubuntu Linux 가상 머신 (예를 들어 18.04 LTS)을 만든 가정 합니다. 설정 및 크기를 원하는 대로 수 있으며 Azure를 통해 배포할 수 있습니다. Linux 빠른 시작에서는 대해서 [linux VM 관리][linux-create]합니다.

먼저 AKS 클러스터를 배포 하는 경우 Azure는 자동으로 필드를 채울 가상 네트워크에 Ubuntu 컴퓨터를 배포할 때 쉽게 동일한 VNET 내 라이브입니다. 피어 링 된 네트워크를 대신 사용 하려는 경우 위의 설명서를 참조 하십시오.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>가상 머신에 NFS 서버를 배포합니다.
Ubuntu 가상 컴퓨터 내에서 NFS 서버를 설정 하는 스크립트는 다음과 같습니다.
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
서버 (스크립트)로 인해 다시 시작 되 고 AKS에 NFS 서버를 탑재할 수 있습니다.

>[!IMPORTANT]  
>으로 대체 해야 합니다 **AKS_SUBNET** 클러스터에서 올바른 것으로 또는 "*" 모든 포트 및 연결 하 여 NFS 서버 열립니다.

VM을 만든 후 파일에 위의 스크립트를 복사 합니다. 그런 다음 로컬 컴퓨터에서 이동할 수 있습니다 하거나 아무 곳에 나 스크립트를 사용 하 여 VM: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
스크립트를 VM에 ssh를 VM에를 명령을 통해 실행:
```console
sudo ./nfs-server-setup.sh
```
권한 거부 오류가 인해 실패 하면 해당 실행 명령을 통해 실행 권한 설정:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>NFS 서버에 AKS 클러스터 연결
영구적 볼륨 및 볼륨에 액세스 하는 방법을 지정 하는 영구적 볼륨 클레임을 프로 비전 하 여 NFS 서버 클러스터에 연결 수 있습니다.  
필요한 경우 동일한 또는 피어 링 된 가상 네트워크의 두 서비스에 연결 동일한 VNET에 있는 클러스터를 설정 하기 위한 지침은 여기: [기존 VNET에 AKS 클러스터 만들기][aks-virtual-network]

이러한 동일한 가상 네트워크에, 피어 링 되 면 해야 영구적 볼륨 및 영구적 볼륨 클레임 프로 비전 AKS 클러스터에서 합니다. 컨테이너는 NFS 드라이브는 로컬 디렉터리를 탑재할 수 있습니다.

Kubernetes 영구적 볼륨 (이 정의 사용자 클러스터 및 VM은 동일한 VNET에서 가정)에 대 한 정의 예제는 다음과 같습니다.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
바꿉니다 **NFS_INTERNAL_IP**를 **NFS_NAME** 하 고 **NFS_EXPORT_FILE_PATH** NFS 서버 정보를 사용 하 여 합니다.

영구적 볼륨 클레임 파일로 해야 합니다. 포함할 항목의 예는 다음과 같습니다.

>[!IMPORTANT]  
>**"storageClassName"** 빈 상태로 유지 해야 하는 경우 문자열 또는 클레임 작동 하지 않습니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>문제 해결
클러스터에서 서버에 연결할 수 없으면에 문제가 내보낸된 디렉터리 또는 부모 있을 수 있으며, 서버에 액세스할 수 있는 권한이 없습니다.

내보내기 디렉터리와 해당 부모 디렉터리를 모두 있는 777 권한이 있는지 확인 합니다.

아래 명령을 실행 하 여 사용 권한을 확인할 수 있습니다 및 디렉터리에 있어야 *'drwxrwxrwx'* 권한:
```console
ls -l
```

## <a name="more-information"></a>자세한 정보
전체 연습은 가져오기 또는 NFS 서버 설정 디버그 하는 데 자세한 자습서는 다음과 같습니다.
  - [NFS 자습서][nfs-tutorial]

## <a name="next-steps"></a>다음 단계

관련된 모범 사례를 참조 하세요 [저장소 및 백업 AKS에 대 한 유용한][operator-best-practices-storage]합니다.

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
