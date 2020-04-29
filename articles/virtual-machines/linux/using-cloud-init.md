---
title: Azure에서 Linux Vm에 대 한 클라우드 초기화 지원 개요
description: Azure에서 프로 비전 시 VM을 구성 하는 클라우드 초기화 기능 개요
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79465042"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure에서 가상 머신에 대 한 클라우드 초기화 지원
이 문서에서는 Azure에서 프로 비전 하는 시간에 VM (가상 머신) 또는 가상 머신 확장 집합을 구성 하기 위해 [클라우드 초기화](https://cloudinit.readthedocs.io) 에 대해 존재 하는 지원을 설명 합니다. 이러한 클라우드 init 구성은 Azure에서 리소스를 프로 비전 한 후 처음 부팅 될 때 실행 됩니다.  

VM 프로 비전은 Azure에서 VM을 전달 하 여 호스트 이름, 사용자 이름, 암호 등의 매개 변수 값을 만들고 VM이 부팅 될 때 VM에서 사용할 수 있도록 하는 프로세스입니다. ' 프로 비전 에이전트 '는 이러한 값을 사용 하 고, VM을 구성 하 고, 완료 되 면 다시 보고 합니다. 

Azure는 두 가지 프로 비전 에이전트 [클라우드 init](https://cloudinit.readthedocs.io)와 [azure LINUX 에이전트 (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)를 지원 합니다.

## <a name="cloud-init-overview"></a>클라우드-초기화 개요
[클라우드 초기화](https://cloudinit.readthedocs.io) 는 처음 부팅 될 때 Linux VM을 사용자 지정 하는 데 널리 사용 되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 호출되므로 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.  `#cloud-config` 파일이 나 기타 입력의 형식을 올바르게 지정 하는 방법에 대 한 자세한 내용은 [클라우드 초기화 설명서 사이트](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)를 참조 하세요.  `#cloud-config` 파일은 base64로 인코딩된 텍스트 파일입니다.

클라우드 초기화는 배포 간에도 작동 합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install**은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. 클라우드 초기화는 자동으로 사용자가 선택한 배포판에 대 한 기본 패키지 관리 도구를 사용 합니다.

Azure Marketplace에서 cloud-init 활성화 이미지를 사용할 수 있도록 하기 위해 승인된 Linux 배포판 파트너와 적극적으로 공조하고 있습니다. 이러한 이미지를 사용하면 VM 및 가상 머신 확장 집합에서 cloud-init 배포 및 구성 작업을 원활하게 진행할 수 있습니다. 처음에는 Azure에서 OS를 사용 하 여 클라우드 초기화 기능을 보장 하기 위해 보증 Linux 배포판 파트너 및 업스트림과 공동 작업을 수행 합니다. 그러면 패키지가 업데이트 되 고 배포판 패키지 리포지토리에서 공개적으로 사용할 수 있게 됩니다. 

Azure에서 보증 Linux 배포판 OS의 클라우드 초기화를 사용할 수 있도록 하는 두 가지 단계가 있습니다. 패키지 지원 및 이미지 지원:
* ' Azure에 대 한 클라우드 초기화 패키지 지원 '은 앞으로 지원 되거나 미리 보기에 있는 클라우드 초기화 패키지를 문서화 하므로 사용자 지정 이미지에서 OS와 함께 이러한 패키지를 사용할 수 있습니다.
* 이미지가 이미 클라우드 초기화를 사용 하도록 구성 된 경우 ' 이미지 클라우드-초기화 준비 ' 문서입니다.


### <a name="canonical"></a>Canonical
| 게시자/버전| 제품 | SKU | 버전 | 이미지 클라우드-초기화 준비 | Azure의 클라우드 초기화 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|정식 18.04 |UbuntuServer |18.04-LTS |최신 |예 | 예 |
|정식 16.04|UbuntuServer |16.04-LTS |최신 |예 | 예 |
|정식 14.04|UbuntuServer |14.04.5-LTS |최신 |예 | 예 |

### <a name="rhel"></a>RHEL
| 게시자/버전 | 제품 | SKU | 버전 | 이미지 클라우드-초기화 준비 | Azure의 클라우드 초기화 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |예 | 예-패키지 버전에 대 한 지원: *18.2-1. el7_6 2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 예 (미리 보기 이미지 이며, 모든 RHEL 7.7 이미지가 클라우드 init를 지 원하는 경우이를 2020 제거 합니다. | 예-패키지 버전: *18.5 -3. el7* 에서 지원|
|RedHat 7.7 |RHEL |7-RAW | 해당 없음| 4 월 2020 끝까지 이미지를 업데이트 하지 않음| 예-패키지 버전: *18.5 -3. el7* 에서 지원|
|RedHat 7.7 |RHEL |7-LVM | 해당 없음| 4 월의 끝을 완료 하기 위한 이미지 업데이트 없음| 예-패키지 버전: *18.5 -3. el7* 에서 지원|
|RedHat 7.7 |RHEL |7.7 | 해당 없음| 4 월의 끝을 완료 하기 위한 이미지 업데이트 없음 | 예-패키지 버전: *18.5 -3. el7* 에서 지원|
|RedHat 7.7 |rhel byos | rhel-lvm77 | 해당 없음|4 월의 끝을 완료 하기 위한 이미지 업데이트 없음  | 예-패키지 버전: *18.5 -3. el7* 에서 지원|

### <a name="centos"></a>CentOS

| 게시자/버전 | 제품 | SKU | 버전 | 이미지 클라우드-초기화 준비 | Azure의 클라우드 초기화 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |예 (미리 보기 이미지 이며, 모든 CentOS 7.7 이미지가 클라우드 init를 지 원하는 경우이를 2020 제거 합니다. | 예-패키지 버전: *18.5 -3. el7. centos* 에서 지원 됩니다.|

* Cloud init를 사용 하도록 설정 된 CentOS 7.7 이미지는 3 월 2020에서 업데이트할 수 있습니다. 

### <a name="oracle"></a>Oracle

| 게시자/버전 | 제품 | SKU | 버전 | 이미지 클라우드-초기화 준비 | Azure의 클라우드 초기화 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| 미리 보기 이미지 (미리 보기 이미지 이며, 모든 Oracle 7.7 이미지가 클라우드 init를 지 원하는 경우이를 2020 제거 합니다. | 아니요, 미리 보기에서 패키지는: *18.5-3.0.1. el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
현재 preview 지원에 대 한 작업을 수행 하 고 있으며 2 월 및 3 월 2020에 업데이트를 제공 합니다.

현재 Azure Stack는 클라우드 초기화 사용 이미지의 프로 비전을 지원 합니다.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init와 Linux 에이전트(WALA)의 차이는 무엇입니까?
WALA는 Vm을 프로 비전 및 구성 하 고 [azure 확장](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)을 처리 하는 데 사용 되는 azure 플랫폼별 에이전트입니다. 

기존 클라우드 init 고객이 현재 클라우드 초기화 스크립트를 사용 하거나 새로운 고객에 게 다양 한 클라우드 초기화 구성 기능을 활용할 수 있도록 하기 위해 Linux 에이전트 대신 클라우드 초기화를 사용 하도록 Vm을 구성 하는 작업을 개선 하 고 있습니다. Linux 시스템을 구성 하기 위한 클라우드 init 스크립트에 대 한 기존 투자가 있는 경우 클라우드 초기화를 사용 하도록 설정 하는 데 **필요한 추가 설정은 없습니다** . 

클라우드 초기화는 Azure 확장을 처리할 수 없으므로 WALA는 이미지에서 확장을 처리 해야 하지만, 프로 비전 코드를 사용 하지 않도록 설정 해야 합니다. 보증 Linux 배포판에서 프로 비전 하 여 프로 비전 하도록 변환 되는 이미지의 경우 WALA가 설치 되 고 올바르게 설정 됩니다.

VM을 만들 때 프로 비전 시간에 Azure CLI `--custom-data` 스위치를 포함 하지 않으면 클라우드 초기화 또는 WALA에서 vm을 프로 비전 하 고 기본값을 사용 하 여 배포를 완료 하는 데 필요한 최소 vm 프로 비전 매개 변수를 사용 합니다.  `--custom-data` 스위치를 사용 하 여 클라우드 init 구성을 참조 하는 경우 VM이 부팅 될 때 사용자 지정 데이터에 포함 된 모든 항목이 클라우드 초기화에 제공 됩니다.

Vm에 적용 되는 클라우드 초기화 구성에는 시간 제한이 없으며 시간 초과로 인해 배포에 실패 하지 않습니다. WALA의 경우에는 적용 되지 않습니다. 사용자 지정 데이터를 처리 하도록 WALA 기본값을 변경 하는 경우 총 VM 프로 비전 시간 허용 한도를 초과할 수 없습니다 .이 경우 VM 만들기가 실패 합니다.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>cloud-init를 사용하는 가상 머신 배포
cloud-init를 사용하는 가상 머신 배포 방법은 배포하는 동안 cloud-init 지원 배포를 참조하는 것만큼 간단합니다.  Linux 배포 유지 관리자는 cloud-init를 사용하도록 설정하고 기본 Azure 게시 이미지와 통합하도록 선택해야 합니다. 배포하려는 이미지에서 cloud-init가 설정되었는지 확인한 후 AzureCLI를 사용하여 이미지를 배포할 수 있습니다. 

이미지 배포의 첫 번째 단계로 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
그 다음 단계로 현재 셸에서 *cloud-init.txt*라는 파일을 만들고 다음 구성을 붙여넣습니다. 이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor cloud-init.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
`ctrl-X` 키를 눌러 파일을 종료하고, `y`를 입력하여 파일을 저장하고 `enter` 키를 눌러 종료 시 파일 이름을 확인합니다.

마지막 단계로 [az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 *centos74*라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  `--custom-data` 매개 변수를 사용하여 cloud-init 구성 파일을 전달합니다. 현재 작업 디렉터리 외부에 파일을 저장한 경우 *cloud-init.txt* 구성의 전체 경로를 제공합니다. 다음 예제에서는 *centos74*라는 VM을 만듭니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

VM이 생성되면 Azure CLI가 배포에 대한 정보를 표시합니다. `publicIpAddress`을 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.  VM을 만들고 패키지를 설치하고 앱을 시작하는 데 시간이 약간 걸립니다. Azure CLI에서 프롬프트로 반환한 후 실행을 계속하는 백그라운드 작업이 있습니다. VM에 SSH한 후 문제 해결 섹션에 설명된 단계를 사용하여 cloud-init 로그를 볼 수 있습니다. 

## <a name="troubleshooting-cloud-init"></a>cloud-init 문제 해결
VM이 프로비전되면 cloud-init는 `--custom-data`에 정의된 모든 모듈과 스크립트를 실행하여 VM을 구성합니다.  구성 오류 또는 누락을 해결해야 하는 경우 **/var/log/cloud-init.log**에 있는 cloud-init 로그에서 모듈 이름(예: `disk_setup` 또는 `runcmd`)을 검색해야 합니다.

> [!NOTE]
> 모든 모듈 실패가 심각한 cloud-init 전체 구성 실패로 이어지는 것은 아닙니다. 예를 들어 `runcmd` 모듈을 사용하는 경우 스크립트가 실패해도 runcmd 모듈이 실행되었기 때문에 cloud-init는 성공한 프로비전을 계속 보고합니다.

cloud-init 로깅에 대한 자세한 내용은 [cloud-init 설명서](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 cloud-init 예제를 보려면 다음 문서를 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
 
