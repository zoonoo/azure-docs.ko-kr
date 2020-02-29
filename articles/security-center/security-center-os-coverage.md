---
title: Azure Security Center에서 지 원하는 플랫폼 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 지 원하는 플랫폼의 목록을 제공 합니다.
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
ms.openlocfilehash: 39e2deead1affd750d9a5e68985b02ebfe8e0efd
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921303"
---
# <a name="supported-platforms"></a>지원 플랫폼 

## 가상 컴퓨터/서버<a name="vm-server"></a>

Security Center는 여러 유형의 하이브리드 환경에서 가상 머신/서버를 지원 합니다.

* Azure만
* Azure 및 온-프레미스
* Azure 및 기타 클라우드
* Azure, 다른 클라우드 및 온-프레미스

Azure 구독에서 활성화 된 Azure 환경의 경우 Azure Security Center는 구독 내에 배포 되는 IaaS 리소스를 자동으로 검색 합니다.

> [!NOTE]
> 전체 보안 기능 집합을 받으려면 [Log Analytics 에이전트가](../azure-monitor/platform/agents-overview.md#log-analytics-agent)있어야 합니다 .이 에이전트는 Azure Security Center에서 사용 하 고 [Azure Security Center에 데이터를 보내도록 올바르게 구성](security-center-enable-data-collection.md#manual-agent)되어 있어야 합니다.


다음 섹션에서는 Azure Security Center에서 사용 되는 [Log Analytics 에이전트](../azure-monitor/platform/agents-overview.md#log-analytics-agent)를 실행할 수 있는 지원 되는 서버 운영 체제를 나열 합니다.

### Windows server 운영 체제<a name="os-windows"></a>

|OS|Azure Security Center에서 지원|Microsoft Defender ATP와의 통합 지원|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

위에 나열 된 Windows 운영 체제에 대해 지원 되는 기능에 대해 자세히 알아보려면 [가상 머신/서버 지원 기능](security-center-services.md#vm-server-features)을 참조 하세요.

### Windows 운영 체제<a name="os-windows (non-server)"></a>

Azure Security Center는 Windows 기반 가상 컴퓨터를 모니터링 하 고 보호 하기 위해 Azure 서비스와 통합 됩니다.

### Linux 운영 체제<a name="os-linux"></a>

64비트

* CentOS 6 및 7
* Amazon Linux 2017.09
* Oracle Linux 6 및 Oracle Linux 7
* Red Hat Enterprise Linux Server 6 및 7
* Debian GNU/Linux 8 및 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS, 18.04 LTS
* SUSE Linux Enterprise Server 12

32비트
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 및 9
* Ubuntu Linux 14.04 LTS 및 16.04 LTS

> [!NOTE]
> 지원 되는 Linux 운영 체제의 목록이 지속적으로 변경 되므로 원하는 경우 [여기](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) 를 클릭 하 여이 항목을 마지막으로 게시 한 후 변경 내용이 있는 경우 지원 되는 버전의 최신 목록을 확인 하세요.

위에 나열 된 Linux 운영 체제에 대해 지원 되는 기능에 대 한 자세한 내용은 [가상 머신/서버 지원 기능](security-center-services.md#vm-server-features)을 참조 하세요.

### 관리 되는 가상 컴퓨터 서비스<a name="virtual-machine"></a>

Azure Kubernetes (AKS), Azure Databricks 등의 일부 Azure 관리 되는 서비스의 일부로도 고객 구독에서 가상 컴퓨터를 만들 수 있습니다. 이러한 가상 머신은 Azure Security Center 에서도 검색 되며, 위에 나열 된 지원 되는 [Windows/Linux 운영 체제](#os-windows)에 따라 Log analytics 에이전트를 설치 하 고 구성할 수 있습니다.

### Cloud Services<a name="cloud-services"></a>

클라우드 서비스에서 실행 되는 가상 컴퓨터도 지원 됩니다. 프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다. 클라우드 서비스에 대한 자세한 내용은 [Azure Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요.

## PaaS 서비스<a name="paas-services"></a>

Azure Security Center에서 지 원하는 Azure PaaS 리소스는 다음과 같습니다.

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* 스토리지 계정
* App Service
* 함수
* 클라우드 서비스
* VNet
* 서브넷
* NIC
* NSG
* Batch 계정
* Service fabric 계정
* Automation 계정
* 부하 분산 장치
* 검색
* Service Bus 네임스페이스
* Stream Analytics
* 이벤트 허브 네임스페이스
* 논리 앱
* Redis
* Data Lake Analytics
* Data Lake Store
* 주요 자격 증명 모음

위의 PaaS 리소스 목록에서 지원 되는 기능에 대해 자세히 알아보려면 [paas 서비스 지원 기능](security-center-services.md#paas-services)을 참조 하세요.

Azure Stack에 있는 Virtual Machines에 대 한 보호도 지원 됩니다. Azure Stack와 Security Center의 통합에 대 한 자세한 내용은 [Security Center에 Azure Stack virtual machines](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)등록을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Security Center에서 데이터 및 Log Analytics 에이전트를 수집](security-center-enable-data-collection.md)하는 방법을 알아봅니다.
- [Security Center에서 데이터를 관리 하 고 보호](security-center-data-security.md)하는 방법을 알아봅니다.
- [디자인 고려 사항을 계획하고 이해하여 Azure Security Center를 채택](security-center-planning-and-operations-guide.md)하는 방법을 알아봅니다.
- 다양 한 [클라우드 환경에 사용할 수 있는 기능](security-center-services.md)에 대해 알아봅니다.
- [Azure Security Center의 Windows 및 Linux 컴퓨터에 대 한 위협 방지](threat-protection.md#windows-machines)에 대해 자세히 알아보세요.