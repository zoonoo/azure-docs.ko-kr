---
title: Azure 스택 Azure CLI를 사용 하 여 Windows 가상 컴퓨터 만들기 | Microsoft Docs
description: Azure CLI를 사용 하 여 Azure 스택에 Windows VM을 만드는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/19/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 5665af14b9b0d0705b68c8a27c593b19c31b053e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-stack-using-azure-cli"></a>빠른 시작: Azure 스택에서 Azure CLI를 사용 하 여 Windows 가상 컴퓨터 만들기

Azure CLI는 사용 하 여을 만들어 명령줄에서 Azure 스택 리소스를 관리 합니다. 이 문서 작성 및 액세스 Azure 스택에서 Windows Server 2016 가상 컴퓨터를 Azure CLI를 사용 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 스택 연산자 Azure 스택 마켓플레이스에 "Windows Server 2016" 이미지에 추가 되었는지 확인 합니다.

* Azure 스택 특정 버전의 Azure CLI를 만들고 리소스를 관리 해야 합니다. Azure 스택에 대해 구성 된 Azure CLI를 설정 하지 않은 경우의 단계에 따라 [설치 및 구성 Azure CLI](azure-stack-version-profiles-azurecli2.md)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 리소스는 Azure 스택에 배포 되 고 관리 하는 논리 컨테이너입니다. Azure 스택 또는 개발 키트에서 실행 하는 시스템을 통합는 [az 그룹 만들기](/cli/azure/group#az_group_create) 리소스 그룹을 만드는 명령입니다. 이 문서에서 모든 변수의 값이 할당 됩니다, 그리고 이러한 값을 사용 하거나 새 값을 할당할 수 있습니다. 다음 예제에서는 로컬 위치에 myResourceGroup 명명 된 리소스 그룹을 만듭니다.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 VM을 만듭니다. 다음 예제에서는 myVM이라는 VM을 만듭니다. 이 예제에서는 관리 사용자 이름에 대 한 Demouser 사용 및 Demouser@123 암호로 합니다. 이러한 값을 사용자 환경에 적절한 값으로 업데이트합니다. 가상 컴퓨터에 연결할 때 이러한 값이 필요 합니다.

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

VM이 생성 하는 경우는 *PublicIPAddress* 매개 변수는 출력입니다. VM에 액세스 하는 데 필요 하기 때문에이 주소를 적어 둡니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

기본적으로 Azure 스택에 배포 된 Windows 가상 컴퓨터에 RDP 연결만 허용 됩니다. 이 VM이 웹 서버가 되는 경우 인터넷에서 포트 80을 열어야 합니다. [az vm open-port](/cli/azure/vm#open-port) 명령을 사용하여 원하는 포트를 엽니다.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

다음 명령을 사용하여 가상 머신과의 원격 데스크톱 세션을 만듭니다. 해당 IP 주소를 가상 컴퓨터의 공용 IP 주소로 바꿉니다. 가상 머신을 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>PowerShell을 사용하여 IIS 설치

Azure VM에 로그인 한 후 IIS를 설치 하 고 웹 트래픽을 허용 하도록 로컬 방화벽 규칙을 사용 하는 한 줄의 PowerShell 사용할 수 있습니다. PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

기본 IIS 시작 페이지를 보려면 사용자가 선택한 웹 브라우저를 사용할 수 있습니다. 기본 페이지를 방문 하 여 이전 섹션에 설명 된 공용 IP 주소를 사용 합니다.

![IIS 기본 사이트](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>리소스 정리

필요 없는 경우 사용할 수 없습니다는 [az 그룹 삭제](/cli/azure/group#az_group_delete) 명령을 VM을 리소스 그룹을 제거 하 고 모든 관련 리소스입니다.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 퀵 스타트의 간단한 Windows 가상 컴퓨터를 배포한 합니다. Azure 스택 가상 컴퓨터에 대 한 자세한 내용은 계속 [스택 Azure의에서 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
