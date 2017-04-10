---
title: "Azure 빠른 시작 - VM 만들기 포털 | Microsoft Docs"
description: "Azure 빠른 시작 - VM 만들기 포털"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 8697ae51b5007cb50ac67accb5dafe48914af8ee
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Azure Portal을 사용하여 Linux 가상 컴퓨터 만들기

Azure Portal을 통해 Azure Virtual Machines를 만들 수 있습니다. 이 메서드는 가상 컴퓨터 및 관련된 모든 리소스를 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다. 이 빠른 시작은 Azure Portal을 사용하여 가상 컴퓨터를 만드는 과정을 설명합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="create-ssh-key-pair"></a>SSH 키 쌍 만들기

이 빠른 시작을 완료하려면 SSH 키 쌍이 필요합니다. 기존 SSH 키 쌍을 사용하는 경우 이 단계를 건너뛸 수 있습니다. Windows 컴퓨터를 사용하는 경우 [여기](ssh-from-windows.md)에서 찾은 지침을 따릅니다. 

Bash 셸에서 이 명령을 실행하고 화면에 나타나는 지시를 따릅니다. 명령 출력은 공개 키 파일의 파일 이름을 포함합니다. 이 파일의 내용은 가상 컴퓨터를 만들 때 필요합니다.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal(http://portal.azure.com)에 로그인합니다.

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **새로 만들기** 블레이드에서 **Compute**를 선택하고 **Compute** 블레이드에서 **Ubuntu Server 16.04 LTS**를 선택한 후 **만들기** 단추를 클릭합니다.

3. 가상 컴퓨터 **기본 사항** 양식을 채웁니다. **인증 유형**으로 **SSH**를 선택합니다. **SSH 공용 키**를 붙여 넣을 때 선행 또는 후행 공백을 모두 제거해야 합니다. **리소스 그룹**에 대해 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 만들어지고 전체적으로 관리되는 논리적 컨테이너입니다. 완료되면 **확인**을 클릭합니다.

    ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/quick-create-portal/create-vm-portal-basic-blade.png)  

4. VM의 크기를 선택하고 **선택**을 클릭합니다. 

    ![포털 블레이드에서 VM에 대한 크기 선택](./media/quick-create-portal/create-vm-portal-size-blade.png)

5. 설정 블레이드의 **Managed Disks 사용**에서 **예**를 선택하고 나머지 설정에 대해 기본값을 그대로 적용한 후 **확인**을 클릭합니다.

6. 요약 페이지에서 **확인**을 클릭하여 가상 컴퓨터 배포를 시작합니다.

7. 배포 상태를 모니터링하려면 가상 컴퓨터를 클릭합니다. Azure Portal 대시보드에서 또는 왼쪽 메뉴에 있는 **가상 컴퓨터**를 선택하여 VM을 찾을 수 있습니다. VM을 만들면 상태가 **배포 중**에서 **실행 중**으로 변경됩니다.


## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기 

기본적으로 Azure에 배포된 Linux 가상 컴퓨터에는 SSH 연결만이 가능합니다. 이 VM이 웹 서버가 되는 경우 웹 트래픽에 포트 80을 열어야 합니다. 이 단계에서는 포트 80에서 인바운드 연결을 허용하도록 NSG(네트워크 보안 그룹) 규칙을 만드는 과정을 설명합니다.

1. 가상 컴퓨터의 블레이드에 있는 **Essentials** 섹션에서 **리소스 그룹**의 이름을 클릭합니다.
2. 리소스 그룹의 블레이드에 있는 리소스 목록에서 **네트워크 보안 그룹**을 클릭합니다. NSG 이름은 끝에 -nsg가 추가된 VM 이름이어야 합니다.
3. **인바운드 보안 규칙** 머리글을 클릭하여 인바운드 규칙의 목록을 엽니다. 이미 목록에서 RDP의 규칙이 표시됩니다.
4. **+ 추가**를 클릭하여 **인바운드 보안 규칙 추가** 블레이드를 엽니다.
5. **이름**에서 **nginx**를 입력하고 **포트 범위**가 80으로 설정되고 **작업**이 **허용**으로 설정되었는지 확인한 다음 **확인**을 클릭합니다.


## <a name="connect-to-virtual-machine"></a>가상 컴퓨터에 연결

배포가 완료된 후 가상 컴퓨터에 대한 SSH 연결을 만듭니다.

1. 가상 컴퓨터 블레이드에서 **연결** 단추를 클릭합니다. 연결 단추를 클릭하면 가상 컴퓨터에 연결하는 데 사용할 수 있는 SSH 연결 문자열이 표시됩니다.

    ![포털 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. 다음 명령을 실행하여 SSH 세션을 만듭니다. 이 연결 문자열을 Azure Portal에서 복사한 것으로 바꿉니다.

```bash 
ssh <replace with IP address>
```

## <a name="install-nginx"></a>NGINX 설치

다음 bash 스크립트를 사용하여 패키지 원본을 업데이트하고 최신 NGINX 패키지를 설치합니다. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>NGIX 시작 페이지 보기

NGINX를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 NGINX 시작 페이지를 볼 수 있습니다. 위에 설명한 `publicIpAddress`을 사용하여 기본 페이지를 방문해야 합니다. 

![NGINX 기본 사이트](./media/quick-create-cli/nginx.png) 
## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않을 때 리소스 그룹, 가상 컴퓨터 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 가상 컴퓨터 블레이드에서 해당 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

[고가용성 가상 컴퓨터 만들기 자습서](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[VM 배포 CLI 샘플 탐색](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

