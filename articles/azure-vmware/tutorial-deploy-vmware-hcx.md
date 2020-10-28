---
title: 자습서 - VMware HCX 배포 및 구성
description: Azure VMware Solution 프라이빗 클라우드를 위한 VMware HCX 솔루션을 배포 및 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: c78eae11497702054bb54b5980228fd0a3962577
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367774"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX 배포 및 구성

이 문서에서는 Azure VMware Solution 프라이빗 클라우드에 대한 온-프레미스 VMware HCX 커넥터를 배포하고 구성하는 절차를 안내합니다. VMware HCX를 사용하면 다양한 마이그레이션 유형을 통해 VMware 워크로드를 Azure VMware Solution 및 기타 연결된 사이트로 마이그레이션할 수 있습니다. Azure VMware Solution은 HCX Cloud Manager를 배포하고 구성하므로 온-프레미스 VMware 데이터 센터에서 HCX 커넥터를 다운로드, 활성화 및 구성해야 합니다.

VMware HCX 고급 커넥터는 Azure VMware Solution에 미리 배포되어 있습니다. 이 커넥터는 최대 3개의 사이트 연결(온-프레미스에서 클라우드로 연결 또는 클라우드에서 클라우드로 연결)을 지원합니다. 사이트 연결 4개 이상 필요한 경우 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 추가 기능을 사용할 수 있도록 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)을 제출하세요. 추가 기능은 현재 미리 보기에 있습니다. 

>[!NOTE]
>VMware HCX EE(Enterprise Edition)는 Azure VMware Solution에서 미리 보기 서비스로 제공됩니다. 무료이며 미리 보기 서비스의 약관이 적용됩니다. VMware HCX EE 서비스가 일반 공급으로 전환되면 요금 청구 방식이 변경된다는 내용의 알림을 30일 전에 받게 됩니다. 서비스를 끄거나 옵트아웃하는 옵션도 제공됩니다.

먼저 이 문서의 [시작하기 전에](#before-you-begin), [소프트웨어 버전 요구 사항](#software-version-requirements) 및 [필수 구성 요소](#prerequisites) 섹션을 철저하게 검토하세요. 

그런 다음, 필요한 모든 절차를 진행하겠습니다.

> [!div class="checklist"]
> * 온-프레미스 VMware HCX OVA(HCX 커넥터) 배포
> * VMware HCX 커넥터 활성화
> * 온-프레미스 HCX 커넥터를 Azure VMware Solution HCX Cloud Manager와 페어링
> * 상호 연결 구성(네트워크 프로필, 컴퓨팅 프로필 및 서비스 메시)
> * 어플라이언스 상태를 확인하고 마이그레이션이 가능한지 확인하여 설정 완료

모두 마쳤으면 이 문서 마지막 부분의 권장하는 다음 단계를 수행합니다.  

## <a name="before-you-begin"></a>시작하기 전에
   
* 기본 Azure VMware Solution SDDC(소프트웨어 정의 데이터 센터) [자습서 시리즈](tutorial-network-checklist.md)를 검토합니다.
* HCX 사용자 가이드를 포함한 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html)를 검토 및 참조합니다.
* VMware 설명서의 [VMware HCX로 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)을 검토합니다.
* 선택 사항으로 [VMware HCX 배포 고려 사항](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)을 검토합니다.
* 필요에 따라 VMware vSphere [블로그 시리즈](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)와 같은 HCX의 관련 VMware 자료를 검토합니다. 
* 필요에 따라 Azure VMware Solution 지원 채널을 통해 Azure VMware Solution HCX Enterprise 활성화를 요청합니다.
* 필요에 따라 [HCX에 필요한 네트워크 포트를 검토](https://ports.vmware.com/home/VMware-HCX)합니다.
* Azure VMware Solution HCX Cloud Manager는 Azure VMware Solution 프라이빗 클라우드에 제공되는 /22 네트워크에서 미리 구성된 상태로 제공되지만, 온-프레미스 HCX 커넥터를 사용하려면 온-프레미스 네트워크에서 네트워크 범위를 할당해야 합니다. 이러한 네트워크 및 범위는 이 문서의 뒷부분에서 설명합니다.

컴퓨팅 및 스토리지 리소스에 대한 워크로드 크기 조정은 필수 계획 단계입니다. 크기 조정 단계는 초기 프라이빗 클라우드 환경 계획의 일부로 다루어야 합니다. 

Azure Migrate 포털에서 [Azure VMware Solution 평가](../migrate/how-to-create-azure-vmware-solution-assessment.md)를 완료하여 워크로드의 크기를 조정할 수 있습니다.

## <a name="software-version-requirements"></a>소프트웨어 버전 요구 사항

인프라 구성 요소는 필요한 최소 버전을 실행해야 합니다. 
                                                         
| 구성 요소 유형    | 원본 환경 요구 사항    | 대상 환경 요구 사항   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>5\.5 U1 또는 이전 버전을 사용하는 경우 HCX 작업에 독립 실행형 HCX 사용자 인터페이스를 사용합니다.  | 6.0 U2 이상   |
| ESXi   | 5.0    | ESXi 6.0 이상   |
| NSX    | 원본에서 논리 스위치의 HCX 네트워크 확장을 사용하는 경우: NSXv 6.2 이상 또는 NSX-T 2.4 이상   | NSXv 6.2+ 또는 NSX-T 2.4+<br/><br/>HCX 근접 라우팅의 경우: NSXv 6.4 이상 (근접 라우팅은 NSX-T에서 지원되지 않음) |
| vCloud Director   | 필수 아님. 원본 사이트에서 vCloud Director와 상호 운용성이 없습니다. | 대상 환경을 vCloud Director와 통합하는 경우 최솟값은 9.1.0.2입니다.  |

## <a name="prerequisites"></a>사전 요구 사항

### <a name="network-and-ports"></a>네트워크 및 포트

* 온-프레미스와 Azure VMware Solution SDDC ExpressRoute 회로 간에 [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)를 구성합니다.

* 온-프레미스 구성 요소와 Azure VMware Solution SDDC 간에 통신이 가능하려면 [필요한 모든 포트](https://ports.vmware.com/home/VMware-HCX)가 열려 있어야 합니다.

자세한 내용은 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)를 참조하세요.


### <a name="ip-addresses"></a>IP 주소

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>VMware HCX 커넥터 OVA 온-프레미스 배포

> [!NOTE]
> 가상 어플라이언스를 온-프레미스 vCenter에 배포하려면 먼저 VMware HCX 커넥터 OVA를 다운로드해야 합니다. 

1. 브라우저 창을 열고, **cloudadmin** 사용자 자격 증명을 사용하여 `https://x.x.x.9` 443 포트에서 Azure VMware Solution HCX Manager에 로그인한 다음, **지원** 으로 이동합니다.

   > [!TIP]
   > Azure VMware Solution에서 HCX Cloud Manager의 IP를 적어 둡니다. IP 주소를 확인하려면 Azure VMware Solution 창에서 **관리** > **연결** 로 이동한 다음, **HCX** 탭을 선택합니다. 
   >
   >vCenter 암호는 프라이빗 클라우드를 설정할 때 정의되었습니다.

1. **다운로드** 링크를 선택하여 VMware HCX 커넥터 OVA 파일을 다운로드합니다.

1. 온-프레미스 vCenter로 이동합니다. OVF 템플릿(다운로드한 OVA 파일)을 선택하여 HCX 커넥터를 온-프레미스 vCenter에 배포합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 이름 및 위치를 선택하고, HCX 커넥터를 배포할 리소스/클러스터를 선택합니다. 그런 다음, 세부 정보 및 필요한 리소스를 검토합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. 사용 조건을 검토합니다. 사용 조건에 동의하는 경우 필요한 스토리지 및 네트워크를 선택하고 **다음** 을 선택합니다.

1. **템플릿사용자 지정** 에서 필요한 모든 정보를 입력합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. **다음** 을 선택하고 구성을 확인한 다음, **마침** 을 선택하여 HCX 커넥터 OVA를 배포합니다.
     
   > [!NOTE]
   > 일반적으로 지금 배포하는 VMware HCX 커넥터는 클러스터의 관리 네트워크에 배포됩니다.  
   
   > [!IMPORTANT]
   > 가상 어플라이언스를 수동으로 켜야 할 수도 있습니다.  이 경우 10~15분 정도 기다린 후 다음 단계를 진행하세요.

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 어플라이언스 배포](https://www.youtube.com/embed/BwSnQeefnso) 비디오를 시청하세요. 


## <a name="activate-vmware-hcx"></a>VMware HCX 활성화

VMware HCX 커넥터 OVA 온-프레미스를 배포하고 어플라이언스를 시작했으면 활성화 준비가 완료된 것입니다. 먼저 Azure VMware Solution 포털에서 라이선스 키를 받아야 합니다.

1. Azure VMware Solution 포털에서 **관리** > **연결** 로 이동하고, **HCX** 탭을 선택한 다음, **추가** 를 선택합니다.

1. `https://HCXManagerIP:9443`에서 **관리자** 자격 증명을 사용하여 온-프레미스 VMware HCX Manager에 로그인합니다. 

   > [!IMPORTANT]
   > VMware HCX Manager IP 주소에 `9443` 포트 번호를 포함해야 합니다.

1. **라이선스** 에서 **HCX 고급 키** 를 입력합니다.  
   
    > [!NOTE]
    > VMware HCX Manager는 인터넷 액세스가 가능하거나 프록시가 구성되어 있어야 합니다.

1. **데이터 센터 위치** 에서, VMware HCX Manager를 온-프레미스에 설치할 가장 가까운 위치를 입력합니다.

1. **시스템 이름** 에서 이름을 수정하거나 기본값을 적용합니다.
   
1. **예, 계속** 을 선택합니다.
    
1. **vCenter에 연결** 에서, vCenter 서버의 FQDN 또는 IP 주소와 적절한 자격 증명을 입력한 다음, **계속** 을 선택합니다.
   
1. **SSO/PSC 구성** 에서, Platform Services Controller의 FQDN 또는 IP 주소를 입력한 다음, **계속** 을 선택합니다.
   
   >[!NOTE]
   >일반적으로 이 항목은 vCenter FQDN 또는 IP 주소와 동일합니다.

1. 모든 입력이 올바른지 확인하고 **다시 시작** 을 선택합니다.
    
   > [!NOTE]
   > 다시 시작한 후 다음 단계를 진행하는 메시지가 표시될 때까지 약간 시간이 걸립니다.

서비스가 다시 시작된 후 표시되는 화면에 vCenter가 녹색으로 표시되어야 합니다. vCenter와 SSO 모두 이전 화면과 동일하게 적절한 구성 매개 변수를 포함하고 있어야 합니다.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 활성화](https://www.youtube.com/embed/BkAV_TNYxdE) 비디오를 시청하세요.


## <a name="configure-the-vmware-hcx-connector"></a>VMware HCX 커넥터 구성

이제 사이트 페어링을 추가하고, 네트워크 및 컴퓨팅 프로필을 만들고, 마이그레이션, 네트워크 확장, 재해 복구 등의 서비스를 사용할 준비가 되었습니다. 

### <a name="add-a-site-pairing"></a>사이트 페어링 추가

Azure VMware Solution의 VMware HCX 커넥터를 데이터 센터의 VMware HCX 커넥터와 연결(페어링)할 수 있습니다. 

1. 온-프레미스 vCenter에 로그인하고, **홈** 에서 **HCX** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. **인프라** 에서 **사이트 페어링** 을 선택한 다음, **원격 사이트에 연결** 옵션(화면 가운데)을 선택합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. 앞에서 적어 둔 원격 HCX URL 또는 IP 주소, Azure VMware Solution cloudadmin@vsphere.local 사용자 이름 및 암호를 입력합니다. 그런 다음 **연결** 을 선택합니다.

   > [!NOTE]
   > 사이트 페어링을 성공적으로 설정하려면 HCX 커넥터가 443 포트를 통해 HCX Cloud Manager IP로 라우팅할 수 있어야 합니다.
   >
   > 암호는 vCenter에 로그인할 때 사용하는 암호와 동일합니다. 초기 배포 화면에서 이 암호를 정의했습니다.

   Azure VMware Solution의 HCX Cloud Manager와 온-프레미스 HCX 커넥터가 연결(페어링)된 것을 보여주는 화면이 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="OVF 템플릿을 탐색하는 스크린샷":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 사이트 페어링](https://www.youtube.com/embed/sKizDCRHOko) 비디오를 시청하세요.



### <a name="create-network-profiles"></a>네트워크 프로필 만들기

VMware HCX는 여러 IP 세그먼트가 필요한 가상 어플라이언스의 하위 집합을 배포합니다(자동화됨). 네트워크 프로필을 만들 때 [VMware HCX 네트워크 세그먼트 배포 전 준비 및 계획 단계](production-ready-deployment-steps.md#vmware-hcx-network-segments)에서 확인한 IP 세그먼트를 정의합니다.

다음과 같은 4개의 네트워크 프로필을 만듭니다.

   - 관리
   - vMotion
   - 복제
   - 업링크

1. **인프라** 에서 **상호 연결** > **다중 사이트 서비스 메시** > **네트워크 프로필** > **네트워크 프로필 만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 네트워크 프로필마다 네트워크 및 포트 그룹을 선택하고, 이름을 입력하고, 해당 세그먼트에 대한 IP 풀을 만듭니다. 그런 다음 **만들기** 를 선택합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="OVF 템플릿을 탐색하는 스크린샷":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: HCX 네트워크 프로필](https://www.youtube.com/embed/NhyEcLco4JY) 비디오를 시청하세요.


### <a name="create-a-compute-profile"></a>컴퓨팅 프로필 만들기

1. **컴퓨팅 프로필** > **컴퓨팅 프로필 만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. 프로필 이름을 입력하고 **계속** 을 선택합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 마이그레이션, 네트워크 확장, 재해 복구 등 사용하도록 설정할 서비스를 선택한 다음, **계속** 을 선택합니다.
  
   > [!NOTE]
   > 일반적으로 여기서는 아무 것도 변경되지 않습니다.

1. **서비스 리소스 선택** 에서, 선택한 VMware HCX 서비스에 사용할 서비스 리소스(클러스터)를 하나 이상 선택합니다.  

1. 온-프레미스 데이터 센터에 클러스터가 표시되면 **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **데이터 저장소 선택** 에서, VMware HCX 상호 연결 어플라이언스를 배포할 데이터 저장소 스토리지 리소스를 선택합니다. 그런 다음, **계속** 을 선택합니다.

   여러 리소스를 선택하는 경우 VMware HCX는 해당 용량을 모두 사용할 때까지 선택된 첫 번째 리소스를 사용합니다.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. **관리 네트워크 프로필** 선택에서, 이전 단계에서 만든 관리 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > 관리 네트워크 프로필을 사용하면 VMware HCX 어플라이언스가 vCenter와 통신할 수 있습니다. 이 프로필을 통해 ESXi 호스트에 연결할 수 있습니다.

1. **업링크 네트워크 프로필 선택** 에서, 이전 단계에서 만든 업링크 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **vMotion 네트워크 프로필 선택** 에서, 이전 단계에서 만든 vMotion 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **vSphere 복제 네트워크 프로필 선택** 에서, 이전 단계에서 만든 복제 네트워크 프로필을 선택합니다. 그런 다음, **계속** 을 선택합니다.

   대부분의 경우 이 복제 네트워크 프로필은 관리 네트워크 프로필과 동일합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **네트워크 확장 분산 스위치 선택** 에서, 계층 2 확장 네트워크의 Azure VMware Solution으로 마이그레이션할 가상 머신을 포함하고 있는 분산 가상 스위치를 선택합니다. 그런 다음, **계속** 을 선택합니다.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 연결 규칙을 검토하고 **계속** 을 선택합니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. **마침** 을 선택하여 컴퓨팅 프로필을 만듭니다.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 컴퓨팅 프로필](https://www.youtube.com/embed/qASXi5xrFzM) 비디오를 시청하세요.

### <a name="create-a-service-mesh"></a>서비스 메시 만들기

이제 온-프레미스와 Azure VMware Solution SDDC 간에 서비스 메시를 구성해야 합니다.

   > [!NOTE]
   > Azure VMware Solution을 사용하여 서비스 메시를 성공적으로 설정하려면 다음을 수행합니다.
   >
   > 포트 UDP 500/4500은 온-프레미스 HCX 커넥터 정의 '업링크' 네트워크 프로필 주소와 Azure VMware Solution HCX Cloud '업링크' 네트워크 프로필 주소 사이에서 열립니다.
   >
   > [HCX 필수 포트](https://ports.vmware.com/home/VMware-HCX)를 검토하세요.

1. **인프라** 에서 **상호 연결** > **서비스 메시** > **서비스 메시 만들기** 를 선택합니다.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 미리 채워진 사이트를 검토한 다음, **계속** 을 선택합니다. 

   >[!NOTE]
   >이번이 첫 번째 서비스 메시 구성인 경우 이 화면을 수정할 필요가 없습니다.  

1. 드롭다운 목록에서 원본 및 원격 컴퓨팅 프로필을 선택한 다음, **계속** 을 선택합니다.  

   선택하는 항목에 따라 VM이 VMware HCX 서비스를 사용할 수 있는 리소스가 정의됩니다.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 사용할 서비스를 검토한 다음, **계속** 을 선택합니다.  

1. **고급 구성 - 업링크 네트워크 프로필 재정의** 에서 **계속** 을 선택합니다.  

   업링크 네트워크 프로필은 원격 사이트의 상호 연결 어플라이언스에 연결할 수 있는 네트워크를 통해 연결됩니다.  
  
1. **고급 구성 - 네트워크 확장 어플라이언스 스케일 아웃** 을 검토하고 **계속** 을 선택합니다. 

1. **고급 구성 - 트래픽 엔지니어링** 을 검토하고 필요한 대로 수정한 다음, **계속** 를 선택합니다.

1. 토폴로지 미리 보기를 검토하고 **계속** 을 선택합니다.

1. 이 서비스 메시에 사용할 사용자에게 친숙한 이름을 입력하고 **완료** 를 선택하여 마칩니다.  

1. **작업 보기** 를 선택하여 배포를 모니터링합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="OVF 템플릿을 탐색하는 스크린샷":::

   서비스 메시 배포가 성공적으로 완료되면 서비스가 녹색으로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 어플라이언스 상태를 검사하여 서비스 메시의 상태를 확인합니다. 
1. **상호 연결** > **어플라이언스** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

이 절차의 엔드투엔드 개요는 [Azure VMware Solution: 서비스 메시](https://www.youtube.com/embed/FyZ0d3P_T24) 비디오를 시청하세요.



### <a name="optional-create-a-network-extension"></a>(선택 사항) 네트워크 확장 만들기

온-프레미스 환경에서 Azure VMware Solution으로 네트워크를 확장하려면 다음 단계를 수행합니다.

1. **서비스** 에서 **네트워크 확장** 을 선택한 다음, **네트워크 확장 만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution으로 확장하려는 각 네트워크를 선택하고 **다음** 을 선택합니다.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="OVF 템플릿을 탐색하는 스크린샷":::

1. 확장하려는 각 네트워크의 온-프레미스 게이트웨이 IP를 입력한 다음, **제출** 을 선택합니다. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="OVF 템플릿을 탐색하는 스크린샷":::

   네트워크 확장이 완료될 때까지 몇 분 정도 걸립니다. 확장이 완료되면 상태가 **확장 완료** 로 표시됩니다.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="OVF 템플릿을 탐색하는 스크린샷" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

이 단계의 엔드투엔드 개요는 [Azure VMware Solution: 네트워크 확장](https://www.youtube.com/embed/cNlp0f_tTr0) 비디오를 시청하세요.


## <a name="next-steps"></a>다음 단계

지금까지 잘 따라 해서 어플라이언스 상호 연결 터널 상태가 **가동 중** 이고 녹색이면 이제 VMware HCX를 사용하여 Azure VMware Solution VM을 마이그레이션하고 보호할 수 있습니다. Azure VMware Solution은 (네트워크 확장을 포함한 또는 포함하지 않은) 워크로드 마이그레이션을 지원합니다. vSphere 환경에서 워크로드를 계속 마이그레이션할 수 있으며, 온-프레미스에 네트워크를 만들고 해당 네트워크에 VM을 배포할 수 있습니다.  

HCX 사용에 대한 자세한 내용은 다음 VMware 기술 문서를 참조하세요.

* [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html)
* [VMware HCX로 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX 필수 포트](https://ports.vmware.com/home/VMware-HCX)
