---
title: Python 3 사용 Linux Azure Virtual Machines 시스템에서 VM 확장을 사용하는 문제
description: Python 3 사용 Linux 시스템에서 VM 확장을 사용하는 방법에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 7c785fbeb5d1c1f0d88c53a75eda6cafca863b9e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108768160"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Python 3 사용 Linux Azure Virtual Machines 시스템에서 VM 확장을 사용하는 문제

> [!NOTE]
> Microsoft는 워크로드에 **Python 2.x** 지원이 필요하지 않는 한 사용자가 시스템에 **Python 3.x** 를 채택하도록 권장합니다. 이러한 요구 사항의 예에는 레거시 관리 스크립트나 **Azure Disk Encryption** 및 **Azure Monitor** 와 같은 확장이 포함 될 수 있습니다.
>
> 프로덕션 환경에 **Python 2.x** 를 설치하기 전에 Python 2.x의 장기 지원, 특히 보안 업데이트를 받을 수 있는 기능에 대한 문제를 고려하십시오. 언급된 확장 중 일부를 포함한 제품이 **Python 3.8** 지원으로 업데이트되므로 Python 2.x 사용을 중단해야 합니다.

일부 Linux 배포판은 Python 3.8로 전환되었으며 Python의 레거시 `/usr/bin/python` 진입점을 모두 제거했습니다. 이러한 전환은 다음과 같은 두 가지 조건에서 특정 VM(가상 머신) 확장의 즉시 사용 가능한 자동화된 배포에 영향을 줍니다.

- Python 3.x 지원으로 전환 중인 확장
- 레거시 `/usr/bin/python` 진입점을 사용하는 확장

**Python 3.x** 로 전환한 Linux 배포 사용자는 해당 확장을 VM에 배포하기 전에 레거시`/usr/bin/python` 진입점이 존재하는지 확인해야 합니다. 그렇지 않으면 확장 배포가 실패할 수 있습니다. 

- 영향을 받는 Linux 보증 배포판에는 **Ubuntu Server 20.04 LTS** 및 **Ubuntu Pro 20.04 LTS** 가 포함됩니다.

- 영향을 받는 VM 확장에는 **Azure Disk Encryption**, **Log Analytics**, **VM 액세스**(암호 재설정에 사용됨) 및 **게스트 진단**(추가 성능 카운터에 사용됨)이 포함됩니다.

**Ubuntu 18.04 LTS** 에서 **Ubuntu 20.04 LTS** 로 업그레이드하는 것과 같은 현재 위치 업그레이드는 `/usr/bin/python` symlink를 유지하고 영향을 받지 않아야 합니다.

## <a name="resolution"></a>해결 방법

앞의 요약에 설명한 영향을 받는 것으로 알려진 시나리오에서 확장을 배포하기 전에 다음과 같은 일반적인 권장 사항을 고려합니다.

1. 확장을 배포하기 전에 Linux 배포 공급업체에서 제공하는 방법을 사용하여 `/usr/bin/python` symlink를 복구합니다.

   - 예를 들어, **Python 2.7** 의 경우 `sudo apt update && sudo apt install python-is-python2`를 사용합니다.

1. 이 권장 사항은 Azure 고객을 위한 것이며 Azure Stack에서는 지원되지 않습니다.

   - 이 문제가 발생하는 인스턴스를 이미 배포한 경우 VM 블레이드의 실행 명령 기능을 사용하여 위에서 언급한 명령을 실행합니다. 실행 명령 확장 자체는 Python 3.8로 전환해도 영향을 받지 않습니다.

1. 새 인스턴스를 배포하고 프로비전 시간에 확장을 설정해야 하는 경우 **cloud-init** 사용자 데이터를 사용하여 위에서 언급한 패키지를 설치합니다.

   예를 들어, Python 2.7의 경우 다음과 같습니다.

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. 조직의 정책 관리자가 VM에 확장을 배포하지 않도록 결정하는 경우 프로비전 시간에 확장 지원을 사용하지 않도록 설정할 수 있습니다.

   - REST API

     이 속성을 사용하여 VM을 배포할 수 있는 경우 확장을 사용하거나 사용하지 않도록 설정하려면 다음을 수행합니다.

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [18.04 LTS 이후 기타 기본 시스템 변경 - 기본적으로 Python 3](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default)을 참조하세요.
