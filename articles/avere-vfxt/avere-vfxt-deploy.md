---
title: Avere vFXT for Azure 배포
description: Azure에 Avere vFXT 클러스터를 배포하는 단계입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 972ba937ad15fa9a6d2eb74e3e4c9e6e8f3923a4
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745438"
---
# <a name="deploy-the-vfxt-cluster"></a>vFXT 클러스터 배포

이 절차에서는 Azure Marketplace에서 제공되는 배포 마법사를 사용하는 과정을 안내합니다. 이 마법사는 Azure Resource Manager 템플릿을 사용하여 클러스터를 자동으로 배포합니다. 양식에 매개 변수를 입력하고 **만들기**를 클릭하면 Azure에서 다음 단계가 자동으로 완료됩니다. 

* 클러스터를 배포하고 관리하는 데 필요한 소프트웨어가 포함된 기본 VM인 클러스터 컨트롤러를 만듭니다.
* 리소스 그룹 및 가상 네트워크 인프라를 설정하고 필요한 경우 새 요소를 만듭니다.
* 클러스터 노드 VM을 만들어 Avere 클러스터로 구성합니다.
* 요청이 표시되면 새 Azure Blob 컨테이너를 만들어 클러스터 코어 필터로 구성합니다.

이 문서의 지침을 따르면 다음 다이어그램과 같이 가상 네트워크, 서브넷, 컨트롤러 및 vFXT 클러스터가 갖추어집니다.

![선택적 Blob Storage가 포함된 vnet 및 vFXT 노드/vFXT 클러스터라는 레이블이 지정되어 그룹화된 세 개의 VM과 클러스터 컨트롤러라는 레이블이 지정된 하나의 VM이 포함된 서브넷을 보여 주는 다이어그램](media/avere-vfxt-deployment.png)

Blob 스토리지를 사용하는 경우 클러스터를 만든 후에 [스토리지 엔드포인트를 생성](#create-a-storage-endpoint-if-using-azure-blob)해야 합니다. 

만들기 템플릿을 사용하기 전에 다음 필수 구성 요소가 충족되었는지 확인하세요.  

1. [새 구독](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [구독 소유자 권한](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [사용자 지정 액세스 역할](avere-vfxt-prereqs.md#create-access-roles) - 클러스터 노드에 할당할 역할 기반 액세스 제어 역할을 만들어야 합니다. 클러스터 컨트롤러용 사용자 지정 액세스 역할도 만들 수 있습니다. 하지만 대부분의 사용자에게는 기본 소유자 역할이 있으므로 리소스 그룹 소유자에 해당하는 컨트롤러 권한이 제공됩니다. 자세한 내용은 [Azure 리소스용 기본 제공 역할](../role-based-access-control/built-in-roles.md#owner)을 참조하세요.

클러스터 배포 단계 및 계획에 대한 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md) 및 [배포 개요](avere-vfxt-deploy-overview.md)를 참조하세요.

## <a name="create-the-avere-vfxt-for-azure"></a>Avere vFXT for Azure 만들기

Avere를 검색한 다음, "Avere vFXT ARM 배포"를 선택하여 Azure Portal에서 만들기 템플릿에 액세스합니다. 

![브라우저 창에 이동 경로 "새로 만들기 > Marketplace > 모두"와 함께 Azure Portal이 표시됩니다. 모두 페이지의 검색 필드에 검색어 "avere"가 표시되고 두 번째 결과 "Avere vFXT ARM 배포"의 외곽선이 빨간색으로 강조 표시됩니다.](media/avere-vfxt-template-choose.png)

Avere vFXT ARM 배포 페이지의 세부 정보를 읽은 후 **만들기**를 클릭하여 시작합니다. 

![배포 템플릿의 첫 페이지가 표시된 Azure Marketplace](media/avere-vfxt-deploy-first.png)

템플릿은 4단계로 구성되어 있습니다. 그중 두 단계는 정보 수집 페이지이고 나머지 두 단계는 각각 유효성 검사 및 확인 단계입니다. 

* 1페이지에서는 클러스터 컨트롤러 VM 설정을 주로 지정합니다. 
* 2페이지에서는 클러스터 및 관련 리소스(예: 서브넷, 스토리지)를 만드는 데 필요한 매개 변수를 수집합니다. 
* 설정 요약 정보가 표시되는 3페이지에서는 구성의 유효성을 검사합니다. 
* 소프트웨어 사용 약관 설명이 표시되는 4페이지에서 클러스터 만들기 프로세스를 시작할 수 있습니다. 

## <a name="page-one-parameters---cluster-controller-information"></a>1페이지 매개 변수 - 클러스터 컨트롤러 정보

배포 템플릿의 1페이지에서는 클러스터 컨트롤러 관련 정보를 수집합니다. 

![배포 템플릿 1페이지](media/avere-vfxt-deploy-1.png)

다음 정보를 입력합니다.

* **클러스터 컨트롤러 이름** - 클러스터 컨트롤러 VM의 이름을 설정합니다.

* **컨트롤러 사용자 이름** - 클러스터 컨트롤러 VM의 루트 사용자 이름을 입력합니다. 

* **인증 유형** - 컨트롤러에 연결하는 데 사용할 인증 유형(암호 또는 SSH 공개 키 인증)을 선택합니다. SSH 공개 키 방법을 사용하는 것이 좋습니다. 도움이 필요한 경우 [SSH 키를 만들고 사용하는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)을 참조하세요.

* **암호** 또는 **SSH 공개 키** - 선택한 인증 유형에 따라 다음 필드에 RSA 공개 키 또는 암호를 입력해야 합니다. 앞에서 입력한 사용자 이름과 함께 이 자격 증명을 사용합니다.

* **Avere 클러스터 만들기 역할 ID** - 이 필드에서 클러스터 컨트롤러의 액세스 제어 역할을 지정합니다. 기본값은 기본 제공 역할인 [소유자](../role-based-access-control/built-in-roles.md#owner)입니다. 클러스터 컨트롤러의 소유자 권한 범위는 클러스터 리소스 그룹으로 제한됩니다. 

  역할에 해당하는 GUID(Globally Unique Identifier)를 사용해야 합니다. 기본값(소유자)의 GUID는 8e3af657-a8ff-443c-a75c-2fe8c4bcb635입니다. 사용자 지정 역할의 GUID를 확인하려면 다음 명령을 사용합니다. 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **구독** - Avere vFXT의 구독을 선택합니다. 

* **리소스 그룹** - 기존의 빈 Avere vFXT 클러스터용 리소스 그룹을 선택하거나 "새로 만들기"를 클릭하고 새 리소스 그룹 이름을 입력합니다. 

* **위치** - 클러스터와 리소스의 Azure 위치를 선택합니다.

작업이 완료되면 **확인**을 클릭합니다. 

> [!NOTE]
> 클러스터 컨트롤러에 공용 IP 주소를 사용하려는 경우 기존 네트워크를 선택하는 대신 클러스터용으로 새 가상 네트워크를 만듭니다. 이 설정은 2페이지에 있습니다.

## <a name="page-two-parameters---vfxt-cluster-information"></a>2페이지 매개 변수 - vFXT 클러스터 정보

배포 템플릿의 2페이지에서는 클러스터 크기, 노드 유형, 캐시 크기 및 스토리지 매개 변수와 같은 여러 설정을 지정할 수 있습니다. 

![배포 템플릿 2페이지](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 클러스터 노드 수** - 클러스터에서 사용할 노드 수를 선택합니다. 최소값은 노드 3개, 최대값은 12개입니다. 

* **클러스터 관리 암호** - 클러스터 관리용 암호를 만듭니다. 이 암호와 사용자 이름 ```admin```을 사용하여 클러스터 제어판에 로그인해 클러스터를 모니터링하고 설정을 구성합니다.

* **Avere 클러스터 작업 역할** - 클러스터 노드의 액세스 제어 역할 이름을 지정합니다. 이 역할은 필수 구성 요소 단계에서 생성된 사용자 지정 역할입니다. 

  [클러스터 노드 액세스 역할 만들기](avere-vfxt-prereqs.md#create-the-cluster-node-access-role)에 설명되어 있는 예제에서는 파일을 ```avere-operator.json```으로 저장하며, 해당 역할 이름은 ```avere-operator```입니다.

* **Avere vFXT 클러스터 이름** - 클러스터에 고유한 이름을 지정합니다. 

* **크기** - 클러스터 노드를 만들 때 사용할 VM 유형을 지정합니다. 

* **노드당 캐시 크기** - 클러스터 캐시는 여러 클러스터 노드에 분산되므로 Avere vFXT 클러스터의 총 캐시 크기는 노드당 캐스 크기에 노드 수를 곱한 값입니다. 

  Standard_D16s_v3 클러스터 노드 사용 시에는 노드당 1GB를 사용하고, Standard_E32s_v3 노드 사용 시에는 노드당 4TB를 사용하는 것이 권장 구성입니다.

* **가상 네트워크** - 클러스터를 저장할 기존 가상 네트워크를 선택하거나 새로 만들 가상 네트워크를 지정합니다. 

  > [!NOTE]
  > 새 가상 네트워크를 만드는 경우 클러스터 컨트롤러에 공용 IP 주소가 지정되므로 새 사설망에 액세스할 수 있습니다. 기존 가상 네트워크를 선택하는 경우에는 공용 IP 주소 없이 클러스터 컨트롤러가 구성됩니다. 
  > 
  > 클러스터 컨트롤러에 공개된 IP 주소가 있으면 vFXT 클러스터에 더 쉽게 액세스할 수 있지만 보안상으로는 다소 위험합니다. 
  >  * 클러스터 컨트롤러의 공용 IP 주소를 사용하면 이 주소를 점프 호스트로 사용하여 사설 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다.
  >  * 컨트롤러에 공용 IP 주소를 설정하지 않은 경우 다른 점프 호스트, VPN 연결 또는 ExpressRoute를 사용하여 클러스터에 액세스해야 합니다. 예를 들어 VPN 연결이 이미 구성된 가상 네트워크 내에 컨트롤러를 만듭니다.
  >  * 공용 IP 주소가 있는 컨트롤러를 만드는 경우 컨트롤러 VM을 네트워크 보안 그룹으로 보호해야 합니다. 기본적으로 Avere vFXT for Azure 배포에서는 보안 네트워크 그룹이 생성되며, 공용 IP 주소가 있는 컨트롤러가 포트 22에만 인바운드 액세스할 수 있도록 제한합니다. 액세스 범위를 IP 원본 주소 범위로 고정(클러스터 액세스에 사용하려는 컴퓨터의 연결만 허용)하면 시스템을 추가로 보호할 수 있습니다.

* **서브넷** - 기존 가상 네트워크에서 서브넷을 선택하거나 새 서브넷을 만듭니다. 

* **Blob 스토리지 사용** - **True**를 선택하여 새 Azure Blob 컨테이너를 만들고 새 Avere vFXT 클러스터용 백 엔드 스토리지로 구성합니다. 이 옵션은 클러스터와 같은 리소스 그룹 내에도 새 스토리지 계정을 만듭니다. 

  새 컨테이너를 만들지 않으려면 이 필드를 **false**로 설정합니다. 이 경우 클러스터를 만든 후 스토리지를 연결하고 구성해야 합니다. 지침은 [스토리지 구성](avere-vfxt-add-storage.md)을 읽으세요. 

* **스토리지 계정** - 새 Azure Blob 컨테이너를 만드는 경우 새 스토리지 계정 이름을 입력합니다. 

## <a name="validation-and-purchase"></a>유효성 검사 및 구매

구성 요약 정보가 표시되는 3페이지에서는 매개 변수의 유효성을 검사합니다. 유효성 검사가 정상적으로 완료되면 **확인** 단추를 클릭하여 계속 진행합니다. 

![배포 템플릿 3페이지 - 유효성 검사](media/avere-vfxt-deploy-3.png)

4페이지에서는 **만들기** 단추를 클릭하여 약관에 동의하고 Avere vFXT for Azure 클러스터를 만듭니다. 

![배포 템플릿 4페이지 - 약관, 만들기 단추](media/avere-vfxt-deploy-4.png)

클러스터 배포는 15~20분 정도 걸립니다.

## <a name="gather-template-output"></a>템플릿 출력 수집

Avere vFXT 템플릿에서 클러스터 만들기가 완료되면 새 클러스터에 대한 몇 가지 중요 정보가 출력됩니다. 

> [!TIP]
> 템플릿 출력에서 관리 IP 주소를 복사해 두세요. 클러스터를 관리하려면 이 주소가 필요합니다.

이 정보를 찾으려면 다음 절차를 수행하세요.

1. 클러스터 컨트롤러에 대한 리소스 그룹으로 이동합니다.

1. 왼쪽에서 **배포**를 클릭한 다음 **microsoft-avere.vfxt-template**을 클릭합니다.

   ![왼쪽에 배포가 선택되어 있고 배포 이름 아래의 표에 microsoft-avere.vfxt-template이 표시되어 있는 리소스 그룹 포털 페이지](media/avere-vfxt-outputs-deployments.png)

1. 왼쪽에서 **출력**을 클릭합니다. 각 필드의 값을 복사합니다. 

   ![레이블 오른쪽의 필드에 SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs, MGMT_IP 값이 표시된 출력 페이지](media/avere-vfxt-outputs-values.png)


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>저장소 엔드포인트 만들기(Azure Blob을 사용하는 경우)

Azure Blob Storage를 백 엔드 데이터 스토리지로 사용하는 경우 가상 네트워크에 스토리지 서비스 엔드포인트를 만들어야 합니다. 이 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 Azure Blob 트래픽을 가상 네트워크 외부에서 라우팅하는 대신 로컬로 유지합니다.

1. 포털의 왼쪽에서 **가상 네트워크**를 클릭합니다.
1. 컨트롤러에 대한 vnet을 선택합니다. 
1. 왼쪽에서 **서비스 엔드포인트**를 클릭합니다.
1. 위쪽에서 **추가**를 클릭합니다.
1. 서비스를 ``Microsoft.Storage``로 두고, 컨트롤러의 서브넷을 선택합니다.
1. 아래쪽에서 **추가**를 클릭합니다.

  ![서비스 엔드포인트를 만드는 단계에 대한 주석이 있는 Azure Portal 스크린샷](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>다음 단계

이제 클러스터가 실행되고 관리 IP 주소를 알고 있으므로 [클러스터 구성 도구에 연결](avere-vfxt-cluster-gui.md)하여 지원을 사용하도록 설정하고, 필요한 경우 스토리지를 추가하고, 기타 클러스터 설정을 사용자 지정할 수 있습니다.
