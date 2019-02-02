---
title: Azure CLI를 사용 하 여 Azure Stack에서 Windows 가상 머신 만들기 | Microsoft Docs
description: Azure CLI를 사용 하 여 Azure Stack에서 Windows VM을 만드는 방법 알아보기
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
ms.openlocfilehash: d2e29e183ab898635f550290b6ebd6eddd42c4d2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659204"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>빠른 시작: Azure Stack에서 Azure CLI를 사용 하 여 Windows Server 가상 머신 만들기

‎*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure CLI를 사용 하 여 Windows Server 2016 가상 컴퓨터를 만들 수 있습니다. 만들고 가상 머신을 사용 하려면이 문서의 단계를 수행 합니다. 또한이 문서에서는 다음 단계를 제공 합니다.

* 원격 클라이언트를 사용 하 여 가상 컴퓨터에 연결 합니다.
* IIS 웹 서버를 설치 하 고 기본 홈페이지를 봅니다.
* 리소스를 정리 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Stack 운영자에 추가 되었는지 확인 합니다 **Windows Server 2016** Azure Stack marketplace 이미지입니다.

* Azure Stack 리소스를 만들고 Azure CLI의 특정 버전에 필요 합니다. Azure Stack에 대해 구성 된 Azure CLI가 없는 경우 수행 하는 단계 [Azure CLI 설치 및 구성](azure-stack-version-profiles-azurecli2.md)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 배포 하 고 Azure Stack 리소스를 관리할 수 있는 논리적 컨테이너입니다. Azure Stack 환경에서 실행 합니다 [az 그룹 만들기](/cli/azure/group#az-group-create) 리소스 그룹을 만드는 명령입니다.

>[!NOTE]
 값은 코드 예제에서 모든 변수에 할당 됩니다. 그러나 하려는 경우 새 값을 할당할 수 있습니다.

다음 예제에서는 로컬 위치에 myResourceGroup 이라는 리소스 그룹을 만듭니다.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

사용 하 여 가상 머신 (VM) 만들기는 [az vm 만들기](/cli/azure/vm#az-vm-create) 명령입니다. 다음 예제에서는 myVM이라는 VM을 만듭니다. 이 예제에서는 관리 사용자 이름에 대 한 Demouser 사용 및 Demouser@123 사용자 암호입니다. 사용자 환경에 적절 한 값으로 이러한 값을 변경 합니다.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

VM을 만든 경우 합니다 **PublicIPAddress** 출력에서 매개 변수는 가상 머신의 공용 IP 주소를 포함 합니다. 가상 컴퓨터에 액세스 하는 데 필요 하기 때문에이 주소를 적어 둡니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

을 때문에이 VM은 IIS 웹 서버를 실행 하려면 인터넷 트래픽에 포트 80을 열려면 해야 합니다.

사용 된 [포트 열기 az vm](/cli/azure/vm) 포트 80을 열려면 명령입니다.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

다음 명령을 사용 하 여 가상 컴퓨터에 원격 데스크톱 연결을 만듭니다. 가상 컴퓨터의 IP 주소를 사용 하 여 "공용 IP 주소"를 대체 합니다. 메시지가 표시 되 면 사용자 이름 및 가상 컴퓨터에 사용한 암호를 입력 합니다.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>PowerShell을 사용하여 IIS 설치

이제는 가상 컴퓨터에 로그인 했으므로 IIS를 설치 하려면 PowerShell을 사용할 수 있습니다. 가상 컴퓨터에서 PowerShell을 시작 하 고 다음 명령을 실행 합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

기본 IIS 시작 페이지를 보려면 사용자가 선택한 웹 브라우저를 사용할 수 있습니다. 이전 섹션에 설명 된 공용 IP 주소를 사용 하 여 기본 페이지를 방문 합니다.

![IIS 기본 사이트](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 리소스를 정리 합니다. 사용 된 [az 그룹 삭제](/cli/azure/group#az-group-delete) 리소스 그룹, 가상 컴퓨터를 제거 하는 명령 및 모든 관련 리소스입니다.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 Windows Server 가상 컴퓨터를 배포 했습니다. Azure Stack virtual machines에 대 한 자세한 내용은 계속 [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
