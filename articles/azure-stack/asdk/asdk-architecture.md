---
title: Azure Stack 개발 키트 아키텍처 | Microsoft Docs
description: Azure Stack 개발 키트 (ASDK) 아키텍처를 설명합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: c69b124f84e87e8f0b937dfa275378c376894f9b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447203"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit 아키텍처
Azure Stack 개발 키트 (ASDK)에 단일 호스트 컴퓨터에서 실행 하는 Azure Stack의 단일 노드 배포입니다. Edge 라우팅 구성 요소는 Azure Stack에 대 한 NAT 및 VPN 기능을 제공 하기 위해 호스트 컴퓨터에 설치 됩니다. Azure Stack 인프라 역할 물리적 호스트 컴퓨터의 Hyper-v 계층에서 실행 됩니다.


## <a name="virtual-machine-roles"></a>가상 컴퓨터 역할
ASDK 개발 키트 호스트 컴퓨터에서 호스트 되는 다음 Vm을 사용 하 여 서비스를 제공 합니다.

| 이름 | 설명 |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack 저장소 서비스입니다.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS)입니다.  |
| **AzS-CA01** | Azure Stack 역할 서비스에 대 한 인증서 기관 서비스입니다.|
| **AzS-DC01** | Microsoft Azure Stack active Directory, DNS 및 DHCP 서비스.|
| **AzS-ERCS01** | 응급 복구 콘솔 Vm입니다. |
| **AzS-GWY01** | Edge 게이트웨이 테 넌 트 네트워크에 대 한 VPN 사이트 간 연결과 같은 서비스.|
| **AzS-NC01** | Azure Stack 네트워크 서비스를 관리 하는 네트워크 컨트롤러입니다.  |
| **AzS-SLB01** | 테 넌 트와 Azure Stack 인프라 서비스에 대 한 분산 멀티플렉서 Azure Stack 서비스를 로드 합니다.  |
| **AzS-SQL01** | Azure Stack 인프라 역할에 대 한 내부 데이터 저장소입니다.  |
| **AzS-WAS01** | Azure Stack 관리 포털 및 Azure Resource Manager 서비스입니다.|
| **AzS-WASP01**| Azure Stack (테 넌 트) 사용자 포털 및 Azure Resource Manager 서비스입니다.|
| **AzS-XRP01** | Compute, Network 및 Storage 리소스 공급자를 포함 하 여 Microsoft Azure Stack에 대 한 인프라 관리 컨트롤러입니다.|


## <a name="next-steps"></a>다음 단계
[기본 ASDK 관리 작업에 대해 알아봅니다](asdk-admin-basics.md)
