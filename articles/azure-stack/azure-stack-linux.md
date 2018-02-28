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
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Azure 스택 Linux 이미지를 추가 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 Marketplace에 Linux 기반 이미지를 추가 하 여 Azure 스택에 Linux 가상 컴퓨터 (Vm)를 배포할 수 있습니다. 마켓플레이스 관리를 통해 Azure 스택에 Linux 이미지를 추가 하는 가장 쉬운 방법은 됩니다. 이러한 이미지가 준비 하 고 Azure 스택와의 호환성에 대 한 테스트 합니다.

## <a name="marketplace-management"></a>마켓플레이스 관리

Azure 마켓플레이스에서 Linux 이미지를 다운로드 하려면 문서에 설명 된 절차를 따르세요. 사용자가 Azure 스택에 제공 하려는 Linux 이미지를 선택 합니다.

[Azure에서 Azure 스택에 마켓플레이스 항목을 다운로드](azure-stack-download-azure-marketplace-item.md)합니다.

## <a name="prepare-your-own-image"></a>사용자 고유의 이미지를 준비 합니다.

다음 지침 중 하나를 사용 하 여 사용자 고유의 Linux 이미지를 준비할 수 있습니다.

   * [CentOS 기반 배포](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES 및 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. 다운로드 및 설치는 [Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/)합니다.

    Azure Linux 에이전트 버전 2.2.2 아니거나 이상이 Linux VM Azure 스택 및 일부 버전에 적용할 수 없습니다 (2.2.12 및 예제를 보려면 2.2.13) 프로 비전 하는 데 필요 합니다. 대부분의 열거 된 배포 이전에 이미 포함 에이전트의 버전 (일반적으로 호출 `WALinuxAgent` 또는 `walinuxagent`). 사용자 지정 이미지를 만드는 경우 에이전트를 수동으로 설치 해야 합니다. 패키지 이름 또는 실행 하 여 설치 된 버전을 확인할 수 있습니다 `/usr/sbin/waagent -version` VM에 있습니다.

    Azure Linux 에이전트를 수동으로 설치 하려면 다음 지침에 따라

   a. 첫째,에서 최신 Azure Linux 에이전트를 다운로드 [GitHub](https://github.com/Azure/WALinuxAgent/releases), 예:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   나. Azure 에이전트 압축을 풉니다.

            # tar -vzxf v2.2.21.tar.gz
   다. Python setuptools 설치

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Azure 에이전트를 설치 합니다.

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     시스템 python 2.x 및 Python 설치 3.x-함께 할 수 있습니다 다음 명령을 실행 하려면:

         sudo python3 setup.py install --register-service
     자세한 내용은 Azure Linux 에이전트를 참조 하십시오. [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md)합니다.
2. [마켓플레이스 이미지를 추가](azure-stack-add-vm-image.md)합니다. 다음 사항을 확인는 `OSType` 로 설정 된 `Linux`합니다.
3. 마켓플레이스 이미지를 추가한 후 마켓플레이스 항목이 만들어지고 사용자가 Linux 가상 컴퓨터를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택에서 서비스 제공의 개요](azure-stack-offer-services-overview.md)
