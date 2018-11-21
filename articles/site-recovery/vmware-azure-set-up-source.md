---
title: Azure Site Recovery를 사용하여 VMware VM을 Azure로 재해 복구하기 위한 원본 환경 및 구성 서버 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 VMware VM을 Azure로 재해 복구하기 위한 온-프레미스 환경 및 구성 서버를 설정하는 방법을 설명합니다.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566315"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>원본 환경 및 구성 서버 설정

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM를 Azure로 재해 복구하기 위한 원본 온-프레미스 환경을 설정하는 방법을 설명합니다. 여기에는 온-프레미스 머신을 Site Recovery 구성 서버로 설정하고 온-프레미스 VM을 자동으로 검색하는 단계가 포함됩니다. 

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 준비를 마쳐야 합니다.

- [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md)를 사용하여 재해 복구 배포 계획 수립. Planner는 조직에서 재해 복구 및 RPO(복구 지점 목표)에 필요한 리소스와 대역폭을 충분히 준비할 수 있도록 도와줍니다.
- [Azure Portal](http://portal.azure.com)에서 [Azure 리소스 설정](tutorial-prepare-azure.md).
- 온-프레미스 VMware VM 자동 검색용 전용 계정을 비롯한 [온-프레미스 VMware 리소스 설정](vmware-azure-tutorial-prepare-on-premises.md).
- 구성 서버 배포 및 관리에 대한 [일반 질문을 검토](vmware-azure-common-questions.md#configuration-server)할 수 있습니다.


## <a name="choose-protection-goals"></a>보호 목표 선택

1. **Recovery Services 자격 증명 모음**에서 자격 증명 모음 이름을 선택합니다. 
2. **시작**에서 Site Recovery를 선택합니다. 그런 다음, **인프라 준비**를 선택합니다.
3. **보호 목표** > **컴퓨터가 있는 위치**에서 **온-프레미스**를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
5. **컴퓨터가 가상화된 경우**에서 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 다음 **확인**을 선택합니다.

## <a name="about-the-configuration-server"></a>구성 서버 정보

VMware VM을 Azure로 재해 복구하도록 설정할 때 온-프레미스 구성 서버를 배포합니다.

- 구성 서버는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다.
- 구성 서버를 VMware VM으로 배포합니다.
- Site Recovery는 사용자가 Azure Portal에서 다운로드하고 vCenter Server로 가져와서 구성 서버 VM을 설정하는 OVA 템플릿을 제공합니다.
- 구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).


>[!NOTE]
온-프레미스 물리적 머신을 Azure로 재해 복구하기 위한 구성 서버를 배포하는 경우 이러한 지침을 사용하지 마세요. 이 시나리오의 경우 [이 문서](physical-azure-set-up-source.md)를 따릅니다.


## <a name="before-you-deploy-the-configuration-server"></a>구성 서버를 배포하기 전에

OVA 템플릿을 사용하여 구성 서버를 VMware VM으로 설치하는 경우 모든 필수 구성 요소가 포함된 상태로 VM이 설치됩니다. 필수 구성 요소를 검토하려면 다음 문서를 사용합니다.

- 구성 서버의 하드웨어, 소프트웨어 및 용량 요구 사항을 [자세히 알아보세요](vmware-azure-configuration-server-requirements.md).
- 여러 VMware VM을 복제하는 경우 [용량 계획 고려 사항](site-recovery-plan-capacity-vmware.md)을 검토하고, VMWare 복제용 [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) 도구를 실행해야 합니다.
- OVA 템플릿용 Windows 라이선스는 180일 동안 유효한 평가판 라이선스입니다. 이 기간이 지나면 유효한 라이선스를 사용하여 Windows를 활성화해야 합니다. 
- OVA 템플릿은 VMware VM으로 구성 서버를 설정하는 간단한 방법을 제공합니다. 어떤 이유로 템플릿을 사용하지 않고 VMware VM을 설정해야 하는 경우 필수 구성 요소를 검토하고, [이 문서](physical-azure-set-up-source.md)의 수동 지침을 따릅니다.
- Azure 계정에는 Azure AD 앱을 만들 수 있는 권한이 필요합니다.


>[!IMPORTANT]
이 문서에 설명된 대로 구성 서버를 배포해야 합니다. 기존 구성 서버를 복제 또는 복사하는 기능은 지원되지 않습니다.

### <a name="set-up-azure-account-permissions"></a>Azure 계정 권한 설정

테넌트/글로벌 관리자는 계정에 Azure AD 앱을 만드는 권한을 할당하거나 계정에 애플리케이션 개발자 역할(권한이 있는)을 할당할 수 있습니다.


테넌트/글로벌 관리자는 다음과 같이 계정의 권한을 부여할 수 있습니다.

1. Azure AD에서 테넌트/글로벌 관리자는 **Azure Active Directory** > **사용자** > **사용자 설정**으로 이동해야 합니다.
2. 관리자는 **앱 등록**을 **예**로 설정해야 합니다.

 > [!NOTE]
 > 이것이 중요한 내용이 포함되지 않는 기본 설정입니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

또는 테넌트/글로벌 관리자는 계정에 역할을 할당하는 권한을 갖고 있습니다. [자세히 알아보기](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 


## <a name="download-the-ova-template"></a>OVA 템플릿 다운로드

1. 자격 증명 모음에서 **인프라 준비** > **원본**으로 이동합니다.
2. **원본 준비**에서 **+구성 서버**를 선택합니다.
3. **서버 추가**에서 **VMware에 대한 구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. 구성 서버에 대한 OVA(Open Virtualization Application) 템플릿을 다운로드합니다.

  > [!TIP]
>[Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 최신 버전의 구성 서버 템플릿을 다운로드할 수도 있습니다.


## <a name="import-the-ova-template-in-vmware"></a>VMware에서 OVA 템플릿 가져오기

1. VMWare vSphere 클라이언트를 사용하여 VMware vCenter 서버 또는 vSphere ESXi 호스트에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포**를 선택하여 OVF 템플릿 배포 마법사를 시작합니다.

     ![OVF 템플릿](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **원본 선택**에서 다운로드한 OVF의 위치를 입력합니다.
4. **세부 정보 검토**에서 **다음**을 선택합니다.
5. **이름 및 폴더 선택**과 **구성 선택**에서 기본 설정을 그대로 적용합니다.
6. 최상의 성능을 발휘할 수 있도록 **저장소 선택**의 **가상 디스크 형식 선택**에서 **씩 프로비전 Eager Zeroed**를 선택합니다.
7. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
8. **완료 준비**에서 기본 설정을 사용하여 VM을 설정하려면 **배포 후 전원 켜기** > **마침**을 선택합니다.
9. 기본적으로 VM은 단일 NIC를 사용하여 배포됩니다. NIC를 추가하려면 **배포 후 전원 켜기**의 선택을 취소하고 **마침**을 클릭합니다. 그리고 다음 절차를 따릅니다.

## <a name="add-an-adapter-to-the-configuration-server"></a>구성 서버에 어댑터 추가

구성 서버에 NIC를 추가하려면 자격 증명 모음에 서버를 등록하기 전에 추가하세요. 등록 후에는 어댑터를 추가할 수 없습니다.

1. VSphere 클라이언트 인벤토리에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.
2. **하드웨어**에서 **추가** > **이더넷 어댑터**를 선택합니다. 그런 후 **다음**을 선택합니다.
3. 어댑터 유형 및 네트워크를 선택합니다.
4. VM이 켜질 때 가상 NIC에 연결하려면 **전원이 켜지면 연결**을 선택합니다. 그런 다음, **다음** > **마침** > **확인**을 선택합니다.

## <a name="register-the-configuration-server"></a>구성 서버 등록 
VM을 켜고 Site Recovery 자격 증명 모음에 구성 서버를 등록합니다.

1. VMWare vSphere 클라이언트 콘솔에서 VM을 켭니다.
2. VM이 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다.



## <a name="set-up-the-configuration-server"></a>구성 서버 설정

배포의 일부로, 구성 서버 VM에 MySQL을 설치해야 합니다. 다음과 같은 몇 가지 방법으로 이를 수행할 수 있습니다.

- Site Recovery가 MySQL을 다운로드하고 설치하게 합니다. 이 옵션을 사용하려는 경우 구성 서버 설치를 시작하기 전에 아무 것도 할 필요가 없습니다.
- 수동으로 MySQL을 다운로드하여 설치: 구성 서버를 설정하기 전에, MySQL 설치 프로그램을 다운로드하여 **C:\Temp\ASRSetup** 폴더에 저장합니다. 이제 MySQL을 설치합니다. 
- 수동으로 다운로드하고 Site Recovery가 설치하게 합니다. 수동으로 구성 서버를 설정하기 전에, MySQL 설치 프로그램을 다운로드하여 **C:\Temp\ASRSetup** 폴더에 저장합니다.


1. 처음으로 VM에 로그인하면 Azure Site Recovery 구성 도구가 시작됩니다.
2. Site Recovery 자격 증명 모음에 구성 서버를 등록하는 데 사용된 이름을 지정합니다. 그런 후 **다음**을 선택합니다.
3. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인**을 선택하여 Azure 구독에 로그인합니다. 구성 서버를 등록하려는 자격 증명 모음에 계정이 액세스할 수 있어야 합니다.
4. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
5. 컴퓨터에 다시 로그인합니다. 몇 초 내로 구성 서버 관리 마법사가 자동으로 시작됩니다.
6. 마법사에서 **연결 설정**을 선택합니다.
7. 프로세스 서버(기본적으로 구성 서버에서 실행)가 VM에서 복제 트래픽을 수신할 때 사용할 NIC를 선택합니다.
8. 그런 다음 **저장**을 선택합니다. 구성 서버가 등록된 후에는 자격 증명 모음 설정을 변경할 수 없습니다. 
9. **Recovery Services 자격 증명 모음 선택**에서 Microsoft Azure에 로그인하고, Azure 구독을 선택한 다음, 관련 리소스 그룹 및 자격 증명 모음을 선택합니다. 
10. **타사 소프트웨어 설치**에서 MySQL을 설치합니다.
     - Site Recovery가 MySQL 다운로드 및 설치를 처리하는 경우 **다운로드 및 설치**를 클릭합니다. 설치가 완료될 때까지 기다렸다가 마법사를 진행합니다.
     - 사용자가 MySQL을 다운로드하고 Site Recovery가 설치하는 경우 **다운로드 및 설치**를 클릭합니다. 설치가 완료될 때까지 기다렸다가 마법사를 계속 진행합니다.
     - 수동으로 MySQL을 다운로드하고 설치하는 경우 **다운로드 및 설치**를 클릭합니다. 앱이 **이미 설치됨**으로 표시됩니다. 마법사를 진행합니다.
11. **어플라이언스 구성 유효성 검사**에서, 필수 구성 요소가 확인되면 계속 진행할 수 있습니다. 
12. **vCenter Server/vSphere ESXi 서버 구성**에서 복제하려는 VM이 있는 vCenter 서버의 FQDN 또는 IP 주소, 또는 vSphere 호스트를 입력합니다. 서버가 수신 대기하는 포트를 입력하고 자격 증명 모음에 있는 VMware 서버의 이름을 입력합니다.
13. 구성 서버가 VMware 서버에 연결하여 복제에 사용 가능한 VMware VM을 자동으로 검색하는 데 사용할 자격 증명을 입력합니다. **추가** >  **계속**을 선택합니다. 자격 증명은 로컬로 저장됩니다.
14. **가상 머신 자격 증명 구성**에서, VM에 복제가 사용되면 Site Recovery가 Mobility 서비스를 자동으로 설치하는 데 사용할 자격 증명을 지정합니다.
    - Windows 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다.
    - Linux의 경우 루트 계정에 대한 세부 정보를 제공합니다.
15. **구성 완료**를 선택하여 등록을 완료합니다.
16. 등록이 완료되면 Azure Portal을 열고, **Recovery Services 자격 증명 모음** > **관리** > **Site Recovery 인프라** > **구성 서버**에 구성 서버 및 VMware 서버가 표시되는지 확인합니다.


## <a name="exclude-antivirus-on-the-configuration-server"></a>구성 서버에서 바이러스 백신 제외

바이러스 백신 소프트웨어가 구성 서버 VM에서 실행 중인 경우 바이러스 백신 작업에서 다음 폴더를 제외해야 합니다. 그러면 복제 및 연결이 예상대로 작동할 것입니다. 

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program Files (x86)\MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\inetpub
- Site Recovery 설치 디렉터리입니다. 예: E:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>다음 단계
- 문제가 발생하면 구성 서버에 대한 [일반 질문](vmware-azure-common-questions.md#configuration server) 및 [문제 해결](vmware-azure-troubleshoot-configuration-server.md)을 검토하세요.
- 이제 Azure에서 [대상 환경을 설정](./vmware-azure-set-up-target.md)하세요.
