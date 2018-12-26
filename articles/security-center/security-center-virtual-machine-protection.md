---
title: Azure Security Center에서 머신 및 응용 프로그램 보호 | Microsoft Docs
description: 이 문서에서는 가상 머신, 컴퓨터, 웹 앱 및 App Service 환경을 보호하는 데 도움이 되는 Security Center의 권장 사항을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d1e7046ed1fc87a85a08b21a48accaaf3b5087b9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316653"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Azure Security Center에서 머신 및 응용 프로그램 보호
Azure Security Center에서는 Azure 리소스의 보안 상태를 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다. 이러한 권장 사항은 VM(가상 머신)과 컴퓨터, 응용 프로그램, 네트워킹, SQL과 ID 및 액세스 등의 Azure 리소스 유형에 적용됩니다.

이 문서에서는 머신과 응용 프로그램에 적용되는 권장 사항에 대해 설명합니다.

## <a name="monitoring-security-health"></a>보안 상태 모니터링
**Security Center - 개요** 대시보드에서 리소스의 보안 상태를 모니터링할 수 있습니다. **리소스** 섹션에는 파악된 문제 수와 각 리소스 유형의 보안 상태가 제공됩니다.

**권장 사항**을 선택하면 모든 문제 목록을 볼 수 있습니다 합니다. 권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.

Compute 및 App Services 권장 사항의 전체 목록은 [권장 사항](security-center-virtual-machine-recommendations.md)을 참조하세요.

계속하려면 **리소스** 또는 Security Center 주 메뉴에서 **Compute 및 앱**을 선택합니다.
![Security Center 대시보드][1]

## <a name="monitor-compute-and-app-services"></a>Compute 및 App Services 모니터링
**Compute** 아래 탭이 4개 있습니다.

- **개요**: Security Center에서 파악된 모니터링 내용 및 권장 사항입니다.
- **VM 및 컴퓨터**: VM과 컴퓨터 목록 및 현재 보안 상태 입니다.
- **Cloud Services**: Security Center에서 모니터링되는 웹 및 작업자 역할의 목록입니다.
- **App Services(미리 보기)**: 앱 서비스 환경 목록과 각각의 현재 보안 상태입니다.
계속하려면 **리소스** 또는 Security Center 주 메뉴에서 **Compute 및 앱**을 선택합니다.

![컴퓨팅][2]

각 탭에는 여러 섹션이 있으며, 각 섹션에서 개별 옵션을 선택하면 특정 문제를 해결하는 권장 단계에 관한 자세한 내용을 볼 수 있습니다.

### <a name="monitoring-recommendations"></a>권장 사항 모니터링
이 섹션은 자동 프로비전을 위해 초기화된 VM 및 컴퓨터의 총 수 및 해당 현재 상태를 보여줍니다. 이 예제에는 **에이전트 상태 문제 모니터링** 권장 사항이 있습니다. 이 권장 사항을 선택합니다.

![에이전트 상태 문제 모니터링][3]

**에이전트 상태 문제 모니터링**이 열립니다. Security Center에서 성공적으로 모니터링할 수 없는 VM 및 컴퓨터가 나열됩니다. 자세한 내용을 보려면 VM 또는 컴퓨터를 선택합니다. **모니터링 상태**는 Security Center에서 모니터링할 수 없는 이유를 제공합니다. **모니터링 상태** 값, 설명 및 해결 단계에 대한 목록은 [Security Center 문제 해결 가이드](security-center-troubleshooting-guide.md)를 참조하세요.

### 모니터링되지 않는 VM 및 컴퓨터 <a name="unmonitored-vms-and-computers"></a>
머신에서 Microsoft Monitoring Agent 확장을 실행하지 않으면 Security Center에서 VM 또는 컴퓨터를 모니터링하지 않습니다. 머신에는 OMS 직접 에이전트 또는 SCOM 에이전트와 같은 로컬 에이전트가 이미 설치되어 있을 수 있습니다. 이러한 에이전트가 설치된 머신은 Security Center에서 완전히 지원되지 않으므로 모니터링되지 않는 것으로 식별됩니다. Security Center의 모든 기능을 최대한 활용하려면 Microsoft Monitoring Agent 확장이 필요합니다.

확장은 이미 설치된 로컬 에이전트 외에도 모니터링되지 않는 VM이나 컴퓨터에 설치할 수 있습니다. 두 에이전트를 동일하게 구성하여 동일한 작업 영역에 연결합니다. 이렇게 하면 Security Center에서 Microsoft Monitoring Agent Extension과 상호 작용하고 데이터를 수집할 수 있습니다. Microsoft Monitoring Agent 확장을 설치하는 방법에 대한 지침은 [VM 확장 사용](../log-analytics/log-analytics-quick-collect-azurevm.md)을 참조하세요.

Security Center에서 자동 프로비전을 위해 초기화된 VM 및 컴퓨터를 성공적으로 모니터링할 수 없는 이유에 대한 자세한 내용은 [에이전트 상태 문제 모니터링](security-center-troubleshooting-guide.md#mon-agent)을 참조하세요.

### <a name="recommendations"></a>권장 사항
이 섹션에는 Security Center가 모니터링하는 VM 및 컴퓨터, 웹 및 작업자 역할, Azure App Service Web Apps 및 Azure App Service Environment 각각에 대한 권장 사항이 있습니다. 첫 번째 열에는 권장 사항이 나열되고, 두 번째 열에는 해당 권장 사항의 영향을 받은 리소스의 총 수가 표시됩니다. 세 번째 열에는 아래와 같이 문제의 심각도가 표시됩니다.

![권장 사항][4]

각 권장 사항에는 선택하여 수행할 수 있는 작업 집합이 있습니다. 예를 들어, **시스템 업데이트 누락**을 클릭하면 패치가 누락된 VM과 컴퓨터의 수와 누락된 업데이트의 심각도가 다음 스크린샷과 같이 나타납니다.

![시스템 업데이트 적용][5]

**시스템 업데이트 적용**에는 그래프 형식으로 Windows 및 Linux에 대한 중요 업데이트에 대한 요약이 있습니다. 두 번째 부분에는 다음 정보가 포함된 테이블이 있습니다.

- **이름**: 누락된 업데이트의 이름입니다.
-  **VM 및 컴퓨터 수**: 이 업데이트를 누락한 VM 및 컴퓨터의 총 수입니다.
- **업데이트 심각도**: 특정 권장 사항의 심각도를 설명합니다.

    - **심각**: 중요한 리소스(응용 프로그램, 가상 머신 또는 네트워크 보안 그룹)에 취약성이 있으며 주의가 필요합니다.
    - **중요**: 프로세스를 완료하거나 취약성을 제거하려면 심각하지 않은 단계나 추가적인 단계를 수행해야 합니다.
    - **보통**: 취약성을 해결해야 하지만 즉각적인 주의가 필요하지는 않습니다. (기본적으로 낮은 권장 사항은 표시되지 않지만 보려는 경우 낮은 권장 사항으로 필터링할 수 있습니다.)


- **상태**: 권장 사항의 현재 상태입니다.

    - **열기**: 아직 해결되지 않은 권장 사항이 있습니다.
    - **진행 중**: 현재 권장 사항이 해당 리소스에 적용되고 있으며 아무런 작업도 수행할 필요가 없습니다.
    - **해결됨**: 권장 사항이 이미 완료되었습니다. 문제가 해결되었으면 해당 항목이 회색으로 표시됩니다.

권장 사항 세부 정보를 보려면 목록에서 누락된 업데이트의 이름을 클릭합니다.

![권장 사항 세부 정보][6]

> [!NOTE]
> 여기와 **권장 사항** 타일에서 보여 주는 보안 권장 사항은 동일합니다. 권장 사항 해결에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.
>
>

### <a name="vms-and-computers"></a>VM 및 컴퓨터
VM 및 컴퓨터 섹션에는 모든 VM과 컴퓨터의 권장 사항에 대한 개요가 제공됩니다. 각 열에는 아래와 같이 일단의 권장 사항이 표시됩니다.

![VM 및 컴퓨터 권장 사항][7]

이 목록에는 네 가지 유형의 아이콘이 표시됩니다.

![비 Azure 컴퓨터][8] 비 Azure 컴퓨터

![Azure Resource Manager VM][9] Azure Resource Manager VM입니다.

![Azure 클래식 VM][10] Azure 클래식 VM입니다.

![작업 영역에서 식별된 VM][11] 본 구독의 일부인 작업 영역에서만 식별되는 VM 여기에는 이 구독에 있는 작업 영역에 보고하는 다른 구독의 VM, SCOM 다이렉트 에이전트를 사용하여 설치된 VM 및 리소스 ID가 없는 VM이 포함됩니다.

각 권장 사항에 표시되는 아이콘은 주의를 기울여야 하는 VM과 컴퓨터 및 권장 사항의 형식을 식별하는 데 도움이 됩니다. 필터 옵션을 사용하여 이 화면에 표시할 옵션을 선택할 수도 있습니다.

![Filter][12]

이전 예제에서 VM 하나에는 엔드포인트 보호에 관한 중요한 권장 사항이 있습니다. VM에 대한 자세한 정보를 보려면 VM을 선택하십시오.

![중요한 권장 사항][13]

VM 또는 컴퓨터에 대한 보안 세부 정보는 다음과 같습니다. 맨 아래에서 권장되는 작업과 각 문제의 심각도를 볼 수 있습니다.

### <a name="cloud-services"></a>클라우드 서비스
클라우드 서비스에는 운영 체제 버전이 오래된 경우 아래의 스크린샷과 같이 권장 사항이 표시됩니다.

![클라우드 서비스][14]

권장 사항(이전 예제에는 해당되지 않음)이 있는 시나리오의 경우 권장 사항의 단계를 따라 운영 체제 버전을 업데이트해야 합니다. 업데이트를 사용할 수 있는 경우 경고가 표시됩니다(문제의 심각도에 따라 빨간색 또는 주황색). IIS에 자동으로 배포된 웹앱으로 Windows Server를 실행하는 WebRole1 또는 WorkerRole1 행에 있는 이 경고를 선택하면 아래 스크린샷과 같이 해당 권장 사항에 대한 자세한 정보가 표시됩니다.

![WorkerRole1][15]

이 권장 사항에 대한 설명을 단계적으로 자세히 보려면 **설명** 열에서 **OS 버전 업데이트**를 클릭합니다.

![OS 버전 업데이트][16]

### <a name="app-services-preview"></a>App Services(미리 보기)

> [!NOTE]
> App Service 모니터링은 미리 보기로 제공되며 Security Center의 표준 계층에서만 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
>
>

**App Services**에는 App Service 환경 목록과 Security Center에서 수행된 평가에 기반한 상태 요약이 있습니다.

![App Services][17]

이 목록에는 세 가지 유형의 아이콘이 표시됩니다.

![App Services 환경][18] App Services 환경입니다.

![웹 응용 프로그램][19] 웹 응용 프로그램입니다.

![함수 응용 프로그램][24] 함수 응용 프로그램입니다.

1. 웹 응용 프로그램을 선택합니다. 요약 보기는 다음과 같은 세 개의 탭으로 열립니다.

  - **권장 사항**: 실패한 Security Center에서 수행된 평가를 기반으로 합니다.
  - **통과된 평가**: 전달된 Security Center에서 수행된 평가의 목록입니다.
  - **사용할 수 없는 평가**: 오류로 인해 실행하지 못한 평가 목록 또는 특정 App Service와 관련이 없는 권장 사항입니다.

  **권장 사항**에는 선택한 웹 응용 프로그램에 대한 권장 사항 목록과 각 권장 사항의 심각도가 표시됩니다.

  ![요약 보기][20]

2. 권장 사항의 설명, 비정상 리소스, 정상 리소스, 검사되지 않은 리소스의 목록의 권장 사항을 선택합니다.

  ![권장 사항 설명][21]

  **통과된 평가**에는 통과된 평가의 목록이 나와 있습니다.  이러한 평가의 심각도는 항상 녹색입니다.

  ![통과된 평가][22]

3. 평가에 대한 설명, 비정상 및 정상 리소스 목록 및 검사되지 않은 리소스 목록에서 통과된 평가를 선택합니다. 비정상 리소스에 대한 탭이 있지만 평가를 통과하기 때문에 이 목록은 항상 비어 있습니다.

    ![정상 리소스][23]

## <a name="compute-and-app-recommendations"></a>컴퓨팅 및 앱 권장 사항
|리소스 종류|보안 점수|권장 사항|설명|
|----|----|----|----|
|컴퓨터|50|머신에 모니터링 에이전트 설치|모니터링 에이전트를 설치하여 각 머신에서 데이터 수집, 업데이트 검사, 기준 검사 및 엔드포인트 보호를 사용하도록 설정합니다.|
|컴퓨터|50|구독에 대한 자동 프로비전 및 데이터 수집을 사용하도록 설정 |구독에 추가된 각 머신에서 데이터 수집, 업데이트 검사, 기준 검사 및 엔드포인트 보호를 사용하기 위해 구독의 머신에 대해 자동 프로비전 및 데이터 수집을 사용하도록 설정합니다.|
|컴퓨터|40|머신의 모니터링 에이전트 상태 문제 해결|완벽한 Security Center 보호를 위해 문제 해결 가이드의 지침에 따라 머신에서 모니터링 에이전트 문제를 해결합니다.| 
|컴퓨터|40|머신의 엔드포인트 보호 상태 문제 해결|완벽한 Security Center 보호를 위해 문제 해결 가이드의 지침에 따라 머신에서 모니터링 에이전트 문제를 해결합니다.|
|컴퓨터|40|머신의 검사 데이터 누락 문제 해결|가상 머신 및 컴퓨터에서 검사 데이터 누락 문제를 해결합니다. 머신의 검사 데이터 누락은 업데이트 검사, 기준 검사 같은 보안 평가 누락과 엔드포인트 보호 솔루션 검사 누락을 초래합니다.|
|컴퓨터|40|머신에 시스템 업데이트 설치|누락된 시스템 보안 및 중요 업데이트를 설치하여 Windows 및 Linux 가상 머신과 컴퓨터를 보호합니다.
|컴퓨터|40|클라우드 서비스 역할에 대한 OS 버전 업데이트|클라우드 서비스 역할의 OS(운영 체제) 버전을 OS 제품군의 사용 가능한 최신 버전으로 업데이트합니다.|
|컴퓨터|35|머신의 보안 구성에서 취약성 수정|머신의 보안 구성에서 취약성을 수정하여 공격으로부터 보호합니다. |
|컴퓨터|35|컨테이너의 보안 구성에서 취약성 수정|Docker가 설치된 머신의 보안 구성에서 취약성을 수정하여 공격으로부터 보호합니다.|
|컴퓨터|25|적응형 애플리케이션 제어 사용|애플리케이션 제어를 사용하도록 설정하여 Azure에 있는 VM에서 실행할 수 있는 애플리케이션을 제어합니다. 이렇게 하면 VM을 강화하여 맬웨어로부터 보호할 수 있습니다. Security Center는 기계 학습을 통해 각 VM에서 실행 중인 애플리케이션을 분석하고 이러한 인텔리전스를 사용하여 허용 목록 규칙을 적용할 수 있습니다. 이 기능은 애플리케이션 허용 목록의 구성 및 유지 관리 프로세스를 간소화합니다.|
|컴퓨터|20|머신에 Endpoint Protection 솔루션 설치|가상 머신에 엔드포인트 보호 솔루션을 설치하여 위협 및 취약성으로부터 보호합니다.|
|컴퓨터|20|시스템 업데이트를 적용하려면 머신 다시 시작|시스템 업데이트를 적용하고 취약성으로부터 머신을 보호하려면 머신을 다시 시작합니다.|
|App Service|20|웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|웹 애플리케이션의 액세스를 HTTPS 사용으로 제한합니다.|
|App Service|20|함수 앱에 HTTPS를 통해서만 액세스 가능|함수 앱의 액세스를 HTTPS 사용으로 제한합니다.|
|컴퓨터|15|가상 머신에 디스크 암호화 적용|Windows 및 Linux 가상 머신 모두에 Azure Disk Encryption를 사용하여 가상 머신 디스크를 암호화합니다. ADE(Azure Disk Encryption)는 OS 및 데이터 암호화를 제공하는 Windows의 업계 표준 BitLocker 기능 및 Linux의 DM-Crypt 기능을 활용하여 데이터를 보호하고 고객 Azure Key Vault에서 조직 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다. 규정 준수 및 보안 요구 사항에서 임시(로컬로 임시 연결) 디스크 암호화를 포함하여 암호화 키를 사용한 데이터의 종단 간 암호화를 요구할 경우 Azure Disk Encryption을 사용합니다. 또는 기본적으로 Managed Disks는 Azure Storage Service Encryption을 사용하여 암호화되며 암호화 키가 Azure에서 Microsoft 관리 키입니다. 이것이 규정 준수 및 보안 요구 사항을 충족한다면 기본 관리 디스크 암호화를 사용하여 요구 사항에 부합할 수 있습니다.|
|Compute 리소스(Service Fabric)|10|Service Fabric의 클라이언트 인증에 Azure Active Directory 사용|Service Fabric에서 Azure Active Directory를 통해서만 클라이언트 인증을 수행합니다.|
|Compute 리소스(Automation 계정)|5| Automation 계정의 암호화 사용|중요한 데이터를 저장할 때 Automation 계정 변수 자산의 암호화를 사용하도록 설정합니다.|
|App Service|5|App Service에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(Load balancer)|5|Load Balancer에서 진단 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(검색)|5|검색 서비스에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(Service Bus)|5|Service Bus에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(스트림 분석)|5|Azure Stream Analytics에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(Service Fabric)|5|Service Fabric에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(Batch)|5|Batch 계정에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(이벤트 허브)|5|이벤트 허브에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Compute 리소스(Logic Apps)|5|Logic Apps에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|컴퓨터|30|가상 머신에 취약성 평가 솔루션 설치|가상 머신에 취약성 평가 솔루션 설치|
|컴퓨터|15|웹 응용 프로그램 방화벽 추가| WAF(웹 애플리케이션 방화벽) 솔루션을 배포하여 웹 애플리케이션을 보호합니다. |
|컴퓨터|30|취약성 수정 - 취약성 평가 솔루션 사용|취약성 평가 타사 솔루션이 배포된 가상 머신이 애플리케이션 및 OS 취약성에 대해 연속적으로 평가됩니다. 이러한 취약성 항목이 발견될 때마다 이 내용을 자세한 권장 구성의 일부로 사용할 수 있습니다.|
|Compute 리소스(Service Fabric)|15|Service Fabric에서 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정|Service Fabric은 기본 클러스터 인증서를 사용하여 노드 간 통신을 위한 3단계 보호(None, Sign 및 EncryptAndSign)를 제공합니다.  모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다. |
|App Service|10|웹 애플리케이션에 대해 원격 디버깅을 해제해야 함|더 이상 사용할 필요가 없으면 웹 애플리케이션에 대해 디버깅을 해제합니다. 원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다.|
|App Service|10|함수 애플리케이션에 대해 원격 디버깅을 해제해야 함|더 이상 사용할 필요가 없는 경우 함수 앱에 대한 디버깅을 해제합니다. 원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다.|
|App Service|10|웹 응용 프로그램에 대한 IP 제한 구성|애플리케이션에 액세스할 수 있는 IP 주소 목록을 정의합니다. IP 제한 사항을 사용하여 일반적인 공격으로부터 웹 응용 프로그램을 보호합니다.|
|App Service|10|함수 앱에 대한 IP 제한 구성| 애플리케이션에 액세스할 수 있는 IP 주소 목록을 정의합니다. IP 제한 사항을 사용하여 일반적인 공격으로부터 함수 앱을 보호합니다.|
|App Service|10|응용 프로그램에 액세스하는 모든('*') 리소스를 허용하지 마십시오.| WEBSITE_LOAD_CERTIFICATES 매개 변수의 "" 설정을 허용하지 않습니다. 매개 변수를 “로 설정하는 것은 모든 인증서가 웹 애플리케이션 개인 인증서 저장소로 로드되는 것을 의미합니다. 사이트는 런타임 시 모든 인증서에 대한 액세스가 필요하지 않으므로 최소 권한의 원칙이 악용될 수 있습니다.|
|App Service|5|웹 응용 프로그램에 대해 웹 소켓이 사용되지 않도록 설정됨|웹 애플리케이션 내에서 웹 소켓 사용을 검토합니다. 웹 소켓 프로토콜은 다양한 유형의 보안 위협에 취약합니다.|
|App Service|5|함수 앱에 대해 웹 소켓이 사용되지 않도록 설정됨|함수 앱 내에서 웹 소켓 사용을 검토합니다. 웹 소켓 프로토콜은 다양한 유형의 보안 위협에 취약합니다.|
|App Service|5|웹 응용 프로그램에 대한 사용자 지정 도메인 사용|사용자 지정 도메인을 사용하여 피싱 및 기타 DNS 관련 공격 같은 일반적인 공격에서 웹 애플리케이션을 보호합니다.|
|App Service|5|함수 앱에 대해 사용자 지정 도메인 사용|사용자 지정 도메인을 사용하여 피싱 및 기타 DNS 관련 공격 같은 일반적인 공격에서 함수 앱을 보호합니다.|
|Compute 리소스(Batch)|1|Batch 계정에서 메트릭 경고 규칙 구성|Batch 계정에 메트릭 경고 규칙을 구성하고 메트릭 풀 삭제 완료 이벤트 및 풀 삭제 시작 이벤트를 사용하도록 설정합니다.|
|Compute 리소스(Service Bus)|1|Service Bus 네임스페이스에서 RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙 제거 |Service Bus 클라이언트는 네임스페이스의 모든 큐 및 토픽에 대한 액세스를 제공하는 네임스페이스 수준 액세스 정책을 사용하지 않아야 합니다. 최소 권한 보안 모델에 맞게 큐 및 토픽에 대한 엔터티 수준의 액세스 정책을 만들어 특정 엔터티에만 액세스를 제공해야 합니다.|
|Compute 리소스(이벤트 허브)|1|이벤트 허브 네임스페이스에서 RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙 제거 |이벤트 허브 클라이언트는 네임스페이스의 모든 큐 및 토픽에 대한 액세스를 제공하는 네임스페이스 수준 액세스 정책을 사용하지 않아야 합니다. 최소 권한 보안 모델에 맞게 큐 및 토픽에 대한 엔터티 수준의 액세스 정책을 만들어 특정 엔터티에만 액세스를 제공해야 합니다.|
|Compute 리소스(이벤트 허브)|5|이벤트 허브 엔터티에 대한 권한 부여 규칙 정의|최소 액세스 권한을 부여하기 위해 이벤트 허브 엔터티에 대한 권한 부여 규칙을 감사합니다.|
|컴퓨터|30|가상 머신에 취약성 평가 솔루션 설치|가상 머신에 취약성 평가 솔루션 설치|
|App Service|20|CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 함|필요한 도메인만 웹 애플리케이션과 상호 작용할 수 있도록 허용합니다. CORS(교차 원본 리소스 공유)는 웹 응용 프로그램에 액세스하는 모든 도메인을 허용하지 않아야 합니다.|
|App Service|20|CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 함| 필요한 도메인만 함수 애플리케이션과 상호 작용할 수 있도록 허용합니다. CORS(교차 원본 리소스 공유)는 함수 응용 프로그램에 액세스하는 모든 도메인을 허용하지 않아야 합니다.|
|컴퓨터|15|웹 응용 프로그램 방화벽 추가| WAF(웹 애플리케이션 방화벽) 솔루션을 배포하여 웹 애플리케이션을 보호합니다. |
|App Service|10|웹 응용 프로그램에 대해 지원되는 최신 .NET Framework 사용|최신 보안 클래스에 대해 최신 .NET Framework 버전을 사용합니다. 오래된 클래스 및 형식을 사용하면 응용 프로그램이 취약해질 수 있습니다.|
|App Service|10|웹 응용 프로그램에 대해 지원되는 최신 Java 버전 사용|최신 보안 클래스에 대해 최신 Java 버전을 사용합니다. 오래된 클래스 및 형식을 사용하면 응용 프로그램이 취약해질 수 있습니다.|
|App Service|10|웹 응용 프로그램에 대해 지원되는 최신 PHP 버전 사용|최신 보안 클래스에 대해 최신 PHP 버전을 사용합니다. 오래된 클래스 및 형식을 사용하면 응용 프로그램이 취약해질 수 있습니다.|
|App Service|10|웹 응용 프로그램에 대해 지원되는 최신 Node.js 버전 사용|최신 보안 클래스에 대해 최신 Node.js 버전을 사용합니다. 오래된 클래스 및 형식을 사용하면 응용 프로그램이 취약해질 수 있습니다.|
|App Service|10|웹 애플리케이션에 대해 지원되는 최신 Python 버전 사용|최신 보안 클래스에 대해 최신 Python 버전을 사용합니다. 오래된 클래스 및 형식을 사용하면 응용 프로그램이 취약해질 수 있습니다.|
|VM 및 컴퓨터|1|새 AzureRM 리소스로 가상 머신 마이그레이션|가상 머신에 대해 새 Azure Resource Manager v2를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, ARM 기반 배포 및 관리, 관리 ID 액세스, 비밀을 위해 키 자격 증명 모음에 액세스, AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다. |
|컴퓨터|30|취약성 수정 - 취약성 평가 솔루션 사용|취약성 평가 타사 솔루션이 배포된 가상 머신이 애플리케이션 및 OS 취약성에 대해 연속적으로 평가됩니다. 이러한 취약성 항목이 발견될 때마다 이 내용을 자세한 권장 구성의 일부로 사용할 수 있습니다.|




## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.


* [가상 머신에 대한 Azure Security Center 권장 사항 이해](security-center-virtual-machine-recommendations.md)
* [Azure Security Center에서 ID 및 액세스 모니터링](security-center-identity-access.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
* [Azure Security Center에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](security-center-azure-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
