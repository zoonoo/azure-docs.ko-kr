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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 21c54e2e996bb987f7a27ac3e6333df6f74d6f4b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338627"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit 아키텍처
Azure Stack 개발 키트 (ASDK)는 Azure Stack의 단일 노드 배포입니다. 모든 구성 요소는 단일 호스트 컴퓨터에서 실행 중인 가상 컴퓨터에 설치 됩니다. 

## <a name="logical-architecture-diagram"></a>논리적 아키텍처 다이어그램
다음 다이어그램은 ASDK 및 해당 구성 요소의 논리적 아키텍처를 보여 줍니다.

![ASDK 아키텍처](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>가상 컴퓨터 역할
ASDK 개발 키트 호스트 컴퓨터에서 호스트 되는 다음 Vm을 사용 하 여 서비스를 제공 합니다.

| 이름 | 설명 |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack 저장소 서비스입니다.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS)입니다.  |
| **AzS-BGPNAT01** | Edge 라우터 및 Azure Stack에 대 한 NAT 및 VPN 기능을 제공 합니다. |
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
