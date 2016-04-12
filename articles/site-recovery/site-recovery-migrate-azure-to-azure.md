<properties
	pageTitle="Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터 마이그레이션 | Microsoft Azure"
	description="Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터를 마이그레이션합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터 마이그레이션

## 개요

이 문서에서는 Azure 지역 간에 Azure VM을 마이그레이션하는 데 Site Recovery를 사용하는 방법을 설명합니다. 시작하기 전에 다음 사항에 주의하세요.

- 이 경우에만 마이그레이션할 수 있습니다. 즉, 한 Azure 지역에서 다른 지역으로 VM을 장애 조치(failover)할 수 있으나 장애 복구(failback)할 수 없습니다.
- 이 문서에서는 복제를 설정하기 위한 최신 고급 지침을 제공하는 [VMware 가상 컴퓨터 또는 물리적 서버를 Azure에 복제](site-recovery-vmware-to-azure-classic.md)에서 자세히 설명한 많은 단계를 요약하고 사용합니다. 이 문서의 자세한 지침을 따라 마이그레이션하는 것이 좋습니다.
- 이 [레거시 문서](site-recovery-vmware-to-azure-classic-legacy.md)의 지침을 **더 이상 사용하지 마세요**.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## 필수 조건

이 배포에 대해 필요한 사항은 다음과 같습니다.

- **관리 서버**: 관리 서버 역할을 하는 Windows Server 2012 R2를 실행 중인 온-프레미스 VM입니다. 이 서버에 사이트 복구 구성요소(구성 서버 및 프로세스 서버 포함)를 설치합니다. 자세한 내용은 [관리 서버 고려사항](site-recovery-vmware-to-azure-classic.md#management-server-considerations) 및 [온-프레미스 필수 구성 요소](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites)에서 참조하세요.
- **IaaS 가상 컴퓨터**: 마이그레이션하려는 VM입니다.

## 배포 단계

1. [자격 증명 모음을 만듭니다](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault).
2. [관리 서버를 배포합니다](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server).
3. 배포한 후에 관리 서버가 마이그레이션할 VM과 통신할 수 있는지 유효성을 검사합니다.
4. [보호 그룹을 만듭니다](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). 보호 그룹에 동일한 복제 설정을 공유하는 보호된 컴퓨터가 포함되어 있습니다. 그룹에 대한 복제 설정을 지정하면 그룹에 추가하는 모든 기계에 적용됩니다.
5. [모빌리티 서비스를 설치합니다](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). 보호하려는 각 VM은 모바일 서비스가 설치되어야 합니다. 이 서비스는 프로세스 서버에 데이터를 보냅니다. 모바일 서비스를 수동으로 설치 또는 푸시하고 VM에 대한 보호를 사용하는 경우 프로세스 서버에서 자동으로 설치합니다. IaaS 가상 컴퓨터에서 마이그레이션할 방화벽 규칙을 구성하여 이 서비스의 강제 설치를 허용해야 합니다.
6. [기계에 대한 보호를 활성화합니다](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). 복제 그룹에 보호하려는 컴퓨터를 추가합니다. 
7. 가상 컴퓨터의 개인 IP 주소를 사용하여 Azure로 마이그레이션하려는 IaaS 가상 컴퓨터를 검색할 수 있습니다. Azure의 가상 컴퓨터 대시보드에서 이 주소를 찾을 수 있습니다.
8. 사용자가 만든 보호 그룹에 대한 탭에서 **컴퓨터 추가** > **물리적 컴퓨터**를 클릭합니다.

	![EC2 검색](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. 가상 컴퓨터의 개인 IP 주소를 지정합니다.

	![EC2 검색](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
	그룹에 컴퓨터를 추가한 후 보호를 사용할 수 있으며 초기 복제는 보호 그룹 설정에 따라 실행됩니다.

10. [계획되지 않은 장애 조치(failover)를 실행합니다](site-recovery-failover.md#run-an-unplanned-failover). 초기 복제가 완료된 후에 Azure 지역 간에 계획되지 않은 장애 조치를 실행할 수 있습니다. 선택적으로 복구 계획을 만들고 장애 조치를 실행하여 Azure 지역 간에 여러 가상 컴퓨터를 마이그레이션할 수 있습니다. 복구 계획을 [자세히 알아봅니다](site-recovery-create-recovery-plans.md).
		
## 다음 단계

[Azure Site Recovery란?](site-recovery-overview.md)의 다른 복제 시나리오에 대해 알아보기

<!---HONumber=AcomDC_0323_2016-->