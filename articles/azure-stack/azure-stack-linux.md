---
title: "Azure 스택 Linux 이미지를 추가 합니다."
description: "자세한 내용은 Azure 스택에 Linux 이미지를 추가 하는 방법입니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: anajod
ms.openlocfilehash: 29e5443de4dc43efe6d536b0f8b9cfc6ad37a669
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Azure 스택 Linux 이미지를 추가 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트* 

Azure 스택 Marketplace에 Linux 기반 이미지를 추가 하 여 Azure 스택의 Linux 가상 컴퓨터를 배포할 수 있습니다. 마켓플레이스 관리를 통해 Azure 스택에 Linux 이미지를 추가 하는 가장 쉬운 방법은 됩니다.

## <a name="marketplace-management"></a>마켓플레이스 관리

Azure 마켓플레이스에서 Linux 이미지를 다운로드 하려면 문서에 설명 된 절차를 따르세요. 사용자가 Azure 스택에 제공 하려는 Linux 이미지를 선택 합니다.

[Azure에서 Azure 스택에 마켓플레이스 항목을 다운로드](azure-stack-download-azure-marketplace-item.md)합니다.

## <a name="download-an-image"></a>이미지 다운로드

다운로드 하 고 다음 링크를 사용 하 여 Azure 스택 호환 Linux 이미지를 추출할 수 있습니다.:


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. 필요한 경우에 VHD 이미지를 추출 하 고 [시장에 이미지를 추가](azure-stack-add-vm-image.md)합니다. 다음 사항을 확인는 `OSType` 로 설정 된 `Linux`합니다.
2. 마켓플레이스 이미지를 추가한 후 마켓플레이스 항목이 만들어지고 사용자가 Linux 가상 컴퓨터를 배포할 수 있습니다.

## <a name="prepare-your-own-image"></a>사용자 고유의 이미지를 준비 합니다.

다음 지침 중 하나를 사용 하 여 사용자 고유의 Linux 이미지를 준비할 수 있습니다.
   
   * [CentOS 기반 배포](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES 및 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. 다운로드 및 설치는 [Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/)합니다.
   
    Azure Linux 에이전트 버전 2.1.3 아니거나 이상이 스택에 Azure Linux VM을 프로 비전 하는 데 필요 합니다. 대부분의 열거 된 배포 이전에 이미 포함이 버전의 에이전트 이상 패키지로 해당 버전의 저장소에서 (일반적으로 호출 `WALinuxAgent` 또는 `walinuxagent`). 그러나 Azure 에이전트 패키지의 버전이 2.1.3 보다 작은 경우 (예를 들어 2.0.18 또는 아래) 에이전트를 수동으로 설치 해야 합니다. 패키지 이름 또는 실행 하 여 설치 된 버전을 확인할 수 있습니다 `/usr/sbin/waagent -version` VM에 있습니다.
   
    Azure Linux 에이전트를 수동으로 설치 하려면 다음 지침에 따라
   
   a. 첫째,에서 최신 Azure Linux 에이전트를 다운로드 [GitHub](https://github.com/Azure/WALinuxAgent/releases), 예:
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   나. Azure 에이전트 압축을 풉니다.
     
            # tar -vzxf v2.2.16.tar.gz
   다. Python setuptools 설치
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Azure 에이전트를 설치 합니다.
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     시스템 python 2.x 및 Python 설치 3.x-함께 할 수 있습니다 다음 명령을 실행 하려면:
     
         sudo python3 setup.py install --register-service
     자세한 내용은 Azure Linux 에이전트를 참조 하십시오. [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md)합니다.
2. [마켓플레이스 이미지를 추가](azure-stack-add-vm-image.md)합니다. 다음 사항을 확인는 `OSType` 로 설정 된 `Linux`합니다.
3. 마켓플레이스 이미지를 추가한 후 마켓플레이스 항목이 만들어지고 사용자가 Linux 가상 컴퓨터를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택에서 서비스 제공의 개요](azure-stack-offer-services-overview.md)

