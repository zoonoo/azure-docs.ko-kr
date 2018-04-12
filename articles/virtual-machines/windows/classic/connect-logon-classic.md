---
title: 클래식 Azure VM에 로그온 | Microsoft Docs
description: Azure Portal을 사용하여 클래식 배포 모델로 만든 Windows 가상 머신에 로그온할 수 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: bcbebc216dbd63acfb33cf72ba774d088149a3a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Azure 포털을 사용하여 Windows 가상 머신에 로그온
Azure 포털에서 **연결** 단추를 사용하여 원격 데스크톱 세션을 시작하고 Windows VM에 로그온합니다.

Linux VM에 연결하고 싶으세요? [Linux를 실행하는 가상 머신에 로그온하는 방법](../../linux/mac-create-ssh-keys.md)을 참조하세요.

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 모델을 사용하여 VM에 로그온하는 방법에 대한 정보는 [여기](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결
1. Azure 포털에 로그인합니다.
2. 액세스할 가상 머신을 클릭합니다. 이름은 **모든 리소스** 창에 나열됩니다.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. 가상 머신 대시보드 위쪽의 명령 모음에서 **연결**을 클릭합니다.

    ![가상 컴퓨터의 연결 아이콘](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>가상 머신에 로그온
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>다음 단계
* **연결** 단추가 비활성 상태이거나 원격 데스크톱 연결에 다른 문제가 있는 경우 구성을 다시 설정해 봅니다. 가상 컴퓨터 대시보드에서 **원격 액세스 재설정**을 클릭합니다.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* 암호에 문제가 있는 경우 암호를 다시 설정합니다. **지원 + 문제 해결** 아래에서 가상 머신 대시보드의 왼쪽 가장자리를 따라 **암호 재설정**을 클릭합니다.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

이러한 팁이 작동하지 않거나 필요한 정보가 아닌 경우 [Windows 기반 Azure Virtual Machine에 대한 원격 데스크톱 연결 문제 해결](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 이 문서에서는 일반적인 문제를 진단 및 해결하는 과정을 안내합니다.
