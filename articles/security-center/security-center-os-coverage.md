---
title: Azure 보안 센터에서 지원하는 플랫폼 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 보안 센터에서 지원하는 플랫폼 목록을 제공합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: e13149ba802f0f8b9a565e0aabd86ae05167f18b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208823"
---
# <a name="supported-platforms"></a>지원 플랫폼 

## <a name="virtual-machines--servers"></a>가상 머신 / 서버<a name="vm-server"></a>

보안 센터는 다양한 유형의 하이브리드 환경에서 가상 컴퓨터/서버를 지원합니다.

* Azure만
* Azure 및 온-프레미스
* Azure 및 기타 클라우드
* Azure, 기타 클라우드 및 온-프레미스

Azure 구독에서 활성화된 Azure 환경의 경우 Azure 보안 센터는 구독 내에 배포된 IaaS 리소스를 자동으로 검색합니다.

> [!NOTE]
> 전체 보안 기능 집합을 받으려면 Azure 보안 센터에서 사용하는 [로그 분석 에이전트가](../azure-monitor/platform/agents-overview.md#log-analytics-agent)설치되고 [제대로 구성되어 Azure 보안 센터로 데이터를 전송하도록 구성되어야](security-center-enable-data-collection.md#manual-agent)합니다.

다음 섹션에서는 Azure 보안 센터에서 사용하는 [로그 분석 에이전트가](../azure-monitor/platform/agents-overview.md#log-analytics-agent)실행할 수 있는 지원되는 서버 운영 체제를 나열합니다.

### <a name="windows-server-operating-systems"></a>윈도우 서버 운영 체제<a name="os-windows"></a>

|OS|Azure 보안 센터에서 지원|마이크로소프트 디펜더 ATP와의 통합 지원|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

위에 나열된 Windows 운영 체제에 대한 지원되는 기능에 대한 자세한 내용은 [가상 컴퓨터/서버 지원 기능을](security-center-services.md#vm-server-features)참조하십시오.

### <a name="windows-operating-systems"></a>윈도우 운영 체제<a name="os-windows (non-server)"></a>

Azure 보안 센터는 Azure 서비스와 통합하여 Windows 기반 가상 컴퓨터를 모니터링하고 보호합니다.

### <a name="linux-operating-systems"></a>리눅스 운영 체제<a name="os-linux"></a>

64비트

* CentOS 6 및 7
* Amazon Linux 2017.09
* 오라클 리눅스 6 오라클 리눅스 7
* Red Hat Enterprise Linux Server 6 및 7
* Debian GNU/Linux 8 및 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS, 18.04 LTS
* SUSE Linux Enterprise Server 12

32비트
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 및 9
* 우분투 리눅스 14.04 LTS, 및 16.04 LTS

> [!NOTE]
> 지원되는 Linux 운영 체제 목록이 지속적으로 변경되므로 원하는 경우 [여기를](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) 클릭하여 이 항목이 마지막으로 게시된 이후 변경된 경우 지원되는 버전의 최신 목록을 확인하십시오.

위에 나열된 Linux 운영 체제에 대한 지원되는 기능에 대한 자세한 내용은 [가상 컴퓨터/ 서버 지원 기능을](security-center-services.md#vm-server-features)참조하십시오.

### <a name="managed-virtual-machine-services"></a>관리형 가상 머신 서비스<a name="virtual-machine"></a>

가상 컴퓨터는 또한 Azure Kubernetes(AKS), Azure Databricks 등과 같은 일부 Azure 관리 서비스의 일부로 고객 구독에서 만들어집니다. 이러한 가상 컴퓨터는 Azure Security Center에서 검색되며 위에 나열된 지원되는 [Windows/Linux 운영 체제에](#os-windows)따라 로그 분석 에이전트를 설치하고 구성할 수 있습니다.

### <a name="cloud-services"></a>클라우드 서비스<a name="cloud-services"></a>

클라우드 서비스에서 실행되는 가상 시스템도 지원됩니다. 프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다. 클라우드 서비스에 대한 자세한 내용은 [Azure Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요.

Azure 스택에 있는 가상 시스템에 대 한 보호도 지원 됩니다. Azure 스택과 보안 센터의 통합에 대 한 자세한 내용은 [Azure 스택 가상 컴퓨터를 보안 센터에 온보보드](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)참조.

## <a name="next-steps"></a>다음 단계

- 보안 센터에서 데이터를 수집하는 방법과 [로그 분석 에이전트에](security-center-enable-data-collection.md)대해 알아봅니다.
- 보안 [센터에서 데이터를 관리하고 보호하는](security-center-data-security.md)방법에 대해 알아보십시오.
- [Azure 보안 센터를 채택하기](security-center-planning-and-operations-guide.md)위한 디자인 고려 사항을 계획하고 이해하는 방법에 대해 알아봅니다.
- 다양한 [클라우드 환경에서 사용할 수 있는 기능에](security-center-services.md)대해 알아봅니다.
- [Azure 보안 센터에서 Windows 및 Linux 컴퓨터에 대한 위협 보호에](threat-protection.md#windows-machines)대해 자세히 알아봅니다.
