---
title: "Azure CLI 샘플 | Microsoft Docs"
description: "Azure CLI 샘플"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: a104e231ed079053ed5671703fe39b853dc094a7
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Linux 가상 컴퓨터에 대한 Azure CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| | |
|---|---|
|**가상 컴퓨터 만들기**||
| [가상 컴퓨터 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | 최소한의 구성으로 Linux 가상 컴퓨터를 만듭니다. |
| [완벽히 구성된 가상 컴퓨터 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | 리소스 그룹, 가상 컴퓨터 및 모든 관련된 리소스를 만듭니다.|
| [고가용성 가상 컴퓨터 만들기](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | 고가용성의 부하가 분산된 구성으로 여러 가상 컴퓨터를 만듭니다. |
| [Docker가 활성화된 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fcli%2fazure%2ftoc.json) | 가상 컴퓨터를 만들고 이 VM을 Docker 호스트로 구성하고 NGINX 컨테이너를 실행합니다. |
| [VM 만들기 및 구성 스크립트 실행](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | 가상 컴퓨터를 만들고 Azure 사용자 지정 스크립트 확장을 사용하여 NGINX를 설치합니다. |
| [WordPress가 설치된 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | 가상 컴퓨터를 만들고 Azure 사용자 지정 스크립트 확장을 사용하여 WordPress를 설치합니다. |
| [관리되는 OS 디스크에서 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 기존 관리되는 디스크를 OS 디스크로 연결하여 가상 컴퓨터를 만듭니다. |
| [스냅숏에서 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 먼저 스냅숏에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅숏에서 가상 컴퓨터를 만듭니다. |
|**네트워크 가상 컴퓨터**||
| [가상 컴퓨터 간의 네트워크 트래픽 보안](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | 두 개의 가상 컴퓨터, 모든 관련된 리소스 및 내부 및 외부 NSG(네트워크 보안 그룹)를 만듭니다. |
|**가상 컴퓨터 모니터링**||
| [Operations Management Suite를 사용하여 VM 모니터링](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | 가상 컴퓨터를 만들고 Operations Management Suite 에이전트를 설치하고 OMS 작업 영역에서 VM을 등록합니다.  |
|**가상 컴퓨터 문제 해결**||
| [VM 운영 체제 디스크 문제 해결](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | 두 번째 VM의 데이터 디스크처럼 첫 번째 VM에서 운영 체제 디스크를 탑재합니다. |
| | |

