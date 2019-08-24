---
title: Data Science Virtual Machine 기반 팀 환경 소개 - Azure | Microsoft Docs
description: 엔터프라이즈 팀 환경에 Data Science VM을 배포하기 위한 패턴입니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 5f34498fbdacf7fc6e62788913c795ab70ceef23
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991624"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine 기반 팀 분석 및 AI 환경 
Dsvm ( [Data Science Virtual Machine](overview.md) )은 AI (인공 지능) 및 데이터 분석을 위해 미리 빌드된 소프트웨어를 사용 하 여 Azure 플랫폼에서 다양 한 환경을 제공 합니다.

일반적으로 DSVM은 개별 분석 데스크톱으로 사용되었습니다. 개별 데이터 과학자는 미리 작성 된이 공유 분석 환경으로 생산성을 향상 합니다. 대량 분석 팀이 데이터 과학자 및 AI 개발자를 위한 환경을 계획할 때, 되풀이 테마 중 하나는 개발 및 실험을 위한 공유 분석 인프라입니다. 이 인프라는 데이터 과학 및 분석 팀 전체의 공동 작업 및 일관성을 용이 하 게 하는 엔터프라이즈 IT 정책에 따라 관리 됩니다.

공유 인프라를 사용 하면 분석 환경의 성능을 향상 시킬 수 있습니다. 일부 조직에서는 *분석 샌드박스에서*팀 기반 데이터 과학/분석 인프라를 호출 합니다. 이를 통해 데이터 과학자는 다양 한 데이터 자산에 액세스 하 여 데이터를 신속 하 게 이해할 수 있습니다. 이 샌드박스 환경에서는 프로덕션 환경에 영향을 주지 않고 데이터 과학자 실험을 실행 하 고, 가설의 유효성을 검사 하 고, 예측 모델을 빌드할 수 있습니다.

DSVM은 Azure 인프라 수준에서 작동하므로, IT 관리자가 엔터프라이즈의 IT 정책을 준수하며 작동하도록 DSVM을 쉽게 구성할 수 있습니다. DSVM은 다양 한 공유 아키텍처를 구현할 때 유연성을 제공 하면서도 제어 된 방식으로 회사 데이터 자산에 대 한 액세스를 제공 합니다.

이 섹션에서는 DSVM을 팀 기반 데이터 과학 인프라로 배포하는 데 사용할 수 있는 몇 가지 패턴 및 지침을 설명합니다. 이러한 패턴의 구성 요소는 Azure IaaS (infrastructure as a service)에서 제공 되기 때문에 모든 Azure Vm에 적용 됩니다. 이 문서 시리즈는 DSVM에 이러한 표준 Azure 인프라 기능을 적용 하는 데 중점을 두 며

엔터프라이즈 팀 분석 환경의 주요 구성 요소는 다음과 같습니다.

* [DSVMs의 자동 조정 되 풀](dsvm-pools.md)
* [공통 ID 및 풀의 아무 DSVM에서나 작업 영역에 액세스](dsvm-common-identity.md)
* [데이터 원본에 안전하게 액세스](dsvm-secure-access-keys.md)


이 시리즈는 위의 각 항목에 대 한 지침과 포인터를 제공 합니다. 또한 DSVMs을 대기업 구성으로 배포 하기 위한 고려 사항 및 요구 사항은 모두 다루지 않습니다. 엔터프라이즈에서 DSVM 인스턴스를 구현할 때 사용할 수 있는 몇 가지 다른 Azure 리소스는 다음과 같습니다.

* [네트워크 보안](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [모니터링](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) 및 [관리](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [로깅 및 감사](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [정책 설정 및 적용](../../governance/policy/overview.md)
* [맬웨어 방지](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [암호화](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [데이터 검색 및 거버넌스](https://docs.microsoft.com/azure/data-catalog/)

마지막으로 [Azure 아키텍처 센터](https://docs.microsoft.com/azure/architecture/) 는 클라우드 기반 분석 인프라를 구축 하 고 관리 하기 위한 자세한 종단 간 아키텍처와 모델을 제공 합니다.
