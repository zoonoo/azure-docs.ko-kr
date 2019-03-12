---
title: Azure Stack에서 Azure CLI를 사용 하 여 Linux 가상 머신 만들기 | Microsoft Docs
description: Azure Stack에서 CLI를 사용 하 여 Linux 가상 머신을 만듭니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b3fbd91ddf06d4515ee55d24ba9330d0af6dc124
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540321"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>빠른 시작: Azure Stack에서 Azure CLI를 사용 하 여 Linux 서버 가상 머신 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure CLI를 사용 하 여 Ubuntu Server 16.04 LTS 가상 컴퓨터를 만들 수 있습니다. 만들고 가상 머신을 사용 하려면이 문서의 단계를 수행 합니다. 이 문서에서는 단계를 제공 합니다.

* 원격 클라이언트를 사용 하 여 가상 컴퓨터에 연결 합니다.
* NGINX 웹 서버를 설치 하 고 기본 홈페이지를 봅니다.
* 사용 되지 않는 리소스를 정리 합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Stack marketplace에서 Linux 이미지**

   Azure Stack marketplace에는 기본적으로 Linux 이미지를 포함 하지 않습니다. 가져올 수 있도록 Azure Stack 운영자를 **Ubuntu Server 16.04 LTS** 해야 하는 이미지입니다. 연산자에 설명 된 단계를 사용할 수는 [Azure에서 Azure Stack marketplace 항목 다운로드](../azure-stack-download-azure-marketplace-item.md) 문서.

* Azure Stack 리소스를 만들고 Azure CLI의 특정 버전에 필요 합니다. Azure Stack에 대해 구성 된 Azure CLI가 없는 경우에 로그인 합니다 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 경우는 Windows 기반 외부 클라이언트가 [VPN을 통해 연결 된](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 하는 단계에 따라 [ Azure CLI 설치 및 구성](azure-stack-version-profiles-azurecli2.md)합니다.

* Windows 사용자 프로필의.ssh 디렉터리에 저장 하는 이름 id_rsa.pub 인 공용 SSH 키입니다. SSH 키를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Windows에서 만드는 SSH 키](../../virtual-machines/linux/ssh-from-windows.md)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 배포 하 고 Azure Stack 리소스를 관리할 수 있는 논리적 컨테이너입니다. 개발 키트 또는 Azure Stack 통합 시스템을 실행 합니다 [az 그룹 만들기](/cli/azure/group#az-group-create) 리소스 그룹을 만드는 명령입니다.

>[!NOTE]
 값은 코드 예제에서 모든 변수에 할당 됩니다. 그러나 하려는 경우 새 값을 할당할 수 있습니다.

다음 예제에서는 로컬 위치에 myResourceGroup 이라는 리소스 그룹을 만듭니다.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

사용 하 여 가상 머신 만들기를 [az vm 만들기](/cli/azure/vm#az-vm-create) 명령입니다. 다음 예제에서는 myVM이라는 VM을 만듭니다. 이 예제에서는 관리 사용자 이름에 대 한 Demouser 사용 및 Demouser@123 사용자 암호입니다. 사용자 환경에 적절 한 값으로 이러한 값을 변경 합니다.

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

공용 IP 주소에 반환 되는 **PublicIpAddress** 매개 변수입니다. 가상 컴퓨터에 액세스 하는 데 필요 하기 때문에이 주소를 적어 둡니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

이 가상 머신에 IIS 웹 서버를 실행 하려는, 때문에 인터넷 트래픽에 포트 80을 열려면 해야 합니다. [az vm open-port](/cli/azure/vm) 명령을 사용하여 원하는 포트를 엽니다.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>SSH를 사용 하 여 가상 머신에 연결

설치 하는 SSH 사용 하 여 클라이언트 컴퓨터에서 가상 머신에 연결 합니다. 사용 하 여 Windows 클라이언트에서 작업할 때는 [Putty](https://www.putty.org/) 연결을 만듭니다. 가상 컴퓨터에 연결 하려면 다음 명령을 사용 합니다.

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>NGINX 웹 서버 설치

패키지 리소스를 업데이트 하 고 최신 NGINX 패키지를 설치 하려면 다음 스크립트를 실행 합니다.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>NGINX 시작 페이지 보기

NGINX를 설치 및 포트 80 열어서 가상 머신에서 사용 하 여 가상 머신의 공용 IP 주소를 사용 하 여 웹 서버를 액세스할 수 있습니다. 웹 브라우저를 열고 ```http://<public IP address>```합니다.

![NGINX 웹 서버에 대 한 시작 페이지](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 리소스를 정리 합니다. 사용할 수는 [az 그룹 삭제](/cli/azure/group#az-group-delete) 이러한 리소스를 제거 하는 명령입니다. 리소스 그룹 및 모든 해당 리소스를 삭제 하려면 다음 명령을 실행 합니다.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 웹 서버를 사용 하 여 기본 Linux server 가상 머신을 배포 합니다. Azure Stack virtual machines에 대 한 자세한 내용은 계속 [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
