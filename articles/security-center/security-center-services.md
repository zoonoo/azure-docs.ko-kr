---
title: Azure Security Center에서 사용할 수 있는 지원되는 기능 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 지원하는 서비스 목록을 제공합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: a311439e8efc5481fbfd7431c1514ba6be576e39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858016"
---
# <a name="feature-coverage-for-machines"></a>머신에 대한 기능 적용 범위

아래 두 탭은 Windows 및 Linux 가상 머신 및 서버에서 사용할 수 있는 Azure Security Center 기능을 보여 줍니다.

## <a name="supported-features-for-virtual-machines-and-servers"></a>가상 머신 및 서버에 지원되는 기능<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows 컴퓨터**](#tab/features-windows)

|**기능**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**비 Azure 머신**|**Azure Defender 필요**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP 통합](security-center-wdatp.md)|✔</br>(지원되는 버전에서만)|✔</br>(지원되는 버전에서만)|✔|예|
|[가상 머신 동작 분석(및 보안 경고)](alerts-reference.md)|✔|✔|✔|예|
|[파일리스 보안 경고](alerts-reference.md#alerts-windows)|✔|✔|✔|예|
|[네트워크 기반 보안 경고](other-threat-protections.md#network-layer)|✔|✔|-|예|
|[Just-in-time VM 액세스](security-center-just-in-time.md)|✔|-|-|예|
|[기본 취약성 평가](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|예|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|예|
|[적응 애플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|예|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|예|
|[적응형 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|예|
|[규정 준수 대시보드 및 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|예|
|Docker에서 호스트되는 IaaS 컨테이너에 대한 권장 사항 및 위협 방지|-|-|-|예|
|누락된 OS 패치 평가|✔|✔|✔|Azure: 예<br><br>비 Azure: 예|
|잘못된 보안 구성 평가|✔|✔|✔|Azure: 예<br><br>비 Azure: 예|
|[Endpoint Protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: 예<br><br>비 Azure: 예|
|디스크 암호화 평가|✔</br>([지원되는 시나리오](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)에 해당)|✔|-|아니요|
|제3자 취약성 평가|✔|-|-|아니요|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|아니요|


### <a name="linux-machines"></a>[**Linux 컴퓨터**](#tab/features-linux)

|**기능**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**비 Azure 머신**|**Azure Defender 필요**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP 통합](security-center-wdatp.md)|-|-|-|예|
|[가상 머신 동작 분석(및 보안 경고)](security-center-alerts-iaas.md)|✔</br>(지원되는 버전에서만)|✔</br>(지원되는 버전에서만)|✔|예|
|[파일리스 보안 경고](alerts-reference.md#alerts-windows)|-|-|-|예|
|[네트워크 기반 보안 경고](other-threat-protections.md#network-layer)|✔|✔|-|예|
|[Just-in-time VM 액세스](security-center-just-in-time.md)|✔|-|-|예|
|[기본 취약성 평가](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|예|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|예|
|[적응 애플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|예|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|예|
|[적응형 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|예|
|[규정 준수 대시보드 및 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|예|
|Docker에서 호스트되는 IaaS 컨테이너에 대한 권장 사항 및 위협 방지|✔|✔|✔|예|
|누락된 OS 패치 평가|✔|✔|✔|Azure: 예<br><br>비 Azure: 예|
|잘못된 보안 구성 평가|✔|✔|✔|Azure: 예<br><br>비 Azure: 예|
|[Endpoint Protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|아니요|
|디스크 암호화 평가|✔</br>([지원되는 시나리오](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)에 해당)|✔|-|아니요|
|제3자 취약성 평가|✔|-|-|아니요|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|아니요|

--- 


> [!TIP]
>Azure Defender에서만 사용할 수 있는 기능을 실험하려면 30일 평가판에 등록할 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.


## <a name="supported-endpoint-protection-solutions"></a>지원되는 Endpoint Protection 솔루션 <a name="endpoint-supported"></a>

아래 표에는 다음 정보의 행렬이 나와 있습니다.

 - Azure Security Center를 사용하여 각 솔루션을 자동으로 설치할 수 있는지 여부
 - Security Center에서 검색할 수 있는 Endpoint Protection 솔루션. 이 목록에서 Endpoint Protection 솔루션이 검색되더라도 Security Center에 솔루션을 설치하는 것이 좋다는 메시지가 표시되지는 않습니다.

이러한 각 보호에 대한 권장 사항이 생성되는 경우에 대한 자세한 내용은 [Endpoint Protection 평가 및 권장 사항](security-center-endpoint-protection.md)을 참조하세요.

| Endpoint Protection| 플랫폼 | Security Center 설치 | Security Center 검색 |
|------|------|-----|-----|
| Microsoft Defender 바이러스 백신| Windows Server 2016 이상| 아니오, OS에 기본 제공| 예 |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2, 2012, 2008 R2(아래 참고 참조) | 확장을 통해 | 예 |
| Trend Micro – Deep Security | Windows Server 제품군  | 아니요 | 예 |
| Symantec v12.1.1100+| Windows Server 제품군  | 아니요 | 예 |
| McAfee v10+ | Windows Server 제품군  | 아니요 | 예 |
| McAfee v10+ | Linux 서버 제품군  | 아니요 | 예 **\*** |
| Sophos V9+| Linux 서버 제품군  | 아니요 | 예  **\***  |

 **\*** 현재는 보호된 구독과 연결된 Log Analytics 작업 영역에서만 적용 범위 상태와 지원 데이터를 사용할 수 있습니다. Azure Security Center 포털에는 반영되지 않습니다.

> [!NOTE]
> Windows Server 2008 R2 가상 머신에서 SCEP(System Center Endpoint Protection)의 검색을 위해서는 PowerShell(v3.0 이상)이 설치된 후 SCEP가 설치되어야 합니다.


## <a name="next-steps"></a>다음 단계

- [Security Center에서 Log Analytics 에이전트를 사용하여 데이터를 수집](security-center-enable-data-collection.md)하는 방법에 대해 알아봅니다.
- [Security Center에서 데이터를 관리하고 보호](security-center-data-security.md)하는 방법을 알아봅니다.
- [Security Center를 지원하는 플랫폼](security-center-os-coverage.md)을 검토합니다.
