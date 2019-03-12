---
title: Azure Stack에 Linux 이미지를 추가 합니다.
description: 에 대해 알아봅니다 어떻게 Azure Stack에 Linux 이미지를 추가 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 277af6f139e815f46894f5b8df82a1d92ef573d1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537136"
---
# <a name="add-linux-images-to-azure-stack"></a>Azure Stack에 Linux 이미지를 추가 합니다.

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack Marketplace에는 Linux 기반 이미지를 추가 하 여 Azure Stack에 Linux 가상 머신 (Vm)를 배포할 수 있습니다. Azure Stack에 Linux 이미지를 추가 하는 가장 쉬운 방법은 Marketplace 관리를 통해 됩니다. 이러한 이미지 준비 되어 Azure Stack을 사용 하 여 호환성을 테스트 합니다.

## <a name="marketplace-management"></a>Marketplace 관리

Azure Marketplace에서 Linux 이미지를 다운로드 하려면의 절차를 사용 합니다 [Azure에서 Azure Stack marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md) 문서. Azure Stack에에서 사용자에 게 제공 하려는 Linux 이미지를 선택 합니다. 

이러한 이미지를 자주 업데이트가 있는지, 따라서 Marketplace 관리를 최신 상태로 유지 하는 빈도 확인 합니다.

## <a name="prepare-your-own-image"></a>사용자 고유의 이미지를 준비 합니다.

가능 하면 준비 되었으며 Azure Stack에 대 한 테스트는 Marketplace 관리를 통해 사용 가능한 이미지를 다운로드 합니다.

Azure Linux 에이전트 (일반적으로 호출 `WALinuxAgent` 또는 `walinuxagent`) 필요한 경우 일부 버전의 에이전트는 Azure Stack에서 작동 하 고 합니다. 사용자 고유의 이미지를 만드는 경우 WALA의 최신 버전 또는 버전 2.2.20 사용 해야 합니다. 2.2.20 2.2.35.1 (배타) 사이의 버전 Azure Stack에서 작동 하지 않는 참고 합니다. 사실은 [에서 cloud-init](https://cloud-init.io/) 이 이번에 Azure Stack에서 지원 되지 않습니다.

다음 지침을 사용 하 여 고유한 Linux 이미지를 준비할 수 있습니다.

* [CentOS 기반 배포](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES 및 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Marketplace에 이미지 추가

따릅니다 [Marketplace 이미지를 추가할](azure-stack-add-vm-image.md)합니다. 있는지 확인 합니다 `OSType` 매개 변수는 설정 `Linux`합니다.

Marketplace 이미지를 추가한 후 마켓플레이스 항목을 생성 되 고 사용자는 Linux 가상 머신을 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure에서 Azure Stack marketplace 항목 다운로드](azure-stack-download-azure-marketplace-item.md)
- [Azure Stack Marketplace 개요](azure-stack-marketplace.md)
