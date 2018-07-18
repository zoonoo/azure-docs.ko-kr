---
title: Data Science Virtual Machine 기반 팀 환경 소개 - Azure | Microsoft Docs
description: 엔터프라이즈 팀 환경에 Data Science VM을 배포하기 위한 패턴입니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 8486b0be1fb5e1385da3c7ad55f6410a1059df93
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309251"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine 기반 팀 분석 및 AI 환경 
DSVM([Data Science Virtual Machine](overview.md))은 Azure 플랫폼에 미리 빌드된 AI(인공 지능) 및 데이터 분석용 소프트웨어를 포함한 풍부한 환경을 제공합니다. 

일반적으로 DSVM은 개별 분석 데스크톱으로 사용되었습니다. 개별 데이터 과학자는 미리 빌드된 분석 환경에 대한 이러한 공유 개념으로 생산성을 확보합니다. 대규모 분석 팀이 데이터 과학자 및 AI 개발자를 위한 분석 환경을 계획하므로, 계속 제기되는 주제 중 하나는 개발 및 실험을 위한 공유 분석 인프라입니다. 이 인프라는 데이터 과학/분석 팀 전체의 공동 작업과 일관성에도 도움이 되는 엔터프라이즈 IT 정책에 따라 관리됩니다. 

또한 IT는 공유 인프라를 사용하여 분석 환경을 더 잘 활용할 수 있습니다. 일부 조직은 팀 기반 데이터 과학/분석 인프라를 “분석 샌드박스”라고 부릅니다. 이 인프라를 통해 데이터 과학자는 다양한 데이터 자산에 액세스하여 데이터를 빠르게 해석하고, 실험을 실행하고, 가설의 유효성을 검사하고, 프로덕션 환경에 영향을 주지 않고 예측 모델을 빌드할 수 있습니다. 

DSVM은 Azure 인프라 수준에서 작동하므로, IT 관리자가 엔터프라이즈의 IT 정책을 준수하며 작동하도록 DSVM을 쉽게 구성할 수 있습니다. DSVM은 회사 데이터 자산에 대한 액세스 권한이 제어된 다양한 공유 아키텍처를 구현하는 완벽한 유연성을 제공합니다. 

이 섹션에서는 DSVM을 팀 기반 데이터 과학 인프라로 배포하는 데 사용할 수 있는 몇 가지 패턴 및 지침을 설명합니다. 이러한 패턴의 구성 요소는 Azure IaaS(서비스 제공 인프라)에서 제공되므로 모든 Azure VM에 적용됩니다. 이 문서 시리즈의 초점은 이러한 표준 Azure 인프라 기능을 Data Science VM에 적용하는 것입니다. 

엔터프라이즈 팀 분석 환경의 몇 가지 주요 구성 요소는 다음과 같습니다.

* [Data Science Virtual Machine의 자동 크기 조정 풀](dsvm-pools.md)
* [공통 ID 및 풀의 아무 DSVM에서나 작업 영역에 액세스](dsvm-common-identity.md)
* [데이터 원본에 안전하게 액세스](dsvm-secure-access-keys.md)


이 문서 시리즈에서는 앞의 각 항목에 대한 지침 및 포인터를 제공합니다. 대규모 엔터프라이즈 구성에 DSVM을 배포하는 경우의 모든 고려 사항과 요구를 다루지는 않습니다. 엔터프라이즈에서 DSVM 인스턴스를 구현하는 동안 사용할 수 있는 다른 Azure 설명서는 다음과 같습니다. 

* [네트워크 보안](https://docs.microsoft.com/azure/security/azure-network-security)
* [모니터링](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) 및 [관리](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [로깅 및 감사](https://docs.microsoft.com/azure/security/azure-log-audit)
* [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [정책 설정 및 적용](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [맬웨어 방지](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [암호화](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [데이터 검색 및 거버넌스](https://docs.microsoft.com/azure/data-catalog/)

[Azure 아키텍처 센터](https://docs.microsoft.com/en-us/azure/architecture/)는 클라우드 기반 분석 인프라를 빌드하고 관리하기 위한 구체적인 종단 간 아키텍처와 패턴을 제공합니다. 
