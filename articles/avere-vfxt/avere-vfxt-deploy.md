---
title: Avere vFXT for Azure 배포
description: Azure에 Avere vFXT 클러스터를 배포하는 단계입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: v-erkell
ms.openlocfilehash: 7ded66c29f12b8f68746726ca6c126bffbc51f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410332"
---
# <a name="deploy-the-vfxt-cluster"></a>vFXT 클러스터 배포

이 절차에서는 Azure Marketplace에서 제공되는 배포 마법사를 사용하는 과정을 안내합니다. 이 마법사는 Azure Resource Manager 템플릿을 사용하여 클러스터를 자동으로 배포합니다. 양식에 매개 변수를 입력하고 **만들기**를 클릭하면 Azure에서 다음 단계가 자동으로 완료됩니다.

* 배포 하 고 클러스터를 관리 하는 데 필요한 소프트웨어를 포함 하는 기본 VM은 클러스터 컨트롤러를 만듭니다.
* 리소스 그룹 및 새 요소를 만드는 등 가상 네트워크 인프라를 설정 합니다.
* 클러스터 노드 Vm을 만들고 Avere 클러스터로 구성 합니다.
* 요청 된 경우 새 Azure Blob 컨테이너를 만들고 클러스터 코어 필터가로 구성 합니다.

이 문서의 지침을 따른 후 가상 네트워크, 서브넷, 컨트롤러 및 다음 다이어그램에 표시 된 대로 vFXT 클러스터 해야 합니다. 이 다이어그램에 새 Blob 저장소 컨테이너 (새 저장소 계정에 표시 되지 않음) 및 서브넷 내에서 Microsoft storage에 대 한 서비스 끝점을 포함 하는 선택적 Azure Blob core 필터가 보여 줍니다. 

![Avere 클러스터 구성 요소를 사용 하 여 세 개의 동심 사각형을 보여 주는 다이어그램입니다. 외부 사각형 '리소스 그룹' 레이블이 지정 되며 '(선택 사항) 저장소 Blob' 레이블이 육각형을 포함 합니다. 다음 사각형은 레이블이 지정 된 ' 가상 네트워크: 10.0.0.0/16' 모든 고유한 구성 요소를 포함 하지 않습니다. 가장 안쪽의 사각형 'Subnet:10.0.0.0/24' 레이블이 지정 되며 '클러스터 컨트롤러', 'vFXT 노드 (vFXT 클러스터)' 라는 3 개의 Vm 및 육각형 '서비스 끝점' 레이블이 지정 된 스택을 레이블이 지정 된 VM을 포함 합니다. 연결 서비스 끝점 (즉 서브넷 내에서) 및 blob storage (이 리소스 그룹에 vnet 및 서브넷 외부) 화살표가 있습니다. 화살표는 서브넷 및 가상 네트워크 경계를 통과합니다.](media/avere-vfxt-deployment.png)  

만들기 템플릿을 사용하기 전에 다음 필수 구성 요소가 충족되었는지 확인하세요.  

1. [새 구독](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [구독 소유자 권한](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [저장소 서비스 끝점 (필요한 경우)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) 필요-blob 저장소를 만들고 기존 가상 네트워크를 사용 하 여 배포

클러스터 배포 단계 및 계획에 대한 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md) 및 [배포 개요](avere-vfxt-deploy-overview.md)를 참조하세요.

## <a name="create-the-avere-vfxt-for-azure"></a>Avere vFXT for Azure 만들기

Avere 검색 하 고 "Azure ARM 템플릿에 대 한 Avere vFXT"를 선택 하 여 Azure portal에서 생성 템플릿에 액세스 합니다. 

![브라우저 창에 이동 경로 "새로 만들기 > Marketplace > 모두"와 함께 Azure Portal이 표시됩니다. 모두 페이지, 검색 필드에 용어 "avere" 및 "Azure ARM 템플릿에 대 한 Avere vFXT" 두 번째 결과 빨간색으로 강조 표시에 설명 된 합니다.](media/avere-vfxt-template-choose.png)

Avere vFXT Azure ARM 템플릿 페이지에 대 한 세부 정보를 읽은 후 다음 클릭 **만들기** 시작 합니다. 

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

* **Avere vFXT 클러스터 이름** - 클러스터에 고유한 이름을 지정합니다. 

* **크기** -이 섹션에서는 클러스터 노드에 대해 사용할 VM 유형을 표시 합니다. 권장된 옵션 하나만 있지만 합니다 **크기를 변경** 링크는이 인스턴스 유형 및 가격 책정 계산기에 대 한 링크에 대 한 정보를 사용 하 여 테이블을 엽니다.  

* **노드당 캐시 크기** - 클러스터 캐시는 여러 클러스터 노드에 분산되므로 Avere vFXT 클러스터의 총 캐시 크기는 노드당 캐스 크기에 노드 수를 곱한 값입니다. 

  권장 구성은 노드당 Standard_E32s_v3 노드에 대 한 4TB를 사용 하는 것입니다.

* **가상 네트워크** -클러스터를 저장할 새 vnet을 정의 하거나에 설명 된 필수 구성 요소를 충족 하는 기존 vnet을 선택할 [Avere vFXT 시스템을 계획](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure)합니다. 

  > [!NOTE]
  > 새 가상 네트워크를 만드는 경우 클러스터 컨트롤러에 공용 IP 주소가 지정되므로 새 사설망에 액세스할 수 있습니다. 기존 가상 네트워크를 선택하는 경우에는 공용 IP 주소 없이 클러스터 컨트롤러가 구성됩니다. 
  > 
  > 클러스터 컨트롤러에 공개된 IP 주소가 있으면 vFXT 클러스터에 더 쉽게 액세스할 수 있지만 보안상으로는 다소 위험합니다. 
  >  * 클러스터 컨트롤러의 공용 IP 주소를 사용하면 이 주소를 점프 호스트로 사용하여 프라이빗 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다.
  >  * 컨트롤러에 공용 IP 주소를 설정하지 않은 경우 다른 점프 호스트, VPN 연결 또는 ExpressRoute를 사용하여 클러스터에 액세스해야 합니다. 예를 들어 VPN 연결이 이미 구성된 가상 네트워크 내에 컨트롤러를 만듭니다.
  >  * 공용 IP 주소가 있는 컨트롤러를 만드는 경우 컨트롤러 VM을 네트워크 보안 그룹으로 보호해야 합니다. 기본적으로 Avere vFXT for Azure 배포에서는 보안 네트워크 그룹이 생성되며, 공용 IP 주소가 있는 컨트롤러가 포트 22에만 인바운드 액세스할 수 있도록 제한합니다. 액세스 범위를 IP 원본 주소 범위로 고정(클러스터 액세스에 사용하려는 컴퓨터의 연결만 허용)하면 시스템을 추가로 보호할 수 있습니다.

  클러스터 서브넷의 Ip만을 잠긴 네트워크 액세스 제어와 Azure Blob storage에 대 한 저장소 서비스 끝점을 사용 하 여 템플릿 배포도 새 vnet을 구성 합니다. 

* **서브넷** - 기존 가상 네트워크에서 서브넷을 선택하거나 새 서브넷을 만듭니다. 

* **만들기 및 blob storage 사용** -선택 **true** 새 Azure Blob 컨테이너를 만들고 새 Avere vFXT 클러스터에 대 한 백 엔드 저장소로 구성 합니다. 이 옵션에는 또한 클러스터와 클러스터 서브넷 내에서 Microsoft 저장소 서비스 끝점을 동일한 리소스 그룹 내에서 새 저장소 계정을 만듭니다. 
  
  기존 가상 네트워크를 제공 하는 경우 클러스터를 만들기 전에 저장소 서비스 끝점을 있어야 합니다. (자세한 내용은 [Avere vFXT 시스템을 계획](avere-vfxt-deploy-plan.md).)

  새 컨테이너를 만들지 않으려면 이 필드를 **false**로 설정합니다. 이 경우 클러스터를 만든 후 스토리지를 연결하고 구성해야 합니다. 지침은 [스토리지 구성](avere-vfxt-add-storage.md)을 읽으세요. 

* **(신규) 저장소 계정** -새 저장소 계정 이름을 입력 하는 새 Azure Blob 컨테이너 만들기. 

## <a name="validation-and-purchase"></a>유효성 검사 및 구매

3 페이지 구성을 요약 하 고 매개 변수 유효성을 검사 합니다. 유효성 검사가 정상적으로 완료되면 **확인** 단추를 클릭하여 계속 진행합니다. 

![배포 템플릿 3페이지 - 유효성 검사](media/avere-vfxt-deploy-3.png)

4 페이지에서 필요한 모든 연락처 정보를 입력 하 고 클릭 합니다 **만들기** 단추 Azure 클러스터에 대 한 Avere vFXT을 만들고 약관에 동의 합니다. 

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

## <a name="next-step"></a>다음 단계

이제 클러스터가 실행되고 관리 IP 주소를 알고 있으므로 [클러스터 구성 도구에 연결](avere-vfxt-cluster-gui.md)하여 지원을 사용하도록 설정하고, 필요한 경우 스토리지를 추가하고, 기타 클러스터 설정을 사용자 지정할 수 있습니다.
