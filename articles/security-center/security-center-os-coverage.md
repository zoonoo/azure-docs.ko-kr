---
title: Azure Security Center에서 지원하는 기능 및 플랫폼 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 지원하는 기능 및 플랫폼 목록을 제공합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 1f5a144728614ee13bf4f08bb594f0546d446880
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640794"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure Security Center에서 지원하는 기능 및 플랫폼

클래식 및 Resource Manager 배포 모델을 둘 다 사용하여 생성된 VM(가상 머신) 및 컴퓨터에 대해 보안 상태 모니터링과 권장 사항이 제공됩니다.

> [!NOTE]
> Azure 리소스의 [클래식 및 리소스 관리자 배포 모델](../azure-classic-rm.md) 에 대해 자세히 알아봅니다.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>데이터 수집 에이전트를 지원하는 플랫폼 
이 섹션에서는 Azure Security Center에서 사용 되는 Log Analytics 에이전트를 실행할 수 있는 플랫폼을 나열 합니다.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 컴퓨터 및 VM에 대해 지원되는 플랫폼
다음 Windows 운영 체제가 지원됩니다.

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Windows Defender ATP와의 통합은 Windows Server 2012 R2 및 Windows Server 2016만 지원합니다.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 컴퓨터 및 VM에 대해 지원되는 플랫폼

다음 Linux 운영 체제가 지원됩니다.

> [!NOTE]
> 지원 되는 Linux 운영 체제의 목록이 지속적으로 변경 되므로 원하는 경우 [여기](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) 를 클릭 하 여이 항목을 마지막으로 게시 한 후 변경 내용이 있는 경우 지원 되는 버전의 최신 목록을 확인 하세요.

64비트
* CentOS 6 및 7
* Amazon Linux 2017.09
* Oracle Linux 6 및 7
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

## <a name="vms-and-cloud-services"></a>VM 및 Cloud Services
클라우드 서비스에서 실행되는 VM도 지원됩니다. 프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다. 클라우드 서비스에 대한 자세한 내용은 [Azure Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요.


## <a name="supported-iaas-features"></a>지원되는 IaaS 기능

> [!div class="mx-tableFixed"]
> 

|서버|Windows|||Linux|||가격 책정|
|----|----|----|----|----|----|----|----|
|**환경**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtual Machine**|**Virtual Machine Scale Set**||**Virtual Machine**|**Virtual Machine Scale Set**|
|VMBA 위협 검색 경고|✔|✔|✔|✔(지원되는 버전에서만)|✔(지원되는 버전에서만)|✔|권장 사항 (무료) 위협 검색 (표준)|
|네트워크 기반 위협 탐지 경고|✔|✔|X|✔|✔|X|표준|
|Windows Defender ATP 통합|✔(지원되는 버전에서만)|✔(지원되는 버전에서만)|✔|X|X|X|표준|
|누락된 패치|✔|✔|✔|✔|✔|✔|Free|
|보안 구성|✔|✔|✔|✔|✔|✔|Free|
|Endpoint protection 평가|✔|✔|✔|X|X|X|Free|
|JIT VM 액세스|✔|X|X|✔|X|X|표준|
|적응형 애플리케이션 제어|✔|X|✔|✔|X|✔|표준|
|FIM|✔|✔|✔|✔|✔|✔|표준|
|디스크 암호화 평가|✔|✔|X|✔|✔|X|Free|
|타사 배포|✔|X|X|✔|X|X|Free|
|NSG 평가|✔|✔|X|✔|✔|X|Free|
|Fileless 위협 탐지|✔|✔|✔|X|X|X|표준|
|네트워크 맵|✔|✔|X|✔|✔|X|표준|
|적응형 네트워크 제어|✔|✔|X|✔|✔|X|표준|
|규정 준수 대시보드 & 보고서|✔|✔|✔|✔|✔|✔|표준|
|Docker에서 호스트 되는 IaaS 컨테이너의 권장 사항 및 위협 감지|X|X|X|✔|✔|✔|표준|

### <a name="supported-endpoint-protection-solutions"></a>지원되는 Endpoint Protection 솔루션

아래 표에는 다음 정보의 행렬이 나와 있습니다.
 - Azure Security Center를 사용하여 각 솔루션을 자동으로 설치할 수 있는지 여부
 - Security Center에서 검색할 수 있는 Endpoint Protection 솔루션. 이러한 Endpoint Protection 솔루션 중 하나가 검색되더라도 Security Center에 솔루션을 설치하는 것이 좋다는 메시지가 표시되지는 않습니다.

이러한 각 보호에 대 한 권장 사항이 생성 되는 경우에 대 한 자세한 내용은 [Endpoint Protection 평가 및 권장 사항](security-center-endpoint-protection.md)을 참조 하세요.

| Endpoint Protection| 플랫폼 | Security Center 설치 | Security Center 검색 |
|------|------|-----|-----|
| Windows Defender(Microsoft 맬웨어 방지 프로그램)| Windows Server 2016| 아니오, OS에 기본 제공| 예 |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2, 2012, 2008 R2(아래 참고 참조) | 확장을 통해 | 예 |
| 추세 마이크로 – 모든 버전 * | Windows Server 제품군  | 아니요 | 예 |
| Symantec v12.1.1100+| Windows Server 제품군  | 아니요 | 예 |
| McAfee v10+ | Windows Server 제품군  | 아니요 | 예 |
| McAfee v10+ | Linux 서버 제품군  | 아니요 | 예로 **\*** |
| Sophos V9 +| Linux 서버 제품군  | 아니요 | 예로 **\***  |

 **\*** 검사 상태 및 지원 데이터는 현재 보호 된 구독과 연결 된 Log Analytics 작업 영역 에서만 사용할 수 있으며 Azure Security Center 포털에는 반영 되지 않습니다.

> [!NOTE]
> - Windows Server 2008 R2 가상 머신에서 SCEP(System Center Endpoint Protection)의 검색을 위해서는 PowerShell 3.0(또는 상위 버전)이 설치된 후 SCEP가 설치되어야 합니다.
> - 심층 보안 에이전트에 대해 추세 마이크로 보호 감지가 지원 됩니다.  OfficeScan 에이전트는 지원 되지 않습니다.

## <a name="supported-paas-features"></a>지원되는 PaaS 기능


|서비스|권장 사항 (무료)|위협 검색 (표준)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob storage 계정|✔| ✔|
|앱 서비스|✔| ✔|
|클라우드 서비스|✔| X|
|VNet|✔| NA|
|서브넷|✔| NA|
|NIC|✔| NA|
|NSG|✔| NA|
|구독|✔ **| ✔|
|일괄 처리|✔| NA|
|Service Fabric|✔| NA|
|Automation 계정|✔| NA|
|부하 분산 장치|✔| NA|
|검색|✔| NA|
|Service Bus|✔| NA|
|Stream Analytics|✔| NA|
|Event Hubs|✔| NA|
|Logic Apps|✔| NA|
|Storage 계정|✔| NA|
|Redis|✔| NA|
|Data Lake Analytics|✔| NA|
|키 자격 증명 모음|✔| NA|




\* 이러한 기능은 현재 공개 미리 보기로 지원됩니다.

\*\*AAD 권장 사항은 표준 구독에만 사용할 수 있습니다.



## <a name="next-steps"></a>다음 단계

- [디자인 고려 사항을 계획하고 이해하여 Azure Security Center를 채택](security-center-planning-and-operations-guide.md)하는 방법을 알아봅니다.
- [Azure Security Center에서 vm & 서버에 대 한 위협 검색](security-center-alerts-iaas.md)에 대해 자세히 알아보세요.
- [Azure Security Center 사용에 관한 질문과 대답](security-center-faq.md)을 찾아봅니다.
- [Azure 보안 및 규정 준수에 관한 블로그 게시물](https://blogs.msdn.com/b/azuresecurity/)을 찾아봅니다.
