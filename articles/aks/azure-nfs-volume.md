---
title: NFS 우분투 리눅스 서버 볼륨 만들기
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes 서비스(AKS)의 포드와 함께 사용할 NFS 우분투 리눅스 서버 볼륨을 수동으로 만드는 방법에 대해 알아보십시오.
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 7db3f806df88e5b23012e97ba5c2f14ca65b2508
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803469"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)를 사용하여 NFS(네트워크 파일 시스템) Linux 서버 볼륨을 수동으로 만들고 사용합니다.
컨테이너 간에 데이터를 공유하는 것은 종종 컨테이너 기반 서비스 및 응용 프로그램의 필수 구성 요소입니다. 일반적으로 외부 영구 볼륨에서 동일한 정보에 액세스해야 하는 다양한 포드가 있습니다.    
Azure 파일은 옵션이지만 Azure VM에서 NFS 서버를 만드는 것은 영구 공유 저장소의 또 다른 형태입니다. 

이 문서에서는 우분투 가상 컴퓨터에 NFS 서버를 만드는 방법을 보여 줄 것 이다. 또한 AKS 컨테이너에게 이 공유 파일 시스템에 대한 액세스 권한을 부여합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용하거나 Azure][aks-quickstart-cli] [포털을 사용하여][aks-quickstart-portal]AKS 빠른 시작을 참조하십시오.

AKS 클러스터는 NFS 서버와 동일하거나 피어있는 가상 네트워크에 있어야 합니다. 클러스터는 VM과 동일한 VNET일 수 있는 기존 VNET에서 만들어야 합니다.

기존 VNET으로 구성하는 단계는 [기존 VNET에서 AKS 클러스터를 만들고][aks-virtual-network] [가상 네트워크를 VNET 피어링과 연결하는][peer-virtual-networks] 설명서에 설명되어 있습니다.

그것은 또한 우분투 리눅스 가상 머신 (예를 들어, 18.04 LTS)를 만들었습니다 가정. 설정 및 크기는 원하는 대로 할 수 있으며 Azure를 통해 배포할 수 있습니다. 리눅스 빠른 시작을 들어, [리눅스 VM 관리를][linux-create]참조하십시오.

AKS 클러스터를 먼저 배포하는 경우 Azure는 우분투 컴퓨터를 배포할 때 가상 네트워크 필드를 자동으로 채우고 동일한 VNET 내에 있게 합니다. 그러나 피어네트워크를 대신 작업하려면 위의 설명서를 참조하십시오.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>NFS 서버를 가상 시스템에 배포
다음은 우분투 가상 머신 내에서 NFS 서버를 설정하는 스크립트입니다.
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
스크립트로 인해 서버가 다시 시작되고 NFS 서버를 AKS에 탑재할 수 있습니다.

>[!IMPORTANT]  
>**AKS_SUBNET** 클러스터에서 올바른 것으로 바꾸거나 다른 "*"가 NFS 서버를 모든 포트와 연결로 열어야 합니다.

VM을 만든 후 위의 스크립트를 파일에 복사합니다. 그런 다음 다음을 사용하여 로컬 컴퓨터 또는 스크립트가 있는 모든 위치에서 VM으로 이동할 수 있습니다. 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
스크립트가 VM에 있으면 VM을 실행하고 명령을 통해 실행할 수 있습니다.
```console
sudo ./nfs-server-setup.sh
```
권한 거부 오류로 인해 실행이 실패하면 명령을 통해 실행 권한을 설정합니다.
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>AKS 클러스터를 NFS 서버에 연결
볼륨에 액세스하는 방법을 지정하는 영구 볼륨 및 영구 볼륨 클레임을 프로비전하여 NFS Server를 클러스터에 연결할 수 있습니다.

동일한 또는 피어가상 네트워크에서 두 서비스를 연결해야 합니다. 동일한 VNET에서 클러스터를 설정하는 방법에 대한 지침은 [다음과 같습니다.][aks-virtual-network]

동일한 가상 네트워크(또는 피어로 피어)에 있으면 AKS 클러스터에서 영구 볼륨 및 영구 볼륨 클레임을 프로비전해야 합니다. 그런 다음 컨테이너는 NFS 드라이브를 로컬 디렉터리로 탑재할 수 있습니다.

다음은 영구 볼륨에 대한 Kubernetes 정의의 예입니다(이 정의는 클러스터와 VM이 동일한 VNET에 있다고 가정합니다).

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
**NFS_INTERNAL_IP** **NFS_NAME** 및 **NFS_EXPORT_FILE_PATH** NFS 서버 정보로 바꿉습니다.

또한 영구 볼륨 클레임 파일이 필요합니다. 다음은 포함할 내용의 예입니다.

>[!IMPORTANT]  
>**"storageClassName"은** 빈 문자열을 유지해야 하거나 클레임이 작동하지 않습니다.

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
클러스터에서 서버에 연결할 수 없는 경우 내보낸 디렉터리또는 해당 상위 디렉터리일 수 있으며 서버에 액세스할 수 있는 충분한 권한이 없을 수 있습니다.

내보내기 디렉터리와 상위 디렉터리모두에 777개의 사용 권한이 있는지 확인합니다.

아래 명령을 실행하여 권한을 확인할 수 있으며 디렉토리에는 *'drwxrwxrwx'* 권한이 있어야 합니다.
```console
ls -l
```

## <a name="more-information"></a>추가 정보
전체 연습또는 NFS 서버 설정을 디버깅하는 데 도움이 되는 자세한 내용은 다음과 같습니다.
  - [NFS 자습서][nfs-tutorial]

## <a name="next-steps"></a>다음 단계

관련 모범 사례는 [AKS의 저장소 및 백업에 대한 모범 사례를][operator-best-practices-storage]참조하십시오.

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
