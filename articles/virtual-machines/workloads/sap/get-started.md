---
title: Azure VM에서 SAP 솔루션 시작 | Microsoft Docs
description: Microsoft Azure의 VM(가상 머신)에서 실행되는 SAP 솔루션에 대한 자세한 정보
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40ed06bef45948068e3845e728d9c1d63ed62e71
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762806"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>SAP 워크로드 시나리오 호스팅 및 실행에 Azure 사용

Microsoft Azure를 선택하면 중요 업무용 SAP 워크로드 및 시나리오를 확장 가능하고 규정을 준수하는 엔터프라이즈 입증 플랫폼에서 안정적으로 실행할 수 있습니다.  Azure의 확장성, 유연성 및 비용 절감을 활용하세요. Microsoft와 SAP 사이의 확장된 파트너 관계 덕분에 Azure에서 개발/테스트 및 프로덕션 시나리오 전체에서 SAP 애플리케이션을 실행할 수 있으며 완전한 지원을 받을 수 있습니다. SAP NetWeaver에서 SAP S/4HANA, SAP BI까지, Linux에서 Windows까지, SAP HANA에서 SQL까지 모두 지원됩니다.

Azure에 다양한 DBMS가 있는 SAP NetWeaver 시나리오 호스팅 외에, Azure의 SAP BI와 같은 다른 다양한 SAP 워크로드 시나리오를 호스팅할 수 있습니다. 

SAP HANA용 Azure의 고유한 특성은 Azure를 경쟁에서 차별화하는 고유한 요소입니다. SAP HANA와 관련된 메모리 및 CPU 리소스 요구량이 높은 SAP 시나리오를 호스팅하기 위해 Azure는 S/4HANA 또는 다른 SAP HANA 워크로드를 위해 최대 24TB(120TB 스케일 아웃)의 메모리가 필요한 SAP HANA 배포를 실행할 용도로 고객 전용 베어 메탈 하드웨어 사용을 제안합니다. 이 고유한 Azure의 SAP HANA Azure 솔루션(큰 인스턴스)을 통해 SAP 애플리케이션 레이어 또는 워크로드 미들웨어 레이어를 네이티브 Azure Virtual Machines에 호스팅하면서 전용 베어 메탈 하드웨어에서 SAP HANA를 실행할 수 있습니다. 이 솔루션은 “Azure의 SAP HANA(큰 인스턴스)” 섹션의 여러 문서에 설명되어 있습니다.   

Azure의 SAP 워크로드 호스팅 시나리오에서는 Azure Activity Directory를 사용한 다양한 SAP 구성 요소 및 SAP SaaS 또는 PaaS 제품에 대한 ID 통합 및 Single-Sign-On 요구 사항도 발생할 수 있습니다. Azure Active Directory(AAD) 및 SAP 엔터티를 사용한 이러한 통합 및 Single-Sign-On 시나리오 목록은 "AAD SAP ID 통합 및 Single-Sign-On” 섹션에 설명 및 문서화되어 있습니다.

## <a name="latest-changes"></a>최신 변경 내용

릴리스의 [Azure HANA Large Instances는 Azure portal을 통해 제어](hana-li-portal.md)

릴리스의 [SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](high-availability-guide-suse-netapp-files.md)

하는 바를 확실히 **Linux OS 매개 변수 net.ipv4.tcp_timestamps** 부하 분산 장치를 Azure와 함께에서 설정

[Azure 가용성 영역을 사용하여 SAP 워크로드 구성](sap-ha-availability-zones.md) 릴리스

[SAP 워크로드 계획 및 배포 검사 목록](sap-deployment-checklist.md)의 릴리스




## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA

일련의 설명서를 통해 Azure의 SAP HANA(대규모 인스턴스) 또는 약식 HANA 대규모 인스턴스에 대해 알아봅니다. 문서는 HANA 대규모 인스턴스의 나열된 영역을 다룹니다.

- [Azure의 SAP HANA(대규모 인스턴스) 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure의 SAP HANA(대규모 인스턴스) 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Azure의 SAP HANA(대규모 인스턴스)에 대한 인프라 및 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Azure의 SAP HANA(대규모 인스턴스) 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Azure의 SAP HANA(대규모 인스턴스)에 대한 고가용성 및 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Azure의 SAP HANA(대규모 인스턴스)에 대한 문제 해결 및 모니터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

다음 단계:

- [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참고하세요.



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. 전제 조건으로 Azure 계산, 저장소 및 네트워킹에 대한 대부분의 지식을 비롯한 Azure IaaS 기본 서비스를 제공하는 Azure의 주요 서비스와 친숙해야 합니다. 이러한 토픽 중 대부분은 SAP NetWeaver 관련 [Azure 계획 지침](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)에서 다룹니다. 

Azure의 HANA 관련 설명서는 문서 및 해당 하위 문서의 목록으로 이루어져 있습니다.

- [빠른 시작: Azure VM에서 단일 인스턴스 SAP HANA 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure에서 SAP S/4HANA 또는 BW/4HANA 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [단일 Azure 지역 내 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 가상 머신에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure Virtual Machines의 SAP HANA 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [저장소 스냅숏에 기반한 SAP HANA 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure Virtual Machines에서 배포된 SAP NetWeaver
이 섹션에서는 Azure의 SAP NetWeaver 및 Business One에 대한 계획 및 배포 설명서를 찾을 수 있습니다. 이 챕터의 설명서는 Azure에서 SAP 워크로드를 사용하는 비 HANA 데이터베이스의 기본 사항 및 사용량에 중점을 둡니다. 반면 HA에 대한 문서는 Azure의 HANA 고가용성에 대한 기반이기도 합니다. 문서는 다음과 같습니다.

- [Azure Virtual Machines의 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Azure에서 SAP IDES EHP7 SP3 for SAP ERP 6.0 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 실행](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Site Recovery를 사용하여 다중 계층 SAP NetWeaver 애플리케이션 배포 보호](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure용 SAP LaMa 커넥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure의 SAP 워크로드에서 비 HANA 데이터베이스에 관련된 문서 목록은 다음과 같습니다.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM에서 SAP MaxDB, liveCache 및 Content Server 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure의 SAP HANA 데이터베이스는 Azure Virtual Machines에서 SAP HANA 섹션을 확인하세요.

Azure의 SAP 워크로드 고가용성에 대한 항목 문서는 다음과 같습니다.

- [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

항목 문서는 다양한 아키텍처 및 시나리오 문서를 가리킵니다. 후속 시나리오 문서에서는 다른 고가용성 방법의 배포 및 구성을 설명하는 자세한 기술 문서에 대한 링크가 제공됩니다. SAP NetWeaver 워크로드에 대해 고가용성을 설정하고 구성하는 다른 문서에서는 Windows 운영 체제뿐만 아니라 Linux도 다룹니다.


AAD(Azure Active Directory), SAP 서비스와 Single Sign-On 간의 통합에 대한 문서는 다음과 같습니다.

- [자습서: SAP Cloud for Customer와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP NetWeaver와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Business ByDesign과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP HANA와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [사용자의 S/4HANA 환경 – Azure AD로 Fiori Launchpad SAML Single Sign-On](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Azure 서비스를 SAP 구성 요소로 통합하는 문서 목록은 다음과 같습니다.

- [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




