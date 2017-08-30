---
title: "SAP용 Microsoft Azure 인증 | Microsoft Docs"
description: "Azure 플랫폼의 SAP에 대한 현재 구성 및 인증의 업데이트된 목록입니다."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: e4d5c78299903659a18aa9b8d04495e215bcca0d
ms.contentlocale: ko-kr
ms.lasthandoff: 08/10/2017

---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure에서 실행되는 SAP 인증 및 구성

SAP와 Microsoft는 고객에게 상호 혜택을 주는 강력한 파트너 관계 속에서 장기간 함께 협력해 왔습니다. Microsoft는 Microsoft Azure가 SAP 작업을 실행할 최상의 플랫폼이 되도록 하기 위해 지속적으로 플랫폼을 업데이트하고 SAP에 새로운 인증 세부 정보를 제출하고 있습니다. 다음 표에서는 지원되는 구성 및 증가하는 인증 목록에 대해 간략히 설명합니다. 

## <a name="sap-hana-certifications"></a>SAP HANA 인증

| SAP 제품 | 지원되는 OS | Azure 제품 |
| --- | --- | --- |
| SAP HANA Developer Edition(SQLODBC, ODBO(Windows 전용), ODBC, JDBC 드라이버로 구성된 HANA Client 소프트웨어, HANA Studio, HANA Database 포함) |Red Hat Enterprise Linux, SUSE Linux Enterprise | D 시리즈 VM 제품군 |
| HANA One |Red Hat Enterprise Linux, SUSE Linux Enterprise |DS14_v2(일반 공급 시) |
| SAP S/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5의 제어되는 가용성, Azure(큰 인스턴스)에서 SAP HANA 사용 |
| Suite on HANA, OLTP |Red Hat Enterprise Linux, SUSE Linux Enterprise |비프로덕션 시나리오를 위한 단일 노드 배포의 GS5, Azure(큰 인스턴스)에서 SAP HANA 사용 |
| HANA Enterprise for BW, OLAP |Red Hat Enterprise Linux, SUSE Linux Enterprise |단일 노드 배포의 GS5, Azure(큰 인스턴스)에서 SAP HANA 사용 |
| SAP BW/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |단일 노드 배포의 GS5, Azure(큰 인스턴스)에서 SAP HANA 사용 |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 인증
Microsoft Azure는 다음과 같은 SAP 제품에서 인증되었고 Microsoft와 SAP의 전폭적인 지원을 받고 있습니다.

| SAP 제품 | 게스트 OS | RDBMS | 가상 컴퓨터 유형 |
| --- | --- | --- | --- |
| SAP Business Suite 소프트웨어 |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5 |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5 |
| SAP BusinessObjects BI |Windows |해당 없음 |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5 |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5 |

