---
title: HCX(하이브리드 클라우드 확장) 설치
description: AVS(Azure VMware 솔루션) 프라이빗 클라우드에 대한 VMware HCX(하이브리드 클라우드 확장) 솔루션 설정
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: dc5f7f82b83c82538b2d5a7b4c87131afb3fcc20
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873658"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Azure VMware 솔루션용 HCX 설치

이 문서에서는 AVS(Azure VMware 솔루션) 프라이빗 클라우드에 대해 VMware HCX(하이브리드 클라우드 확장) 솔루션을 설정하는 절차를 진행합니다. HCX Advanced(기본 설치)는 최대 3개의 외부 사이트를 지원하며, 각 외부 사이트에서 HCX Enterprise 관리자 또는 커넥터를 설치하고 활성화해야 합니다.
HCX를 사용하면 다양한 기본 제공 HCX 지원 마이그레이션 유형을 통해 VMware 워크로드를 클라우드 및 연결된 다른 사이트로 마이그레이션할 수 있습니다. 4개 이상의 사이트가 필요한 경우 고객은 지원을 통해 HCX Enterprise 추가 기능을 사용하도록 설정할 수 있습니다. HCX Enterprise는 GA(일반 공급) 후 [추가 기능](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)도 제공합니다. 단, 고객에게 추가 요금이 발생합니다.

[시작하기 전에](#before-you-begin), [소프트웨어 버전 요구 사항](#software-version-requirements) 및 [필수 구성 요소](#prerequisites)를 철저하게 검토합니다. 

그런 다음, 모든 필수 구성 요소를 진행합니다.

> [!div class="checklist"]
> * 온-프레미스 HCX OVA 배포
> * HCX 활성화 및 구성
> * 네트워크 업링크 및 서비스 메시 구성
> * 어플라이언스 상태를 확인하여 설치 완료

설치를 완료한 후 권장되는 다음 단계가 제공됩니다.

## <a name="before-you-begin"></a>시작하기 전에
    
* 기본 AVS SDDC(소프트웨어 정의 데이터 센터) [자습서 시리즈](tutorial-network-checklist.md) 검토
* HCX 사용자 가이드를 포함한 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html) 검토 및 참조
* VMware 설명서 [VMware HCX로 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) 검토
* 선택적으로 [VMware HCX 배포 고려 사항](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html) 검토
* 필요에 따라 HCX의 VMware vSphere [블로그 시리즈](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)와 같은 HCX의 관련 VMware 자료 검토 
* AVS 지원 채널을 통해 AVS HCX Enterprise 활성화 주문

컴퓨팅 및 스토리지 리소스에 대한 워크로드 크기 조정은 AVS 프라이빗 클라우드 HCX 솔루션을 사용하기 위한 준비를 할 때 필수적인 계획 단계입니다. 이 크기 조정 단계는 초기 프라이빗 클라우드 환경 계획의 일부로 다루어야 합니다. 

## <a name="software-version-requirements"></a>소프트웨어 버전 요구 사항
인프라 구성 요소는 필요한 최소 버전을 실행해야 합니다. 
                                                         
| 구성 요소 유형                                                          | 원본 환경 요구 사항                                                                   | 대상 환경 요구 사항                                                                      |
| --- | --- | --- |
| vCenter Server                                                          | 5.1<br/><br/>5\.5 U1 또는 이전 버전을 사용하는 경우 HCX 작업에 독립 실행형 HCX 사용자 인터페이스를 사용합니다.         | 6.0 U2 이상                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6.0 이상                                                                                        |
| NSX                                                                     | 원본에서 논리 스위치의 HCX 네트워크 확장의 경우: NSXv 6.2+ 또는 NSX-T 2.4+              | NSXv 6.2+ 또는 NSX-T 2.4+<br/>HCX 근접 라이팅의 경우: NSXv 6.4+(근접 라우팅은 NSX-T에서 지원되지 않음) |
| vCloud Director                                                         | 필요하지 않음 - 원본 사이트에서 vCloud Director와의 상호 운용성 없음 | 대상 환경이 vCloud Director와 통합된 경우 최솟값은 9.1.0.2입니다.              |

## <a name="prerequisites"></a>사전 요구 사항

* 글로벌 접근은 온-프레미스와 AVS SDDC ER 회로 간에 구성되어야 합니다.

* 온-프레미스와 AVS SDDC 사이에 필요한 모든 포트가 열려 있어야 합니다([VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)참조).

* 온-프레미스에서 HCX Manager용 IP 주소 1개와 상호 연결(IX) 및 NE(네트워크 확장) 어플라이언스에 대한 IP 주소 최소 2개.

* 온-프레미스 HCX IX 및 NE 어플라이언스는 vCenter 및 ESXi 인프라에 도달할 수 있어야 합니다.

* WAN 상호 연결 어플라이언스를 배포하려면 Azure Portal에서 SDDC 배포에 사용되는 /22 CIDR 네트워크 주소 블록 외에도 HCX에는 /29 블록이 필요합니다. 이 점을 네트워크 계획에 고려해야 합니다.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>VMware HCX OVA 온-프레미스 배포

1. AVS SDDC vCenter에 로그인하고 **HCX**를 선택합니다.

    ![AVS vCenter에서 HCX를 선택합니다.](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. VMware HCX OVA 파일을 다운로드하려면 **관리** > **시스템 업데이트**를 선택합니다.

    ![시스템 업데이트 가져오기](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. 온-프레미스 vCenter에 배포할 OVF 템플릿을 선택합니다.  

    ![OVF 템플릿 선택](./media/hybrid-cloud-extension-installation/select-template.png)

1. 이름 및 위치를 선택한 다음, HCX를 배포해야 하는 리소스/클러스터를 선택합니다. 그런 다음, 세부 정보 및 필요한 리소스를 검토합니다.  

    ![템플릿 세부 정보 검토](./media/hybrid-cloud-extension-installation/configure-template.png)

1. 사용 조건을 검토하고 동의하는 경우 필요한 스토리지 및 네트워크를 선택합니다. 그런 후 **다음**을 선택합니다.

1. **템플릿사용자 지정**에서 필요한 모든 정보를 입력합니다. 

    ![템플릿 사용자 지정](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. **다음**을 선택하고, 구성을 확인한 후, **마침**을 선택하여 HCX OVA를 배포합니다.

## <a name="activate-hcx"></a>HCX 활성화

설치 후에 다음 단계를 수행합니다.

1. `https://HCXManagerIP:9443`에서 HCX Manager를 열고 사용자 이름 및 암호를 사용하여 로그인합니다. 

1. **라이선스**에서 **HCX 고급 키**를 입력합니다.  

    ![HCX 키 입력](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager는 인터넷 액세스가 가능하거나 프록시가 구성되어 있어야 합니다.

1. vCenter를 구성합니다.

    ![vCenter 구성](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. **데이터 센터 위치**에서 필요한 경우 데이터 센터 위치를 편집합니다.

    ![데이터베이스 위치](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>HCX 구성 

1. 온-프레미스 vCenter에 로그인한 다음, **홈** > **HCX**를 선택합니다.

    ![vCenter의 HCX](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. **인프라** > **사이트 페어링** > **사이트 페어링 추가**를 선택합니다.

    ![사이트 페어링 추가](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. **원격 HCX URL**, **사용자 이름** 및 **암호**를 입력합니다. 그런 다음 **연결**을 선택합니다.

   시스템에서 연결된 사이트가 표시됩니다.
   
    ![사이트 연결](./media/hybrid-cloud-extension-installation/site-connection.png)

1. **상호 연결** > **다중 사이트 서비스 메시** > **네트워크 프로필** > **네트워크 프로필 만들기**를 선택합니다.

    ![네트워크 프로필 만들기](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. HCX IX 및 NE IP 주소 범위를 입력합니다(IX 및 NE 어플라이언스에는 최소 2개의 IP 주소가 필요함).
    
   ![IP 주소 범위 입력](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > 네트워크 확장 어플라이언스(HCX-NE)에는 DVS(분산 가상 스위치)와 일 대 일 관계가 있습니다.  

1. 이제 **컴퓨팅 프로필** > **컴퓨팅 프로필 만들기**를 선택합니다.

1. 컴퓨팅 프로필 이름을 입력하고 **계속**을 선택합니다.  

    ![컴퓨팅 프로필 만들기](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. 마이그레이션, 네트워크 확장 또는 재해 복구 등 사용하도록 설정할 서비스를 선택합니다. **계속**을 선택합니다.

    ![서비스 선택](./media/hybrid-cloud-extension-installation/select-services.png)

1. **서비스 리소스 선택**에서 선택한 HCX 서비스를 사용하도록 설정해야 하는 서비스 리소스를 하나 이상 선택합니다. **계속**을 선택합니다.
    
   ![서비스 리소스 선택](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > HCX를 사용하여 마이그레이션을 위해 대상으로 지정하는 원본 VM의 특정 클러스터를 선택합니다.

1. **Datastore**, **계속**을 차례로 선택합니다. 
      
    HCX 상호 연결 어플라이언스를 배포하기 위한 각 컴퓨팅 및 스토리지 리소스를 선택합니다. 여러 리소스를 선택하는 경우 HCX는 해당 용량을 모두 사용할 때까지 선택된 첫 번째 리소스를 사용합니다.  
    
    ![배포 리소스 선택](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. **네트워크 프로필**에서 만든 관리 네트워크 프로필을 선택하고 **계속**을 선택합니다.  
      
    vCenter 및 ESXi 호스트의 관리 인터페이스에 연결할 수 있는 네트워크 프로필을 선택합니다. 이러한 네트워크 프로필을 아직 정의하지 않은 경우 여기에서 만들 수 있습니다.  
    
    ![관리 네트워크 프로필 선택](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. **네트워크 업링크**를 선택하고 **계속**을 선택합니다.
      
    다음 중 하나에 해당하는 네트워크 프로필을 하나 이상 선택합니다.  
    * 원격 사이트의 상호 연결 어플라이언스를 이 네트워크를 통해 연결할 수 있습니다.  
    * 원격 측 어플라이언스를 이 네트워크를 통해 로컬 상호 작용 어플라이언스에 연결할 수 있습니다.  
    
    여러 사이트에서 공유되지 않는 지점 간 네트워크(예: Direct Connect)를 사용하는 경우 컴퓨팅 프로필은 여러 사이트에서 공유되므로 이 단계를 건너뛸 수 있습니다. 이러한 경우, 상호 연결 서비스 메시를 만드는 동안 업링크 네트워크 프로필을 재정의하고 지정할 수 있습니다.  
    
    ![업링크 네트워크 프로필 선택](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. **vMotion Network Profile**을 선택하고 **계속**을 선택합니다.
      
    ESXi 호스트의 vMotion 인터페이스에 연결할 수 있는 네트워크 프로필을 선택합니다. 이러한 네트워크 프로필을 아직 정의하지 않은 경우 여기에서 만들 수 있습니다. vMotion 네트워크가 없는 경우 **관리 네트워크 프로필**을 선택합니다.  
    
    ![vMotion 네트워크 프로필 선택](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. **vSphere 복제 네트워크 프로필**을 선택하고 **계속**을 선택합니다.
      
    ESXi 호스트의 vSphere 복제 인터페이스에 연결할 수 있는 네트워크 프로필을 선택합니다. 대부분의 경우 이 프로필은 관리 네트워크 프로필과 동일합니다.  
    
    ![vSphere 복제 네트워크 프로필 선택](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. **네트워크 확장 분산 스위치**를 선택하고 **계속**을 선택합니다.  
      
    마이그레이션할 Virtual Machines가 연결된 네트워크가 있는 분산 가상 스위치를 선택합니다.

    ![분산 가상 스위치 선택](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. 연결 규칙을 검토하고 **계속**을 선택합니다. **마침**을 선택하여 컴퓨팅 프로필을 만듭니다.  

    ![컴퓨팅 프로필 만들기](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

## <a name="configure-network-uplink"></a>네트워크 업링크 구성

이제 네트워크 업링크에 대해 AVS SDDC에서 네트워크 프로필 변경을 구성합니다.

1. SDDC NSX-T에 로그인하여 새 논리 스위치를 만들거나 온-프레미스와 AVS SDDC 간의 네트워크 업링크에 사용할 수 있는 기존 논리 스위치를 사용합니다.

1. 온-프레미스에서 AVS SDDC 통신용으로 사용할 수 있는 AVS SDDC의 HCX 업링크에 대한 네트워크 프로필을 만듭니다.  
    
   ![업링크에 대한 네트워크 프로필 만들기](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. 네트워크 프로필의 이름을 입력하고 필요한 L2 네트워크 확장을 기준으로 4-5개 이상의 사용 가능한 IP 주소를 입력합니다.  
    
   ![업링크에 대한 네트워크 프로필 구성](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. **만들기**를 선택하여 AVS SDDC 구성을 완료합니다.

## <a name="configure-service-mesh"></a>서비스 메시 구성

이제 온-프레미스와 AVS SDDC 간에 서비스 메시를 구성합니다.

1. AVS SDDC vCenter에 로그인하고 **HCX**를 선택합니다.

1. **인프라** > **상호 연결** > **서비스 메시** > **서비스 메시 만들기**를 선택합니다.  이전 단계에서 만든 네트워크 및 컴퓨팅 프로필을 구성합니다.    
      
    ![서비스 메시 구성](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. **서비스 메시 만들기**를 선택하고 **계속**을 선택합니다.  
      
    하이브리드 모바일을 사용할 쌍을 이루는 사이트를 선택합니다.  
    
    ![쌍을 이루는 사이트 선택](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. **컴퓨팅 프로필**을 선택하고 **계속**을 선택합니다.
      
    하이브리드 서비스를 사용하도록 설정하려면 원본 및 원격 사이트에서 각각 하나의 컴퓨팅 프로필을 선택합니다. 선택 항목은 Virtual Machines에서 HCX 서비스를 사용할 수 있는 리소스를 정의합니다.  
      
    ![하이브리드 서비스 사용](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. HCX에 사용할 서비스를 선택하고 **계속**을 선택합니다.  
      
    ![HCX 서비스 선택](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. **고급 구성 - 업링크 네트워크 프로필 재정의**에서 **계속**을 선택합니다.  
      
    업링크 네트워크 프로필은 원격 사이트의 상호 연결 어플라이언스에 연결할 수 있는 네트워크에 연결하는 데 사용됩니다.  
      
    ![업링크 프로필 재정의](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. **고급 구성 – 네트워크 확장 어플라이언스 스케일 아웃**에서 **네트워크 확장 어플라이언스 스케일 아웃 구성**을 선택합니다. 
      
    ![네트워크 확장 스케일 아웃](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. DVS 스위치 수에 해당하는 어플라이언스 수를 입력합니다.  
      
    ![어플라이언스 수 구성](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. **고급 구성 - 트래픽 엔지니어링**에서 **계속**을 선택합니다.  
      
    ![트래픽 엔지니어링 구성](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. 토폴로지 미리 보기를 검토하고 **계속**을 선택합니다. 그런 다음, 이 서비스 메시에 사용할 사용자에게 친숙한 이름을 입력하고 **완료**를 선택하여 마칩니다.  
      
    ![서비스 메시 완료](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    서비스 메시가 배포되고 구성됩니다.  
      
    ![배포된 서비스 메시](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>어플라이언스 상태 확인
어플라이언스의 상태를 확인하려면 **상호 연결** > **어플라이언스**를 선택합니다. 
      
![어플라이언스 상태](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>다음 단계

어플라이언스 상호 연결 **터널 상태**가 **UP** 및 녹색이면 HCX를 사용하여 AVS VM을 마이그레이션하고 보호할 준비가 된 것입니다. VMware 기술 설명서에서 [VMware HCX 설명서](https://docs.vmware.com/en/VMware-HCX/index.html) 및 [VMware HCX로 Virtual Machines 마이그레이션](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)을 참조하세요.
