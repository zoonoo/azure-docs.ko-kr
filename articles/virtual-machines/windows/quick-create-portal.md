---
title: Azure 빠른 시작 - Windows VM 만들기 포털 | Microsoft Docs
description: Azure 빠른 시작 - Windows VM 만들기 포털
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/11/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 42e17e2eb4ef64f8d0e80713041d22e6fe182538
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Azure Portal을 사용하여 Windows 가상 머신 만들기

Azure Portal을 통해 Azure Virtual Machines를 만들 수 있습니다. 이 메서드는 가상 머신 및 관련된 모든 리소스를 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다. 이 빠른 시작에서는 가상 머신을 만들고 VM에 웹 서버를 설치하는 방법에 대해 단계별로 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal ( https://portal.azure.com ) 에 로그인합니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다. 

3. 가상 머신 정보를 입력합니다. 여기에서 입력한 이사용자 이름과 암호는 가상 머신에 로그인하는 데 사용됩니다 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다. 완료되면 **확인**을 클릭합니다.

    ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

4. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 

    ![VM 크기를 보여 주는 스크린샷](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

5. **설정** 아래에서 기본값을 유지하고 **확인**을 클릭합니다. 

6. 요약 페이지에서 **확인**을 클릭하여 가상 머신 배포를 시작합니다.

7. Azure Portal 대시보드에 VM을 고정합니다. 배포가 완료되면 VM 요약이 자동으로 열립니다.


## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

1. 가상 머신 속성에서 **연결** 단추를 클릭합니다. 원격 데스크톱 프로토콜 파일(.rdp 파일)이 생성되고 다운로드됩니다.

    ![포털 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 클릭합니다. Mac의 Mac 앱 스토어에서 이 [원격 데스크톱 클라이언트](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)와 같은 RDP 클라이언트가 필요합니다.

3. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다.

4. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.


## <a name="install-iis-using-powershell"></a>PowerShell을 사용하여 IIS 설치

가상 머신에서 PowerShell 세션을 시작하고 다음 명령을 실행하여 IIS를 설치합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

작업이 완료되면 RDP 세션을 종료하고 Azure Portal에서 VM 속성을 반환합니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기 

NSG(네트워크 보안 그룹)는 인바운드 및 아웃바운드 트래픽의 보안을 유지합니다. Azure Portal에서 VM이 만들어지면 RDP 연결의 포트 3389에서 인바운드 규칙이 만들어집니다. 이 VM이 웹 서버를 호스트하기 때문에 포트 80에 NSG 규칙을 만들어야 합니다.

1. 가상 머신에서 **리소스 그룹**의 이름을 클릭합니다.
2. **네트워크 보안 그룹**을 선택합니다. NSG는 **형식** 열을 사용하여 식별할 수 있습니다. 
3. 왼쪽 메뉴의 설정에서 **인바운드 보안 규칙**을 클릭합니다.
4. **추가**를 클릭합니다.
5. **이름**에서 **http**를 입력합니다. **포트 범위**를 80으로 설정하고 **작업**을 **허용**으로 설정해야 합니다. 
6. **확인**을 클릭합니다.


## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

IIS를 설치하고 VM에 포트 80을 열어서 인터넷에서 웹 서버에 액세스할 수 있습니다. 웹 브라우저를 열고 VM의 공용 IP 주소를 입력합니다. 공용 IP 주소는 Azure Portal의 *Virtual Machines* 아래에서 찾을 수 있습니다.

![IIS 기본 사이트](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 때 리소스 그룹, 가상 머신 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 VM에 대한 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 간단한 가상 머신, 네트워크 보안 그룹 규칙을 배포했으며 웹 서버를 설치했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Windows VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Windows 가상 머신 자습서](./tutorial-manage-vm.md)
