---
title: Azure Security Center에서 사용할 수 있는 지원 되는 기능 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 지원 되는 서비스 목록을 제공 합니다.
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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 8700421551af227f158abaa38d5f96c8e2987ba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603396"
---
# <a name="supported-features-available-in-azure-security-center"></a>Azure Security Center에서 사용할 수 있는 지원 되는 기능

> [!NOTE]
>일부 기능은 표준 계층 에서만 사용할 수 있습니다. Security Center의 표준 계층에 아직 등록 하지 않은 경우 무료 평가판 기간을 사용할 수 있습니다. 자세한 내용은 [Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조 하세요.

다음 섹션에서는 지원 되는 [플랫폼](security-center-os-coverage.md)에 사용할 수 있는 Security Center 기능을 보여 줍니다.

* [가상 컴퓨터/서버](#vm-server-features)
* [PaaS 서비스](#paas-services)


## 가상 컴퓨터/서버 지원 기능<a name="vm-server-features"></a>

### <a name="windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**비 Azure 컴퓨터**|**가격**
|[Microsoft Defender ATP 통합](security-center-wdatp.md)|✔(지원되는 버전에서만)|✔(지원되는 버전에서만)|✔|Standard|
|[가상 컴퓨터 동작 분석 위협 검색 경고](security-center-alerts-iaas.md)|✔|✔|✔|권장 사항 (무료) 위협 검색 (표준)|
|[Fileless 위협 검색 경고](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[네트워크 기반 위협 검색 경고](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Just-in-time VM 액세스](security-center-just-in-time.md)|✔|-|-|Standard|
|[기본 취약성 평가](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[적응 애플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[적응 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|적응형 네트워크 제어|✔|✔|-|Standard|
|[규정 준수 대시보드 & 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker에서 호스트 되는 IaaS 컨테이너의 권장 사항 및 위협 감지|-|-|-|Standard|
|누락 된 OS 패치 평가|✔|✔|✔|무료|
|보안 잘못 평가|✔|✔|✔|무료|
|[Endpoint protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|무료|
|디스크 암호화 평가|✔|✔|-|무료|
|타사 취약성 평가|✔|-|-|무료|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|무료|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**비 Azure 컴퓨터**|**가격**
|[Microsoft Defender ATP 통합](security-center-wdatp.md)|-|-|-|Standard|
|[가상 컴퓨터 동작 분석 위협 검색 경고](security-center-alerts-iaas.md)|✔(지원되는 버전에서만)|✔(지원되는 버전에서만)|✔|권장 사항 (무료) 위협 검색 (표준)|
|[Fileless 위협 검색 경고](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[네트워크 기반 위협 검색 경고](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Standard|
|[Just-in-time VM 액세스](security-center-just-in-time.md)|✔|-|-|Standard|
|[기본 취약성 평가](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[적응 애플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[적응 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|적응형 네트워크 제어|✔|✔|-|Standard|
|[규정 준수 대시보드 & 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker에서 호스트 되는 IaaS 컨테이너의 권장 사항 및 위협 감지|✔|✔|✔|Standard|
|누락 된 OS 패치 평가|✔|✔|✔|무료|
|보안 잘못 평가|✔|✔|✔|무료|
|[Endpoint protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|무료|
|디스크 암호화 평가|✔|✔|-|무료|
|타사 취약성 평가|✔|-|-|무료|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|무료|

--- 

## 지원 되는 endpoint protection 솔루션<a name="endpoint-supported"></a>

아래 표에는 다음 정보의 행렬이 나와 있습니다.

 - Azure Security Center를 사용하여 각 솔루션을 자동으로 설치할 수 있는지 여부
 - Security Center에서 검색할 수 있는 Endpoint Protection 솔루션. 이 목록에서 endpoint protection 솔루션을 검색 하는 경우 Security Center 설치 하지 않는 것이 좋습니다.

이러한 각 보호에 대 한 권장 사항이 생성 되는 경우에 대 한 자세한 내용은 [Endpoint Protection 평가 및 권장 사항](security-center-endpoint-protection.md)을 참조 하세요.

| 엔드포인트 보호| 플랫폼 | Security Center 설치 | Security Center 검색 |
|------|------|-----|-----|
| Windows Defender(Microsoft 맬웨어 방지 프로그램)| Windows Server 2016| 아니오, OS에 기본 제공| yes |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2, 2012, 2008 R2(아래 참고 참조) | 확장을 통해 | yes |
| 추세 마이크로 – 모든 버전 * | Windows Server 제품군  | 예 | yes |
| Symantec v12.1.1100+| Windows Server 제품군  | 예 | yes |
| McAfee v10+ | Windows Server 제품군  | 예 | yes |
| McAfee v10+ | Linux 서버 제품군  | 예 | 예 **\*** |
| Sophos V9 +| Linux 서버 제품군  | 예 | 예 **\***  |

 **\*** 현재는 보호 된 구독과 연결 된 Log Analytics 작업 영역 에서만 검사 상태와 지원 데이터를 사용할 수 있습니다. Azure Security Center 포털에는 반영 되지 않습니다.

> [!NOTE]
> - Windows Server 2008 R2 가상 머신에서 SCEP(System Center Endpoint Protection)의 검색을 위해서는 PowerShell 3.0(또는 상위 버전)이 설치된 후 SCEP가 설치되어야 합니다.
> - 심층 보안 에이전트에 대해 추세 마이크로 보호 감지가 지원 됩니다.  OfficeScan 에이전트는 지원 되지 않습니다.


## PaaS 서비스 지원 기능 <a name="paas-services"></a>

Azure Security Center에서 지 원하는 PaaS 리소스는 다음과 같습니다.

|서비스|권장 사항 (무료)|위협 검색 경고 (표준)|취약성 평가 (표준)|
|----|:----:|:----:|:----:|
|SQL Database|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Azure Database for MySQL *|✔|✔|-|
|Azure CosmosDB *|-|✔|-|
|Storage 계정|✔|-|-|
|Blob Storage|✔|✔|-|
|App Service|✔|✔|-|
|함수 앱|✔|-|-|
|Cloud Services|✔|-|-|
|Virtual Network|✔|-|-|
|서브넷|✔|-|-|
|NIC|✔|-|-|
|네트워크 보안 그룹|✔|-|-|
|Subscription|✔ **|✔|-|
|Batch 계정|✔|-|-|
|Service Fabric 계정|✔|-|-|
|Automation 계정|✔|-|-|
|Load Balancer|✔|-|-|
|Cognitive Search|✔|-|-|
|Service Bus 네임스페이스|✔|-|-|
|Stream Analytics|✔|-|-|
|이벤트 허브 네임스페이스|✔|-|-|
|논리 앱|✔|-|-|
|Redis에 대 한 캐시|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake 스토리지|✔|-|-|
|Key Vault|✔|✔ *|-|

\* 이러한 기능은 현재 미리 보기에서 지원 됩니다.

Azure AD (\*\* Azure Active Directory 권장 사항은 표준 구독에 대해서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Security Center에서 데이터 및 Log Analytics 에이전트를 수집](security-center-enable-data-collection.md)하는 방법을 알아봅니다.
- [Security Center에서 데이터를 관리 하 고 보호](security-center-data-security.md)하는 방법을 알아봅니다.
- [디자인 고려 사항을 계획하고 이해하여 Azure Security Center를 채택](security-center-planning-and-operations-guide.md)하는 방법을 알아봅니다.
- [Security center를 지 원하는 플랫폼](security-center-os-coverage.md)을 검토 합니다.
- [Azure Security Center에서 vm & 서버에 대 한 위협 검색](security-center-alerts-iaas.md)에 대해 자세히 알아보세요.
- [Azure Security Center에 대 한 질문과 대답](faq-general.md)을 찾습니다.
- [Azure 보안 및 규정 준수에 관한 블로그 게시물](https://blogs.msdn.com/b/azuresecurity/)을 찾아봅니다.