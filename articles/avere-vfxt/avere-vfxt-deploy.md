---
title: Avere vFXT for Azure 배포
description: Azure에 Avere vFXT 클러스터를 배포하는 단계입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d7c207f89b9cb50f940f071fbbf6ee81b4d44976
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164326"
---
# <a name="deploy-the-vfxt-cluster"></a>vFXT 클러스터 배포

Azure에서 vFXT 클러스터를 만드는 가장 쉬운 방법은 클러스터 컨트롤러를 사용하는 것입니다. 클러스터 컨트롤러란 vFXT 클러스터를 만들고 관리하는 데 필요한 스크립트, 템플릿 및 소프트웨어 인프라가 포함된 VM을 말합니다.

새 vFXT 클러스터를 배포하는 단계는 다음과 같습니다.

1. [클러스터 컨트롤러를 만듭니다](#create-the-cluster-controller-vm).
1. Azure Blob 저장소를 사용하는 경우 가상 네트워크에 [저장소 엔드포인트를 만듭니다](#create-a-storage-endpoint-if-using-azure-blob).
1. [클러스터 컨트롤러에 연결합니다](#access-the-controller). 나머지 단계는 클러스터 컨트롤러 VM에서 수행됩니다. 
1. 클러스터 노드에 대한 [액세스 역할을 만듭니다](#create-the-cluster-node-access-role). 프로토타입이 제공됩니다.
1. 만들려는 vFXT 클러스터 유형에 대한 [클러스터 만들기 스크립트를 사용자 지정합니다](#edit-the-deployment-script).
1. [클러스터 만들기 스크립트를 실행합니다](#run-the-script).

클러스터 배포 단계 및 계획에 대한 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md) 및 [배포 개요](avere-vfxt-deploy-overview.md)를 참조하세요. 

이 문서의 지침을 따르면 다음 다이어그램과 같이 가상 네트워크, 서브넷, 컨트롤러 및 vFXT 클러스터가 갖추어집니다.

![선택적 Blob 저장소가 포함된 vnet 및 vFXT 노드/vFXT 클러스터라는 레이블이 지정되어 그룹화된 세 개의 VM과 클러스터 컨트롤러라는 레이블이 지정된 하나의 VM이 포함된 서브넷을 보여 주는 다이어그램](media/avere-vfxt-deployment.png)

시작하기 전에 먼저 다음 필수 조건이 처리되었는지 확인합니다.  

1. [새 구독](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [구독 소유자 권한](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

필요에 따라 클러스터 컨트롤러를 만들기 [전에](avere-vfxt-pre-role.md) 클러스터 노드 역할을 만들 수 있지만 나중에 더 간단하게 수행할 수 있습니다.

## <a name="create-the-cluster-controller-vm"></a>클러스터 컨트롤러 VM 만들기

첫 번째 단계는 vFXT 클러스터 노드를 만들고 구성할 가상 머신을 만드는 것입니다. 

클러스터 컨트롤러는 Avere vFXT 클러스터 만들기 소프트웨어와 스크립트가 사전 설치된 Linux VM입니다. 상당한 처리 능력이나 저장소 공간이 필요하지 않으므로 저렴한 옵션을 선택할 수 있습니다. 이 VM은 vFXT 클러스터의 수명 동안 가끔 사용됩니다.

클러스터 컨트롤러 VM을 만드는 두 가지 방법이 있습니다. 프로세스를 간소화하기 위해 [Azure Resource Manager 템플릿](#create-controller---arm-template)이 [아래](#create-controller---arm-template)에 제공되지만, [Azure Marketplace 이미지](#create-controller---azure-marketplace-image)에서 컨트롤러를 만들 수도 있습니다. 

컨트롤러 만들기의 일환으로 새 리소스 그룹을 만들 수 있습니다.

> [!TIP]
>
> 클러스터 컨트롤러에서 공용 IP 주소를 사용할지 여부를 결정합니다. 공용 IP 주소를 사용하면 vFXT 클러스터에 쉽게 액세스할 수 있지만 보안 위험이 적습니다.
>
>  * 클러스터 컨트롤러의 공용 IP 주소를 사용하면 이 주소를 점프 호스트로 사용하여 사설 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다.
>  * 컨트롤러에 공용 IP 주소를 설정하지 않은 경우 다른 점프 호스트, VPN 연결 또는 ExpressRoute를 사용하여 클러스터에 액세스해야 합니다. 예를 들어 VPN 연결이 구성된 가상 네트워크 내에 컨트롤러를 만듭니다.
>  * 공용 IP 주소가 있는 컨트롤러를 만드는 경우 컨트롤러 VM을 네트워크 보안 그룹으로 보호해야 합니다. 22 포트를 통해서만 액세스를 허용하여 인터넷 액세스를 제공합니다.

### <a name="create-controller---resource-manager-template"></a>컨트롤러 만들기 - Resource Manager 템플릿

포털에서 클러스터 컨트롤러 노드를 만들려면 아래의 "Azure에 배포" 단추를 클릭합니다. 이 배포 템플릿은 Avere vFXT 클러스터를 만들고 관리할 VM을 만듭니다.

[![컨트롤러 만들기 단추](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

다음 정보를 제공합니다.

**기본** 섹션에서  

* 클러스터에 대한 **구독**
* 클러스터에 대한 **리소스 그룹** 
* **위치**: 

**설정** 섹션에서

* 새 가상 네트워크를 만들지 여부

  * 새 vnet을 만들면 클러스터 컨트롤러에 연결할 수 있도록 공용 IP 주소가 할당됩니다. 이 vnet에 대해 인바운드 트래픽을 22 포트로만 제한하는 네트워크 보안 그룹도 만들어집니다.
  * ExpressRoute 또는 VPN을 사용하여 클러스터 컨트롤러에 연결하려면 이 값을 `false`로 설정하고 나머지 필드에는 기존 vnet을 지정합니다. 클러스터 컨트롤러는 네트워크 통신에 해당 vnet을 사용합니다. 

* 가상 네트워크 리소스 그룹, 이름 및 서브넷 이름 - 기존 vnet을 사용하는 경우 기존 리소스의 이름을 입력하거나, 새 vnet을 만드는 경우 새 이름을 입력합니다
* **컨트롤러 이름** - 컨트롤러 VM에 대한 이름을 설정합니다.
* 컨트롤러 관리자 사용자 이름 -기본값은 `azureuser`입니다.
* SSH 키 - 관리자 사용자 이름과 연결할 공개 키를 붙여넣습니다. 도움이 필요한 경우 [SSH 키를 만들고 사용하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요.

**사용 약관** 아래에서 

* 서비스 약관을 읽고, 확인란을 클릭하여 해당 약관에 동의합니다. 

  > [!NOTE] 
  > 구독 소유자가 아닌 경우 [소프트웨어 사용 약관에 미리 동의](avere-vfxt-prereqs.md#accept-software-terms-in-advance)의 필수 조건 단계에 따라 소유자가 사용 약관에 동의하도록 합니다. 


완료되면 **구매**를 클릭합니다. 5-6분 후에 컨트롤러 노드가 가동되고 실행됩니다.

출력 페이지를 방문하여 클러스터를 만드는 데 필요한 컨트롤러 정보를 수집합니다. 자세한 정보는 [클러스터를 만드는 데 필요한 정보](#information-needed-to-create-the-cluster)를 읽어보세요.

### <a name="create-controller---azure-marketplace-image"></a>컨트롤러 만들기 - Azure Marketplace 이미지

Azure Marketplace에서 ``Avere``라는 이름을 검색하여 컨트롤러 템플릿을 찾습니다. **Avere vFXT for Azure 컨트롤러** 템플릿을 선택합니다.

아직 수행하지 않은 경우 사용 약관에 동의하고, **만들기** 단추 아래에 있는 "프로그래밍 방식으로 배포하시겠습니까?" 링크를 클릭하여 Marketplace 이미지에 프로그래밍 방식으로 액세스할 수 있도록 설정합니다.

![[만들기] 단추 아래에 있는 프로그래밍 방식 액세스에 대한 링크의 스크린샷](media/avere-vfxt-deploy-programmatically.png)

**사용** 단추를 클릭하고 해당 설정을 저장합니다.

![프로그래밍 방식으로 액세스하도록 설정하기 위한 마우스 클릭을 보여 주는 스크린샷](media/avere-vfxt-enable-program.png)

**Avere vFXT for Azuree 컨트롤러** 템플릿의 기본 페이지로 돌아가서 **만들기**를 클릭합니다. 

첫 번째 패널에서 다음 기본 옵션을 작성하거나 확인합니다.

* **구독**
* **리소스 그룹**(새 그룹을 만들려면 새 이름을 입력함)
* **가상 머신 이름** - 컨트롤러의 이름
* **지역**
* **가용성 옵션** - 중복성은 필요하지 않음
* **이미지** - Avere vFXT 컨트롤러 노드 이미지
* **크기** - 기본값으로 그대로 두거나 다른 저렴한 유형을 선택합니다.
* **관리자 계정** - 클러스터 컨트롤러에 로그인하는 방법을 설정합니다. 
  * 사용자 이름/암호 또는 SSH 공개 키(권장)를 선택합니다.
  
    > [!TIP] 
    > SSH 키가 더 안전합니다. 도움이 필요한 경우 [SSH 키를 만들고 사용하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요. 
  * 사용자 이름을 지정합니다. 
  * SSH 키를 붙여넣거나, 암호를 입력하고 확인합니다.
* **인바운드 포트 규칙** - 공용 IP 주소를 사용하는 경우 22(SSH) 포트를 엽니다.

**다음**을 클릭하여 디스크 옵션을 설정합니다.

* **OS 디스크 유형** - HDD의 기본값은 충분합니다.
* **관리되지 않는 디스크 사용** - 필요하지 않음
* **데이터 디스크** - 사용하지 않음

**다음**을 클릭하여 네트워킹 옵션을 설정합니다.

* **가상 네트워크** - 컨트롤러에 대한 vnet을 선택하거나, 이름을 입력하여 새 vnet을 만듭니다. 컨트롤러에서 공용 IP 주소를 사용하지 않으려면 ExpressRoute 또는 다른 액세스 방법이 이미 설정된 vnet 내에 있는 주소를 찾는 것이 좋습니다.
* **서브넷** - vnet에 있는 서브넷을 선택합니다(선택 사항). 새 vnet을 만들면 새 서브넷을 동시에 만들 수 있습니다.
* **공용 IP** - 공용 IP 주소를 사용하려면 여기서 해당 주소를 지정할 수 있습니다. 
* **네트워크 보안 그룹** - 기본 설정(**기본**) 그대로 둡니다. 
* **공용 인바운드 포트** - 공용 IP 주소를 사용하는 경우 이 컨트롤을 사용하여 SSH 트래픽으로부터의 액세스를 허용합니다. 
* **가속화된 네트워킹**은 이 VM에 대해 사용할 수 없습니다.

**다음**을 클릭하여 관리 옵션을 설정합니다.

* **부트 진단** - **끄기**로 변경합니다.
* **OS 게스트 진단** - '사용 안 함'으로 둡니다.
* **진단 저장소 계정** - 필요에 따라 진단 정보를 저장할 새 계정을 선택하거나 지정합니다.
* **관리 서비스 ID** - 이 옵션을 **켜기**로 변경합니다. 이렇게 하면 클러스터 컨트롤러에 대한 Azure AD 서비스 주체가 만들어집니다.
* **자동 종료** - '끄기'로 둡니다. 

이 시점에서 인스턴스 태그를 사용하지 않으려면 **검토 + 만들기**를 클릭할 수 있습니다. 그렇지 않으면 **다음**을 두 번 클릭하여 **게스트 구성** 페이지를 건너뛰고 태그 페이지로 이동합니다. 완료되면 **검토 + 만들기**를 클릭합니다. 

선택 사항이 확인되었으면 **만들기** 단추를 클릭합니다.  

만드는 데 5-6분이 걸립니다.

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>저장소 엔드포인트 만들기(Azure Blob을 사용하는 경우)

Azure Blob 저장소를 백 엔드 데이터 저장소로 사용하는 경우 가상 네트워크에 저장소 서비스 엔드포인트를 만들어야 합니다. 이 [서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)는 Azure Blob 트래픽을 인터넷을 통해 라우팅하는 대신 로컬로 유지합니다.

1. 포털의 왼쪽에서 **가상 네트워크**를 클릭합니다.
1. 컨트롤러에 대한 vnet을 선택합니다. 
1. 왼쪽에서 **서비스 엔드포인트**를 클릭합니다.
1. 위쪽에서 **추가**를 클릭합니다.
1. 서비스를 ``Microsoft.Storage``로 두고, 컨트롤러의 서브넷을 선택합니다.
1. 아래쪽에서 **추가**를 클릭합니다.

  ![서비스 엔드포인트를 만드는 단계에 대한 주석이 있는 Azure Portal 스크린샷](media/avere-vfxt-service-endpoint.png)

## <a name="information-needed-to-create-the-cluster"></a>클러스터를 만드는 데 필요한 정보

클러스터 컨트롤러를 만든 후 다음 단계에 필요한 정보를 알고 있는지 확인합니다. 

컨트롤러에 연결하는 데 필요한 정보: 

* 컨트롤러 사용자 이름 및 SSH 키(또는 암호)
* 컨트롤러 IP 주소 또는 컨트롤러 VM에 연결하는 다른 방법

클러스터에 필요한 정보: 

* 리소스 그룹 이름
* Azure 위치 
* 가상 네트워크 이름
* 서브넷 이름
* 클러스터 노드 역할 이름 - 이 이름은 [아래](#create-the-cluster-node-access-role)에 설명된 역할을 만들 때 설정됩니다.
* Blob 컨테이너를 만드는 경우 저장소 계정 이름

Resource Manager 템플릿을 사용하여 컨트롤러 노드를 만든 경우 [템플릿 출력](#find-template-output)에서 정보를 가져올 수 있습니다. 

Azure Marketplace 이미지를 사용하여 컨트롤러를 만든 경우 이러한 항목의 대부분을 직접 제공합니다. 

컨트롤러 VM 정보 페이지로 이동하여 누락된 항목을 찾습니다. 예를 들어 **모든 리소스**를 클릭하고, 컨트롤러 이름을 검색한 다음, 컨트롤러 이름을 클릭하여 세부 정보를 확인합니다.

### <a name="find-template-output"></a>템플릿 출력 찾기

Resource Manager 템플릿 출력에서 이 정보를 찾으려면 다음 절차를 수행합니다.

1. 클러스터 컨트롤러에 대한 리소스 그룹으로 이동합니다.

1. 왼쪽에서 **배포**를 클릭한 다음, **Microsoft.Template**을 클릭합니다.

   ![왼쪽에 '배포'가 선택되고 테이블의 '배포 이름' 아래에 Microsoft.Template을 보여 주는 리소스 그룹 포털 페이지](media/avere-vfxt-deployment-template.png)

1. 왼쪽에서 **출력**을 클릭합니다. 각 필드의 값을 복사합니다. 

   ![레이블 오른쪽의 필드에 표시된 SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK, SUBNET 및 SUBNET_ID가 있는 출력 페이지](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>컨트롤러에 액세스

나머지 배포 단계를 수행하려면 클러스터 컨트롤러에 연결해야 합니다.

1. 클러스터 컨트롤러에 연결하는 방법은 설정에 따라 달라집니다.

   * 컨트롤러에 공용 IP 주소가 있는 경우 컨트롤러의 IP에 대한 SSH를 설정한 관리자 사용자 이름(예: ``ssh azureuser@40.117.136.91``)으로 지정합니다.
   * 컨트롤러에 공용 IP가 없으면 vnet에 대한 VPN 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 연결을 사용합니다.

1. 컨트롤러에 로그인한 후 `az login`을 실행하여 인증합니다. 셸에 제공된 인증 코드를 복사한 다음, 웹 브라우저를 사용하여 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)을 로드하고 Microsoft 시스템을 통해 인증합니다. 확인을 위해 셸로 돌아갑니다.

   ![브라우저 링크 및 인증 코드를 표시하는 'AZ login' 명령의 명령줄 출력](media/avere-vfxt-azlogin.png)

1. 구독 ID를 사용하여 이 명령을 실행함으로써 구독을 추가합니다(```az account set --subscription YOUR_SUBSCRIPTION_ID```).

## <a name="create-the-cluster-node-access-role"></a>클러스터 노드 액세스 역할 만들기

> [!NOTE] 
> * 구독 소유자가 아니고 역할을 아직 만들지 않은 경우 구독 소유자에게 다음 단계를 수행하도록 하거나 [컨트롤러 없이 Avere vFXT 클러스터 런타임 액세스 역할 만들기](avere-vfxt-pre-role.md)의 절차를 사용합니다.
> 
> * Microsoft 내부 사용자는 새로 만들려고 시도하는 대신 “Avere 클러스터 런타임 운영자”라는 기존 역할을 사용해야 합니다. 

[RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/)는 필요한 작업을 수행할 수 있는 권한을 vFXT 클러스터 노드에 부여합니다.  

일반적인 vFXT 클러스터 작업의 일환으로, 개별 vFXT 노드는 Azure 리소스 속성 읽기, 저장소 관리 및 다른 노드의 네트워크 인터페이스 설정 제어와 같은 작업을 수행해야 합니다. 

1. 컨트롤러의 편집기에서 ``/avere-cluster.json`` 파일을 엽니다.

   ![목록 명령 및 "vi /avere-cluster.json"을 보여 주는 콘솔](media/avere-vfxt-open-role.png)

1. 구독 ID가 포함되도록 파일을 편집하고 위의 줄을 삭제합니다. 파일을 ``avere-cluster.json``(으)로 저장합니다.

   ![구독 ID 및 삭제를 위해 선택된 "remove this line(이 줄 제거)"을 보여 주는 콘솔 텍스트 편집기](media/avere-vfxt-edit-role.png)

1. 다음 명령을 사용하여 역할을 만듭니다.  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

다음 단계에서는 역할 이름을 클러스터 만들기 스크립트에 전달합니다. 

## <a name="create-nodes-and-configure-the-cluster"></a>노드 만들기 및 클러스터 구성

Avere vFXT 클러스터를 만들려면 컨트롤러에 포함된 샘플 스크립트 중 하나를 편집하여 실행합니다. 샘플 스크립트는 클러스터 컨트롤러의 루트 디렉터리(`/`)에 있습니다.

* Avere vFXT의 백 엔드 저장소 시스템으로 사용할 Blob 컨테이너를 만들려면 ``create-cloudbacked-cluster`` 스크립트를 사용합니다.

* 나중에 저장소를 추가하려면 ``create-minimal-cluster`` 스크립트를 사용합니다.

> [!TIP]
> 노드를 추가하고 vFXT 클러스터를 삭제하는 프로토타입 스크립트도 클러스터 컨트롤러 VM의 `/` 디렉터리에 포함되어 있습니다.

### <a name="edit-the-deployment-script"></a>배포 스크립트 편집

편집기에서 샘플 스크립트를 엽니다. 원래 샘플을 덮어쓰지 않도록 사용자 지정된 스크립트를 다른 이름으로 저장하는 것이 좋습니다.

이러한 스크립트 변수에 대한 값을 입력합니다.

* 리소스 그룹 이름

  * 서로 다른 리소스 그룹에 있는 네트워크 또는 저장소 구성 요소를 사용하는 경우 변수의 주석 처리를 제거하고 해당 이름도 제공합니다. 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* 위치 이름
* 가상 네트워크 이름
* 서브넷 이름
* Azure AD 런타임 역할 이름 - [클러스터 노드 액세스 역할 만들기](#create-the-cluster-node-access-role)의 예제를 수행한 경우 ``avere-cluster``를 사용합니다. 
* 저장소 계정 이름(새 Blob 컨테이너를 만드는 경우)
* 클러스터 이름 - 동일한 리소스 그룹에 동일한 이름을 사용하는 두 개의 vFXT 클러스터가 있을 수 없습니다. 모범 사례를 위해 각 클러스터에 고유한 이름을 지정합니다.
* 관리 암호 - 클러스터를 모니터링하고 관리하기 위한 보안 암호를 선택합니다. 이 암호는 ``admin`` 사용자에게 할당됩니다. 
* 노드 인스턴스 유형 - 자세한 내용은 [vFXT 노드 크기](avere-vfxt-deploy-plan.md#vfxt-node-sizes) 참조
* 노드 캐시 크기 - 자세한 내용은 [vFXT 노드 크기](avere-vfxt-deploy-plan.md#vfxt-node-sizes) 참조

파일을 저장하고 종료합니다.

### <a name="run-the-script"></a>스크립트 실행

만든 파일 이름을 입력하여 스크립트를 실행합니다(예: `./create-cloudbacked-cluster-west1`).  

> [!TIP]
> 연결이 끊기는 경우에 대비하여 `screen` 또는 `tmux`와 같은 [터미널 멀티플렉서](http://linuxcommand.org/lc3_adv_termmux.php) 내에서 이 명령을 실행하는 것이 좋습니다.  

출력도 `~/vfxt.log`에 기록됩니다.

스크립트가 완료되면 클러스터 관리에 필요한 관리 IP 주소를 복사합니다.

![끝 부분에 관리 IP 주소를 표시하는 스크립트의 명령줄 출력](media/avere-vfxt-mgmt-ip.png)

## <a name="next-step"></a>다음 단계

이제 클러스터가 실행되고 관리 IP 주소를 알고 있으므로 [클러스터 구성 도구에 연결](avere-vfxt-cluster-gui.md)하여 지원을 사용하도록 설정하고, 필요한 경우 저장소를 추가할 수 있습니다.
