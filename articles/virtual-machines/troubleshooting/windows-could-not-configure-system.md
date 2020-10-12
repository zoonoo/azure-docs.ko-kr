---
title: Windows에서 시스템 구성을 완료할 수 없는 문제 해결
titlesuffix: Azure Virtual Machines
description: 이 문서에서는 Sysprep 프로세스가 Azure VM의 부팅을 방지 하는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90078811"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Windows에서 시스템 구성을 완료할 수 없는 문제 해결

이 문서에서는 Sysprep 프로세스에서 Azure VM (가상 머신)의 부팅을 방지 하는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 windows 설치 프로그램에서 서비스를 시작 하는 동안 스크린샷에 windows 설치 오류가 표시 되는 것을 볼 수 있습니다. 오류가 발생 하면 다음과 같은 메시지가 표시 됩니다.

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![그림 1에는 "Windows에서 시스템 구성을 완료할 수 없습니다." 라는 메시지와 함께 Windows 설치 오류가 표시 됩니다. 구성을 다시 시작 하려면 컴퓨터를 다시 시작 합니다. 설치 프로그램이 서비스를 시작 합니다. "](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>원인

OS (운영 체제)에서 [Sysprep 프로세스](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview)를 완료할 수 없는 경우이 오류가 발생 합니다. 이 오류는 일반화 된 VM의 초기 부팅을 시도할 때 발생 합니다. 이 문제가 발생 하는 경우 이미지가 배포 되지 않은 상태 이며 복구할 수 없기 때문에 일반화 된 이미지를 다시 만듭니다.

## <a name="solution"></a>솔루션

이 문제를 해결 하려면 [이미지 준비/캡처](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) 및 새로운 일반화 된 이미지 준비에 대 한 Azure 지침을 따르세요.
