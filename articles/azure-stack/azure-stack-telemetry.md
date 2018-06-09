---
title: Azure 스택 원격 분석 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure 스택 원격 분석 설정을 구성 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248200"
---
# <a name="azure-stack-telemetry"></a>Azure 스택 원격 분석

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 원격 분석은 자동으로 연결 된 사용자 경험을 통해 Microsoft에 시스템 데이터를 업로드합니다. Microsoft 팀 고객 만족도 개선 하기 위해 Azure 스택 원격 분석 수집 하는 데이터를 사용 합니다. 이 데이터는 보안, 상태, 품질 및 성능 분석에도 사용 됩니다.

Azure 스택 연산자에 대 한 원격 분석 엔터프라이즈 배포에 대 한 귀중 한 통찰력을 제공할 수 있습니다 및 셰이프 이후 버전의 Azure 스택을 도움이 되는 음성을 제공 합니다.

> [!NOTE]
> Azure 스택 대금 청구를 위해 Azure에 사용 정보를 전달 하도록 구성할 수도 있습니다. 이것이 사용량 기준 과금-으로--사용 요금 청구 선택 Multi-node Azure 스택 고객을 위해 필요 합니다. 사용 현황 보고 원격 분석에서 독립적으로 제어 됩니다 및 다중 노드 고객에 게 용량 모델을 선택 하거나 사용자가 Azure 스택 개발 키트에 대 한 필요 하지 않습니다. 이러한 시나리오에 대 한 사용 보고 설정 해제할 수 있습니다 [등록 스크립트를 사용 하 여](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting)합니다.

Azure 스택 원격 분석을 사용 하는 Windows Server 2016 연결 된 사용자 환경 및 원격 분석 구성에 따라는 [이벤트 추적에 대 한 ETW (Windows)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging 기술 수집 하 고 이벤트 및 데이터를 저장 합니다. Azure 스택 구성 요소 이벤트 및 public 운영 체제 이벤트 로깅 및 추적 Api를 사용 하 여 수집 된 데이터를 게시 하려면 같은 기술을 사용 합니다. 이러한 Azure 스택 구성 요소의 예로 이러한 공급자: 네트워크 리소스, 저장소 리소스, 리소스 모니터링 및 업데이트 리소스입니다. 사용자 환경 연결 및 원격 분석 구성 요소 SSL을 사용 하 여 데이터를 암호화 한 Microsoft 데이터 관리 서비스에 HTTPS를 통해 데이터를 전송 하는 데 인증서 고정 기능을 사용 합니다.

> [!IMPORTANT]
> 원격 분석 데이터 흐름을 사용 하려면 포트 443 (HTTPS) 네트워크에서 열려 있어야 합니다. 사용자 환경 연결 및 원격 분석 구성 요소에는 Microsoft 데이터 관리 서비스에 연결 https://v10.vortex-win.data.microsoft.com합니다. 사용자 환경 연결 및 원격 분석 구성 요소에도 연결 https://settings-win.data.microsoft.com 구성 정보를 다운로드 합니다.

## <a name="privacy-considerations"></a>개인정보처리방침 고려 사항

ETW 서비스 원격 분석 데이터를 보호 된 클라우드 저장소 다시 라우팅합니다. 최소 권한의 원칙 원격 분석 데이터에 대 한 액세스를 안내합니다. 유효한 비즈니스 요구를 사용 하 여 Microsoft 직원만 원격 분석 데이터에 대 한 액세스를 제공 됩니다. 에 설명 된 제한 된 목적 또는 고객의 판단에 따라 Microsoft 제외한 제 3 자의 개인 고객 데이터 공유 하지는 [Microsoft 개인정보취급방침](https://privacy.microsoft.com/PrivacyStatement)합니다. Oem 및 파트너와 공유 되는 비즈니스 보고서 익명화 된 집계 된 데이터를 포함 합니다. 의사 결정을 공유 하는 데이터 개인 정보, 법률 및 데이터 관리 관련자를 포함 하는 내부 Microsoft 팀에서 수행 됩니다.

Microsoft에서는 있다고 인식 하 고 정보 최소화 사례입니다. 가 필요 하 고 범위 내 에서만 서비스를 제공 하는 필요에 따라 또는 분석에 대 한 저장 된 정보만 수집을 줄이려고 합니다. 대부분의 Azure 스택 시스템 및 Azure 서비스 작동 하는 방법에 대 한 정보는 6 개월 내에서 삭제 됩니다. 요약 또는 집계 된 데이터를 오랜 기간 동안 유지 됩니다.

고객 정보의 보안과 개인 정보 보호 하는 중요 점을 이해 합니다.  Microsoft에서는 고객 개인 정보 및 Azure 스택의 고객 데이터의 보호는 신중 하 고 포괄적인 접근 방식을 사용 합니다. IT 관리자는 언제 든 지 기능 및 개인 정보 설정 사용자 지정 하는 컨트롤이 있습니다. 투명도 신뢰를 위한 노력 만으로도:

- 여러분에서는 수집할 데이터의 형식에 대 한 고객으로 엽니다.
- 기업 고객 컨트롤에 넣을까요-자신의 개인 정보 설정을 사용자 지정할 수 있습니다.
- 고객의 개인 정보 및 보안 먼저 입력.
- 이제 원격 분석 데이터 사용 되는 방법에 대 한 투명 한 것입니다.
- 고객 만족도 향상 하기 위해 원격 분석 데이터를 사용 합니다.

Microsoft 신용 카드 번호, 사용자 이름 및 암호, 전자 메일 주소 또는 유사한 중요 한 정보 등의 중요 한 데이터를 수집 하려고 하지 않습니다. 중요 한 정보 수신 실수로, 판단 되는 경우 삭제 합니다.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft 원격 분석 데이터를 사용 하는 방법의 예

원격 분석을 신속 하 게 식별 하 고 고객 배포 및 구성에 대 한 중요 한 안정성 문제를 해결 하는 데 중요 한 역할을 재생 합니다. 원격 분석 데이터 로부터 이해는 서비스 또는 하드웨어 구성 문제를 식별할 수 있습니다. Microsoft의 수를 에코 시스템 고객 및 향상 된 드라이브에서이 데이터를 가져올 통합된 Azure 스택 솔루션의 품질에 대 한 막대를 발생 시킵니다.

원격 분석에는 또한 Microsoft에 더 잘 고객 구성 요소를 배포 하는 방법을 이해 기능을 사용 하 고 서비스를 사용 하 여 자신의 비즈니스 목표를 달성할 수 있습니다. 이러한 insights 고객 환경 및 워크 로드 직접 영향을 줄 수 있는 영역에 대 한 엔지니어링 투자 우선 순위를 지정 하는 데 도움이 됩니다.

몇 가지 예의 컨테이너, 저장소, 네트워킹 구성을 Azure 스택 역할과 연결 된 고객 사용이 포함 됩니다. 또한 insights 드라이브 개선 사항 및 Azure 스택 관리 및 모니터링 솔루션에는 인텔리전스를 사용 합니다. 이러한 향상 된이 기능 쉽게 문제를 진단 하는 고객에 대 한 호출과 더 적은 지원 하 여 비용을 절감할 microsoft 합니다.

## <a name="manage-telemetry-collection"></a>원격 분석 컬렉션 관리

권장 조직에서 원격 분석 해제 되지 않습니다. 그러나 일부 시나리오에서는이 필요할 수 있습니다.

이러한 시나리오에서는 Azure 스택을 배포 하기 전에 레지스트리 설정을 사용 하 여 또는 Azure 스택을 배포한 후 원격 분석 끝점을 사용 하 여 Microsoft로 전송 하는 원격 분석 수준을 구성할 수 있습니다.

### <a name="telemetry-levels-and-data-collection"></a>원격 분석 수준 및 데이터 수집

원격 분석 설정으로 변경 하기 전에 원격 분석 수준과 각 수준에서 수집 되는 데이터를 이해 해야 합니다.

원격 분석 설정이 누적와 다음과 같이 범주별로 분류 되는 4 개의 수준 (0-3)으로 그룹화 됩니다.

**0 (보안)**</br>
보안 데이터에만 해당 합니다. 운영 체제를 안전 하 게 유지 하는 데 필요한 정보입니다. 여기에 사용자 환경이 연결 및 원격 분석 구성 요소 설정, 및 Windows Defender에 대 한 데이터가 포함 됩니다. Azure 스택 관련 원격 분석은이 수준에서 내보내집니다.

**1 (기본)**</br>
보안 데이터 및 기본 상태 및 품질 데이터입니다. 기본 장치 정보를 포함 하 여: 품질 관련 데이터, 응용 프로그램 호환성, 응용 프로그램 사용 현황 데이터와 데이터를는 **보안** 수준입니다. 원격 분석 수준을 기본 수 있도록 Azure 스택 원격 분석 설정 합니다. 이 수준에서 수집 되는 데이터에는 다음이 포함 됩니다.

- *기본 장치 정보* 형식 및 에코 시스템에서 네이티브 및 가상 Windows Server 2016 인스턴스의 구성에 대 한 이해를 제공 하는 합니다. 다음 내용이 포함됩니다.

  - OEM 및 모델 등의 컴퓨터 특성
  - 네트워크 어댑터의 수와 프로세서 속도 같은 네트워킹 특성입니다.
  - 프로세서 및 메모리 특성, 코어 수 및 설치 된 메모리 양 등.
  - 드라이브의 수, 유형, 드라이브 및 드라이브 크기 등 저장소 특성입니다.

- *원격 분석 기능*, 이벤트를 업로드, 삭제 된 이벤트 및 마지막 데이터의 비율을 포함 하 여 시간을 업로드 합니다.
- *품질 관련 정보* Microsoft Azure 스택 수행 되는 방법을 대 한 기본적인 이해를 개발할 수 있게 해 주는 합니다. 예를 들어 특정 하드웨어 구성에서 중요 한 경고의 수입니다.
- *호환성 데이터* 수는 시스템 및 가상 컴퓨터에 설치 된 리소스 공급자에 대 한 이해를 제공 합니다. 잠재적인 호환성 문제를 식별합니다.

**2 (고급)**</br>
추가 정보를 포함 하 여: 운영 체제와 Azure 스택 서비스 사용 방법, 이러한 서비스를 수행 하는 방법, 고급 안정성 데이터 및 데이터를는 **보안** 및 **기본** 수준입니다.

> [!NOTE]
> 기본 원격 분석 설정입니다.

**3 (전체)**</br>
모든 식별 하 고 문제를 해결 하는 데 필요한 데이터와 데이터를는 **보안**, **기본**, 및 **고급** 수준입니다.

> [!IMPORTANT]
> 이러한 원격 분석 수준 Microsoft Azure 스택 구성 요소에만 적용 됩니다. Microsoft 이외 소프트웨어 구성 요소 및 Azure 스택 하드웨어 파트너에서 하드웨어 수명 주기 호스트에서 실행 되는 서비스는 이러한 원격 분석 수준 외부에서 해당 클라우드 서비스와 통신할 수 있습니다. 원격 분석 정책이 옵트인 하거나 옵트아웃 하는 방법 이해 하는 Azure 스택 하드웨어 솔루션 공급자와 함께 작동 해야 합니다.

Windows 및 Azure 스택 원격 분석 해제 SQL 원격 분석을도 사용 하지 않습니다. Windows Server 원격 분석 설정의 의미에 대 한 자세한 내용은 참조는 [Windows 원격 분석 백서](https://aka.ms/winservtelemetry)합니다.

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Windows 레지스트리에 있는 원격 분석 수준을 설정합니다

Azure 스택을 배포 하기 전에 원격 분석 수준을 물리적 호스트 컴퓨터에서 수동으로 설정 하려면 Windows 레지스트리 편집기를 사용할 수 있습니다. 그룹 정책과 같은 관리 정책에서 이미 있는 경우이 레지스트리 설정을 재정의 합니다.

Azure 스택 개발 키트 호스트를 배포 하기 전에 CloudBuilder.vhdx로 부팅 하 고 관리자 권한 PowerShell 창에서 다음 스크립트를 실행 합니다.

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK 및 다중 노드: 설정 또는 배포 후 원격 분석 해제

를 사용 하거나 배포 후 원격 분석을 사용 하지 않도록 설정 하려면 액세스 하는 권한 있는 끝점 (PEP) ERCS Vm에 노출 되가 필요 합니다.

1. 사용 하려면: `Set-Telemetry -Enable`
2. 사용 하지 않으려면: `Set-Telemetry -Disable`

매개 변수 세부 정보:
> . 매개 변수 사용-원격 분석 데이터 업로드 켜기</br>
> . 매개 변수 사용 안 함-원격 분석 데이터 업로드를 해제  

**원격 분석을 사용 하도록 설정 하려면 스크립트:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**원격 분석을 사용 하지 않도록 설정 하는 스크립트:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>다음 단계

- [Azure 스택 개발 키트 배포 패키지를 다운로드 합니다.](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [Azure 스택 개발 키트를 배포 합니다.](azure-stack-run-powershell-script.md)
