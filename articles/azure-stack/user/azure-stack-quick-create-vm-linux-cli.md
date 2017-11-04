---
title: "Azure 스택의 Azure CLI를 사용 하 여 Linux 가상 컴퓨터 만들기 | Microsoft Docs"
description: "Azure 스택의 CLI와 Linux 가상 컴퓨터를 만듭니다."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: de2ff697c083493b43ab0d1b5bcde532c28684e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Azure 스택의 Azure CLI를 사용 하 여 Linux 가상 컴퓨터 만들기

*적용 대상: Azure 스택 시스템 통합*

Azure CLI는 사용 하 여을 만들어 명령줄에서 Azure 스택 리소스를 관리 합니다. Azure 스택에서 Linux 가상 컴퓨터를 만드는 Azure CLI를 사용 하 여이 빠른 시작 정보입니다.  VM을 만든 후에 웹 서버가 설치 된 하 고 포트 80 웹 트래픽을 허용 하도록 열립니다.

## <a name="prerequisites"></a>필수 조건 

* Azure 스택 연산자 Azure 스택 마켓플레이스에 "Ubuntu Server 16.04 LTS" 이미지에 추가 되었는지 확인 합니다. 

* Azure 스택 특정 버전의 Azure CLI를 만들고 리소스를 관리 해야 합니다. Azure 스택에 대해 구성 된 Azure CLI가 없는 경우에 로그인는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 단계에 따라 [설치 Azure CLI를 구성 하 고](azure-stack-connect-cli.md)합니다.

* Windows 사용자 프로필의.ssh 디렉터리에 이름이 id_rsa.pub 사용 하 여 공용 SSH 키를 만들어야 합니다. SSH 키를 만드는 방법에 대 한 자세한 내용은 참조 하십시오. [Windows에서 만드는 SSH 키](../../virtual-machines/linux/ssh-from-windows.md)합니다. 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 리소스는 Azure 스택에 배포 되 고 관리 하는 논리 컨테이너입니다. Azure 스택 또는 개발 키트에서 실행 하는 시스템을 통합는 [az 그룹 만들기](/cli/azure/group#create) 리소스 그룹을 만드는 명령입니다. 이 문서에서 모든 변수에 대 한 값을 할당할 것, 되었거나 다른 값을 할당할 그대로 사용할 수 있습니다. 다음 예제에서는 로컬 위치에 myResourceGroup 명명 된 리소스 그룹을 만듭니다.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>가상 컴퓨터 만들기

[az vm create](/cli/azure/vm#create) 명령을 사용하여 VM을 만듭니다. 다음 예제에서는 myVM이라는 VM을 만듭니다. 이 예제에서는 관리 사용자 이름에 대 한 Demouser 사용 및 Demouser@123 암호로 합니다. 이러한 값을 사용자 환경에 적절한 값으로 업데이트합니다. 가상 컴퓨터에 연결할 때 이러한 값이 필요 합니다.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

완료 되 면이 명령은 가상 컴퓨터에 대 한 매개 변수를 출력 합니다.  기록 된 *PublicIPAddress*이므로 연결 하 고 가상 컴퓨터를 관리 하는 데 사용할 수 있습니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

기본적으로 Azure에 배포된 Linux 가상 컴퓨터에는 SSH 연결만이 가능합니다. 이 VM이 웹 서버가 되는 경우 인터넷에서 포트 80을 열어야 합니다. [az vm open-port](/cli/azure/vm#open-port) 명령을 사용하여 원하는 포트를 엽니다.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>VM에 SSH 수행

SSH가 설치된 시스템에서 다음 명령을 사용하여 가상 컴퓨터에 연결합니다. Windows에서 작업하는 경우 [Putty](http://www.putty.org/)를 사용하여 연결을 만들 수 있습니다. 가상 컴퓨터의 올바른 공용 IP 주소를 교체할 수 있는지 확인 합니다. 위의 예에서 IP 주소가 192.168.102.36를 되었습니다.

```bash
ssh <publicIpAddress>
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

## <a name="view-the-nginx-welcome-page"></a>NGINX 시작 페이지 보기

NGINX를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 NGINX 시작 페이지를 볼 수 있습니다. 위에 설명한 *publicIpAddress*를 사용하여 기본 페이지를 방문해야 합니다. 

![NGINX 기본 사이트](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 간단한 Linux 가상 컴퓨터를 배포 했습니다. Azure 스택 가상 컴퓨터에 대 한 자세한 내용은 계속 [스택 Azure의에서 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.

