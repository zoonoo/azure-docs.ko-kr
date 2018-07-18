---
title: AKS(Azure Kubernetes Service) 클러스터 만들기
description: CLI 또는 Azure Portal에서 AKS 클러스터를 만듭니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 304f3807a70179e4aab2ede80dc08a1aa85a2e51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098909"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 만들기

AKS(Azure Kubernetes Service) 클러스터는 Azure CLI 또는 Azure Portal에서 만들 수 있습니다.

## <a name="azure-cli"></a>Azure CLI

AKS 클러스터를 만들려면 [az aks create][az-aks-create] 명령을 사용합니다.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

다음 옵션은 `az aks create` 명령을 통해 사용할 수 있습니다. 이러한 각 인수의 자세한 정보는 AKS에 대한 [Azure CLI 참조][az-aks-create]를 참조하세요.

| 인수 | 설명 | 필수 |
|---|---|:---:|
| `--name` `-n` | 관리 클러스터에 대한 리소스 이름입니다. | 예 |
| `--resource-group` `-g` | Azure Kubernetes Service 리소스 그룹의 이름입니다. | 예 |
| `--admin-username` `-u` | Linux Virtual Machines의 사용자 이름입니다.  기본값: azureuser. | no |
| `--aad-client-app-id` | (미리 보기) "Native" 유형의 Azure Active Directory 클라이언트 응용 프로그램의 ID입니다. | no |
| `--aad-server-app-id` | (미리 보기) "웹앱/API" 유형의 Azure Active Directory 서버 응용 프로그램의 ID입니다. | no |
| `--aad-server-app-secret` | (미리 보기) Azure Active Directory 서버 응용 프로그램의 비밀입니다. | no |
| `--aad-tenant-id` | (미리 보기) Azure Active Directory 테넌트의 ID입니다. | no |
| `--admin-username` `-u` | SSH 액세스에 대한 노드 VM에서 만드는 사용자 계정입니다.  기본값: azureuser. | no |
| ` --client-secret` | 서비스 사용자와 연결된 비밀입니다. | no |
| `--dns-name-prefix` `-p` | 클러스터 공용 ip 주소의 DNS 접두사입니다. | no |
| `--dns-service-ip` | Kubernetes DNS 서비스에 할당된 IP 주소입니다. | no |
| `--docker-bridge-address` | Docker 브리지에 할당된 IP 주소 및 넷마스크입니다. | no |
| `--enable-addons` `-a` | 쉼표로 구분된 목록에서 Kubernetes 추가 기능을 활성화합니다. | no |
| `--enable-rbac` `-r` | Kubernetes 역할 기반 액세스 제어를 활성화합니다. | no |
| `--generate-ssh-keys` | 없는 경우 SSH 공용 및 개인 키 파일을 생성합니다. | no |
| `--kubernetes-version` `-k` | '1.7.9' 또는 '1.9.6'처럼 클러스터를 만드는 데 사용할 Kubernetes 버전입니다. | no |
| `--locaton` `-l` | 자동 생성된 리소스 그룹에 대한 위치 | no |
| `--max-pods` `-m` | 노드로 배포할 수 있는 Pod의 최대 수입니다. | no |
| `--network-plugin` | 사용할 Kubernetes 네트워크 플러그 인입니다. | no |
| `--no-ssh-key` `-x` | 로컬 SSH 키를 사용하거나 만들지 마세요. | no |
| `--no-wait` | 장기 실행 작업이 완료될 때까지 기다리지 마세요. | no |
| `--node-count` `-c` | 노드 풀의 기본 노드 수입니다.  기본값: 3. | no |
| `--node-osdisk-size` | 노드 풀 가상 머신의 OS 디스크 크기(GB)입니다. | no |
| `--node-vm-size` `-s` | 가상 머신의 크기입니다.  기본값: Standard_D1_v2. | no |
| `--pod-cidr` | kubenet이 사용되는 경우 Pod IP를 할당하는 CIDR 표기법 IP 범위입니다. | no |
| `--service-cidr` | 서비스 클러스터 IP를 할당하는 CIDR 표기법 IP 범위입니다. | no |
| `--service-principal` | 클러스터 인증에 사용되는 서비스 사용자입니다. | no |
| `--ssh-key-value` | SSH 키 파일 값 또는 키 파일 경로입니다.  기본값: ~/.ssh/id_rsa.pub. | no |
| `--tags` | 'key[=value]' 형식의 공백으로 구분된 태그입니다. 기존 태그를 지우려면 ''를 사용합니다. | no |
| `--vnet-subnet-id` | 클러스터를 배포하는 기존 VNet의 서브넷 ID입니다. | no |
| `--workspace-resource-id` | 모니터링 데이터를 저장하는 데 사용하는 기존 Log Analytics 작업 영역의 리소스 ID입니다. | no |

## <a name="azure-portal"></a>Azure portal

Azure Portal에서 AKS 클러스터를 배포하는 방법에 대한 지침은 AKS(Azure Kubernetes Service) [Azure Portal 빠른 시작][aks-portal-quickstart]을 참조하세요.

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
