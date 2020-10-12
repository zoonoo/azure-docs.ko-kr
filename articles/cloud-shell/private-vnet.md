---
title: Azure Virtual Network에서 Cloud Shell
description: Azure 가상 네트워크에 Cloud Shell 배포
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 722d935c242a51ddfc01377676f026b71a8951b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468541"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Azure 가상 네트워크에 Cloud Shell 배포
> [!NOTE]
> 이 기능은 공개 미리 보기로 제공 됩니다.

일반 Cloud Shell 세션은 리소스와 별도로 Microsoft 네트워크의 컨테이너에서 실행 됩니다. 즉, 컨테이너 내에서 실행 되는 명령은 특정 가상 네트워크 에서만 액세스할 수 있는 리소스에 액세스할 수 없습니다. 예를 들어 SSH를 사용 하 여 Cloud Shell에서 개인 IP 주소만 있는 가상 머신에 연결 하거나, kubectl를 사용 하 여 액세스를 잠근 Kubernetes 클러스터에 연결할 수 없습니다. 

이 선택적 기능은 이러한 제한을 해결 하 고 사용자가 제어 하는 Azure 가상 네트워크에 Cloud Shell를 배포할 수 있도록 합니다. 여기서 컨테이너는 선택한 가상 네트워크 내의 리소스와 상호 작용할 수 있습니다.  

아래에서이 시나리오에서 배포 및 사용 되는 리소스 아키텍처를 확인할 수 있습니다.

![Cloud Shell isolated VNET 아키텍처를 보여 줍니다.](media/private-vnet/data-diagram.png)

Azure Virtual Network에서 Cloud Shell를 사용 하려면이 기능을 지원 하기 위해 여러 리소스를 만들어야 합니다. 이 문서에서는 ARM 템플릿을 사용 하 여 필요한 리소스를 설정 하는 방법을 보여 줍니다.

> [!NOTE]
> 이러한 리소스는 가상 네트워크에 대해 한 번만 설정 하면 됩니다. 그런 다음 가상 네트워크에 대 한 액세스 권한이 있는 모든 관리자가 공유할 수 있습니다.

## <a name="required-network-resources"></a>필요한 네트워크 리소스

### <a name="virtual-network"></a>가상 네트워크
가상 네트워크는 하나 이상의 서브넷이 생성 되는 주소 공간을 정의 합니다.

Cloud Shell에 사용할 수 있는 원하는 가상 네트워크를 식별 해야 합니다. 이는 일반적으로 관리 하려는 리소스를 포함 하는 기존 가상 네트워크 또는 리소스를 포함 하는 네트워크와 피어 링 하는 네트워크입니다.

### <a name="subnet"></a>서브넷
선택한 가상 네트워크 내에서 전용 서브넷을 Cloud Shell 컨테이너에 사용 해야 합니다. 이 서브넷은 ACI (Azure Container Instances) 서비스로 위임 됩니다.  사용자가 가상 네트워크에서 Cloud Shell 컨테이너를 요청 하면 Cloud Shell ACI를 사용 하 여이 위임 된 서브넷에 있는 컨테이너를 만듭니다.  이 서브넷에는 다른 리소스를 만들 수 없습니다.

### <a name="network-profile"></a>네트워크 프로필
네트워크 프로필은 리소스의 특정 네트워크 속성을 지정 하는 Azure 리소스에 대 한 네트워크 구성 템플릿입니다.

### <a name="azure-relay"></a>Azure Relay
[Azure Relay](../azure-relay/relay-what-is-it.md) 를 사용 하면 직접 연결할 수 없는 두 개의 끝점이 통신할 수 있습니다. 이 경우 관리자의 브라우저가 개인 네트워크의 컨테이너와 통신 하도록 허용 하는 데 사용 됩니다.

Cloud Shell에 사용 되는 Azure Relay 인스턴스는 컨테이너 리소스에 액세스할 수 있는 네트워크를 제어 하도록 구성할 수 있습니다. 
- 공용 인터넷에서 액세스할 수 있습니다 .이 구성에서는 Cloud Shell 외부에서 내부 리소스에 연결 하는 방법을 제공 합니다. 
- 지정 된 네트워크에서 액세스할 수 있습니다 .이 구성에서 관리자는 Cloud Shell를 사용할 수 있도록 적절 한 네트워크에서를 실행 하는 컴퓨터의 Azure Portal에 액세스 해야 합니다.

## <a name="storage-requirements"></a>저장소 요구 사항
표준 Cloud Shell와 마찬가지로 가상 네트워크에서 Cloud Shell를 사용 하는 동안 저장소 계정이 필요 합니다. 각 관리자는 파일을 저장할 파일 공유가 필요 합니다.  Cloud Shell에서 사용 하는 가상 네트워크에서 저장소 계정에 액세스할 수 있어야 합니다. 

## <a name="virtual-network-deployment-limitations"></a>가상 네트워크 배포 제한 사항
* 추가 네트워킹 리소스가 관련 되어 있으므로 가상 네트워크에서 Cloud Shell를 시작 하는 것은 일반적으로 표준 Cloud Shell 세션 보다 느립니다.

* 미리 보기 중에는 가상 네트워크의 Cloud Shell에 대해 더 작은 지역이 지원 됩니다. 현재 WestUS 및 WestCentralUS로 제한 됩니다.

* [Azure Relay](../azure-relay/relay-what-is-it.md) 무료 서비스가 아닙니다. [가격](https://azure.microsoft.com/pricing/details/service-bus/)을 확인 하세요. Cloud Shell 시나리오에서는 Cloud Shell를 사용 하는 동안 각 관리자에 대해 하나의 하이브리드 연결이 사용 됩니다. Cloud Shell 세션이 완료 된 후 연결이 자동으로 종료 됩니다.

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

사용 하려는 가상 네트워크를 보유 하는 구독에 ContainerInstances 리소스 공급자를 등록 해야 합니다. 에서 적절 한 구독 `Set-AzContext -Subscription {subscriptionName}` 을 선택 하 고 다음을 실행 합니다.

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

**Registrationstate** 가 이면 `Registered` 아무 조치도 필요 하지 않습니다. 이면 `NotRegistered` 를 실행 `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` 합니다. 

## <a name="deploy-network-resources"></a>네트워크 리소스 배포
 
### <a name="create-a-resource-group-and-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기
연결 하려는 VNET이 이미 있는 경우이 섹션을 건너뜁니다.

Azure Portal 또는 Azure CLI, Azure PowerShell 등을 사용 하 여 새 리소스 그룹에 리소스 그룹 및 가상 네트워크를 만들고 **리소스 그룹 및 가상 네트워크는 동일한 지역에**있어야 합니다.

> [!NOTE]
> 공개 미리 보기 상태에서 리소스 그룹과 가상 네트워크는 WestCentralUS 또는 WestUS 중 하나에 있어야 합니다.

### <a name="arm-templates"></a>ARM 템플릿
[Azure 빠른 시작 템플릿](https://aka.ms/cloudshell/docs/vnet/template) 을 사용 하 여 가상 네트워크에 Cloud Shell 리소스를 만들고, 필요한 저장소를 만들기 위한 [Azure 빠른 시작 템플릿을](https://aka.ms/cloudshell/docs/vnet/template/storage) 활용 합니다. 리소스 이름 (주로 파일 공유 이름)을 기록해 둡니다.

### <a name="open-relay-firewall"></a>오픈 릴레이 방화벽
위의 템플릿을 사용 하 여 만든 릴레이로 이동 하 고, 설정에서 "네트워킹"을 선택 하 고 브라우저 네트워크에서 릴레이로의 액세스를 허용 합니다. 기본적으로 릴레이는 생성 된 가상 네트워크 에서만 액세스할 수 있습니다. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>가상 네트워크를 사용 하도록 Cloud Shell 구성
> [!NOTE]
> 각 관리자가 Cloud Shell를 사용할 수 있도록이 단계를 완료 해야 합니다.

위의 단계를 완료 한 후에는 Azure Portal 또는에서 Cloud Shell로 이동 https://shell.azure.com 합니다. 격리 된 Cloud Shell 환경에 연결할 때마다 이러한 환경 중 하나를 사용 해야 합니다.

> [!NOTE]
> 이전에 Cloud Shell를 사용한 경우 기존 clouddrive를 분리 해야 합니다. 활성 Cloud Shell 세션에서이 작업을 실행 하려면 `clouddrive unmount` 페이지를 새로 고칩니다.

Cloud Shell에 연결 하 고 첫 실행 환경에 대 한 메시지가 표시 됩니다. 원하는 셸 환경을 선택 하 고 "고급 설정 표시"를 선택한 다음 "VNET 격리 설정 표시" 상자를 선택 합니다. 팝업의 필드를 입력 합니다.  대부분의 필드는 가상 네트워크의 Cloud Shell와 연결 될 수 있는 사용 가능한 리소스로 자동 채우기 됩니다.  사용자가 파일 공유 이름을 입력 해야 합니다.


![Cloud Shell isolated VNET first 환경 설정에 대해 설명 합니다.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>다음 단계
[Azure Virtual Network에 대 한 자세한 정보](../virtual-network/virtual-networks-overview.md)