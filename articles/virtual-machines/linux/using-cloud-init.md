---
title: Azure의 Linux VM에 대한 클라우드 init 지원 개요
description: Azure에서 프로비저닝 시 VM을 구성하는 클라우드 init 기능 개요입니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465042"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure의 가상 시스템에 대한 클라우드 init 지원
이 문서에서는 Azure에서 프로비저닝 시 가상 시스템(VM) 또는 가상 시스템 규모 집합을 구성하기 위해 [클라우드 초기화에](https://cloudinit.readthedocs.io) 존재하는 지원에 대해 설명합니다. 이러한 클라우드-init 구성은 Azure에서 리소스를 프로비전한 후 첫 번째 부팅에서 실행됩니다.  

VM 프로비저닝은 Azure에서 호스트 이름, 사용자 이름, 암호 등과 같은 VM Create 매개 변수 값을 전달하고 부팅시 VM에서 사용할 수 있도록 하는 프로세스입니다. '프로비저닝 에이전트'는 이러한 값을 사용하고 VM을 구성하며 완료되면 다시 보고합니다. 

Azure는 두 개의 프로비저닝 에이전트 [클라우드-init](https://cloudinit.readthedocs.io)및 [AZURE Linux 에이전트(WALA)를](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)지원합니다.

## <a name="cloud-init-overview"></a>클라우드-인it 개요
[클라우드-이니트는](https://cloudinit.readthedocs.io) Linux VM을 처음으로 부팅할 때 사용자 지정하는 데 널리 사용되는 접근 방식입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 호출되므로 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.  `#cloud-config` 파일 또는 기타 입력의 형식을 올바르게 지정하는 방법에 대한 자세한 내용은 [클라우드-init 문서 사이트를](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)참조하십시오.  `#cloud-config` 파일은 base64로 인코딩된 텍스트 파일입니다.

클라우드-init은 또한 배포판 에서 작동합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install**은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. 클라우드-init 자동으로 선택한 배포에 대 한 네이티브 패키지 관리 도구를 사용 합니다.

Azure Marketplace에서 cloud-init 활성화 이미지를 사용할 수 있도록 하기 위해 승인된 Linux 배포판 파트너와 적극적으로 공조하고 있습니다. 이러한 이미지를 사용하면 VM 및 가상 머신 확장 집합에서 cloud-init 배포 및 구성 작업을 원활하게 진행할 수 있습니다. 처음에 우리는 승인 된 리눅스 배포판 파트너 및 업스트림 Azure에 OS와 클라우드 초기화 기능을 보장 하기 위해 협력, 다음 패키지 업데이트 하 고 배포판 패키지 저장소에서 공개적으로 사용할 수 있게. 

Azure에서 승인된 Linux 배포판 OS에서 클라우드 초기화, 패키지 지원 및 이미지 지원에 사용할 수 있도록 하는 두 단계가 있습니다.
* '클라우드 init 패키지 지원 Azure' 문서 이후 패키지를 지원 하거나 미리 보기, 그래서 사용자 지정 이미지에서 OS와 함께 이러한 패키지를 사용할 수 있습니다.
* 이미지가 이미 클라우드-init을 사용하도록 구성된 경우 '이미지 클라우드-init 준비' 문서입니다.


### <a name="canonical"></a>Canonical
| 게시자 / 버전| 제안 | SKU | 버전 | 이미지 클라우드-init 준비 | Azure에서 클라우드 init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|정식 18.04 |UbuntuServer |18.04-LTS |최신 |예 | 예 |
|표준 16.04|UbuntuServer |16.04-LTS |최신 |예 | 예 |
|표준 14.04|UbuntuServer |14.04.5-LTS |최신 |예 | 예 |

### <a name="rhel"></a>RHEL
| 게시자 / 버전 | 제안 | SKU | 버전 | 이미지 클라우드-init 준비 | Azure에서 클라우드 init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |예 | 예 - 패키지 버전에서 지원: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 예 (이 미리보기 이미지입니다 참고, 모든 RHEL 7.7 이미지는 클라우드 init을 지원하면,이 2020 년 중반에 제거됩니다, 통지가 주어질 것이다) | 예 - 패키지 버전에서 지원 : *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-RAW | 해당 없음| 아니오 - 2020년 4월 말 에 이미지 업데이트 완료| 예 - 패키지 버전에서 지원 : *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | 해당 없음| 아니오 - 4월 말 완료를 위한 이미지 업데이트| 예 - 패키지 버전에서 지원 : *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | 해당 없음| 아니오 - 4월 말 완료를 위한 이미지 업데이트 | 예 - 패키지 버전에서 지원 : *18.5-3.el7*|
|RedHat 7.7 |렐 바이오스 | rhel-lvm77 | 해당 없음|아니오 - 4월 말 완료를 위한 이미지 업데이트  | 예 - 패키지 버전에서 지원 : *18.5-3.el7*|

### <a name="centos"></a>CentOS

| 게시자 / 버전 | 제안 | SKU | 버전 | 이미지 클라우드-init 준비 | Azure에서 클라우드 init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|오픈로직 7.7 |CentOS |7-CI |7.7.20190920 |예 (이 미리보기 이미지입니다 참고, 모든 CentOS 7.7 이미지는 클라우드 초기화지원되면, 이것은 2020 년 중반에 제거됩니다, 통지가 주어질 것이다) | 예 - 패키지 버전에서 지원 : *18.5-3.el7.centos*|

* 클라우드 초기화가 활성화될 CentOS 7.7 이미지는 2020년 3월에 업데이트될 예정입니다. 

### <a name="oracle"></a>Oracle

| 게시자 / 버전 | 제안 | SKU | 버전 | 이미지 클라우드-init 준비 | Azure에서 클라우드 init 패키지 지원|
|:--- |:--- |:--- |:--- |:--- |:--- |
|오라클 7.7 |Oracle-Linux |77-시 |7.7.01| 미리보기 이미지 (이 미리보기 이미지입니다 참고, 그것은 모든 오라클 7.7 이미지가 클라우드 init을 지원하면, 이것은 2020 년 중반에 제거됩니다, 통지가 주어질 것이다) | 아니요, 미리 보기에서 패키지는 *: 18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>데비안 & 수세 SLES
우리는 현재 지원을 미리 보기 위해 노력하고 있습니다, 2 월과 3 월에 업데이트를 기대 2020.

현재 Azure 스택 클라우드 init 사용 가능한 이미지의 프로비저닝을 지원 합니다.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init와 Linux 에이전트(WALA)의 차이는 무엇입니까?
WALA는 VM을 프로비전 및 구성하고 Azure 확장을 처리하는 데 사용되는 Azure 플랫폼별 [에이전트입니다.](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux) 

기존 클라우드 init 고객이 현재 클라우드-init 스크립트를 사용하거나 새로운 고객이 풍부한 클라우드 init 구성 기능을 활용할 수 있도록 Linux 에이전트 대신 클라우드 init을 사용하도록 VM을 구성하는 작업을 강화하고 있습니다. Linux 시스템을 구성하기 위한 클라우드 init 스크립트에 기존 투자가 있는 경우 클라우드 init 프로세스를 활성화하는 **데 필요한 추가 설정이 없습니다.** 

클라우드-init Azure 확장을 처리할 수 없습니다., 그래서 WALA는 여전히 확장을 처리 하는 이미지에 필요 합니다., 하지만 그것의 프로비저닝 코드를 사용 하지 않도록 설정 해야 합니다., 승인 된 리눅스 배포판 이미지 에 의해 프로비저닝으로 변환 되 고, 그들은 WALA를 해야 합니다. 설치하고 올바르게 설정합니다.

VM을 만들 때 프로비저닝 시 `--custom-data` Azure CLI 스위치를 포함하지 않으면 클라우드-init 또는 WALA는 VM을 프로비전하고 기본값으로 배포를 완료하는 데 필요한 최소한의 VM 프로비저닝 매개 변수를 사용합니다.  스위치를 사용하여 클라우드 init 구성을 `--custom-data` 참조하는 경우 사용자 지정 데이터에 포함된 모든 것을 VM이 부팅할 때 클라우드 init에 사용할 수 있습니다.

VM에 적용된 클라우드 init 구성에는 시간 제약 조건이 없으며 시간 제한으로 인해 배포가 실패하지 않습니다. WALA 기본값을 사용자 지정 데이터를 처리하도록 변경하면 VM 만들기가 실패할 경우 총 VM 프로비저닝 시간 허용량 40분초과를 초과할 수 없습니다.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>cloud-init를 사용하는 가상 머신 배포
cloud-init를 사용하는 가상 머신 배포 방법은 배포하는 동안 cloud-init 지원 배포를 참조하는 것만큼 간단합니다.  Linux 배포 유지 관리자는 cloud-init를 사용하도록 설정하고 기본 Azure 게시 이미지와 통합하도록 선택해야 합니다. 배포하려는 이미지에서 cloud-init가 설정되었는지 확인한 후 AzureCLI를 사용하여 이미지를 배포할 수 있습니다. 

이미지 배포의 첫 번째 단계로 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *동쪽* 위치에 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

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
 
