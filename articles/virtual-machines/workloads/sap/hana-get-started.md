---
title: Azure 가상 머신에 SAP HANA 설치 | 마이크로소프트 문서'
description: Azure VM에 SAP HANA 설치 가이드
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123357"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Azure 가상 머신에 SAP HANA 설치
## <a name="introduction"></a>소개
이 가이드는 Azure 가상 시스템에 HANA를 성공적으로 배포할 수 있는 올바른 리소스를 가리키는 데 도움이 됩니다. 이 가이드에서는 Azure VM에 SAP HANA를 설치하기 전에 확인해야 하는 문서 리소스를 가리킵니다. 따라서 Azure VM에서 지원되는 SAP HANA 구성으로 종료하는 올바른 단계를 수행할 수 있습니다.  

> [!NOTE]
> 이 가이드에서는 Azure VM에 SAP HANA를 배포하는 방법에 대해 설명합니다. SAP HANA를 HANA 대형 인스턴스에 배포하는 방법에 대한 자세한 내용은 [Azure에서 SAP HANA(대형 인스턴스)를 설치하고 구성하는 방법을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)참조하세요.
 
## <a name="prerequisites"></a>사전 요구 사항
또한 이 가이드에서는 다음과 같은 점을 잘 알고 있다고 가정합니다.
* SAP HANA, SAP NetWeaver 및 온-프레미스에 설치하는 방법
* Azure에서 SAP HANA 및 SAP 응용 프로그램 인스턴스를 설치하고 운영하는 방법
* 다음에 설명된 개념 및 절차:
   * Azure 가상 네트워크 계획 및 Azure 저장소 사용을 포함하는 Azure에서의 SAP 배포 계획입니다. [Azure 가상 머신에서 SAP NetWeaver 참조 - 계획 및 구현 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Azure에 VM을 배포하는 배포 원칙 및 방법 - [SAP용 Azure 가상 시스템 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide) 참조
   * Azure 가상 시스템에 대한 [SAP HANA 고가용성에 설명된 SAP HANA에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) 개념

## <a name="step-by-step-before-deploying"></a>배포 전 단계별
이 섹션에서는 Azure 가상 컴퓨터에서 SAP HANA 설치를 시작하기 전에 수행해야 하는 여러 단계가 나열됩니다. 순서는 수반되며, 이에 따라 다음을 통해 다음을 수행해야 합니다.

1. 가능한 모든 배포 시나리오가 Azure에서 지원되는 것은 아닙니다. 따라서 SAP HANA 배포를 염두에 두고 있는 시나리오에 대해 Azure 가상 컴퓨터 지원 시나리오에서 문서 [SAP 워크로드를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) 확인해야 합니다. 시나리오가 나열되지 않은 경우 테스트되지 않았으며 결과적으로 지원되지 않는다고 가정해야 합니다.
2. SAP HANA 배포에 대한 메모리 요구 사항에 대해 대략적인 아이디어가 있다고 가정할 때 적합한 Azure VM을 찾아야 합니다. SAP NetWeaver인증을 받은 모든 VM이 #1928533 [SAP 지원 노트에](https://launchpad.support.sap.com/#/notes/1928533)설명된 대로 SAP HANA 인증을 받은 것은 아닙니다. SAP HANA 인증 Azure VM에 대한 진실의 근원은 웹 사이트 [SAP HANA 하드웨어 디렉토리입니다.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) **S로** 시작하는 단위는 Azure VM이 아닌 [HANA 대형 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 단위입니다.
3. 다른 Azure VM 유형은 SUSE Linux 또는 Red Hat Linux에 대해 서로 다른 최소 운영 체제 릴리스를 갖습니다. 웹 사이트 [SAP HANA 하드웨어 디렉토리에서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)이 장치의 자세한 데이터를 얻으려면 SAP HANA 인증 단위 목록의 항목을 클릭해야합니다. 지원되는 HANA 워크로드 외에도 SAP HANA에 대해 해당 장치와 함께 지원되는 OS 릴리스가 나열됩니다.
4. 운영 체제 릴리스는 특정 최소 커널 릴리스를 고려해야 합니다. 이러한 최소 릴리스는 다음 SAP 지원 노트에 설명되어 있습니다.
    - [SAP 지원 노트 #2814271 SAP HANA 백업 체크 섬 오류와 Azure에서 실패](https://launchpad.support.sap.com/#/notes/2814271)
    - [타이머 대체로 인한 잠재적 성능 저하를 #2753418 SAP 지원 참고 사항](https://launchpad.support.sap.com/#/notes/2753418)
    - [Azure에서 Hyper-V에 대한 VDSO 지원이 누락되어 성능 이저하되는 #2791572 SAP 지원 사항](https://launchpad.support.sap.com/#/notes/2791572)
4. 선택한 가상 시스템 유형에 대해 지원되는 OS 릴리스를 기반으로 원하는 SAP HANA 릴리스가 해당 운영 체제 릴리스에서 지원되는지 확인해야 합니다. 다른 운영 체제 릴리스와 함께 SAP HANA 릴리스의 지원 매트릭스에 대한 #2235581 SAP [지원 노트를](https://launchpad.support.sap.com/#/notes/2235581) 읽어보십시오.
5. Azure VM 유형, 운영 체제 릴리스 및 SAP HANA 릴리스의 유효한 조합을 찾았을 수 있으므로 SAP 제품 가용성 매트릭스를 체크 인해야 합니다. SAP 가용성 매트릭스에서 SAP HANA 데이터베이스에 대해 실행하려는 SAP 제품이 지원되는지 여부를 확인할 수 있습니다.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>단계별 VM 배포 및 게스트 OS 고려 사항
이 단계에서는 HANA를 설치하고 설치 후 선택한 운영 체제를 최적화하기 위해 VM을 배포하는 단계를 거쳐야 합니다.

1. Azure 갤러리에서 기본 이미지를 선택했습니다. SAP HANA에 대한 자체 운영 체제 이미지를 구축하려면 성공적인 SAP HANA 설치에 필요한 모든 다른 패키지를 알아야 합니다. 그렇지 않으면 Azure 이미지 갤러리에서 SAP 또는 SAP HANA에 대한 SUSE 및 Red Hat 이미지를 사용하는 것이 좋습니다. 이러한 이미지에는 성공적인 HANA 설치에 필요한 패키지가 포함됩니다. 운영 체제 공급자와의 지원 계약에 따라 자신의 라이선스를 가져오는 이미지를 선택해야 합니다. 또는 지원이 포함된 OS 이미지를 선택합니다.
2. 자신의 라이선스를 가져와야 하는 게스트 OS 이미지를 선택한 경우 최신 패치를 다운로드하여 적용할 수 있도록 구독에 OS 이미지를 등록해야 합니다. 이 단계는 공용 인터넷 액세스가 필요합니다. Azure의 SMT 서버와 같은 개인 인스턴스를 설정하지 않는 한
3. VM의 네트워크 구성을 결정합니다. Azure 에서 [문서 SAP HANA 인프라 구성 및 작업에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)자세한 내용을 읽을 수 있습니다. Azure의 가상 네트워크 카드에 할당할 수 있는 네트워크 처리량 할당량이 없습니다. 따라서 다른 vNI를 통해 트래픽을 전달하는 유일한 목적은 보안 고려 사항을 기반으로 합니다. 여러 vNI를 통한 트래픽 라우팅의 복잡성과 보안 측면에서 적용되는 요구 사항 간에 지원 가능한 절충안을 찾을 수 있다고 믿습니다.
3. VM이 배포되고 등록되면 운영 체제에 최신 패치를 적용합니다. 사용자 고유의 구독으로 등록했습니다. 또는 운영 체제 가 포함된 이미지를 선택한 경우 VM은 이미 패치에 액세스할 수 있어야 합니다. 
4. SAP HANA에 필요한 곡을 적용합니다. 이러한 곡은 이러한 SAP 지원 노트에 나열되어 있습니다.

    - [SAP 지원 노트 #2694118 - 레드 햇 엔터프라이즈 리눅스 HA 추가 기능 Azure에](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP 지원 노트 #1984787 - SUSE 리눅스 엔터프라이즈 서버 12: 설치 노트](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP 지원 노트 #2578899 - SUSE 리눅스 엔터프라이즈 서버 15: 설치 노트](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP 지원 노트 #2002167 - 레드 햇 엔터프라이즈 리눅스 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/0002292690)(영문) 
    -  [SAP 지원 노트 #2772999 - 레드 햇 엔터프라이즈 리눅스 8.x: 설치 및 구성](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP 지원 노트 #2777782 - SAP HANA DB: RHEL 8에 권장되는 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP 지원 노트 #2455582 - 리눅스 : GCC 6.x로 컴파일 된 SAP 응용 프로그램을 실행](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [#2382421 SAP 지원 사항 - HANA 및 OS 수준에서 네트워크 구성 최적화](https://launchpad.support.sap.com/#/notes/2382421)

1. SAP HANA의 Azure 저장소 유형을 선택합니다. 이 단계에서는 SAP HANA 설치를 위한 저장소 레이아웃을 결정해야 합니다. 연결된 Azure 디스크 또는 네이티브 Azure NFS 공유를 사용할 예정입니다. 지원되거나 지원되는 Azure 저장소 유형과 사용할 수 있는 다른 Azure 저장소 형식의 조합은 [SAP HANA Azure 가상 시스템 저장소 구성에 설명되어 있습니다.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 시작점으로 문서화된 구성을 수행합니다. 비프로덕션 시스템의 경우 낮은 처리량 또는 IOPS를 구성할 수 있습니다. 프로덕션 목적을 위해 처리량과 IOPS를 좀 더 구성해야 할 수 있습니다.
2. M-시리즈 또는 Mv2 시리즈 VM을 사용할 때 DBMS 트랜잭션 로그를 포함하거나 로그를 다시 실행하려는 볼륨에 대해 [Azure Write 가속기를](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) 구성했는지 확인합니다. 문서화된 대로 액셀러레이터 쓰기의 제한 사항에 유의하십시오.
2. 배포된 VM에서 [Azure 가속 네트워킹이](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 활성화되어 있는지 확인합니다.

> [!NOTE]
> 다른 수액 조정 프로필의 모든 명령이나 메모에 설명된 대로 Azure에서 성공적으로 실행될 수 있는 것은 아닙니다. VM의 전원 모드를 조작하는 명령은 일반적으로 기본 Azure 호스트 하드웨어의 전원 모드를 조작할 수 없으므로 오류와 함께 반환됩니다.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Azure 가상 머신에 대한 단계별 준비
Azure 세부 사항 중 하나는 SAP 호스트 에이전트에 대한 모니터링 데이터를 제공하는 Azure VM 확장의 설치입니다. 이 모니터링 확장의 설치에 대한 자세한 내용은 다음과 같습니다.

-  [SAP 참고 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) Azure에서 Linux VM을 통한 SAP 향상된 모니터링에 대해 설명합니다. 
-  [SAP 참고 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) 리눅스에서 SAPOSCOL에 대 한 정보를 설명 합니다. 
-  [SAP Note 2178632는](https://launchpad.support.sap.com/#/notes/2178632/E) Microsoft Azure에서 SAP에 대한 주요 모니터링 메트릭에 대해 설명합니다.
-  [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 설치
Azure 가상 시스템이 배포되고 운영 체제가 등록 및 구성되면 SAP 설치에 따라 SAP HANA를 설치할 수 있습니다. 이 설명서를 시작하기에 좋은 시작으로 이 SAP 웹 사이트 [HANA 리소스로](https://www.sap.com/products/hana/implementation/resources.html) 시작하십시오.

Azure Premium Storage 또는 Ultra 디스크의 직접 연결된 디스크를 사용하는 SAP HANA 확장 구성의 경우 [Azure에서 문서 SAP HANA 인프라 구성 및 작업의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out) 세부 사항을 읽어보십시오.


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA 백업을 위한 추가 리소스
Azure VM에서 SAP HANA 데이터베이스를 백업하는 방법에 대한 자세한 내용은 다음을 참조하십시오.
* [Azure Virtual Machines의 SAP HANA 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>다음 단계
설명서 읽기:

- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





