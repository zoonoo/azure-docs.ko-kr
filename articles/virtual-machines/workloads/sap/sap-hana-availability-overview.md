---
title: Azure VM의 SAP HANA 가용성 - 개요 | Microsoft Docs
description: Azure 기본 VM에서 SAP HANA 운영
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA 고가용성 가이드
Azure는 Azure VM에서 SAP HANA와 같은 업무상 중요한 데이터베이스를 배포할 수 있는 다양한 기능을 제공합니다. 이 문서에서는 Azure Virtual Machines에서 호스트되는 SAP HANA 인스턴스에 대한 가용성을 달성하는 방법에 대한 지침을 제공합니다. 이 문서에서는 Azure의 SAP HANA 가용성을 높이기 위해 Azure 인프라에서 구현될 수 있는 몇 가지 시나리오에 대해 설명합니다. 

## <a name="prerequisites"></a>필수 조건
여기서는 다음과 같은 Azure의 IaaS(Infrastructure as a Service) 기본 사항에 대해 잘 알고 있다고 가정합니다. 

- Azure Portal 또는 PowerShell을 통해 가상 머신 또는 가상 네트워크를 배포하는 방법
- JSON(JavaScript Object Notification) 템플릿을 사용할 수 있는 옵션을 포함한 Azure 플랫폼 간 CLI(명령줄 인터페이스)

또한 이 가이드는 사용자가 SAP HANA 인스턴스를 설치하고 SAP HANA 인스턴스를 관리 및 운영하는 데 익숙하다고 가정합니다. 여기에는 HANA 시스템 복제와 관련된 설정 및 운영 작업이나, SAP HANA 데이터베이스의 백업 및 복원과 같은 태스크가 포함됩니다.

Azure에서 SAP HANA 항목에 대한 개요를 제공하는 다른 문서는 다음과 같습니다.

- [Azure VM에서 단일 인스턴스 SAP HANA 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure VM에서 SAP HANA 시스템 복제 설정](sap-hana-high-availability.md)
- [Azure Virtual Machines의 SAP HANA 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

다음과 같이 사용자가 친숙하면 도움이 되는 SAP HANA에 대한 문서 및 콘텐츠도 제공될 수 있습니다.

- [SAP HANA의 고가용성](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [FAQ: SAP HANA의 고가용성](https://archive.sap.com/documents/docs/DOC-66702)
- [SAP HANA에 대해 시스템 복제를 수행하는 방법](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 새로운 기능: 고가용성](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA 시스템 복제에 대한 네트워크 권장 사항](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA 시스템 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA 서비스 자동 시작](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA 시스템 복제 구성](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


Azure에서 VM을 배포하는 작업을 숙지하는 것 외에도, Azure에서 가용성 아키텍처를 정의하기 전에 먼저 [Azure에서 Windows 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) 문서를 읽어보는 것이 좋습니다.

## <a name="service-level-agreements-for-different-azure-components"></a>다양한 Azure 구성 요소에 대한 서비스 수준 계약
Azure는 네트워킹, 저장소 및 VM 등의 여러 다른 구성 요소에 대해 다른 가용성 SLA를 유지합니다. 이러한 모든 SLA는 문서화되며 [Microsoft Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 페이지에서 찾을 수 있습니다. [가상 머신에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)를 확인하면 Azure에서 두 개의 다른 구성을 갖는 두 가지 다른 SLA를 제공한다는 것을 알 수 있습니다. SLA는 다음과 같이 정의됩니다.

- OS 디스크 및 모든 데이터 디스크에 대해 [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)를 사용하는 단일 VM은 매월 99.9%의 가동 시간을 제공합니다.
- [Azure 가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)으로 구성된 여러(2개 이상) VM은 매월 99.95%의 가동 시간을 제공합니다.

Azure 구성 요소에서 제공하는 SLA에 대한 가용성 요구 사항을 측정하고, 제공하려는 가용성을 달성하기 위해 SAP HANA로 구현해야 하는 다양한 시나리오를 결정하세요.

## <a name="next-steps"></a>다음 단계
추가 문서:

- [단일 Azure 지역 내 SAP HANA 가용성](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


