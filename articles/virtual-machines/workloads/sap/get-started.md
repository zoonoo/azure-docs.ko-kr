---
title: Azure VM의 SAP 시작 | Microsoft Docs
description: Microsoft Azure의 VM(가상 머신)에서 실행되는 SAP 솔루션에 대한 자세한 정보
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
ms.date: 05/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b95112146c0003f3fc5ea563e5561dc26ea064e8
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800720"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure를 사용하여 SAP 워크로드 시나리오 호스트 및 실행

Microsoft Azure를 사용하면 확장성 있고 규정을 준수하는 엔터프라이즈 입증 플랫폼에서 중요 업무용 SAP 워크로드 및 시나리오를 안정적으로 실행할 수 있습니다. Azure의 확장성, 유연성 및 비용 절감을 누릴 수 있습니다. Microsoft와 SAP 사이의 확장된 파트너 관계 덕분에 Azure에서 개발과 테스트 및 프로덕션 시나리오 전체에서 SAP 애플리케이션을 실행할 수 있으며 완전한 지원을 받을 수 있습니다. SAP NetWeaver부터 SAP S/4HANA까지, Linux의 SAP BI부터 Windows까지, SAP HANA에서 SQL까지 모두 지원됩니다.

Azure에 다양한 DBMS가 있는 SAP NetWeaver 시나리오 호스트 외에 Azure의 SAP BI와 같은 다양한 SAP 워크로드 시나리오를 호스트할 수 있습니다. 

SAP HANA용 Azure의 고유한 특성은 Azure를 차별화하는 고유한 요소입니다. SAP HANA와 관련된 더 많은 메모리 및 CPU 리소스 수요가 많은 SAP 시나리오 호스트를 지원하기 위해 Azure는 고객 전용 운영 체제 미설치 하드웨어를 제공합니다. 이 솔루션을 사용하면 S/4HANA 또는 기타 SAP HANA 워크로드에 대해 최대 24TB(120TB 스케일 아웃)의 메모리가 있어야 하는 SAP HANA 배포를 실행할 수 있습니다. 

Azure에서 SAP 워크로드 시나리오를 호스트하면 ID 통합 및 Single Sign-On 요구 사항을 만들 수 있습니다. 이 상황은 Azure AD(Azure Active Directory)를 사용하여 다양한 SAP 구성 요소와 SAP SaaS(Software as a Service) 또는 PaaS(Platform as a Service) 제품을 연결하는 경우에 발생할 수 있습니다. Azure AD 및 SAP 엔터티를 사용한 이러한 통합 및 Single Sign-On 시나리오 목록은 “AAD SAP ID 통합 및 Single Sign-On” 섹션에 설명 및 문서화되어 있습니다.

## <a name="changes-to-the-sap-workload-section"></a>SAP 워크로드 섹션에 대한 변경 내용
Azure의 SAP 워크로드 섹션의 문서에 대한 변경 내용은 이 문서의 끝에 나열되어 있습니다. 변경 로그의 항목은 약 180일 동안 유지됩니다.

## <a name="you-want-to-know"></a>자주 하는 질문과 대답
특정 질문이 있을 경우 시작 페이지의 이 섹션에서 특정 문서 또는 흐름으로 안내합니다. 자주 하는 질문과 대답은 다음과 같습니다.

- SAP 소프트웨어 릴리스 및 운영 체제 버전별로 지원되는 Azure VM 및 HANA(대규모 인스턴스) 단위는 무엇인가요? 답변 및 정보를 찾는 과정은 [Azure 배포에 대해 지원되는 SAP 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 문서를 참조하세요.
- Azure VM 및 HANA(대규모 인스턴스)에서 지원되는 SAP 배포 시나리오는 무엇인가요? 지원되는 시나리오에 대한 정보는 다음 문서에서 찾을 수 있습니다.
    - [Azure 가상 머신의 SAP 워크로드 지원 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA(대규모 인스턴스)의 지원되는 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- 여러 Azure 지역에서 사용할 수 있는 Azure 서비스, Azure VM 유형 및 Azure 스토리지 서비스에 대한 정보는 [지역별 제공 제품](https://azure.microsoft.com/global-infrastructure/services/) 사이트에서 확인할 수 있습니다. 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 사용

일련의 설명서를 통해 Azure의 SAP HANA(대규모 인스턴스) 또는 약식 HANA(대규모 인스턴스)에 대해 알아봅니다. HANA(대규모 인스턴스)에 대한 자세한 내용은 [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 문서부터 시작하여 HANA(대규모 인스턴스) 섹션에서 관련 설명서를 참조하세요.



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 가상 머신의 SAP HANA
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. 전제 조건으로 Azure IaaS 기본 서비스를 제공하는 Azure의 주요 서비스를 잘 알고 있어야 합니다. 따라서 Azure 컴퓨팅, 스토리지 및 네트워킹에 대한 지식이 필요합니다. 이러한 주제 중 대부분은 SAP NetWeaver 관련 [Azure 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)에서 다룹니다. 

Azure의 HANA에 대한 자세한 내용은 다음 문서 및 해당 하위 문서를 참조하세요.

- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 가상 머신에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 가상 머신의 SAP HANA Backup 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 가상 머신에서 배포된 SAP NetWeaver
이 섹션에서는 Azure의 SAP NetWeaver 및 Business One에 대한 계획 및 배포 설명서를 나열합니다. 이 설명서에서는 Azure의 SAP 워크로드에서 HANA가 아닌 데이터베이스의 기본 사항 및 사용에 중점을 둡니다. 고가용성에 대한 문서는 다음과 같은 Azure의 HANA 고가용성에 대한 기반이기도 합니다.

- [Azure 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [Azure 가상 머신의 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Site Recovery를 사용하여 다중 계층 SAP NetWeaver 애플리케이션 배포 보호](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure용 SAP LaMa 커넥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure의 SAP 워크로드에서 HANA가 아닌 데이터베이스에 대한 자세한 내용은 다음을 참조하세요.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM에서 SAP MaxDB, Live Cache 및 Content Server 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure의 SAP HANA 데이터베이스에 대한 내용은 “Azure 가상 머신에서 SAP HANA 섹션”을 확인하세요.

Azure에서 SAP 워크로드의 고가용성에 대한 자세한 내용은 다음을 참조하세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

이 문서는 다양한 아키텍처 및 시나리오 문서를 가리킵니다. 이후 시나리오 문서에서는 다른 고가용성 방법의 배포 및 구성을 설명하는 자세한 기술 문서에 대한 링크가 제공됩니다. SAP NetWeaver 워크로드에 대해 고가용성을 설정하고 구성하는 방법을 보여 주는 다른 문서에서는 Windows 운영 체제 및 Linux도 다룹니다.


Azure AD(Azure Active Directory), SAP 서비스와 Single Sign-On 간의 통합에 대한 내용은 다음을 참조하세요.

- [자습서: SAP Cloud for Customer와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP NetWeaver와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Business ByDesign과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP HANA와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 환경: Azure AD로 Fiori Launchpad SAML Single Sign-On](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

SAP 구성 요소에 Azure 서비스를 통합하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>변경 로그

- 2020년 5월 21일: [Azure에서 SLES의 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 및 [Azure에서 RHEL의 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)을 변경하여 [SAP HA 시나리오에서 Azure 표준 ILB를 사용한 VM의 공용 엔드포인트 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)에 링크 추가  
- 2020년 5월 19일: [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)에서 HANA 관련 볼륨에 대해 LVM을 사용하는 경우 루트 볼륨 그룹을 사용하지 않도록 중요한 메시지 추가
- 2020년 5월 19일: HANA(대규모 인스턴스) 유형 II에 대해 지원되는 새 OS[HANA(대규모 인스턴스)에 대한 호환 운영 체제] 추가(https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)
- 2020년 5월 12일: [SAP HA 시나리오에서 Azure 표준 ILB를 사용하여 VM에 대한 공용 엔드포인트 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)을 변경하여 링크를 업데이트하고 타사 방화벽 구성에 대한 정보 추가
- 2020년 5월 11일: [SLES의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)을 변경하여 보다 간소화된 장애 조치(failover)가 되도록 netcat 리소스에 대한 리소스 연결 유지를 0으로 설정 
- 2020년 5월 5일: Gen2 배포를 Mv1 VM 제품군에 사용할 수 있음을 표현하도록 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현 내용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 변경
- 2020년 4월 24일: [SLES의 ANF를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse), [RHEL의 ANF를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel), [ANF를 사용한 SLES의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 및 [ANF를 사용한 RHEL의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)을 변경하여 ANF 볼륨의 IP 주소는 자동으로 할당됨을 명확하게 설명
- 2020년 4월 22일: 클러스터를 유지 관리 모드 내외에 배치하는 것과 충돌하므로 [SLES의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)을 변경하여 지침에서 메타 특성 `is-managed`를 제거
- 2020년 4월 21일: SQL Azure DB를 SAP(Hybris) Commerce Platform 1811 이상에 대해 지원되는 DBMS로 문서 [Azure 배포를 지원하는 SAP 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 및 [Microsoft Azure에서 실행되는 SAP 인증 및 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)에 추가
- 2020년 4월 16일: SAP HANA를 SAP(Hybris) Commerce Platform에 대해 지원되는 DBMS로 문서 [Azure 배포를 지원하는 SAP 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 및 [Microsoft Azure에서 실행되는 SAP 인증 및 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)에 추가
- 2020년 4월 13일: [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)에서 정확한 SAP ASE 릴리스 번호로 수정
- 2020년 4월 7일: [Azure에서 SLES의 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)을 변경하여 cloud-netconfig 지침을 명확하게 설명
- 2020년 4월 6일: [SLES의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 및 [RHEL의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)을 변경하여 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf)에 대한 참조를 제거([TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)로 변경함)
- 2020년 3월 31일: [SLES의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 및 [RHEL의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)을 변경하여 스트라이프 볼륨을 만들 때 스트라이프 크기를 지정하는 방법에 대한 지침을 추가
- 2020년 3월 27일: [SAP 애플리케이션용 ANF를 사용한 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)을 변경하여 파일 시스템 탑재 옵션을 NetApp TR-4746으로 맞춤(동기화 탑재 옵션 제거)
- 2020년 3월 26일: [SLES 멀티 SID 가이드의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)을 변경하여 NetApp TR-4746에 대한 참조를 추가
- 2020년 3월 26일: [SAP 애플리케이션용 SLES의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [SAP 애플리케이션용 Azure NetApp Files를 사용한 SLES의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [SLES의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [SLES 멀티 SID 가이드의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid), [SAP 애플리케이션용 RHEL의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel), [SAP 애플리케이션용 Azure NetApp Files를 사용한 RHEL의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)을 변경하여 다이어그램을 업데이트하고 Azure Load Balancer 백 엔드 풀 만들기에 대한 지침을 명확하게 설명
- 2020년 3월 19일: 문서 [빠른 시작: Azure Virtual Machines에서 단일 인스턴스 SAP HANA 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)를 [Azure Virtual Machines에 SAP HANA 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)로 주요하게 수정
- 2020년 3월 17일: [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)에서 더 이상 필요하지 않은 SBD 구성 설정을 제거
- 2020년 3월 16일: [Azure 배포를 지원하는 SAP 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)에서 SAP HANA IaaS 인증 플랫폼의 열 인증 시나리오에 대한 명확한 설명
- 2020년 3월 11일: [Azure 가상 머신의 SAP 워크로드 지원 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)를 변경하여 DBMS 인스턴스당 여러 데이터베이스를 명확하게 설명
- 2020년 3월 11일: 1세대 및 2세대 VM을 설명하는 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)을 변경
- 2020년 3월 10일: [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)을 변경하여 ANF의 실제 기존 처리량 한도를 명확히 설명
- 2020년 3월 9일: [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [SAP 애플리케이션용 Azure NetApp Files를 사용한 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Pacemaker를 사용한 SUSE Linux Enterprise Server의 Azure VM에 있는 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), [SUSE Linux Enterprise Server의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) 및 [SLES 멀티 SID 가이드의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)을 변경하여 리소스 에이전트 azure-lb로 클러스터 리소스 업데이트 
- 2020년 3월 5일: [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)에서 Azure 지역 및 Azure Virtual Machines에 대한 구조체 변경 및 콘텐츠 변경
- 2020년 3월 3일: [SAP 애플리케이션용 ANF를 사용한 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)을 변경하여 보다 효율적인 ANF 볼륨 레이아웃으로 변경
- 2020년 3월 1일: [Azure Virtual Machines의 SAP HANA 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)를 재작성하여 Azure Backup 서비스를 추가함. [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) 콘텐츠를 축소 및 압축하고 디스크 스냅샷을 통해 백업을 처리하는 세 번째 문서를 삭제함. 콘텐츠는 Azure Virtual Machines의 SAP HANA Backup 가이드에서 다룸 
- 2020년 2월 27일: [SAP 애플리케이션용 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [SAP 애플리케이션용 ANF를 사용한 SLES의 Azure VM에서 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 및 [SLES 멀티 SID 가이드의 Azure VM에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)을 변경하여 “on fail” 클러스터 매개 변수를 조정
- 2020년 2월 26일: [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)을 변경하여 Azure의 HANA용 파일 시스템 선택을 명확히 설명
- 2020년 2월 26일: [SAP의 고가용성 아키텍처 및 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)를 변경하여 RHEL 다중 SID 가이드의 Azure VM에서 SAP NetWeaver에 대한 HA 링크를 포함함
- 2020년 2월 26일: [SAP 애플리케이션용 SLES의 Azure VM에 있는 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [SAP 애플리케이션용 ANF를 사용한 SLES의 Azure VM에 있는 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [RHEL에 있는 SAP NetWeaver의 Azure VM 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 및 [Azure NetApp Files를 사용한 RHEL에 있는 SAP NetWeaver의 Azure VM 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)을 변경하여 멀티 SID ASCS/ERS 클러스터가 지원되지 않는다는 설명을 제거
- 2020년 2월 26일: [RHEL 멀티 SID 가이드의 Azure VM에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)의 릴리스로 SUSE 다중 SID 클러스터 가이드에 링크 추가
- 2020년 2월 25일: [SAP의 고가용성 아키텍처 및 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)를 변경하여 최신 HA 문서에 대한 링크를 추가
- 2020년 2월 25일: [Pacemaker를 사용한 SUSE Linux Enterprise Server의 Azure VM에 있는 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)을 변경하여 표준 Azure 부하 분산 장치를 사용한 공용 엔드포인트에 대한 액세스를 설명하는 문서를 가리키도록 함
- 2020년 2월 21일: [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) 문서의 완전 수정
- 2020년 2월 21일: [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)을 변경하여 /hana/data의 스트라이프 크기에 대한 새로운 권장 사항을 표시하고 I/O 스케줄러 설정을 추가
- 2020년 2월 21일: HANA(대규모 인스턴스) 문서를 변경하여 S224 및 S224m의 새로 인증된 SKU를 표시
- 2020년 2월 21일: [RHEL에 있는 SAP NetWeaver의 Azure VM 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 및 [Azure NetApp Files를 사용한 RHEL에 있는 SAP NetWeaver의 Azure VM 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)을 변경하여 큐에 넣기 서버 복제 2 아키텍처(ENSA2)에 대한 클러스터 제약 조건을 조정
- 2020년 2월 20일: [SLES 멀티 SID 가이드의 Azure VM에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)을 변경하여 SUSE 다중 SID 클러스터 가이드에 링크 추가
- 2020년 2월 13일: [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)을 변경하여 새 문서의 링크 구현
- 2020년 2월 13일: 새 문서 [Azure 가상 머신의 SAP 워크로드 지원 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) 추가
- 2020년 2월 13일: 새 문서 [Azure 배포를 지원하는 SAP 소프트웨어](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) 추가
- 2020년 2월 13일: [Red Hat Enterprise Linux Server의 Azure VM에 있는 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)을 변경하여 표준 Azure 부하 분산 장치를 사용한 공용 엔드포인트에 대한 액세스를 설명하는 문서를 가리키도록 함
- 2020년 2월 13일: 새 VM 유형을 [Microsoft Azure에서 실행되는 SAP 인증 및 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)에 추가
- 2020년 2월 13일: 새 SAP 지원 정보 [Azure의 SAP 워크로드: 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 추가
- 2020년 2월 13일: [RHEL에 있는 SAP NetWeaver의 Azure VM 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) 및 [Azure NetApp Files를 사용한 RHEL에 있는 SAP NetWeaver의 Azure VM 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)을 변경하여 클러스터 리소스 시간 제한을 Red Hat 시간 제한 권장 사항에 맞춤
- 2020년 2월 11일: [Azure Virtual Machines로 Azure의 SAP HANA(대규모 인스턴스) 마이그레이션](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration) 릴리스
- 2020년 2월 7일: [SAP HA 시나리오에서 Azure 표준 ILB를 사용하여 VM에 대한 공용 엔드포인트 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)을 변경하여 샘플 NSG 스크린샷 업데이트
- 2020년 2월 3일: [SAP 애플리케이션용 SLES의 Azure VM에 있는 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) 및 [SAP 애플리케이션용 ANF를 사용한 SLES의 Azure VM에 있는 SAP NW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)을 변경하여 SLES에서 클러스터 노드의 호스트 이름에 대시를 사용하는 것에 대한 경고를 제거
- 2020년 1월 28일: [RHEL의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)을 변경하여 SAP HANA 클러스터 리소스 시간 제한을 Red Hat 시간 제한 권장 사항에 맞춤
- 2020년 1월 17일: [SAP 애플리케이션 네트워크 대기 시간을 최소화하는 Azure Proximity Placement Group](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)을 변경하여 기존 VM을 근접 배치 그룹으로 이동 섹션을 변경
- 2020년 1월 17일: [Azure 가용성 영역을 사용하여 SAP 워크로드 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)을 변경하여 가용성 영역 간의 대기 시간 측정을 자동화하는 프로시저를 가리킴
- 2020년 1월 16일: [Azure에서 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)을 변경하여 OS 릴리스를 HANA IaaS 하드웨어 디렉터리에 맞게 조정
- 2020년 1월 16일: [SLES 멀티 SID 가이드의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)을 변경하여 큐에 넣기 서버 복제 2 아키텍처(ENSA2)를 사용하도록 SAP에 대한 지침 추가
- 2020년 1월 10일: [SLES의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 및 [RHEL의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)을 변경하여 `nfs4_disable_idmapping` 변경을 영구적으로 만드는 방법에 관한 지침을 추가
- 2020년 1월 10일: [SAP 애플리케이션용 Azure NetApp Files를 사용한 SLES의 Azure VM에 있는 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 및 [SAP 애플리케이션용 Azure NetApp Files를 사용한 RHEL에 있는 SAP NetWeaver의 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)을 변경하여 Azure NetApp Files NFSv4 볼륨을 탑재하는 방법에 대한 지침 추가
- 2019년 12월 23일: [SLES 멀티 SID 가이드의 Azure VM에서 SAP NetWeaver의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)의 릴리스
- 2019년 12월 18일: [RHEL의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)의 릴리스
- 2019년 11월 21일: [SUSE Linux Enterprise Server의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)을 변경하여 NFS ID 매핑에 대한 구성을 단순화하고 라우팅을 단순화하기 위해 권장 주요 네트워크 인터페이스를 변경
- 2019년 11월 15일: [SAP 애플리케이션용 Azure NetApp Files를 사용한 SUSE Linux Enterprise Server에 있는 SAP NetWeaver의 고가용성](high-availability-guide-suse-netapp-files.md) 및 [SAP 애플리케이션용 Azure NetApp Files를 사용한 Red Hat Enterprise Linux에 있는 SAP NetWeaver의 고가용성](high-availability-guide-rhel-netapp-files.md)을 약간 변경하여 용량 풀 크기 제한 사항을 명확히 설명하고 NFSv3 버전만 지원된다는 설명 제거
- 2019년 11월 12일: [Azure NetApp Files(SMB)를 사용한 Windows의 SAP NetWeaver 고가용성](high-availability-guide-windows-netapp-files-smb.md)의 릴리스
- 2019년 11월 8일: [SUSE Linux Enterprise Server의 Azure VM에 있는 SAP HANA의 고가용성](sap-hana-high-availability.md), [Azure VM(가상 머신)에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability-rhel.md), [SAP 애플리케이션용 SUSE Linux Enterprise Server에 있는 SAP NetWeaver의 Azure Virtual Machines 고가용성](high-availability-guide-suse.md), [Azure NetApp Files를 사용한 SUSE Linux Enterprise Server에 있는 SAP NetWeaver의 Azure Virtual Machines 고가용성](high-availability-guide-suse-netapp-files.md), [Red Hat Enterprise Linux에 있는 SAP NetWeaver의 Azure Virtual Machines 고가용성](high-availability-guide-rhel.md), [Azure NetApp Files를 사용한 Red Hat Enterprise Linux에 있는 SAP NetWeaver의 Azure Virtual Machines 고가용성](high-availability-guide-rhel-netapp-files.md), [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](high-availability-guide-suse-nfs.md), [SAP NetWeaver용 Red Hat Enterprise Linux에 있는 Azure VM의 GlusterFS](high-availability-guide-rhel-glusterfs.md)를 변경하여 Azure 표준 부하 분산 장치를 권장  
- 2019년 11월 8일: [SAP 워크로드 계획 및 배포 검사 목록](sap-deployment-checklist.md)을 변경하여 암호화 권장 사항을 명시  
- 2019년 11월 4일: [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](high-availability-guide-suse-pacemaker.md)을 변경하여 유니캐스트 구성으로 직접 클러스터를 만들도록 함  


