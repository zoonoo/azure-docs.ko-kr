---
title: Azure Vm에서 SAP 시작 | Microsoft Docs
description: Microsoft Azure의 Vm (가상 머신)에서 실행 되는 SAP 솔루션에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf8c797edec143d09739272917b5781a239280ba
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147739"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure를 사용 하 여 SAP 워크 로드 시나리오 호스트 및 실행

Microsoft Azure를 사용 하는 경우 확장 가능 하 고 규정을 준수 하는 엔터프라이즈 입증 플랫폼에서 중요 업무용 SAP 워크 로드 및 시나리오를 안정적으로 실행할 수 있습니다. Azure의 확장성, 유연성 및 비용 절감 효과를 얻을 수 있습니다. Microsoft와 SAP 간의 확장 된 파트너 관계를 사용 하 여 Azure에서 개발 및 테스트 및 프로덕션 시나리오에 걸쳐 SAP 응용 프로그램을 실행 하 고 완벽 하 게 지원할 수 있습니다. SAP NetWeaver에서 SAP S/4HANA, Linux의 SAP BI에서 Windows로, SAP HANA SQL에 대해 설명 했습니다.

Azure에서 다른 DBMS를 사용 하 여 SAP NetWeaver 시나리오를 호스트 하는 것 외에도 Azure에서 SAP BI와 같은 다른 SAP 워크 로드 시나리오를 호스트할 수 있습니다. 

Azure for SAP HANA의 고유성은 Azure를 따로 설정 하는 제품입니다. SAP HANA와 관련 된 추가 메모리 및 CPU 리소스를 많이 사용 하는 SAP 시나리오 호스팅을 지원 하기 위해 Azure는 고객 전용 운영 체제 미 설치 하드웨어를 제공 합니다. S/4HANA 또는 기타 SAP HANA 워크 로드에 대해 최대 24tb (120-TB)의 메모리를 필요로 하는 SAP HANA 배포를 실행 하려면이 솔루션을 사용 합니다. 

Azure에서 SAP 워크 로드 시나리오를 호스트 하는 것도 id 통합 및 Single Sign-On 요구 사항을 만들 수 있습니다. 이 상황은 Azure Active Directory (Azure AD)를 사용 하 여 다양 한 SAP 구성 요소와 SAP SaaS (software as a service) 또는 PaaS (platform as a service) 제품을 연결 하는 경우에 발생할 수 있습니다. Azure AD 및 SAP 엔터티와 이러한 통합 및 Single Sign-On 시나리오의 목록은 "AAD SAP identity integration and Single Sign-On" 섹션에서 설명 하 고 설명 합니다.

## <a name="changes-to-the-sap-workload-section"></a>SAP 워크 로드 섹션의 변경 내용
Azure의 SAP 워크 로드 섹션에서 문서에 대 한 변경 내용은이 문서의 끝에 나열 되어 있습니다. 변경 로그의 항목은 약 180 일 동안 유지 됩니다.

## <a name="you-want-to-know"></a>알고 싶습니다.
특정 질문이 있는 경우 시작 페이지의이 섹션에 특정 문서 또는 흐름이 표시 됩니다. 다음 사항을 알고 싶습니다.

- SAP 소프트웨어 릴리스 및 운영 체제 버전에 대해 지원 되는 Azure Vm 및 HANA Large Instance 장치는 무엇 인가요? [Azure 배포에 대해 지원 되는 SAP 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 및 정보를 찾는 프로세스에 대 한 문서를 참조 하십시오.
- Azure Vm 및 HANA Large Instances에서 지원 되는 SAP 배포 시나리오 지원 되는 시나리오에 대 한 정보는 다음 문서에서 찾을 수 있습니다.
    - [Azure virtual machine의 SAP 워크 로드 지원 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA Large Instance에 대해 지원 되는 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- 다른 Azure 지역에서 사용할 수 있는 Azure 서비스, Azure VM 유형 및 Azure storage 서비스는 [지역별 사용 가능한 사이트 제품](https://azure.microsoft.com/global-infrastructure/services/) 을 확인 하세요. 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 사용

일련의 문서는 Azure (Large Instances) 또는 간단한 HANA Large Instances에 대 한 SAP HANA를 안내 합니다. HANA Large Instances에 대 한 자세한 내용은 [Azure (Large instances)의 SAP HANA 문서 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 에서 시작 하 고 Hana large Instance 섹션에서 관련 설명서를 참조 하세요.



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 가상 컴퓨터의 SAP HANA
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. 필수 구성 요소로 서 azure IaaS의 기본 서비스를 제공 하는 Azure의 주요 서비스에 대해 잘 알고 있어야 합니다. 따라서 Azure 계산, 저장소 및 네트워킹에 대 한 지식이 필요 합니다. 이러한 주제 중 상당수는 SAP NetWeaver 관련 [Azure 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)에서 처리 됩니다. 

Azure의 HANA에 대 한 자세한 내용은 다음 문서 및 해당 하위 문서를 참조 하세요.

- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 가상 머신에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure virtual machines의 SAP HANA에 대 한 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure virtual machines에 배포 된 SAP NetWeaver
이 섹션에서는 Azure의 SAP NetWeaver 및 Business One에 대 한 계획 및 배포 설명서를 제공 합니다. 이 설명서에서는 Azure의 SAP 워크 로드에서 HANA가 아닌 데이터베이스를 사용 하는 기본 사항 및 사용에 중점을 설명 합니다. 고가용성에 대 한 문서와 문서는 다음과 같이 Azure의 HANA 고가용성을 위한 기초 이기도 합니다.

- [Azure 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [Azure virtual machines의 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Site Recovery를 사용 하 여 다중 계층 SAP NetWeaver 응용 프로그램 배포 보호](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure용 SAP LaMa 커넥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure의 SAP 워크 로드에서 HANA가 아닌 데이터베이스에 대 한 자세한 내용은 다음을 참조 하세요.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure Vm에서 SAP MaxDB, 라이브 캐시 및 콘텐츠 서버 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure의 SAP HANA 데이터베이스에 대 한 자세한 내용은 "Azure virtual machines에서 SAP HANA" 섹션을 참조 하세요.

Azure에서 SAP 워크 로드의 고가용성에 대 한 자세한 내용은 다음을 참조 하세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

이 문서에서는 다양 한 기타 아키텍처 및 시나리오 문서를 가리킵니다. 이후 시나리오 문서에서는 다양 한 고가용성 방법의 배포 및 구성을 설명 하는 자세한 기술 문서에 대 한 링크가 제공 됩니다. SAP NetWeaver 워크 로드에 대 한 고가용성을 설정 하 고 구성 하는 방법을 보여 주는 다른 문서는 Linux 및 Windows 운영 체제입니다.


Azure Active Directory (Azure AD)와 SAP 서비스 및 Single Sign-On 간 통합에 대 한 자세한 내용은 다음을 참조 하세요.

- [자습서: SAP Cloud for Customer와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP NetWeaver와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Business ByDesign과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP HANA와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 환경: Azure AD를 사용 하 여 Fiori 실행 패드 SAML Single Sign-On](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

SAP 구성 요소에 Azure 서비스를 통합 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>변경 로그

- 04/24/2020 SAP HANA: SAP HANA [SLES의 ANF](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)를 사용 하 여 azure vm에서 azure vm의 대기 노드를 사용 하는 azure VM의 [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel), NetWeaver의 azure vm에서 azure vm에 대 한 고가용성, anf를 사용 하는 [SLES의 azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) vm에서 sap NetWeaver에 대 한 고가용성, anf 볼륨의 IP 주소가 자동으로 할당 되는 설명을 추가 하는 [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
- 04/22/2020: 클러스터를 유지 관리 모드에서 제거 하는 것과 충돌 하므로 SLES의 `is-managed` [Azure vm에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 을 변경 하 여 해당 지침에서 meta 특성을 제거 합니다.
- 04/21/2020: SQL Azure DB를 SAP (Hybris) Commerce Platform 1811에 대해 지원 되는 DBMS로 추가 했습니다 .이 문서에서는 [Azure 배포에 대해 지원 되는 sap 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 및 [sap 인증 및 구성에서 실행 되는 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) 에 대해 다룹니다 Microsoft Azure
- 04/16/2020: [Azure 배포에 대해 지원 되는](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) Sap (Hybris) 상거래 플랫폼에 대해 지원 되는 DBMS 및 sap [인증 및 구성에서 실행 되는 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) 에 대 한 지원 되는 DBMS로 SAP HANA 추가 되었습니다 Microsoft Azure
- 04/13/2020: sap ASE의 정확한 SAP ASE 릴리스 번호에 [대 한 올바른 sap 워크 로드 용 DBMS 배포 Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 04/07/2020: 클라우드 netconfig를 명확 하 게 하기 위해 [azure에서 SLES On Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 변경-azure 지침
- 04/06/2020: Azure Vm의 대기 노드를 사용 하 여 SLES 및 RHEL에 [Azure NetApp Files를 사용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 하는 azure vm의 대기 노드를 사용 하 여 SAP HANA를 확장 하 SAP HANA 고 Netapp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) 에 대 한 참조를 제거 하는 [azure Azure NetApp Files vm의 대기 노드를 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 합니다 ( [4746 tr](https://www.netapp.com/us/media/tr-4746.pdf)으로 대체).
- 03/31/2020: [SLES에서 Azure vm의 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 고가용성을 변경 하 고 [RHEL에 있는 azure vm의 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) 고가용성을 통해 스트라이프 볼륨을 만들 때 스트라이프 크기를 지정 하는 방법에 대 한 지침을 추가 합니다.
- 03/27/2020: [sap 응용 프로그램용 ANF를 사용 하 여 SLES의 Azure vm에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 을 변경 하 여 파일 시스템 탑재 옵션을 NETAPP TR-4746 (동기화 탑재 옵션 제거)에 맞춥니다.
- 03/26/2020: NetApp 4746 TR에 참조를 추가 하기 위한 [SLES 다중 SID 가이드의 SAP NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 변경
- 03/26/2020: sap 응용 프로그램에 대해 SLES의 azure vm에서 sap [NetWeaver에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)고가용성 변경, sap [응용 프로그램에 대 한 Azure NetApp Files SLES의 AZURE Vm에서 sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)에 대 한 고가용성 [SLES의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)azure VM에 대 한 고가용성, [SLES 다중 SID 가이드의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)azure vm에서 sap NetWeaver에 대 한 고가용성, sap 응용 프로그램 [에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 대 한 RHEL의 azure vm에 대 한 고가용성, sap 응용 [프로그램을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 위한 NetWeaver의 sap RHEL에 대 한 고가용성, 다이어그램을 업데이트 하 고 Azure Load Balancer 백 엔드 풀 만들기에 대 한 지침을 설명 합니다. Azure NetApp Files
- 03/19/2020: 문서 빠른 시작의 주요 수정 버전: azure에서 [단일 인스턴스 SAP HANA의 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) 로 azure Virtual Machines에서 [SAP HANA를 설치 Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020: [Azure에서 SUSE Linux Enterprise Server On Pacemaker on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 을 변경 하 여 더 이상 필요 하지 않은 SBD 구성 설정을 제거 하세요.
- 03/16/2020: [SAP 소프트웨어가 Azure 배포에 대해 지원 되는](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) SAP HANA IaaS 인증 플랫폼의 열 인증 시나리오에 대 한 설명이 있습니다.
- 03/11/2020: DBMS 인스턴스당 여러 데이터베이스를 명확 하 게 설명 하기 위해 [Azure 가상 머신에서 SAP 워크 로드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) 를 변경 합니다.
- 03/11/2020: [SAP NetWeaver의 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 에서 변경 1 세대 및 2 세대 vm 설명
- 03/10/2020: [Azure 가상 머신 저장소 구성을 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 변경 하 여 anf의 실제 기존 처리량 제한을 명확히 합니다.
- 03/09/2020: sap 응용 프로그램에 대 한 [SUSE Linux Enterprise Server Azure vm에서 Sap NetWeaver에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)고가용성 변경, sap [응용 프로그램에 대 한 Azure NetApp Files SUSE Linux Enterprise Server의 AZURE Vm에서 sap NetWeaver에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)대 한 고가용성 azure vm에서 azure vm에 [대 한 고가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)azure [에서 Pacemaker 설정,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)LUW의 AZURE Vm에서 [IBM Db2](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 의 고가용성 [, Pacemaker를 사용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)하는 azure vm의 고가용성 및 고가용성의 Azure vm에 대 한 고가용성 SUSE Linux Enterprise Server 및 azure Vm의 SAP NetWeaver에 대 [한 고가용성 SUSE Linux Enterprise Server SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) SUSE Linux Enterprise Server SAP HANA 
- 03/05/2020: Azure에서 azure 지역 및 Azure 가상 머신에 대 한 구조 변경 및 콘텐츠 변경 [VIRTUAL MACHINES SAP NetWeaver 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: [sap 응용 프로그램에 대해 ANF를 사용 하 여 SLES의 Azure vm에서 SAP NW에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 을 변경 하 여 보다 효율적인 anf 볼륨 레이아웃으로 변경
- 03/01/2020: Azure Backup 서비스를 포함 하도록 [Azure Virtual Machines에서 SAP HANA에 대 한 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) 를 수정 했습니다. [파일 수준에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) 콘텐츠를 축소 및 압축 하 고, 디스크 스냅숏을 통한 백업을 처리 하는 세 번째 문서를 삭제 Azure Backup SAP HANA. 콘텐츠는 Azure의 SAP HANA에 대 한 백업 가이드에서 처리 됩니다 Virtual Machines 
- 02/27/2020: sap [응용 프로그램에 대 한 SLES의 Azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)에서 sap nw에 대 한 고가용성 변경, SAP 용 [Anf의 Azure vm에서 azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) vm에 대 한 고가용성 및 [SLES 다중 SID 가이드의 azure vm에서 sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 에 대 한 고가용성 (영문) "장애 조치 (failover)" 클러스터 매개 변수 조정
- 02/26/2020: azure에서 HANA에 대 한 파일 시스템 선택을 명시 하기 위해 [azure 가상 머신 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 변경
- 02/26/2020: [sap에 대해 고가용성 아키텍처 및 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) 를 변경 하 여 RHEL 다중 SID 가이드에서 Azure VM의 sap NetWeaver에 대 한 HA 링크를 포함 합니다.
- 02/26/2020: sap 응용 프로그램에 대해 [SLES의 azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)vm에서 sap nw에 대 한 고가용성 변경, sap [응용 프로그램용 SLES의 AZURE VM에서 sap nw](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)의 고가용성, sap NetWeaver에 대 한 azure [vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 고가용성, [Azure NetApp Files RHEL on에 대](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 한 azure vm 고가용성 (영문)
- 02/26/2020: [RHEL 다중 sid 가이드의 Azure vm에서 SAP NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) 릴리스 가이드 SUSE 다중 sid 클러스터 가이드에 대 한 링크 추가
- 02/25/2020: SAP에서 최신 HA 아티클에 대 한 링크를 추가 하 [는 고가용성 아키텍처 및 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) 변경
- 02/25/2020: 표준 Azure 부하 분산 장치를 사용 하 여 공용 끝점에 대 한 액세스를 설명 하는 문서를 가리키도록 [Pacemaker로 SUSE Linux Enterprise Server의 Azure vm에서 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) 을 변경 합니다.
- 02/21/2020: sap [ASE Azure VIRTUAL MACHINES sap 워크 로드에 대 한 DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) 아티클의 수정이 완료 되었습니다.
- 02/21/2020:/hana/data의 스트라이프 크기에 새로운 권장 사항을 표시 하 고 i/o scheduler 설정을 추가 하 여 [Azure 가상 머신 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 를 변경 합니다.
- 02/21/2020: S224 및 S224m의 새로 인증 된 Sku를 나타내기 위해 HANA Large Instance 문서를 변경 했습니다.
- 02/21/2020: RHEL에서 [Sap NetWeaver의 Sap에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 고가용성 및 [azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 의 변경 (RHEL)을 Azure NetApp Files 사용 하 여의 sap NetWeaver에 대 한 고가용성-큐에 넣기 서버 복제 2 아키텍처 (ENSA2)에 대 한 클러스터 제약 조건을 조정
- 02/20/2020: [SLES 다중 sid 가이드의 Azure vm에서 SAP NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 을 변경 하 여 SUSE 다중 sid 클러스터 가이드에 대 한 링크를 추가 합니다.
- 02/13/2020: [SAP NetWeaver에 대 한 Azure Virtual Machines 계획 및 구현을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 변경 하 여 새 문서에 대 한 링크를 구현 합니다.
- 02/13/2020: [Azure virtual machine에서 지원 되는 시나리오에서 새 문서 SAP 워크 로드를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) 추가 했습니다.
- 02/13/2020: [Azure 배포에 대해 지원 되는 SAP 소프트웨어를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 새 문서로 추가 했습니다.
- 02/13/2020: 표준 Azure 부하 분산 장치를 사용 하 여 공용 끝점에 대 한 액세스를 설명 하는 문서를 가리키도록 [Red Hat Enterprise Linux 서버의 Azure vm에서 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) 을 변경 합니다.
- 02/13/2020: [Microsoft Azure에서 실행 되는 SAP 인증 및 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) 에 새 VM 유형을 추가 합니다.
- 02/13/2020: Azure에서 새 SAP 지원 정보 [sap 워크 로드 추가: 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: [Azure vm의 Sap NetWeaver에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 고가용성 및 RHEL의 Sap Azure NetApp Files NetWeaver에 대 한 고가용성 및 [azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 의 경우 클러스터 리소스 시간 제한을 Red Hat 시간 제한 권장 사항에 맞게 조정 합니다.
- 02/11/2020: azure [Virtual Machines에 대 한 Azure Large Instance 마이그레이션의 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration) 릴리스
- 02/07/2020: [SAP HA 시나리오에서 Azure 표준 ILB를 사용 하 여 샘플 NSG 스크린샷 업데이트를 사용 하는 vm에 대 한 공용 끝점 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) 을 변경 합니다.
- 02/03/2020: sap 응용 프로그램용 [SLES의 Azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) 에서 sap nw에 대 한 고가용성을 변경 하 고, [SLES의 anf를 사용 하는 AZURE VM의 sap nw 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 을 사용 하 여 SLES에서 클러스터 노드의 호스트 이름에 대시 사용에 대 한 경고를 제거 합니다.
- 01/28/2020: RHEL의 [Azure vm에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) 을 변경 하 여 SAP HANA 클러스터 리소스 시간 제한을 Red Hat 시간 제한 권장 사항에 맞춥니다.
- 01/17/2020: [SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간을 위해 Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 을 변경 하 여 기존 vm을 근접 배치 그룹으로 이동 하는 섹션을 변경 합니다.
- 01/17/2020: Azure 가용성 영역을 [사용 하 여 SAP 워크 로드 구성을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 변경 하 여 가용성 영역 간의 대기 시간 측정을 자동화 하는 절차를 가리키도록 합니다.
- 01/16/2020: [Azure에서 SAP HANA (Large Instances)를 설치 및 구성 하는 방법에 따라](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) OS 릴리스를 HANA IaaS 하드웨어 디렉터리에 맞게 조정 합니다.
- 01/16/2020: [SLES 다중 SID 가이드에서 Azure vm의 Sap NetWeaver에 대 한 고가용성 변경 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) : 큐에 대기 중인 서버 2 아키텍처 (ENSA2)를 사용 하 여 sap 시스템에 대 한 지침 추가
- 01/10/2020: `nfs4_disable_idmapping` 변경 내용을 영구적으로 적용 하는 방법에 대 한 지침을 추가 하는 방법에 대 한 지침을 추가 하기 위해 SLES [SAP HANA 및 RHEL의 Azure NetApp Files를 사용 하 여 azure vm의 대기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 노드를 확장 하는 [azure Azure NetApp Files vm의 대기 노드를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 사용 하 여 SAP HANA 확장 합니다.
- 01/10/2020: sap 응용 프로그램 및 [Azure NetApp Files Virtual Machines azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 에서 sap [NetWeaver의 sap에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 고가용성의 변경 사항은 sap 응용 프로그램에 대해 Azure NetApp Files SLES를 사용 하 여 sap 응용 프로그램에서 Azure NetApp Files NFSv4 볼륨을 탑재 하는 방법에 대 한 지침을 추가할 수 있습니다.
- 12/23/2019: [SLES 다중 SID 가이드의 Azure vm에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 릴리스
- 12/18/2019: RHEL의 [Azure NetApp Files를 사용 하 여 Azure vm에서 대기 노드를 사용 하 여 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 릴리스
- 11/21/2019: SAP HANA의 변경 내용은 [Azure vm에서 SUSE Linux Enterprise Server Azure NetApp Files를 사용 하 여 Azure vm의 대기 노드로 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 되어 NFS ID 매핑의 구성을 간소화 하 고 권장 되는 기본 네트워크 인터페이스를 변경 하 여 라우팅을 간소화 합니다.
- 11/15/2019: sap 응용 프로그램에 대 한 [Azure NetApp Files와 sap SUSE Linux Enterprise Server NetWeaver](high-availability-guide-suse-netapp-files.md) 의 고가용성에 대 한 고가용성의 사소한 변경 내용으로, 용량 풀 크기 제한을 명확 하 게 설명 하 고 NFSv3 버전만 지원 되는 NetWeaver [Azure NetApp Files Red Hat Enterprise Linux에](high-availability-guide-rhel-netapp-files.md) 대 한 sap 응용 프로그램의 경우 고가용성을 제공 합니다.
- 11/12/2019: [Windows에서 SMB (Azure NetApp Files)를 사용 하는 SAP NetWeaver의 고가용성](high-availability-guide-windows-netapp-files-smb.md) 릴리스
- 11/08/2019: [SUSE Linux Enterprise Server의 Azure vm에서 SAP HANA의 고가용성](sap-hana-high-availability.md)이 변경 되 면 [azure vm (가상 머신)에서 SAP HANA 시스템 복제를 설정](sap-hana-high-availability-rhel.md)합니다. sap [NetWeaver on SUSE Linux Enterprise Server on sap 응용 프로그램](high-availability-guide-suse.md)에 대 한 azure Virtual Machines 고가용성, [azure Virtual Machines 고가용성](high-availability-guide-suse-netapp-files.md), sap NetWeaver에 대 한 Azure Azure NetApp Files [고가용성](high-availability-guide-rhel.md), Virtual Machines의 sap NetWeaver [에](high-availability-guide-rhel-netapp-files.md)대 한 고가용성, Red Hat Enterprise Linux의 azure vm에 대 한 SUSE Linux Enterprise Server 고가용성, Virtual Machines의 [azure vm](high-availability-guide-suse-nfs.md)에 대 한 고가용성, Red Hat Enterprise Linux의 azure Vm에 대 한 고가용성, [sap GlusterFS에 대 한 Azure NetApp Files on azure vm에서 NetWeaver](high-availability-guide-rhel-glusterfs.md)  
- 11/08/2019: 암호화 권장 사항을 명시 하기 위해 [SAP 워크 로드 계획 및 배포 검사 목록의](sap-deployment-checklist.md) 변경 내용  
- 11/04/2019: 유니캐스트 구성을 사용 하 여 클러스터를 직접 만들 수 있도록 [Azure에서 SUSE Linux Enterprise Server Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 의 변경 내용  
- 10/29/2019: [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 Virtual Machines에 대 한 공용 끝점 연결](high-availability-guide-standard-load-balancer-outbound-connections.md) 릴리스
- 10/25/2019:/hana/shared 볼륨에 대 한 NFS 프로토콜을 명확 하 게 설명 하 [는 SUSE Linux Enterprise Server Azure NetApp Files를 사용 하 여 Azure vm에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) azure [가상 머신 저장소 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 및 SAP HANA의 대기 노드를 확장 하는 SAP HANA의 변경
- 10/22/2019: [sap 응용 프로그램 SUSE Linux Enterprise Server에서 Azure vm의 Sap NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)변경 sap 응용 프로그램용 Azure NetApp Files, [SUSE Linux Enterprise Server에서 AZURE vm의 NFS에 대 한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)고가용성, [azure에서 SUSE Linux Enterprise Server에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)대 한 고가용성, Pacemaker를 사용 하는 [SUSE LINUX ENTERPRISE SERVER의 azure vm에서 IBM Db2 LUW의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)고가용성, azure 부하 분산 장치 검색 강화를 위한 SAP HANA의 [azure vm에 대](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 한 고가용성을 제공 하는 azure vm의 sap [NetWeaver에 대 한 고가용성 SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure 가상 컴퓨터 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 의 anf 섹션과 헤더 섹션을 변경 합니다.
- 10/21/2019: SLES의 [Azure NetApp Files를 사용 하 여 Azure vm에서 대기 노드를 사용 하 여 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 릴리스
- 10/16/2019: [백업 및 복원](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore) 에서 끊어진 링크를 수정 하십시오.
- 10/16/2019: [Pacemaker를 사용 하는 SUSE Linux Enterprise Server의 Azure vm에서 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) 을 SLES 12 s p 3에서 SLES 12 SP4로 변경 합니다.
- 10/11/2019: [Azure 가상 머신 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 에서 Ultra disk 저장소 구성 및 anf 소개의 변경 내용
- 10/01/2019: 보다 명확한 정보를 얻기 위해 [SAP 응용 프로그램의 네트워크 대기 시간을 최적화 하기 위해 Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 의 그래픽을 변경 합니다.
- 10/01/2019:/hana/shared.에 대 한 항상 사용 가능한 NFS 공유에 대 한 문을 수정 하기 위해 [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) 을 변경 합니다. 



