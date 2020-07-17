---
title: Azure에서 가상 머신을 만들 때 VHD는 지원 되지 않습니다. | Microsoft Docs
description: 이 문서에서는 Microsoft Azure에서 가상 컴퓨터를 실행할 때 VHD 오류를 수정 하는 방법을 설명 합니다.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: ff4822b513ed2aea6a18ba45bffc1d060ee2410e
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937577"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>Azure에서 가상 머신을 만들 때 VHD는 지원 되지 않습니다.

이 문서는 Windows 또는 Linux에서 가상 컴퓨터를 실행할 때 VHD 오류를 수정 하는 데 도움이 됩니다.

## <a name="symptoms"></a>증상

업로드 된 VHD를 사용 하 여 Microsoft Azure에서 가상 컴퓨터를 만드는 경우 배포에 실패 하 고 다음과 같은 오류 메시지가 반환 됩니다. 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>원인

이 문제는 다음과 같은 이유로 발생합니다.

- VHD는 1MB 맞춤(오프셋)을 준수하지 않습니다. 지원되는 디스크 크기는 1MB * N입니다. 예를 들어 디스크는 102,401MB여야 합니다.
- VHD가 손상 되었거나 지원 되지 않습니다. 

## <a name="resolution"></a>해결 방법

> [!NOTE]
> 다음 수정 작업을 수행 하려면 Azure에 VHD를 업로드 하기 전에 고객이 이러한 단계를 수행 해야 합니다.

이 문제를 해결 하려면 디스크 크기를 조정 하 여 1mb 맞춤을 따르도록 합니다.

- Windows에서이 문제를 해결 하려면 [크기 조정 VHD PowerShell cmdlet](https://docs.microsoft.com/powershell/module/hyper-v/resize-vhd)을 사용 합니다. **크기 조정-VHD** 는 Azure PowerShell cmdlet이 아닙니다.

  1. [Windows Server에 Hyper-v 역할 설치](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [가상 디스크를 고정 크기의 VHD로 변환](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Linux에서 문제를 해결 하려면 [qemu-img 명령을](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)사용 합니다.

Azure VM을 만들기 위한 VHD를 만들고 업로드 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure CLI 1.0을 사용하여 사용자 지정 디스크 이미지에서 Linux VM 업로드 및 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)
- [Windows Server VHD 만들기 및 Azure에 업로드](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

문제가 계속 되 면 VHD가 손상 된 것일 수 있습니다. 이 경우 VHD를 처음부터 다시 작성 하는 것이 좋습니다.

자세한 내용은 다음 항목을 참조하세요.

- [Windows VHD 정보](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#about-vhds)
- [Linux VHD 정보](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds#about-vhds)