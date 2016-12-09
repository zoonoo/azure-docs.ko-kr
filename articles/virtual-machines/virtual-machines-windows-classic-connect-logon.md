---
title: "클래식 Azure VM에 로그온 | Microsoft Docs"
description: "Azure 클래식 포털을 사용하여 클래식 배포 모델로 만든 Windows 가상 컴퓨터에 로그온할 수 있습니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b2a66b134d42e0b2d965c1f3ae83f93bed9cdfba


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Azure 클래식 포털을 사용하여 Windows 가상 컴퓨터에 로그온
Azure 클래식 포털에서 **연결** 버튼을 사용하여 원격 데스크톱 세션을 시작 및 Windows VM에 로그온합니다.

Linux VM에 연결하고 싶으세요? [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

[새 Azure Portal을 사용하여 이러한 단계를 수행](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하는 방법을 알아봅니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Resource Manager 모델을 사용하여 VM에 로그온하는 방법에 대한 정보는 [여기](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="video-walkthrough"></a>연습 동영상
이 자습서의 비디오 연습은 다음과 같습니다. 또한 Azure에서 Windows VM에 연결하는 데 사용되는 끝점과 공용 및 개인 포트에 대해서도 설명합니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>가상 컴퓨터에 연결
1. Azure 클래식 포털에 로그인합니다.
2. **가상 컴퓨터**를 클릭한 다음 가상 컴퓨터를 선택합니다.
3. 페이지 아래쪽의 명령 모음에서 **연결**을 클릭합니다.
   
    ![가상 컴퓨터에 로그온](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> **연결** 버튼을 사용할 수 없는 경우 이 문서의 끝에 문제 해결 팁을 참조하세요.
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>가상 컴퓨터에 로그온
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>다음 단계
* **연결** 단추가 비활성 상태이거나 원격 데스크톱 연결에 다른 문제가 있는 경우 구성을 다시 설정해 봅니다. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **원격 구성 재설정**을 클릭합니다.
* 암호에 문제가 있는 경우 암호를 다시 설정합니다. 가상 컴퓨터 대시보드에서, **빠른 보기** 아래 **암호 재설정**을 클릭합니다.

이러한 팁이 작동하지 않거나 필요한 정보가 아닌 경우 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.




<!--HONumber=Nov16_HO3-->


