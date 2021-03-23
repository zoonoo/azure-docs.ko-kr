---
title: 자습서 - VMware HCX 배포 및 구성
description: Azure VMware Solution 프라이빗 클라우드를 위한 VMware HCX 솔루션을 배포 및 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: d1b0792ab9b9975edf45ab8addb81410e1da4107
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462315"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX 배포 및 구성

이 문서에서는 Azure VMware Solution 프라이빗 클라우드용 온-프레미스 VMware HCX Connector를 배포하고 구성하는 방법을 보여 줍니다. VMware HCX를 사용하면 다양한 마이그레이션 유형을 통해 VMware 워크로드를 Azure VMware Solution 및 기타 연결된 사이트로 마이그레이션할 수 있습니다. Azure VMware Solution은 HCX Cloud Manager를 배포하고 구성하므로 온-프레미스 VMware 데이터 센터에서 HCX 커넥터를 다운로드, 활성화 및 구성해야 합니다.

VMware HCX 고급 커넥터는 Azure VMware Solution에 미리 배포되어 있습니다. 이 커넥터는 최대 3개의 사이트 연결(온-프레미스에서 클라우드로 연결 또는 클라우드에서 클라우드로 연결)을 지원합니다. 사이트 연결 4개 이상 필요한 경우 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 추가 기능을 사용할 수 있도록 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 제출하세요.  

>[!TIP]
>VMware 구성 최대 도구는 온-프레미스 커넥터와 클라우드 관리자 간의 최대 25개까지 사이트 쌍을 설명하지만, 라이선스는 이를 3개(HCX Advanced의 경우) 및 10개(HCX Enterprise Edition의 경우)로 제한합니다.

>[!NOTE]
>VMware HCX Enterprise는 Azure VMware Solution에서 미리 보기 서비스로 사용할 수 있습니다. 무료이며 미리 보기 서비스의 약관이 적용됩니다. VMware HCX Enterprise 서비스가 일반 공급되면 30일 이내에 요금 청구가 변경된다는 알림을 받게 됩니다. 또한 서비스를 해제하거나 옵트아웃하는 옵션이 있습니다. VMware HCX Enterprise에서 VMware HCX Advanced로 다운그레이드하는 간단한 경로가 없습니다. 다운그레이드하도록 결정하는 경우 다시 배포해야 하므로 가동 중지 시간이 발생합니다.

먼저 [시작하기 전에](#before-you-begin), [소프트웨어 버전 요구 사항](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) 및 [필수 구성 요소](#prerequisites)를 검토합니다. 

그런 다음, 필요한 모든 절차를 진행하겠습니다.

> [!div class="checklist"]
> * VMware HCX Connector OVA 다운로드
> * 온-프레미스 VMware HCX OVA(VMware HCX Connector) 배포
> * VMware HCX 커넥터 활성화
> * 온-프레미스 VMware HCX Connector를 Azure VMware Solution HCX Cloud Manager와 페어링
> * 상호 연결 구성(네트워크 프로필, 컴퓨팅 프로필 및 서비스 메시)
> * 어플라이언스 상태를 확인하고 마이그레이션이 가능한지 확인하여 설정 완료

작업이 완료되면 이 문서의 끝에서 추천되는 다음 단계를 수행합니다.  

## <a name="before-you-begin"></a>시작하기 전에

배포를 준비할 때 다음 VMware 설명서를 검토하는 것이 좋습니다.

* [VMware HCX 사용자 가이드](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [VMware HCX를 사용하여 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [VMware HCX 배포 고려 사항](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware 블로그 시리즈 - 클라우드 마이그레이션](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [VMware HCX에 필요한 네트워크 포트](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>필수 구성 요소

VMware HCX Enterprise를 사용하려면 Azure VMware Solution 지원 채널을 통해 활성화를 요청했는지 확인합니다.


### <a name="on-premises-vsphere-environment"></a>온-프레미스 vSphere 환경

온-프레미스 vSphere 환경(원본 환경)에서 [최소 요구 사항](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)을 충족하는지 확인합니다. 

### <a name="network-and-ports"></a>네트워크 및 포트

* [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)는 온-프레미스 및 Azure VMware Solution 프라이빗 클라우드 ExpressRoute 회로 간에 구성됩니다.

* [필요한 모든 포트](https://ports.vmware.com/home/VMware-HCX)는 온-프레미스 구성 요소 및 Azure VMware Solution 프라이빗 간의 통신을 위해 열려 있습니다.

### <a name="ip-addresses"></a>IP 주소

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>VMware HCX Connector OVA 다운로드

가상 어플라이언스를 온-프레미스 vCenter에 배포하려면 먼저 VMware HCX 커넥터 OVA를 다운로드해야 합니다.  

1. Azure Portal에서 Azure VMware Solution 프라이빗 클라우드를 선택합니다. 

1. **관리** > **연결** 을 차례로 선택하고, **HCX** 탭을 선택하여 Azure VMware Solution HCX Manager의 IP 주소를 확인합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="VMware HCX IP 주소의 스크린샷" lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. **관리** > **ID** 를 선택합니다. 

   프라이빗 클라우드 vCenter 및 NSX-T Manager에 대한 URL 및 사용자 자격 증명이 표시됩니다.

   > [!TIP]
   > vCenter 암호는 프라이빗 클라우드를 설정할 때 정의되었습니다. Azure VMware Solution HCX Manager에 로그인하는 데 사용하는 것과 동일한 암호입니다. **새 암호 생성** 을 선택하여 새 vCenter 및 NSX-T 암호를 생성할 수 있습니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="프라이빗 클라우드 vCenter와 NSX 관리자의 URL 및 자격 증명을 표시합니다." border="true":::


1. 브라우저 창을 열고, **cloudadmin\@vsphere.local** 사용자 자격 증명을 사용하여 `https://x.x.x.9` 443 포트에서 Azure VMware Solution HCX Manager에 로그인합니다.

1. **관리** > **시스템 업데이트** 를 차례로 선택한 다음, **다운로드 링크 요청** 을 선택합니다.

1. 원하는 옵션을 선택하여 VMware HCX Connector OVA 파일을 다운로드합니다.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>VMware HCX 커넥터 OVA 온-프레미스 배포

1. 온-프레미스 vCenter에서 [OVF 템플릿](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)을 선택하여 VMware HCX Connector를 온-프레미스 vCenter에 배포합니다. 

   > [!TIP]
   > 이전 섹션에서 다운로드한 OVA 파일을 선택합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 이름 및 위치를 선택하고, VMware HCX Connector를 배포할 리소스 또는 클러스터를 선택합니다. 그런 다음, 세부 정보 및 필요한 리소스를 검토하고, **다음** 을 선택합니다.  

1. 사용 조건을 검토합니다. 사용 조건에 동의하는 경우 필요한 스토리지 및 네트워크를 선택하고 **다음** 을 선택합니다.

1. 스토리지를 선택하고, **다음** 을 선택합니다.

1. 이전에 [IP 주소 필수 구성 요소](#ip-addresses) 섹션에서 정의한 VMware HCX 관리 네트워크 세그먼트를 선택합니다.  그다음에 **다음** 을 선택합니다.

1. **템플릿 사용자 지정** 에서 필요한 모든 정보를 입력하고, **다음** 을 선택합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="템플릿을 사용자 지정하는 상자의 스크린샷" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 구성을 확인한 다음, **마침** 을 선택하여 VMware HCX Connector OVA를 배포합니다.
   
   > [!IMPORTANT]
   > 가상 어플라이언스를 수동으로 설정해야 합니다.  전원을 켜고 10~15분 정도 기다린 후에 다음 단계로 진행하세요.

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 어플라이언스 배포](https://www.youtube.com/embed/UKmSTYrL6AY) 비디오를 시청하세요. 


## <a name="activate-vmware-hcx"></a>VMware HCX 활성화

VMware HCX 커넥터 OVA 온-프레미스를 배포하고 어플라이언스를 시작했으면 활성화 준비가 완료된 것입니다. 먼저 Azure VMware Solution 포털에서 라이선스 키를 받아야 합니다.

1. Azure VMware Solution 포털에서 **관리** > **연결** 로 이동하고, **HCX** 탭을 선택한 다음, **추가** 를 선택합니다.

1. `https://HCXManagerIP:9443`에서 **관리자** 자격 증명을 사용하여 온-프레미스 VMware HCX Manager에 로그인합니다. 

   > [!TIP]
   > VMware HCX Manager OVA 파일을 배포하는 중에 **관리자** 사용자 암호를 정의했습니다.

   > [!IMPORTANT]
   > VMware HCX Manager IP 주소에 `9443` 포트 번호를 포함해야 합니다.

1. **라이선스** 에서 **HCX Advanced 키** 에 대한 키를 입력하고, **활성화** 를 선택합니다.  
   
    > [!NOTE]
    > VMware HCX Manager는 인터넷 액세스가 가능하거나 프록시가 구성되어 있어야 합니다.

1. **데이터 센터 위치** 에서, VMware HCX Manager를 온-프레미스에 설치할 가장 가까운 위치를 입력합니다. 그런 다음, **계속** 을 선택합니다.

1. **시스템 이름** 에서 이름을 수정하거나 기본값을 적용하고, **계속** 을 선택합니다.
   
1. **예, 계속** 을 선택합니다.

1. **vCenter에 연결** 에서, vCenter 서버의 FQDN 또는 IP 주소와 적절한 자격 증명을 입력한 다음, **계속** 을 선택합니다.
   
   > [!TIP]
   > vCenter Server는 VMware HCX Connector를 데이터 센터에 배포한 위치입니다.

1. **SSO/PSC 구성** 에서, Platform Services Controller의 FQDN 또는 IP 주소를 입력한 다음, **계속** 을 선택합니다.
   
   > [!NOTE]
   > 일반적으로 vCenter FQDN 또는 IP 주소와 동일합니다.

1. 입력한 정보가 올바른지 확인하고 **다시 시작** 을 선택합니다.
    
   > [!NOTE]
   > 다시 시작한 후 다음 단계를 진행하는 메시지가 표시될 때까지 약간 시간이 걸립니다.

서비스가 다시 시작되면 나타나는 화면에 vCenter가 녹색으로 표시됩니다. vCenter와 SSO 모두 이전 화면과 동일하게 적절한 구성 매개 변수를 포함하고 있어야 합니다.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="vCenter 상태가 녹색인 대시보드의 스크린샷" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 활성화](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720) 비디오를 시청하세요.

   > [!IMPORTANT]
   > HCX Advanced 또는 HCX Enterprise를 사용하는지 여부에 관계없이 VMware의 [KB 문서 81558](https://kb.vmware.com/s/article/81558)에서 패치를 설치해야 할 수 있습니다. 

## <a name="configure-the-vmware-hcx-connector"></a>VMware HCX 커넥터 구성

이제 사이트 페어링을 추가하고, 네트워크 및 컴퓨팅 프로필을 만들고, 마이그레이션, 네트워크 확장, 재해 복구 등의 서비스를 사용할 준비가 되었습니다. 

### <a name="add-a-site-pairing"></a>사이트 페어링 추가

Azure VMware Solution의 VMware HCX Cloud Manager를 데이터 센터의 VMware HCX Connector와 연결하거나 페어링할 수 있습니다. 

1. 온-프레미스 vCenter에 로그인하고, **홈** 에서 **HCX** 를 선택합니다.

1. **인프라** 에서 **사이트 페어링** 을 선택한 다음, **원격 사이트에 연결** 옵션(화면 가운데)을 선택합니다. 

1. 앞서 언급한 Azure VMware Solution HCX Cloud Manager URL 또는 IP 주소(`https://x.x.x.9`), Azure VMware Solution cloudadmin\@vsphere.local 사용자 이름 및 암호를 입력합니다. 그런 다음 **연결** 을 선택합니다.

   > [!NOTE]
   > 사이트 쌍을 성공적으로 설정하려면 다음을 수행합니다.
   > * VMware HCX Connector는 443 포트를 통해 HCX Cloud Manager IP로 라우팅할 수 있어야 합니다.
   >
   > * vCenter에 로그인할 때 사용한 것과 동일한 암호를 사용합니다. 초기 배포 화면에서 이 암호를 정의했습니다.

   Azure VMware Solution의 VMware HCX Cloud Manager와 온-프레미스 VMware HCX Connector가 연결(페어링)된 것을 보여주는 화면이 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Azure VMware Solution의 HCX Manager와 VMware HCX Connector의 페어링을 보여주는 스크린샷.":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 사이트 페어링](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720) 비디오를 시청하세요.

### <a name="create-network-profiles"></a>네트워크 프로필 만들기

VMware HCX Connector는 여러 IP 세그먼트가 필요한 가상 어플라이언스의 하위 집합을 배포합니다(자동화됨). 네트워크 프로필을 만들 때 [VMware HCX 네트워크 세그먼트 배포 전 준비 및 계획 단계](production-ready-deployment-steps.md#vmware-hcx-network-segments)에서 식별한 IP 세그먼트를 사용합니다.

다음과 같은 4개의 네트워크 프로필을 만듭니다.

   - 관리
   - vMotion
   - 복제
   - 업링크

1. **인프라** 에서 **상호 연결** > **다중 사이트 서비스 메시** > **네트워크 프로필** > **네트워크 프로필 만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="네트워크 프로필 만들기를 시작하기 위한 선택 항목의 스크린샷" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 각 네트워크 프로필에 대해 네트워크 및 포트 그룹을 선택하고, 이름을 제공하고, 세그먼트의 IP 풀을 만듭니다. 그런 다음 **만들기** 를 선택합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="새 네트워크 프로필에 대한 세부 정보의 스크린샷":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 네트워크 프로필](https://www.youtube.com/embed/O0rU4jtXUxc) 비디오를 시청하세요.


### <a name="create-a-compute-profile"></a>컴퓨팅 프로필 만들기

1. **인프라** 아래에서 **상호 연결** > **컴퓨팅 프로필** > **컴퓨팅 프로필 만들기** 를 차례로 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="컴퓨팅 프로필 만들기를 시작하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. 프로필 이름을 입력하고 **계속** 을 선택합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="컴퓨팅 프로필 이름의 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 마이그레이션, 네트워크 확장, 재해 복구 등 사용하도록 설정할 서비스를 선택한 다음, **계속** 을 선택합니다.
  
   > [!NOTE]
   > 일반적으로 여기서는 아무 것도 변경되지 않습니다.

1. **서비스 리소스 선택** 에서, 선택한 VMware HCX 서비스에 사용할 서비스 리소스(클러스터)를 하나 이상 선택합니다.  

1. 온-프레미스 데이터 센터에 클러스터가 표시되면 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="선택한 서비스 리소스와 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **데이터 저장소 선택** 에서, VMware HCX 상호 연결 어플라이언스를 배포할 데이터 저장소 스토리지 리소스를 선택합니다. 그런 다음, **계속** 을 선택합니다.

   여러 리소스를 선택하는 경우 VMware HCX는 해당 용량을 모두 사용할 때까지 선택된 첫 번째 리소스를 사용합니다.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="선택한 데이터 스토리지 리소스와 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. **관리 네트워크 프로필** 선택에서, 이전 단계에서 만든 관리 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="관리 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. **업링크 네트워크 프로필 선택** 에서 이전 절차에서 만든 업링크 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="업링크 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **vMotion 네트워크 프로필 선택** 에서, 이전 단계에서 만든 vMotion 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="vMotion 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **vSphere 복제 네트워크 프로필 선택** 에서, 이전 단계에서 만든 복제 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="복제 네트워크 프로필의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **네트워크 확장 분산 스위치 선택** 에서 계층 2 확장 네트워크의 Azure VMware Solution으로 마이그레이션할 가상 머신이 포함된 스위치를 선택합니다. 그런 다음, **계속** 을 선택합니다.

   > [!NOTE]
   > 계층 2 확장 네트워크에서 가상 머신을 마이그레이션하지 않는 경우 이 단계를 건너뛸 수 있습니다.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="분산 가상 스위치의 선택 항목과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 연결 규칙을 검토하고 **계속** 을 선택합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="연결 규칙과 [계속] 단추를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. **마침** 을 선택하여 컴퓨팅 프로필을 만듭니다.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="컴퓨팅 프로필 정보를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 컴퓨팅 프로필](https://www.youtube.com/embed/e02hsChI3b8) 비디오를 시청하세요.

### <a name="create-a-service-mesh"></a>서비스 메시 만들기

이제 온-프레미스와 Azure VMware Solution 프라이빗 클라우드 간에 서비스 메시를 구성해야 합니다.



> [!NOTE]
> Azure VMware Solution을 사용하여 서비스 메시를 성공적으로 설정하려면 다음을 수행합니다.
>
> * 포트 UDP 500/4500은 온-프레미스 VMware HCX Connector '업링크' 네트워크 프로필 주소와 Azure VMware Solution HCX Cloud '업링크' 네트워크 프로필 주소 사이에서 열립니다.
>
> * [VMware HCX 필수 포트](https://ports.vmware.com/home/VMware-HCX)를 검토하세요.

1. **인프라** 에서 **상호 연결** > **서비스 메시** > **서비스 메시 만들기** 를 선택합니다.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="서비스 메시 만들기를 시작하기 위한 선택 항목의 스크린샷" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 미리 채워진 사이트를 검토한 다음, **계속** 을 선택합니다. 

   > [!NOTE]
   > 이번이 첫 번째 서비스 메시 구성인 경우 이 화면을 수정할 필요가 없습니다.  

1. 드롭다운 목록에서 원본 및 원격 컴퓨팅 프로필을 선택한 다음, **계속** 을 선택합니다.  

   선택하는 항목에 따라 VM이 VMware HCX 서비스를 사용할 수 있는 리소스가 정의됩니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="원본 컴퓨팅 프로필을 선택하는 것을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="원격 컴퓨팅 프로필을 선택하는 것을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 사용할 서비스를 검토한 다음, **계속** 을 선택합니다.  

1. **고급 구성 - 업링크 네트워크 프로필 재정의** 에서 **계속** 을 선택합니다.  

   업링크 네트워크 프로필은 원격 사이트의 상호 연결 어플라이언스에 연결할 수 있는 네트워크를 통해 연결됩니다.  
  
1. **고급 구성 - 네트워크 확장 어플라이언스 스케일 아웃** 을 검토하고 **계속** 을 선택합니다. 

   어플라이언스당 최대 8개의 VLAN을 가질 수 있지만 다른 어플라이언스를 배포하여 8개의 VLAN을 추가할 수 있습니다. 또한 추가 어플라이언스를 고려할 수 있는 IP 공간이 있어야 하며, 어플라이언스당 하나의 IP입니다.  자세한 내용은 [VMware HCX 구성 제한](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)을 참조하세요.
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="VLAN 수를 늘릴 위치를 보여주는 스크린샷." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. **고급 구성 - 트래픽 엔지니어링** 을 검토하고 필요한 대로 수정한 다음, **계속** 를 선택합니다.

1. 토폴로지 미리 보기를 검토하고 **계속** 을 선택합니다.

1. 이 서비스 메시에 사용할 사용자에게 친숙한 이름을 입력하고 **완료** 를 선택하여 마칩니다.  

1. **작업 보기** 를 선택하여 배포를 모니터링합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="작업을 살펴보는 단추를 보여주는 스크린샷.":::

   서비스 메시 배포가 성공적으로 완료되면 서비스가 녹색으로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="서비스의 녹색 표시기를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 어플라이언스 상태를 검사하여 서비스 메시의 상태를 확인합니다. 

1. **상호 연결** > **어플라이언스** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="어플라이언스의 상태를 확인하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 서비스 메시](https://www.youtube.com/embed/COY3oIws108) 비디오를 시청하세요.

### <a name="optional-create-a-network-extension"></a>(선택 사항) 네트워크 확장 만들기

온-프레미스 환경에서 Azure VMware Solution으로 네트워크를 확장하려면 다음 단계를 수행합니다.

1. **서비스** 아래에서 **네트워크 확장** > **네트워크 확장 만들기** 를 차례로 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="네트워크 확장 만들기를 시작하기 위한 선택 항목을 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution으로 확장하려는 각 네트워크를 선택하고 **다음** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="네트워크 선택 항목을 보여주는 스크린샷":::

1. 확장하려는 각 네트워크의 온-프레미스 게이트웨이 IP를 입력한 다음, **제출** 을 선택합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="게이트웨이 IP 주소의 항목을 보여주는 스크린샷":::

   네트워크 확장이 완료될 때까지 몇 분 정도 걸립니다. 확장이 완료되면 상태가 **확장 완료** 로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="[확장 완료] 상태를 보여주는 스크린샷" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 네트워크 확장](https://www.youtube.com/embed/gYR0nftKui0) 비디오를 시청하세요.


## <a name="next-steps"></a>다음 단계

HCX 상호 연결 터널 상태가 **가동 중** 이고 녹색인 경우 VMware HCX를 사용하여 Azure VMware Solution VM을 마이그레이션하고 보호할 수 있습니다. Azure VMware Solution은 (네트워크 확장을 포함한 또는 포함하지 않은) 워크로드 마이그레이션을 지원합니다. vSphere 환경에서 워크로드를 계속 마이그레이션할 수 있으며, 온-프레미스에 네트워크를 만들고 해당 네트워크에 VM을 배포할 수 있습니다.  

HCX 사용에 대한 자세한 내용은 다음 VMware 기술 문서를 참조하세요.

* [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html)
* [VMware HCX를 사용하여 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX 필수 포트](https://ports.vmware.com/home/VMware-HCX)
