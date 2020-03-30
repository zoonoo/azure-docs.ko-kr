---
title: Azure 보안 센터에서 사용할 수 있는 지원되는 기능 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 보안 센터에서 지원하는 서비스 목록을 제공합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245240"
---
# <a name="feature-coverage-for-machines"></a>기계에 대한 기능 범위

아래 표에는 가상 컴퓨터 및 서버에 사용할 수 있는 Azure 보안 센터 기능이 표시됩니다.

## <a name="supported-features-for-virtual-machines-and-servers"></a>가상 컴퓨터 및 서버에 대한 지원되는 기능<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows 컴퓨터](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**기능**|**Azure 가상 시스템**|**Azure Virtual Machine Scale Sets**|**Azure가 아닌 컴퓨터**|**가격 책정**
|[마이크로소프트 수비수 ATP 통합](security-center-wdatp.md)|✔</br>(지원되는 버전에서)|✔</br>(지원되는 버전에서)|✔|Standard|
|[가상 시스템 행동 분석(및 보안 경고)](threat-protection.md)|✔|✔|✔|권장 사항(무료) </br></br> 보안 경고(표준)|
|[파일 없는 보안 경고](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[네트워크 기반 보안 경고](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[적시 VM 액세스](security-center-just-in-time.md)|✔|-|-|Standard|
|[기본 취약성 평가](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[적응형 어플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[적응형 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|적응형 네트워크 제어|✔|✔|-|Standard|
|[규정 준수 대시보드 & 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker 호스팅 IaaS 컨테이너의 권장 사항 및 위협 보호|-|-|-|Standard|
|누락된 OS 패치 평가|✔|✔|✔|무료|
|보안 구성 오류 평가|✔|✔|✔|무료|
|[엔드포인트 보호 평가](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|무료|
|디스크 암호화 평가|✔|✔|-|무료|
|타사 취약성 평가|✔|-|-|무료|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|무료|


### <a name="linux-machines"></a>[Linux 컴퓨터](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**기능**|**Azure 가상 시스템**|**Azure Virtual Machine Scale Sets**|**Azure가 아닌 컴퓨터**|**가격 책정**
|[마이크로소프트 수비수 ATP 통합](security-center-wdatp.md)|-|-|-|Standard|
|[가상 시스템 행동 분석(및 보안 경고)](security-center-alerts-iaas.md)|✔</br>(지원되는 버전에서)|✔</br>(지원되는 버전에서)|✔|권장 사항(무료) </br></br> 보안 경고(표준)|
|[파일 없는 보안 경고](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[네트워크 기반 보안 경고](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[적시 VM 액세스](security-center-just-in-time.md)|✔|-|-|Standard|
|[기본 취약성 평가](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[적응형 어플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[적응형 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|적응형 네트워크 제어|✔|✔|-|Standard|
|[규정 준수 대시보드 & 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker 호스팅 IaaS 컨테이너의 권장 사항 및 위협 보호|✔|✔|✔|Standard|
|누락된 OS 패치 평가|✔|✔|✔|무료|
|보안 구성 오류 평가|✔|✔|✔|무료|
|[엔드포인트 보호 평가](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|무료|
|디스크 암호화 평가|✔|✔|-|무료|
|타사 취약성 평가|✔|-|-|무료|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|무료|

--- 


> [!TIP]
>표준 가격 책정 계층에서만 사용할 수 있는 기능을 실험하려면 프리 티어 사용자는 30일 평가판에 등록할 수 있습니다. 자세한 내용은 가격 [책정 페이지를](https://azure.microsoft.com/pricing/details/security-center/)참조하십시오.


## <a name="supported-endpoint-protection-solutions"></a>지원되는 엔드포인트 보호 솔루션<a name="endpoint-supported"></a>

아래 표에는 다음 정보의 행렬이 나와 있습니다.

 - Azure Security Center를 사용하여 각 솔루션을 자동으로 설치할 수 있는지 여부
 - Security Center에서 검색할 수 있는 Endpoint Protection 솔루션. 이 목록에서 엔드포인트 보호 솔루션을 검색하면 보안 센터에서 는 이를 설치하지 않는 것이 좋습니다.

이러한 각 보호에 대해 권장 사항이 생성되는 시기에 대한 자세한 내용은 [엔드포인트 보호 평가 및 권장 사항을](security-center-endpoint-protection.md)참조하십시오.

| 엔드포인트 보호| 플랫폼 | Security Center 설치 | Security Center 검색 |
|------|------|-----|-----|
| Windows Defender(Microsoft 맬웨어 방지 프로그램)| Windows Server 2016| 아니오, OS에 기본 제공| yes |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2, 2012, 2008 R2(아래 참고 참조) | 확장을 통해 | yes |
| 트렌드 마이크로 - 모든 버전* | Windows Server 제품군  | 예 | yes |
| Symantec v12.1.1100+| Windows Server 제품군  | 예 | yes |
| McAfee v10+ | Windows Server 제품군  | 예 | yes |
| McAfee v10+ | 리눅스 서버 제품군  | 예 | 예**\*** |
| 소포스 V9+| 리눅스 서버 제품군  | 예 | 예**\***  |

 **\*** 적용 범위 상태 및 지원 데이터는 현재 보호된 구독과 연결된 Log Analytics 작업 영역에서만 사용할 수 있습니다. Azure 보안 센터 포털에 반영 되지 않습니다.

> [!NOTE]
> - Windows Server 2008 R2 가상 머신에서 SCEP(System Center Endpoint Protection)의 검색을 위해서는 PowerShell 3.0(또는 상위 버전)이 설치된 후 SCEP가 설치되어야 합니다.
> - 심층 보안 에이전트에 대해 Trend 마이크로 보호 감지가 지원됩니다.  OfficeScan 에이전트는 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

- 보안 센터에서 데이터를 수집하는 방법과 [로그 분석 에이전트에](security-center-enable-data-collection.md)대해 알아봅니다.
- 보안 [센터에서 데이터를 관리하고 보호하는](security-center-data-security.md)방법에 대해 알아보십시오.
- [Azure 보안 센터를 채택하기](security-center-planning-and-operations-guide.md)위한 디자인 고려 사항을 계획하고 이해하는 방법에 대해 알아봅니다.
- 보안 [센터를 지원하는 플랫폼을](security-center-os-coverage.md)검토합니다.
- [Azure 보안 센터에서 Windows 및 Linux 컴퓨터에 대한 위협 보호에](threat-protection.md#windows-machines)대해 자세히 알아봅니다.
- Azure 보안 센터에 대해 자주 묻는 질문을 찾을 수 [있습니다.](faq-general.md)