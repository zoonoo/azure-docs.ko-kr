---
title: Azure VM에서 SAP 시작 | 마이크로 소프트 문서
description: Microsoft Azure의 가상 컴퓨터(VM)에서 실행되는 SAP 솔루션에 대해 알아보기
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
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0866db6b549bc940d2e73bccfc77b8fad19abd51
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273225"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure를 사용하여 SAP 워크로드 시나리오를 호스트하고 실행

Microsoft Azure를 사용하면 확장 가능하고 규정을 준수하며 엔터프라이즈에서 검증된 플랫폼에서 미션 크리티컬 SAP 워크로드 및 시나리오를 안정적으로 실행할 수 있습니다. Azure의 확장성, 유연성 및 비용 절감 효과를 얻을 수 있습니다. Microsoft와 SAP 간의 확장된 파트너십을 통해 Azure의 개발 및 테스트 및 프로덕션 시나리오에서 SAP 응용 프로그램을 실행하고 완벽하게 지원할 수 있습니다. SAP NetWeaver에서 SAP S/4HANA, 리눅스에서 Windows로의 SAP BI, SAP HANA에서 SQL에 이르기까지 다양한 방법을 제공합니다.

Azure에서 다른 DBMS를 통해 SAP NetWeaver 시나리오를 호스팅하는 것 외에도 Azure에서 SAP BI와 같은 다른 SAP 워크로드 시나리오를 호스팅할 수 있습니다. 

SAP HANA용 Azure의 고유성은 Azure를 구분하는 제안입니다. SAP HANA와 관련된 더 많은 메모리 및 CPU 리소스요구 SAP 시나리오를 호스팅할 수 있도록 Azure는 고객 전용 베어메탈 하드웨어를 제공합니다. 이 솔루션을 사용하여 S/4HANA 또는 기타 SAP HANA 워크로드에 대해 최대 24TB(120TB 확장)의 메모리가 필요한 SAP HANA 배포를 실행합니다. 

Azure에서 SAP 워크로드 시나리오를 호스팅하면 ID 통합 및 단일 사인온에 대한 요구 사항을 만들 수도 있습니다. 이러한 상황은 Azure Active Directory(Azure AD)를 사용하여 다양한 SAP 구성 요소와 SAaS(서비스형 소프트웨어) 또는 PaaS(서비스로서의 플랫폼) 오퍼를 연결하는 경우에 발생할 수 있습니다. Azure AD 및 SAP 엔터티와의 이러한 통합 및 단일 사인온 시나리오 목록은 "AAD SAP ID 통합 및 단일 사인온" 섹션에 설명되고 설명되어 있습니다.

## <a name="changes-to-the-sap-workload-section"></a>SAP 워크로드 섹션 변경 사항
Azure 워크로드 섹션의 SAP에 있는 문서에 대한 변경 내용은 이 문서의 끝에 나열됩니다. 변경 로그의 항목은 약 180일 동안 유지됩니다.

## <a name="you-want-to-know"></a>알고 싶어요.
특정 질문이 있는 경우 시작 페이지의 이 섹션에서 특정 문서 또는 흐름을 가리킵니다. 다음을 알고 싶습니다.

- SAP 소프트웨어가 릴리스하고 어떤 운영 체제 버전에 대해 지원되는 Azure VM 및 HANA 대형 인스턴스 단위 문서 읽기 어떤 SAP 소프트웨어 에 [대 한 지원 되는 Azure 배포에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 대 한 답변 및 정보를 찾는 프로세스
- Azure VM 및 HANA 대형 인스턴스에서 지원되는 SAP 배포 시나리오 지원되는 시나리오에 대한 정보는 문서에서 찾을 수 있습니다.
    - [Azure 가상 시스템 지원 시나리오에서 SAP 워크로드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA 대형 인스턴스에 지원되는 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- 다른 Azure 지역에서 사용할 수 있는 Azure 서비스, Azure VM 유형 및 Azure 저장소 서비스는 [리전에서 사용할 수](https://azure.microsoft.com/global-infrastructure/services/) 있는 사이트 제품을 확인합니다. 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 사용

일련의 문서는 Azure(큰 인스턴스)에서 SAP HANA를 통해 또는 짧게 HANA 대형 인스턴스를 안내합니다. HANA 대형 인스턴스에 대한 자세한 내용은 [Azure에서 SAP HANA의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 문서 개요 및 아키텍처로 시작하여 HANA 대형 인스턴스 섹션의 관련 설명서를 참조하십시오.



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 가상 머신의 SAP HANA
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. 전제 조건으로 Azure IaaS의 기본 서비스를 제공하는 Azure의 주요 서비스에 대해 잘 알고 있어야 합니다. 따라서 Azure 계산, 저장소 및 네트워킹에 대한 지식이 필요합니다. 이러한 과목 의 대부분은 SAP NetWeaver 관련 [Azure 계획 가이드에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)처리됩니다. 

Azure에서 HANA에 대한 자세한 내용은 다음 문서 및 해당 하위 문서를 참조하십시오.

- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 가상 머신에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 가상 머신에서 SAP HANA에 대한 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 가상 머신에 배포된 SAP NetWeaver
이 섹션에서는 Azure에서 SAP NetWeaver 및 Business One에 대한 계획 및 배포 설명서를 나열합니다. 이 설명서는 Azure에서 SAP 워크로드가 있는 HANA 이외의 데이터베이스의 기본 및 사용에 중점을 둡니다. 고가용성을 위한 문서와 문서는 다음과 같은 Azure에서 HANA 고가용성의 토대이기도 합니다.

- [Azure 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [Azure 가상 머신의 SAP 비즈니스 원](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [사이트 복구를 사용하여 다중 계층 SAP NetWeaver 응용 프로그램 배포 보호](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure용 SAP LaMa 커넥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure에서 SAP 워크로드에서 HANA가 아닌 데이터베이스에 대한 자세한 내용은 다음을 참조하십시오.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM에서 SAP MaxDB, 라이브 캐시 및 콘텐츠 서버 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure의 SAP HANA 데이터베이스에 대한 자세한 내용은 "Azure 가상 시스템의 SAP HANA" 섹션을 참조하십시오.

Azure에서 SAP 워크로드의 고가용성에 대한 자세한 내용은 다음을 참조하십시오.

- [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

이 문서는 다양한 다른 아키텍처 및 시나리오 문서를 가리킵니다. 이후 시나리오 문서에서는 다양한 고가용성 방법의 배포 및 구성을 설명하는 자세한 기술 문서에 대한 링크가 제공됩니다. SAP NetWeaver 워크로드에 대한 고가용성을 설정하고 구성하는 방법을 보여 주는 다양한 문서는 Linux 및 Windows 운영 체제를 다룹니다.


Azure Active Directory(Azure AD) 및 SAP 서비스 및 단일 사인온 간의 통합에 대한 자세한 내용은 다음을 참조하십시오.

- [자습서: SAP Cloud for Customer와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP NetWeaver와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Business ByDesign과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP HANA와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 환경: Azure AD를 사용하면 피오리 런치패드 SAML 단일 사인온](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Azure 서비스를 SAP 구성 요소에 통합하는 방법에 대한 자세한 내용은 다음을 참조하십시오.

- [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>변경 로그
- 2020년 04/13: [SAP 워크로드에 대한 SAP ASE Azure 가상 시스템 DBMS 배포에서 정확한 SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) 릴리스 번호 수정
- 2020/04/07: 클라우드-netconfig-azure 지침을 명확히 하기 위해 [Azure에서 SLES에서 페이스 메이커 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 변경
- 2020년 04/06: [SAP HANA 확장 노드에서 SLES의 Azure NetApp 파일이 있는 Azure VM및](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) [SAP HANA의 대기 노드에서 RHEL의 Azure NetApp 파일이 있는 Azure NetApp 파일이 있는 대기 노드로](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 확장되어 NetApp [TR-4435에](https://www.netapp.com/us/media/tr-4746.pdf) 대한 참조를 제거합니다(TR-4746으로 대체됨) [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)
- 2020년 03월 31일: [SLES의 Azure VM에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 변경 및 [RHEL의 Azure VM에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) 변경으로 스트라이프 볼륨을 만들 때 스트라이프 크기를 지정하는 방법에 대한 지침을 추가합니다.
- 2020년 03월 27일: [SAP 응용 프로그램에 대한 ANF를 사용하여 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 변경으로 파일 시스템 마운트 옵션을 NetApp TR-4746에 정렬합니다(동기화 마운트 옵션 제거).
- 2020년 03월 26일: [SLES 다중 SID 가이드에서 Azure VM에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 변경으로 NetApp TR-4746에 대한 참조를 추가합니다.
- 2020년 03/26: [SAP 응용 프로그램에 대한 SLES용 SLES의 Azure VM에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)변경, [SAP 응용 프로그램에 대한 Azure NetApp 파일이 있는 SLES의 Azure VM에서 SAP NetWeaver의 고가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [SLES의 Azure VM에서 NFS에 대한 고가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) [고가용성 SLES 다중 SID 가이드에 Azure VM에 SAP NetWeaver,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) [SAP 응용 프로그램에 대 한 RHEL에 대 한 Azure VM에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 대 한 SAP NetWeaver에 대 한 고가용성 및 SAP 응용 프로그램에 [대 한 Azure NetApp 파일 에서 Azure NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 다이어그램을 업데이트 하 고 Azure 로드 밸로드서 백 엔드 풀 만들기에 대 한 지침을 명확히
- 2020년 03월 19일: 문서 [퀵스타트의 주요 개정: Azure 가상 머신에서 단일 인스턴스 SAP HANA를 Azure 가상 머신에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) [SAP HANA 설치에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) 수동으로 설치
- 2020년 03월 17일: 더 이상 필요하지 않은 SBD 구성 설정을 제거하기 위해 [Azure의 SUSE Linux 엔터프라이즈 서버에서 페이스메이커 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 변경
- 2020년 03월 16일: [Azure 배포에 지원되는 SAP 소프트웨어의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) SAP HANA IaaS 인증 플랫폼의 열 인증 시나리오 설명
- 2020년 03/11: DBMS 인스턴스 지원당 여러 데이터베이스를 명확히 하기 위해 [지원되는 Azure 가상 머신 시나리오에서 SAP 워크로드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) 변경
- 2020년 03/11: 1세대 및 2세대 VM을 설명하는 [SAP NetWeaver에 대한 Azure 가상 머신 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 변경
- 2020년 03월 10일: ANF의 실제 기존 처리량 제한을 명확히 하기 위해 [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 변경
- 03/09/2020: [SAP 응용 프로그램에 대 한 SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 Azure NetWeaver에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)대 한 고가용성 변경, [SAP 응용 프로그램에 대 한 Azure NetApp 파일 Azure NetApp 서버에서 Azure NetWeaver에 대 한 고가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 대 한 고가용성, SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) [Azure에서 SUSE Linux 엔터프라이즈 서버의 페이스 메이커 업,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) [페이스 메이커가있는 SUSE Linux 엔터프라이즈 서버의 Azure VM에서 IBM Db2 LUW의 고가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) [SUSE Linux 엔터프라이즈 서버의 Azure VM에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 및 [SLES 멀티 SID가이드에서 Azure VM의 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 리소스 에이전트 Azure-lb로 클러스터 리소스를 업데이트합니다. 
- 2020년 03월 05일: [Azure NetWeaver에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 대한 Azure 가상 머신 계획 및 구현에서 Azure 지역 및 Azure 가상 시스템에 대한 구조 변경 및 콘텐츠 변경
- 2020년 03월 03일: [SAP 응용 프로그램에 대한 ANF를 사용하여 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 변경으로 보다 효율적인 ANF 볼륨 레이아웃으로 변경
- 2020년 03월 01일: [Azure 가상 머신에서 SAP HANA에 대한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) 백업 가이드를 재작업하여 Azure 백업 서비스를 포함합니다. [파일 수준에서 SAP HANA Azure Backup의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) 콘텐츠를 줄이고 압축한 후 디스크 스냅숏을 통해 백업을 처리하는 세 번째 문서를 삭제했습니다. Azure 가상 컴퓨터에서 SAP HANA에 대한 백업 가이드에서 콘텐츠를 처리합니다. 
- 2020년 02/27: [SAP 응용 프로그램의 SLES용 SLES용 Azure VM에서 SAP NW의 SAP NW에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)변경, [SAP 응용 프로그램에 대한 ANF가 있는 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 및 [SLES 다중 SID 가이드의 Azure VM에서 SAP NetWeaver에 대한 고가용성으로](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) "실패" 클러스터 매개 변수를 조정합니다.
- 2020년 02월 26일: [Azure에서 HANA에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 대한 파일 시스템 선택을 명확히 하기 위해 SAP HANA Azure 가상 머신 스토리지 구성 변경
- 2020년 02/26: RHEL 다중 SID 가이드의 Azure VM에서 SAP NetWeaver용 HA에 대한 링크를 포함하도록 [SAP에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) 대한 고가용성 아키텍처 및 시나리오 변경
- 02/26/2020: [SAP 응용 프로그램에 대 한 SLES에 Azure VM에 SAP NW에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)대 한 고가용성 변경, [SAP 응용 프로그램에 대 한 ANF와 SLES에 Azure VM에 대 한 SAP NW에 대 한 높은 가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [RHEL에 SAP NetWeaver에 대 한 Azure VM 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 및 Azure VM 높은 가용성 에 대한 SAP NetWeaver 에 대한 [SAP NetW높은 가용성 SAP NetApp 에 대한 SAP NetApp 지원되는 SMS는](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 다중 SAPP 파일을 제거하지 않습니다.
- 2020년 02/26: SUSE 다중 SID 클러스터 가이드에 대한 링크를 추가하는 [RHEL 다중 SID 가이드의 Azure VM에서 SAP NetWeaver용 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) 릴리스
- 2020년 02월 25일: [SAP가](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) 최신 HA 문서에 대한 링크를 추가하기 위한 고가용성 아키텍처 및 시나리오 변경
- 2020년 02/25: [Pacemaker를 사용하는 SUSE Linux 엔터프라이즈 서버의 Azure VM에서 IBM Db2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) 변경으로 표준 Azure 로드 밸런서를 사용하여 공용 끝점에 대한 액세스를 설명하는 문서를 가리킵니다.
- 2020년 02월 21일: [SAP 워크로드에 대한 SAP ASE Azure 가상 시스템 DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) 문서의 전체 개정
- 2020년 02월 21일: [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 변경으로 /hana/data에 대한 스트라이프 크기의 새로운 권장 사항 및 I/O 스케줄러 설정 추가
- 2020년 02월 21일: S224 및 S224m의 새로 인증된 SUS를 나타내는 HANA 대형 인스턴스 문서 변경
- 2020년 02/21: RhEL 및 Azure [NetWeaver에서 SAP NetWeaver의 SAP NetWeaver에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 대한 Azure VM 고가용성 변경 [Azure NetApp 파일을 사용하여](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) Enqueue 서버 복제 2 아키텍처(ENSA2)에 대한 클러스터 제약 조건을 조정합니다.
- 2020년 02/20: [SLES 다중 SID 가이드에서 Azure VM의 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 변경
- 2020년 02/13: 새 문서에 대한 링크를 구현하기 [위한 SAP NetWeaver의 Azure 가상 컴퓨터 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 변경
- 2020년 02/13: [Azure 가상 머신 지원 시나리오에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) 새 문서 SAP 워크로드 추가
- 2020년 02/13: [Azure 배포에 지원되는 SAP 소프트웨어에 대한](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 새 문서 추가
- 2020년 02/13: [Red Hat 엔터프라이즈 Linux 서버의 Azure VM에서 IBM Db2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) 변경으로 표준 Azure 로드 밸런서를 사용하여 공용 끝점에 대한 액세스를 설명하는 문서를 가리킵니다.
- 2020년 02월 13일: [Microsoft Azure에서 실행 중인 SAP 인증 및 구성에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications) 새 VM 유형을 추가합니다.
- 2020년 02/13: Azure에서 새 SAP 지원 노트 추가 [SAP 워크로드: 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 2020년 02/13: [RhEL및 Azure NetWeaver에서 SAP NetWeaver의 SAP NetWeaver에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 대한 Azure VM 고가용성 변경 [Azure NetApp 파일과 함께 RhEL의 SAP NetWeaver에 대한 고가용성으로](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 클러스터 리소스 시간 시간을 Red Hat 시간 단축 권장 사항에 맞게 정렬합니다.
- 2020년 02/11: [Azure 대형 인스턴스 에서 Azure 가상 컴퓨터로의 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration) 릴리스
- 2020년 02/07: [SAP HA 시나리오에서 Azure 표준 ILB를 사용하여 샘플](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) NSG 스크린샷을 업데이트하는 VM에 대한 공용 엔드포인트 연결 변경
- 2020년 02/03: [SAP 응용 프로그램에 대한 SLES용 SAP VM의 SAP NW](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) 에 대한 고가용성 변경 및 SAP 응용 프로그램에 [대한 AnF가 있는 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 변경으로 SLES의 클러스터 노드 호스트 이름에서 대시 사용에 대한 경고를 제거합니다.
- 2020년 01월 28일: SAP HANA 클러스터 리소스 시간 시간을 Red Hat 시간 단축 권장 사항에 맞게 조정하기 위해 [RHEL의 Azure VM에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) 변경
- 2020년 01월 17일: [SAP 응용 프로그램에서 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 변경으로 기존 VM을 근접 배치 그룹으로 이동하는 섹션 변경
- 2020년 01/17: [Azure 가용성 영역을 사용하여 SAP 워크로드 구성 변경으로](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 가용성 영역 간의 대기 시간 측정을 자동화하는 프로시저를 가리킵니다.
- 2020년 01월 16일: AZURE에서 OS 릴리스를 HANA IaaS 하드웨어 디렉토리에 적용하도록 [Azure에서 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) 변경
- 2020년 01/16: [SLES 다중 SID 가이드에서 Azure VM의 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 변경 사항 으로 Enqueue 서버 2 아키텍처(ENSA2)를 사용하여 SAP 시스템에 대한 지침을 추가합니다.
- 2020년 01/10: [SAP HANA 확장 노드에서 SLES의 Azure NetApp 파일이 있는 Azure VM및](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) [RHEL의 Azure NetApp 파일이 있는 Azure NetApp 파일이 있는 SAP HANA 의 대기 노드와 SAP HANA 확장 노드의 변경 사항으로, RHEL에 있는 Azure NetApp 파일이 있는 Azure NetApp 파일이](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 있는 경우 영구적으로 `nfs4_disable_idmapping` 변경하는 방법에 대한 지침을 추가합니다.
- 2020년 01/10: [SAP 응용 프로그램에 대한 Azure NetApp 파일과 SAP 응용 프로그램에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 대한 Azure NetApp 파일이 있는 SLES의 Azure NetWeaver 및 [SAP 응용 프로그램에 대한 Azure NetApp 파일의 Azure NetWeaver에 대한 Azure NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) 변경으로 Azure NetApp 파일 NFSv4 볼륨을 탑재하는 방법에 대한 지침을 추가합니다.
- 2019년 12월 23일: [SLES 다중 SID 가이드의 Azure VM에서 SAP NetWeaver용 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 릴리스
- 2019년 12월 18일: [RHEL에 Azure NetApp 파일이 있는 Azure VM에서 대기 노드를 사용하는 SAP HANA 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 릴리스
- 2019년 11월 21일: [SAP HANA 확장 노드가 Azure VM에서 SUSE Linux 엔터프라이즈 서버의 Azure NetApp 파일을 사용하여](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) NFS ID 매핑에 대한 구성을 단순화하고 라우팅을 단순화하기 위해 권장되는 기본 네트워크 인터페이스를 변경합니다.
- 2019년 11월 15일: [SAP 응용 프로그램에 대한 Azure NetApp 파일이 있는 SUSE Linux 엔터프라이즈 서버의 SAP NetWeaver에 대한 고가용성의](high-availability-guide-suse-netapp-files.md) 사소한 변경 사항및 SAP 응용 프로그램에 [대한 Azure NetApp 파일이 있는 Red Hat Enterprise Linux의 SAP NetWeaver용 고가용성은](high-availability-guide-rhel-netapp-files.md) 용량 풀 크기 제한을 명확히 하고 NFSv3 버전만 지원된다는 진술을 제거합니다.
- 2019년 11월 12일: [Azure NetApp 파일(SMB)이 있는 Windows에서 SAP NetWeaver용 고가용성](high-availability-guide-windows-netapp-files-smb.md) 릴리스
- 11/08/2019: [SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 SAP HANA의 고가용성](sap-hana-high-availability.md)변경, [Azure 가상 머신(VM)에서 SAP HANA 시스템 복제 설정,](sap-hana-high-availability-rhel.md) [SAP 애플리케이션용 SUSE Linux 엔터프라이즈 서버의 SAP NetWeaver용 Azure 가상 머신 고가용성,](high-availability-guide-suse.md) [SUSE Linux 엔터프라이즈 서버의 SAP NetWeaver용 Azure 가상 머신 고가용성 Azure NetApp 파일,](high-availability-guide-suse-netapp-files.md) [Azure NetWeaver에 대한 Azure 가상 머신 고가용성 레드 햇 엔터프라이즈 리눅스에 SAP](high-availability-guide-rhel.md) [NetWeaver에 대 한 고가용성, Azure NetApp 파일과 레드 햇 엔터프라이즈 리눅스에 SAP NetWeaver에](high-availability-guide-rhel-netapp-files.md)대 한 높은 가용성, [SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 NFS에 대 한 고가용성,](high-availability-guide-suse-nfs.md) [SAP 넷 넷에 대 한 Azure VMs에 GlusterFS 표준](high-availability-guide-rhel-glusterfs.md) 로드 밸러커를 권장 합니다.  
- 2019년 11월 08일: 암호화 권장 사항을 명확히 하기 위한 [SAP 워크로드 계획 및 배포 검사 목록의](sap-deployment-checklist.md) 변경 사항  
- 2019년 11월 04일: 유니캐스트 구성으로 클러스터를 직접 만들기 위해 [Azure의 SUSE Linux 엔터프라이즈 서버에서 페이스메이커 를 설정하는](high-availability-guide-suse-pacemaker.md) 변경 사항  
- 2019년 10월 29일: [SAP 고가용성 시나리오에서 Azure 표준 로드 밸런서를 사용하는 가상 시스템에 대한 공용 엔드포인트 연결](high-availability-guide-standard-load-balancer-outbound-connections.md) 릴리스
- 2019년 10월 25일: [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 및 [SAP HANA 확장 노드에서 Azure VM의 대기 노드와 SUSE Linux 엔터프라이즈 서버의 Azure NetApp 파일이](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 적용되어 /hana/공유 볼륨에 대한 NFS 프로토콜을 명확히 합니다.
- 2019년 10월 22일: [SAP 응용 프로그램에 대한 SUSE Linux 엔터프라이즈 서버의 Azure VM에서 SAP NetWeaver의 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)변경, SAP 응용 프로그램에 [대한 Azure NetApp 파일이 있는 SUSE Linux 엔터프라이즈 서버의 Azure VM에서 SAP NetWeaver의 고가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [Azure On AzureNFS용 고가용성 SUSE Linux 엔터프라이즈 서버의 VM,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) [Azure의 SUSE Linux 엔터프라이즈 서버에서 페이스메이커 설정,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) [페이스메이커를 사용하는 SUSE Linux 엔터프라이즈 서버의 Azure VM에서 IBM Db2 LUW의 고가용성,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)Azure 로드 밸런서 감지 강화를 위한 [SUSE Linux 엔터프라이즈 서버의 Azure VM에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [SAP HANA Azure 가상 시스템 저장소 구성에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) ANF 섹션 및 헤더 섹션 변경
- 2019년 10월 21일: [SLES의 Azure NetApp 파일이 있는 Azure VM에서 대기 노드를 사용하는 SAP HANA 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 릴리스
- 10/16/2019: [백업 및 복원에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore) 끊어진된 링크 수정
- 10/16/2019: SLES에서 최소 권장 된 OS 변경 12 SP3 SLES 12 SP4 [에서 IBM Db2 LUW의 고가용성에서 Azure VM에 Azure VM에 SUSE 리눅스 엔터프라이즈 서버와 페이스 메이커](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 2019년 10월 11일: [SAP HANA Azure 가상 머신 스토리지 구성에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 울트라 디스크 스토리지 구성 변경 및 ANF 도입
- 2019년 10월 01일: [SAP 응용 프로그램으로 최적의 네트워크 대기 시간을 위해 Azure 근접 배치 그룹의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 그래픽 변경
- 2019년 10월 01일: [SAP HANA 인프라 구성 및 Azure의 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) 변경으로 /hana/shared에 대해 가용성이 높은 NFS 공유에 대한 문을 수정합니다. 



