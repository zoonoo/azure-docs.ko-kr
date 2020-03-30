---
title: Azure 사이트 복구에 구성 서버 배포
description: 이 문서에서는 Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버를 관리하는 방법을 설명합니다.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257382"
---
# <a name="deploy-a-configuration-server"></a>구성 서버 배포

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 배포합니다. 구성 서버는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다. 이 문서에서는 VMware VM을 Azure로 복제할 때 구성 서버를 배포하는 데 필요한 단계를 안내합니다. 실제 서버 복제를 위해 구성 서버를 설정해야 하는 경우 [물리적 서버를 Azure로 복구하기 위한 구성 서버 설정을](physical-azure-set-up-source.md)참조하십시오.

> [!TIP]
> Azure 사이트 복구 아키텍처의 일부로 구성 서버의 역할에 대해 자세히 알아보려면 [VMware에서 Azure 재해 복구 아키텍처를](vmware-azure-architecture.md)참조하십시오.

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>OVA 템플릿을 통해 구성 서버 배포

구성 서버는 특정 최소 하드웨어 및 크기 조정 요구 사항이 있는 가용성이 높은 VMware VM으로 설정해야 합니다. 편리하고 간편한 배포를 위해 사이트 복구는 다운로드 가능한 OVA(개방형 가상화 응용 프로그램) 템플릿을 제공하여 여기에 나열된 모든 필수 요구 사항을 준수하는 구성 서버를 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

구성 서버에 대한 최소 하드웨어 요구 사항은 다음 섹션으로 요약되어 있습니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory 권한 요구 사항

Azure 사이트 복구 서비스에 구성 서버를 등록하려면 Azure Active Directory(Azure AD)에 설정된 다음 권한 중 하나를 가진 사용자가 있어야 합니다.

1. 사용자는 응용 프로그램을 만들 려면 응용 프로그램 개발자 역할이 있어야 합니다.
    - 확인하려면 Azure 포털에 로그인합니다.</br>
    - Azure **Active 디렉터리** > **역할 및 관리자로**이동합니다.</br>
    - 응용 프로그램 개발자 역할이 사용자에게 할당되었는지 확인합니다. 그렇지 않은 경우 이 권한이 있는 사용자를 사용하거나 [관리자에게 문의하여 권한을 활성화합니다.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)
    
2. 응용 프로그램 개발자 역할을 할당할 수 없는 경우 **사용자가 응용 프로그램 플래그를 등록할 수** 있는지 확인 합니다. **true** 이러한 권한을 사용하려면 다음을 수행하십시오.
    - Azure Portal에 로그인합니다.
    - Azure **Active 디렉터리** > **사용자 설정으로**이동합니다.
    - **앱 등록에서** **사용자는 응용 프로그램을 등록하고** **예**.

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> 활성 디렉터리 페더레이션 서비스는 *지원되지 않습니다.* [Azure Active Directory를](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)통해 관리되는 계정을 사용합니다.

## <a name="download-the-template"></a>템플릿 다운로드

1. 볼트에서 **인프라** > **원본**준비로 이동합니다.
2. **원본 준비**에서 **+구성 서버**를 선택합니다.
3. **서버 추가**에서 **VMware에 대한 구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. 구성 서버에 대한 OVA 템플릿을 다운로드합니다.

   > [!TIP]
   >[Microsoft 다운로드 센터에서](https://aka.ms/asrconfigurationserver)직접 최신 버전의 구성 서버 템플릿을 다운로드할 수도 있습니다.

> [!NOTE]
> OVA 템플릿과 함께 제공되는 라이선스는 180일 동안 유효한 평가판 라이센스입니다. 이 기간 이후에는 라이센스를 조달해야 합니다.

## <a name="import-the-template-in-vmware"></a>VMware에서 템플릿 가져오기

1. VMWare vSphere 클라이언트를 사용하여 VMware vCenter 서버 또는 vSphere ESXi 호스트에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포를** 선택하여 **OVF 템플릿 배포** 마법사를 시작합니다.

     ![OVF 템플릿 배포](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **원본 선택**에서 다운로드한 OVF의 위치를 입력합니다.
4. **리뷰 세부 정보**에서 **다음**을 선택합니다.
5. **이름 및 폴더 선택**과 **구성 선택**에서 기본 설정을 그대로 적용합니다.
6. 최상의 성능을 발휘할 수 있도록 **스토리지 선택**의 **가상 디스크 형식 선택**에서 **씩 프로비전 Eager Zeroed**를 선택합니다. 씬 프로비저닝 옵션을 사용하면 구성 서버의 성능에 영향을 줄 수 있습니다.
7. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
8. **완료 준비**에서:

    * 기본 설정으로 VM을 설정하려면 **배포** > **완료**후 전원을 선택합니다.
    * 추가 네트워크 인터페이스를 추가하려면 **배포 후 전원 켜기**의 선택을 취소한 다음, **마침**을 선택합니다. 기본적으로 구성 서버 템플릿은 단일 NIC를 사용하여 배포됩니다. 배포 후 NIC를 추가할 수 있습니다.

> [!IMPORTANT]
> 메모리, 코어 및 CPU 제한과 같은 리소스 구성을 변경하거나 배포 후 구성 서버에 설치된 서비스 또는 파일을 수정하거나 삭제하지 마십시오. 이러한 유형의 변경 내용은 Azure 서비스를 사용한 구성 서버의 등록 및 구성 서버의 성능에 영향을 미칩니다.

## <a name="add-an-additional-adapter"></a>어댑터 추가

> [!NOTE]
> 장애 조치(failover)에서 원본 컴퓨터의 IP 주소를 유지하려는 경우 나중에 온-프레미스로 장애 조치(fail-c)로 다시 장애 조치(fail-of-ci)를 유지하려면 두 개의 NIC가 필요합니다. 하나의 NIC는 원본 컴퓨터에 연결되고 다른 NIC는 Azure 연결에 사용됩니다.

구성 서버에 NIC를 추가하려면 자격 증명 모음에 서버를 등록하기 전에 추가하세요. 등록 후에는 어댑터를 추가할 수 없습니다.

1. VSphere 클라이언트 인벤토리에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.
2. **하드웨어**에서 **추가** > **이더넷 어댑터**를 선택합니다. 그런 다음 을 **선택합니다.**
3. 어댑터 유형 및 네트워크를 선택합니다.
4. VM이 켜질 때 가상 NIC에 연결하려면 **전원이 켜지면 연결**을 선택합니다. 그런 다음 **다음** > **완료** > **확인을**선택합니다.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Azure Site Recovery 서비스에 구성 서버 등록

1. VMWare vSphere 클라이언트 콘솔에서 VM을 켭니다.
2. VM이 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다.
4. 처음 로그인하면 몇 초 내에 Azure 사이트 복구 구성 도구가 시작됩니다.
5. Site Recovery에 구성 서버를 등록하는 데 사용된 이름을 입력합니다. 그런 다음 을 **선택합니다.**
6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인**을 선택하여 Azure 구독에 로그인합니다.</br>
    a. 구성 서버를 등록하려는 자격 증명 모음에 자격 증명이 액세스할 수 있어야 합니다.</br>
    b. 선택한 사용자 계정에 Azure에서 응용 프로그램을 만들 수 있는 권한이 있는지 확인합니다. 필요한 권한을 사용하려면 [Azure Active Directory 권한 요구 사항](#azure-active-directory-permission-requirements)섹션의 지침을 따릅니다.
7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
8. 컴퓨터에 다시 로그인합니다. 구성 서버 관리 마법사는 몇 초 후에 자동으로 시작됩니다.

### <a name="configure-settings"></a>설정 구성

1. 구성 서버 관리 마법사에서 **연결 설정**을 선택합니다. 드롭다운 상자에서 먼저 내장 프로세스 서버가 소스 컴퓨터에서 모빌리티 서비스의 검색을 위해 사용하는 NIC를 선택하고 이동성 서비스의 설치를 푸시합니다. 그런 다음 구성 서버가 Azure와의 연결을 위해 사용하는 NIC를 선택합니다. **저장**을 선택합니다. 구성된 후에는 이 설정을 변경할 수 없습니다. 구성 서버의 IP 주소를 변경하지 마십시오. 구성 서버에 할당된 IP가 DHCP IP가 아닌 정적 IP인지 확인합니다.
2. **복구 서비스 자격 증명 모음 선택에서**Azure 사이트 복구 서비스를 사용하여 구성 서버 등록 6단계에서 사용되는 자격 증명을 사용하여 Microsoft [Azure에](#register-the-configuration-server-with-azure-site-recovery-services)로그인합니다.
3. 로그인 한 후 Azure 구독 및 관련 리소스 그룹 및 자격 증명 모음을 선택합니다.

    > [!NOTE]
    > 등록 후에는 복구 서비스 자격 증명 모음을 변경할 수 있는 유연성이 없습니다.
    > 복구 서비스 자격 증명 모음을 변경하려면 현재 볼트에서 구성 서버의 연결이 끊어지고 구성 서버에서 보호된 모든 가상 시스템의 복제가 중지됩니다. 자세한 내용은 [VMware VM 재해 복구의 구성 서버 관리를](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)참조하십시오.

4. **타사 소프트웨어 설치**시:

    |시나리오   |수행할 단계  |
    |---------|---------|
    |MySQL을 수동으로 다운로드하여 설치할 수 있습니까?     |  예. MySQL 응용 프로그램을 다운로드하고 **C:\Temp\ASRSetup**폴더에 배치한 다음 수동으로 설치합니다. 약관에 동의하고 **다운로드 및 설치를**선택하면 포털에 *이미 설치되었다는*것이 표시됩니다. 다음 단계로 진행할 수 있습니다.       |
    |MySQL 온라인 다운로드를 방지할 수 있나요?     |   예. MySQL 설치 관리자 애플리케이션을 **C:\Temp\ASRSetup** 폴더에 배치합니다. 조건에 동의하고 **다운로드 및 설치를**선택하고 포털에서 추가한 설치 프로그램을 사용하여 응용 프로그램을 설치합니다. 설치가 완료된 후 다음 단계로 진행합니다.    |
    |Azure 사이트 복구를 통해 MySQL을 다운로드하여 설치하려고 합니다.    |  사용권 계약에 동의하고 **다운로드 및 설치를**선택합니다. 설치가 완료된 후 다음 단계로 진행합니다.       |

5. **어플라이언스 구성 유효성 검사에서**전제 조건은 계속하기 전에 확인됩니다.
6. **vCenter 서버/vSphere ESXi 서버 구성에서**복제하려는 VM이 있는 vCenter 서버 또는 vSphere 호스트의 FQDN 또는 IP 주소를 입력합니다. 서버가 수신 대기하는 포트를 입력합니다. 자격 증명 모음의 VMware 서버에 사용할 이름을 입력합니다.
7. 구성 서버가 VMware 서버에 연결하는 데 사용할 자격 증명을 입력합니다. Site Recovery는 이러한 자격 증명을 사용하여 복제에 사용 가능한 VMware VM을 자동으로 검색합니다. 계속 **추가를** > **선택합니다.** 여기서 입력한 자격 증명은 로컬로 저장됩니다.
8. **가상 시스템 자격 증명 구성에서**가상 시스템의 사용자 이름과 암호를 입력하여 복제 중에 이동성 서비스를 자동으로 설치합니다. **Windows** 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다. **Linux**의 경우 루트 계정에 대한 세부 정보를 제공합니다.
9. **구성 완료**를 선택하여 등록을 완료합니다.
10. 등록이 완료되면 Azure 포털을 열고 구성 서버 및 VMware 서버가 **복구 서비스 Vault** > **사이트** > **복구 인프라** > 구성 서버에 나열되어 있는지**확인합니다.**

## <a name="upgrade-the-configuration-server"></a>구성 서버 업그레이드

구성 서버를 최신 버전으로 업그레이드하려면 [VMware VM 재해 복구를 위한 구성 서버 관리를](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)참조하십시오. 모든 사이트 복구 구성 요소를 업그레이드하는 방법에 대한 지침은 사이트 복구 의 [서비스 업데이트를](service-updates-how-to.md)참조하십시오.

## <a name="manage-the-configuration-server"></a>구성 서버 관리

지속적인 복제가 중단되지 않도록 하려면 구성 서버가 볼트에 등록된 후 구성 서버의 IP 주소가 변경되지 않도록 하십시오. 일반적인 구성 서버 관리 작업에 대한 자세한 내용은 [VMware VM 재해 복구의 구성 서버 관리를](vmware-azure-manage-configuration-server.md)참조하십시오.

## <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

구축 & 연결 문제를 해결하려면 [문제 해결 문서를](vmware-azure-troubleshoot-configuration-server.md) 참조하십시오.

## <a name="faqs"></a>FAQ

* OVF를 통해 배포된 구성 서버에 제공된 라이선스는 얼마 동안 유효합니까? 라이선스를 다시 활성화하지 않으면 어떻게 되나요?

    OVA 템플릿과 함께 제공되는 라이센스는 180일 동안 유효한 평가판 라이센스입니다. 만료되기 전에 라이센스를 활성화해야 합니다. 그렇지 않으면 구성 서버가 자주 종료되어 복제 활동에 방해가 될 수 있습니다. 자세한 내용은 [VMware VM 재해 복구의 구성 서버 관리를](vmware-azure-manage-configuration-server.md#update-windows-license)참조하십시오.

* 구성 서버가 다른 용도로 설치된 VM을 사용할 수 있습니까?

    아니요. 구성 서버의 유일한 용도로 VM을 사용합니다. 재해 복구의 효율적인 관리를 위해 [필수 구성 요소에서](#prerequisites) 언급한 모든 사양을 준수해야 합니다.
* 구성 서버에 이미 등록된 자격 증명 모음을 새로 만든 자격 증명 모음으로 전환할 수 있나요?

    아니요. 볼트가 구성 서버에 등록된 후에는 변경할 수 없습니다.
* 동일한 구성 서버를 사용하여 물리적 컴퓨터와 가상 컴퓨터를 모두 보호할 수 있습니까?

    예. 동일한 구성 서버를 사용하여 물리적 및 가상 컴퓨터를 복제할 수 있습니다. 그러나 물리적 컴퓨터는 VMware VM으로만 다시 장애로 돌아갈 수 있습니다.
* 구성 서버의 목적은 무엇이며 어디에서 사용됩니까?

    구성 서버 및 해당 기능에 대한 자세한 내용은 [VMware에서 Azure 복제 아키텍처를](vmware-azure-architecture.md)참조하십시오.
* 구성 서버의 최신 버전은 어디에서 찾을 수 있습니까?

    포털을 통해 구성 서버를 업그레이드하는 단계는 [구성 서버 업그레이드](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)를 참조하세요. 모든 사이트 복구 구성 요소를 업그레이드하는 방법에 대한 지침은 사이트 복구 의 [서비스 업데이트를](https://aka.ms/asr_how_to_upgrade)참조하십시오.
* 구성 서버의 암호는 어디서 다운로드할 수 있나요?

    암호를 다운로드하려면 [VMware VM 재해 복구의 구성 서버 관리를](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)참조하십시오.
* 암호를 변경할 수 있나요?

    아니요. 구성 서버의 암호를 변경하지 마십시오. 암호가 변경하면 보호된 컴퓨터의 복제가 중단되고 심각한 상태가 됩니다.
* 자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

    **복구 서비스 볼트에서****사이트 복구 인프라** > 구성 서버 **관리를** > **선택합니다.** **서버**에서 **등록 키 다운로드**를 선택하여 자격 증명 모음 자격 증명 파일을 다운로드합니다.
* 기존 구성 서버를 복제하여 복제 오케스트레이션에 사용할 수 있습니까?

    아니요. 복제된 구성 서버 구성 요소의 사용은 지원되지 않습니다. 확장 프로세스 서버의 복제도 지원되지 않는 시나리오입니다. 사이트 복구 구성 요소를 복제하는 것은 진행 중인 복제에 영향을 줍니다.

* 구성 서버의 IP를 변경할 수 있습니까?

    아니요. 구성 서버의 IP 주소를 변경하지 마십시오. 구성 서버에 할당된 모든 IP가 DHCP IP가 아닌 정적 IP인지 확인합니다.
* Azure에서 구성 서버를 설정할 수 있습니까?

    v-Center를 사용하여 직접 가시줄을 사용하여 온-프레미스 환경에서 구성 서버를 설정하고 데이터 전송 대기 시간을 최소화합니다. [장애 복구](vmware-azure-manage-configuration-server.md#failback-requirements)를 위해 구성 서버의 예약된 백업을 수행할 수 있습니다.

* 구성 서버또는 스케일 아웃 프로세스 서버에서 캐시 드라이버를 변경할 수 있습니까?

    아니요, 설정이 완료되면 캐시 드라이버를 변경할 수 없습니다.

구성 서버에 대한 자세한 FAQ는 [구성 서버의 일반적인 질문을](vmware-azure-common-questions.md#configuration-server)참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure에 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정합니다.
