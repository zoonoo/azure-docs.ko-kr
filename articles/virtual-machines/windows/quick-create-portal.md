---
title: "Azure 빠른 시작 - Windows VM 만들기 포털 | Microsoft Docs"
description: "Azure 빠른 시작 - Windows VM 만들기 포털"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/13/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 8a86cf64dcd65e74285a1073f7494eba0708ddcd
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Azure Portal을 사용하여 Windows 가상 컴퓨터 만들기

Azure Portal을 통해 Azure Virtual Machines를 만들 수 있습니다. 이 메서드는 가상 컴퓨터 및 관련된 모든 리소스를 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다. 이 빠른 시작은 Azure Portal을 사용하여 가상 컴퓨터를 만드는 과정을 설명합니다. 배포가 완료되면 서버에 연결하고 IIS를 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

2. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

3. **새로 만들기** 블레이드에서 **Compute**를 선택하고 **Compute** 블레이드에서 **Windows Server 2016 Datacenter**를 선택한 후 **만들기** 단추를 클릭합니다.

4. 가상 컴퓨터 **기본 사항** 양식을 채웁니다. 여기에서 입력한 이사용자 이름과 암호는 가상 컴퓨터에 로그인하는 데 사용됩니다 **리소스 그룹**에 대해 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 만들어지고 전체적으로 관리되는 논리적 컨테이너입니다. 완료되면 **확인**을 클릭합니다.

    ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 

    ![VM 크기를 보여 주는 스크린샷](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

6. 설정 블레이드의 **Managed Disks 사용**에서 **예**를 선택하고 나머지 설정에 대해 기본값을 그대로 적용한 후 **확인**을 클릭합니다.

7. 요약 페이지에서 **확인**을 클릭하여 가상 컴퓨터 배포를 시작합니다.

8. 배포 상태를 모니터링하려면 가상 컴퓨터를 클릭합니다. Azure Portal 대시보드에서 또는 왼쪽 메뉴에 있는 **가상 컴퓨터**를 선택하여 VM을 찾을 수 있습니다. VM을 만들면 상태가 **배포 중**에서 **실행 중**으로 변경됩니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기 

IIS에 트래픽을 허용하려면 웹 트래픽에 포트 80을 열어야 합니다. 이 단계에서는 포트 80에서 인바운드 연결을 허용하도록 NSG(네트워크 보안 그룹) 규칙을 만드는 과정을 설명합니다.

1. 가상 컴퓨터의 블레이드에 있는 **Essentials** 섹션에서 **리소스 그룹**의 이름을 클릭합니다.
2. 리소스 그룹의 블레이드에 있는 리소스 목록에서 **네트워크 보안 그룹**을 클릭합니다. NSG 이름은 끝에 -nsg가 추가된 VM 이름이어야 합니다.
3. **인바운드 보안 규칙** 머리글을 클릭하여 인바운드 규칙의 목록을 엽니다. 이미 목록에서 RDP의 규칙이 표시됩니다.
4. **+ 추가**를 클릭하여 **인바운드 보안 규칙 추가** 블레이드를 엽니다.
5. **이름**에서 **IIS**를 입력합니다. **포트 범위**를 80으로 설정하고 **작업**을 **허용**으로 설정해야 합니다. **확인**을 클릭합니다.


## <a name="connect-to-virtual-machine"></a>가상 컴퓨터에 연결

배포가 완료된 후 가상 컴퓨터에 대한 원격 데스크톱 연결을 만듭니다.

1. 가상 컴퓨터 블레이드에서 **연결** 단추를 클릭합니다. 원격 데스크톱 프로토콜 파일(.rdp 파일)이 생성되고 다운로드됩니다.

    ![포털 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 클릭합니다. Mac의 Mac 앱 스토어에서 이 [원격 데스크톱 클라이언트](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)와 같은 RDP 클라이언트가 필요합니다.

3. 가상 컴퓨터를 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다.

4. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.


## <a name="install-iis-using-powershell"></a>PowerShell을 사용하여 IIS 설치

가상 컴퓨터에서 PowerShell 프롬프트를 열고 다음 명령을 실행하여 IIS를 설치하고 로컬 방화벽 규칙을 사용하도록 설정하여 웹 트래픽을 허용합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

IIS를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 IIS 시작 페이지를 볼 수 있습니다. VM의 블레이드에서 **공용 IP 주소**를 가져오고 이를 사용하여 기본 웹 페이지를 방문합니다. 

![IIS 기본 사이트](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않을 때 리소스 그룹, 가상 컴퓨터 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 가상 컴퓨터 블레이드에서 해당 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

[역할 설치 및 방화벽 구성 자습서](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[VM 배포 CLI 샘플 탐색](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

