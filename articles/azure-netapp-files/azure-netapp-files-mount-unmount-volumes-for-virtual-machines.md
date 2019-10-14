---
title: Windows 또는 Linux 가상 머신용 Azure NetApp Files 볼륨 탑재 또는 탑재 해제 | Microsoft Docs
description: 가상 머신 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 탑재 해제하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: b-juche
ms.openlocfilehash: 76e01055043932f2c7e7d57bd7eed6265d666a8c
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302780"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제 

필요에 따라 Windows 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 탑재 해제할 수 있습니다.  Linux 가상 머신에 대한 탑재 지침은 Azure NetApp Files에서 사용할 수 있습니다.  

1. **볼륨** 블레이드를 클릭 하 고 탑재 하려는 볼륨을 선택 합니다. 
2. 선택한 볼륨에서 **명령 탑재** 를 클릭 한 다음 지침에 따라 볼륨을 탑재 합니다. 

    ![NFS 탑재 명령](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![탑재 명령 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
NFSv 4.1을 사용 하는 경우 다음 명령을 사용 하 여 파일 시스템을 탑재 합니다. `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`
