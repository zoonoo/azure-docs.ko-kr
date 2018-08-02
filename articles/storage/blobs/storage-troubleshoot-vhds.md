---
title: Azure VM에 연결된 디스크 문제 해결 | Microsoft Docs
description: Azure Blob 저장소는 텍스트 또는 이진 데이터와 같이 구조화되지 않은 대량의 개체 데이터를 저장하도록 설계되었습니다. 응용 프로그램은 Blob 저장소의 개체를 PowerShell 또는 Azure CLI에서 액세스하거나, Azure Storage 클라이언트 라이브러리 또는 REST를 통해 코드에서 액세스할 수 있습니다.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.component: disks
ms.openlocfilehash: 0dbd89c28d18d64908d92cd38d8bd1cf3138fd5c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397970"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Azure VM에 연결된 디스크 문제 해결 

Azure VM(Virtual Machines)은 OS 디스크 및 연결된 모든 데이터 디스크에 VHD(가상 하드 디스크)를 사용합니다. VHD는 하나 이상의 Azure Storage 계정에 페이지 Blob으로 저장됩니다. 이 문서에서는 VHD에서 발생할 수 있는 일반적인 문제 해결 방법을 설명합니다. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>VM의 저장소 리소스 삭제 오류 문제 해결

경우에 따라 연결된 VHD가 Resource Manager 배포의 VM에 들어 있을 때 저장소 리소스를 삭제하면 오류가 발생할 수 있습니다. 이 문제를 해결하기 위한 도움말은 다음 문서 중 하나를 참조하세요. 

  * Linux VM: [Resource Manager 배포의 저장소 삭제 오류](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Windows VM: [Resource Manager 배포의 저장소 삭제 오류](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>VHD가 연결된 VM이 예기치 않게 다시 부팅되는 문제 해결

많은 VHD가 연결된 VM이 예기치 않게 재부팅되는 경우 다음 문서 중 하나를 참조하세요.

  * Linux VM: [VHD가 연결된 VM이 예기치 않게 다시 부팅되는 경우](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Windows VM: [VHD가 연결된 VM이 예기치 않게 다시 부팅되는 경우](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
