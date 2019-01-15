---
title: Azure Stack 원격 분석 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure Stack 원격 분석 설정을 구성 하는 방법을 설명 합니다.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: 190a80d5807dcc8ad9666d3ba450691bc6453b41
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265620"
---
# <a name="azure-stack-telemetry"></a>Azure Stack 원격 분석

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 원격 분석 연결 된 사용자 환경을 통해 Microsoft에 시스템 데이터를 자동으로 업로드합니다. Microsoft 팀 고객 환경을 개선 하기 위해 Azure Stack 원격 분석을 수집 하는 데이터를 사용 합니다. 이 데이터 보안, 상태, 품질 및 성능 분석에도 사용 됩니다.

Azure Stack 운영자에 대 한 원격 분석 엔터프라이즈 배포에 대 한 중요 한 정보를 제공할 수 있습니다 및 Azure Stack의 이후 버전 셰이프는 데 도움이 되는 음성 기능을 제공 합니다.

> [!NOTE]
> Azure Stack 청구에 대 한 azure 사용량 정보를 전달 하도록 구성할 수도 있습니다. 이것이-수-종 청구를 선택 하는 다중 노드 Azure Stack 고객에 대 한 필요 합니다. 사용 현황 보고 원격 분석에서 독립적으로 제어 됩니다 및 용량 모델을 선택 하는 다중 노드 고객 또는 Azure Stack Development Kit 사용자에 대해 필요 하지 않습니다. 이러한 시나리오에 대 한 사용 보고 설정 해제할 수 있습니다 [등록 스크립트를 사용 하 여](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting)입니다.

Azure Stack 원격 분석을 Windows Server 2016 연결 된 사용자 환경 및 원격 분석 구성 요소를 사용 하는 기반으로 합니다 [이벤트 추적에 대 한 Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging 기술 수집 하 고 이벤트 및 데이터를 저장 합니다. Azure Stack 구성 요소에서는 동일한 기술을 사용 하 여 이벤트 및 공용 운영 체제 이벤트 로깅 및 추적 Api를 사용 하 여 수집 된 데이터를 게시 합니다. 이러한 Azure Stack 구성 요소의 예로 이러한 공급자: 네트워크 리소스, 저장소 리소스, 리소스 모니터링 하 고 리소스를 업데이트할 키를 누릅니다. 연결 된 사용자 환경 및 원격 분석 구성 요소는 SSL을 사용 하 여 데이터를 암호화 하 고 인증서 고정을 사용 하 여 Microsoft 데이터 관리 서비스에 HTTPS를 통한 데이터 전송.

> [!IMPORTANT]
> 원격 분석 데이터 흐름을 사용 하려면 포트 443(https)은 네트워크에 열려 있어야 합니다. 연결 된 사용자 환경 및 원격 분석 구성 요소에서 Microsoft 데이터 관리 서비스에 연결 https://v10.vortex-win.data.microsoft.com합니다. 연결 된 사용자 환경 및 원격 분석 구성 요소에도 연결 https://settings-win.data.microsoft.com 구성 정보를 다운로드 합니다.

## <a name="privacy-considerations"></a>개인정보처리방침 고려 사항

ETW 서비스 원격 분석 데이터를 보호 된 클라우드 저장소로 다시 라우팅합니다. 최소 권한의 원칙 원격 분석 데이터에 대 한 액세스를 안내합니다. 타당 한 비즈니스 필요를 사용 하 여 Microsoft 직원만 원격 분석 데이터에 액세스를 제공 됩니다. 에 설명 된 제한 된 목적 또는 고객의 판단에 따라 Microsoft 제외 하 고 개인 고객 데이터를 제 3 자와 공유 하지 합니다 [Microsoft 개인정보취급방침](https://privacy.microsoft.com/PrivacyStatement)합니다. Oem 및 파트너와 공유 되는 비즈니스 보고서 익명으로 집계 된 데이터를 포함 합니다. 의사 결정을 공유 하는 데이터 프라이버시, 법률 및 데이터 관리 이해 관계자를 포함 하는 내부 Microsoft 팀에서 이루어집니다.

Microsoft에서는 판단 하 고 사례 정보 최소화 합니다. 필요한 범위 내 에서만 서비스를 제공 하는 데 필요한 또는 분석에 대해를 저장 하는 정보를 수집 하기 위해 노력 합니다. 대부분의 Azure Stack 시스템 및 Azure 서비스를 작동 하는 방법에 대 한 정보는 6 개월 이내 삭제 됩니다. 요약 또는 집계 된 데이터를 오랜 기간 동안 유지 됩니다.

개인 정보 및 고객 정보의 보안을 반드시 이해 합니다.  Microsoft 고객 개인 정보 보호 및 Azure Stack에서 고객 데이터의 보호 신중 하 고 포괄적인 접근 방식을 사용 합니다. IT 관리자는 언제 든 지 기능 및 개인 정보 설정을 사용자 지정할 수 있습니다. 투명도 및 신뢰 하기 위한 노력의 명확 합니다.

- 에서는 수집할 데이터의 형식에 대 한 고객을 사용 하 여 오픈 소스입니다.
- 컨트롤의 기업 고객은 추가-자신만 개인 정보 설정을 사용자 지정할 수 있습니다.
- 우리 고객 개인 정보 보호 및 보안 먼저 배치 합니다.
- 원격 분석 데이터 사용 되는 방법에 대 한 투명 한 것입니다.
- 고객 환경을 개선 하기 위해 원격 분석 데이터를 사용 합니다.

Microsoft는 신용 카드 번호, 사용자 이름 및 암호, 전자 메일 주소 또는 유사한 중요 한 정보 등의 중요 한 데이터를 수집 하려고 하지 않습니다. 중요 한 정보가 실수로 수신 확인을 삭제 합니다.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft의 원격 분석 데이터를 사용 하는 방법의 예

원격 분석에서 신속 하 게 식별 하 고 고객 배포 및 구성에 대 한 중요 한 안정성 문제를 해결 하는 데 중요 한 역할을 재생 합니다. 원격 분석 데이터에서 정보는 서비스 또는 하드웨어 구성을 사용 하 여 문제를 식별할 수 있습니다. Microsoft의 고객 및 향상 된 드라이브에서이 데이터를 에코 시스템을 이동할 수 있는 통합 된 Azure Stack 솔루션의 품질 표시줄을 발생 시킵니다.

원격 분석에 더 잘 고객은 구성 요소를 배포 하는 방법을 이해 기능을 사용 하 고 비즈니스 목표를 달성 하기 위해 서비스를 사용 하 여 Microsoft도 도움이 됩니다. 이러한 정보 도움말 고객 환경 및 워크 로드 직접 영향을 줄 수 있는 영역에 대 한 엔지니어링 투자 우선 순위를 지정 합니다.

몇 가지 예로 컨테이너, 저장소 및 Azure Stack 역할을 사용 하 여 연결 된 네트워킹 구성의 고객이 사용 합니다. 드라이브 개선 사항 및 Azure Stack 관리 및 모니터링 솔루션에 인텔리전스 insights를 사용할 수도 있습니다. 이러한 향상 된이 기능 쉽게 문제를 진단 하는 고객에 대 한 및 microsoft 호출 적은 지원 하 여 비용을 절약 합니다.

## <a name="manage-telemetry-collection"></a>원격 분석 컬렉션 관리

에서는 권장 되는 조직에서 원격 분석 해제 하지 않습니다. 그러나 일부 시나리오에서는이 필요할 수 있습니다.

이러한 시나리오에서는 Azure Stack을 배포 하기 전에 레지스트리 설정을 사용 하 여 또는 Azure Stack을 배포한 후에 원격 분석 끝점을 사용 하 여 Microsoft로 보내는 원격 분석 수준을 구성할 수 있습니다.

### <a name="telemetry-levels-and-data-collection"></a>원격 분석 수준 및 데이터 수집

원격 분석 설정으로 변경 하기 전에 원격 분석 수준 및 각 수준에서 수집 되는 데이터를 이해 해야 합니다.

원격 분석 설정은 누적 및를 다음과 같이 범주별로 분류 된 4 개의 수준 (0-3)으로 그룹화 됩니다.

**0 (보안)**</br>
보안 데이터에만 해당 합니다. 운영 체제를 안전 하 게 유지 하는 데 필요한 정보입니다. 연결 된 사용자 환경 및 원격 분석 구성 요소 설정 및 Windows Defender에 대 한 데이터가 포함 됩니다. Azure Stack에 특정 원격 분석이이 수준에서 내보내집니다.

**1 (기본)**</br>
보안 데이터 및 기본 상태 및 품질 데이터입니다. 기본 장치 정보를 포함 하 여: 품질 관련 데이터, 응용 프로그램 호환성, 앱 사용 현황 데이터 및 데이터를는 **보안** 수준입니다. 사용자 원격 분석 수준 수 있도록 기본 Azure Stack 원격 분석을 설정합니다. 이 수준에서 수집 된 데이터에 포함 됩니다.

- *기본 장치 정보가* 형식 및 에코 시스템의 네이티브 및 가상 Windows Server 2016 인스턴스의 구성에 대 한 이해를 제공 하는 합니다. 다음 내용이 포함됩니다.

  - OEM 및 모델을 같은 컴퓨터 특성
  - 네트워크 어댑터의 수 및 속도 같은 네트워킹 특성입니다.
  - 프로세서 및 메모리 특성, 코어 수 및 설치 된 메모리 양 등입니다.
  - 드라이브의 수, 유형, 드라이브 및 드라이브 크기와 같은 저장소 특성입니다.

- *원격 분석 기능*, 업로드 된 이벤트, 삭제 된 이벤트 및 마지막으로 데이터의 백분율을 포함 하 여 업로드 시간입니다.
- *품질 관련 정보* Microsoft Azure Stack을 수행 하는 방법에 대 한 기본적인 이해를 개발할 수 있습니다. 예를 들어, 특정 하드웨어 구성에서 중요 한 경고의 수입니다.
- *호환성 데이터* 시스템 및 가상 컴퓨터에 설치 된 리소스 공급자에 대 한 이해를 제공 하는 데 도움이 됩니다는 합니다. 이 잠재적 호환성 문제를 식별합니다.

**2 (고급)**</br>
추가 정보를 포함 하 여: 운영 체제 및 Azure Stack 서비스를 사용 하는 방법, 이러한 서비스를 수행 하는 방법, 고급 안정성 데이터 및 데이터를는 **보안** 하 고 **기본** 수준입니다.

> [!NOTE]
> 기본 원격 분석 설정입니다.

**3 (전체)**</br>
모든 식별 하 고 문제를 해결 하는 데 도움이 되는 데 필요한 데이터와의 데이터를 **보안**, **기본**, 및 **고급** 수준입니다.

> [!IMPORTANT]
> 이러한 원격 분석 수준 Microsoft Azure Stack 구성 요소에만 적용 됩니다. 타사 소프트웨어 구성 요소 및 Azure Stack 하드웨어 파트너 로부터 하드웨어 수명 주기 호스트에서 실행 되는 서비스는 이러한 원격 분석 수준 외부에서 해당 클라우드 서비스와 통신할 수 있습니다. 해당 원격 분석 정책 및 옵트인 하거나 옵트아웃 하는 방법 이해 Azure Stack 하드웨어 솔루션 공급 업체와 함께 작업 해야 합니다.

Azure Stack 및 Windows 원격 분석을 해제 하면 SQL 원격 분석을도 사용 하지 않습니다. Windows 서버 원격 분석 설정의 의미에 대 한 자세한 내용은 참조는 [Windows 원격 분석 백서](https://aka.ms/winservtelemetry)합니다.

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Windows 레지스트리에서 원격 분석 수준 설정

Azure Stack을 배포 하기 전에 원격 분석 수준 물리적 호스트 컴퓨터에서 수동으로 설정 하려면 Windows 레지스트리 편집기를 사용할 수 있습니다. 그룹 정책과 같은 관리 정책에서 이미 있는 경우이 레지스트리 설정을 재정의 합니다.

Azure Stack 개발 키트 호스트를 배포 하기 전에 CloudBuilder.vhdx 부팅할 하 고 관리자 권한 PowerShell 창에서 다음 스크립트를 실행 합니다.

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK 및 다중 노드: 사용 하도록 설정 또는 배포 후 원격 분석을 사용 하지 않도록 설정

사용 하려면 배포 후 원격 분석을 사용 하지 않도록 설정 하는 권한 있는 끝점 (PEP) ERCS Vm에 노출 되는 액세스 해야 합니다.

1. 사용 하려면: `Set-Telemetry -Enable`
2. 사용 하지 않도록 설정 합니다. `Set-Telemetry -Disable`

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

[Azure를 사용 하 여 Azure Stack 등록](azure-stack-registration.md)