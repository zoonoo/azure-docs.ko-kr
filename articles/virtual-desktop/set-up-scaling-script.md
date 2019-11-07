---
title: 동적 크기 조정 Windows 가상 데스크톱 세션 호스트-Azure
description: Windows 가상 데스크톱 세션 호스트에 대 한 자동 크기 조정 스크립트를 설정 하는 방법을 설명 합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 744f7d5c191180757620e87d926422c9f1e0baba
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607451"
---
# <a name="scale-session-hosts-dynamically"></a>동적으로 세션 호스트 크기 조정

Azure의 많은 Windows 가상 데스크톱 배포의 경우 가상 머신 비용은 전체 Windows 가상 데스크톱 배포 비용의 상당 부분을 나타냅니다. 비용을 줄이려면 사용량이 적은 시간에 세션 호스트 Vm (가상 머신)을 종료 하 고 할당을 취소 한 다음 사용량이 가장 많은 시간에 다시 시작 하는 것이 가장 좋습니다.

이 문서에서는 간단한 크기 조정 스크립트를 사용 하 여 Windows 가상 데스크톱 환경에서 세션 호스트 가상 컴퓨터의 크기를 자동으로 조정 합니다. 크기 조정 스크립트의 작동 방식에 대 한 자세한 내용은 [크기 조정 스크립트 작동 방법](#how-the-scaling-script-works) 섹션을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

스크립트를 실행 하는 환경에는 다음 항목이 있어야 합니다.

- 해당 테 넌 트 (예: RDS 참가자)를 쿼리할 수 있는 권한이 있는 Windows 가상 데스크톱 테 넌 트 및 계정 또는 서비스 주체입니다.
- Windows 가상 데스크톱 서비스를 사용 하 여 구성 하 고 등록 한 세션 호스트 풀 Vm입니다.
- 작업 스케줄러를 통해 예약 된 작업을 실행 하 고 세션 호스트에 대 한 네트워크 액세스 권한이 있는 추가 가상 컴퓨터입니다. 이는 나중에 scaler VM으로 문서에서 참조 됩니다.
- 예약 된 작업을 실행 하는 VM에 설치 된 [Microsoft Azure 리소스 관리자 PowerShell 모듈](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) 입니다.
- 예약 된 작업을 실행 하는 VM에 설치 된 [Windows 가상 데스크톱 PowerShell 모듈](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) 입니다.

## <a name="recommendations-and-limitations"></a>권장 사항 및 제한 사항

크기 조정 스크립트를 실행 하는 경우 다음 사항을 염두에 두어야 합니다.

- 이 크기 조정 스크립트는 크기 조정 스크립트를 실행 하는 예약 된 작업 인스턴스당 하나의 호스트 풀만 처리할 수 있습니다.
- 크기 조정 스크립트를 실행 하는 예약 된 작업은 항상 켜져 있는 VM에 있어야 합니다.
- 크기 조정 스크립트와 구성의 각 인스턴스에 대해 별도의 폴더를 만듭니다.
- 이 스크립트는 multi-factor authentication이 필요한 Azure AD 사용자 계정을 사용 하 여 Windows 가상 데스크톱에 관리자로 로그인 하는 것을 지원 하지 않습니다. 서비스 주체를 사용 하 여 Windows 가상 데스크톱 서비스 및 Azure에 액세스 하는 것이 좋습니다. [이 자습서](create-service-principal-role-powershell.md) 에 따라 PowerShell을 사용 하 여 서비스 주체 및 역할 할당을 만듭니다.
- Azure의 SLA 보증은 가용성 집합의 Vm에만 적용 됩니다. 최신 버전의 문서는 크기 조정을 수행 하는 단일 VM이 있는 환경에 대해 설명 하며,이는 가용성 요구 사항을 충족 하지 못할 수 있습니다.

## <a name="deploy-the-scaling-script"></a>크기 조정 스크립트 배포

다음 절차에서는 크기 조정 스크립트를 배포 하는 방법을 설명 합니다.

### <a name="prepare-your-environment-for-the-scaling-script"></a>크기 조정 스크립트를 위한 환경 준비

먼저 크기 조정 스크립트를 위한 환경을 준비 합니다.

1. 도메인 관리 계정을 사용 하 여 예약 된 작업을 실행할 VM (scaler VM)에 로그인 합니다.
2. Scaler VM에서 크기 조정 스크립트와 해당 구성을 저장할 폴더를 만듭니다 (예 **: C:\\HostPool1**).
3. [크기 조정 스크립트 리포지토리에서](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) **basicscale. ps1**, **Config.xml**및 **Functions-PSStoredCredentials** 파일 및 **powershellmodules** 폴더를 다운로드 하 고 2 단계에서 만든 폴더에 복사 합니다. Scaler VM에 복사 하기 전에 두 가지 주요 방법으로 파일을 가져올 수 있습니다.
    - 로컬 컴퓨터에 git 리포지토리를 복제 합니다.
    - 각 파일의 **원시** 버전을 확인 하 고 각 파일의 내용을 복사 하 여 텍스트 편집기에 붙여 넣은 다음 해당 파일 이름 및 파일 형식으로 파일을 저장 합니다. 

### <a name="create-securely-stored-credentials"></a>안전 하 게 저장 된 자격 증명 만들기

다음으로 안전 하 게 저장 된 자격 증명을 만들어야 합니다.

1. 관리자 권한으로 PowerShell ISE를 엽니다.
2. 다음 cmdlet을 실행 하 여 RDS PowerShell 모듈을 가져옵니다.

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. 편집 창을 열고 **Function-PSStoredCredentials** 파일을 로드 한 다음 전체 스크립트를 실행 합니다 (F5).
4. 다음 cmdlet을 실행합니다.
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    예를 들어, **KeyPath "c:\\HostPool1"와 같이 변수 이름으로 설정 합니다.**
5. **StoredCredential-KeyPath \$KeyPath** cmdlet을 실행 합니다. 메시지가 표시 되 면 호스트 풀을 쿼리할 수 있는 권한이 있는 Windows 가상 데스크톱 자격 증명을 입력 합니다 (호스트 풀이 **config.xml**에서 지정 됨).
    - 다른 서비스 주체 또는 표준 계정을 사용 하는 경우 각 계정에 대해 **StoredCredential-KeyPath \$KeyPath** cmdlet을 한 번 실행 하 여 로컬에 저장 된 자격 증명을 만듭니다.
6. **StoredCredential** 를 실행 하 여 자격 증명이 성공적으로 만들어졌는지 확인 합니다.

### <a name="configure-the-configxml-file"></a>Config.xml 파일 구성

다음 필드에 관련 값을 입력 하 여 config.xml에서 크기 조정 스크립트 설정을 업데이트 합니다.

| 필드                     | 설명                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | 세션 호스트 Vm이 실행 되는 구독을 연결 하는 Azure AD 테 넌 트 ID     |
| AADApplicationId              | 서비스 사용자 응용 프로그램 ID                                                       |
| AADServicePrincipalSecret     | 이는 테스트 단계에서 입력 될 수 있지만 Functions-PSStoredCredentials. p s 1을 사용 하 여 자격 증명을 만든 후에는 비워 두어야 **합니다.**    |
| currentAzureSubscriptionId    | 세션 호스트 Vm이 실행 되는 Azure 구독의 ID입니다.                        |
| tenantName                    | Windows 가상 데스크톱 테 넌 트 이름                                                    |
| hostPoolName                  | Windows 가상 데스크톱 호스트 풀 이름                                                 |
| RDBroker                      | WVD 서비스 URL, 기본값 https:\//rdbroker.wvd.microsoft.com             |
| 사용자 이름                      | 서비스 사용자 응용 프로그램 ID (AADApplicationId에서와 동일한 서비스 주체를 사용할 수 있음) 또는 multi-factor authentication 없이 표준 사용자 |
| isServicePrincipal            | 허용 되는 값은 **true** 또는 **false**입니다. 사용 되는 두 번째 자격 증명 집합이 서비스 사용자 또는 표준 계정 인지 여부를 나타냅니다. |
| BeginPeakTime                 | 피크 사용 시간이 시작 되는 경우                                                            |
| EndPeakTime                   | 최대 사용 시간이 종료 되는 경우                                                              |
| TimeDifferenceInHours         | 현지 시간과 UTC 사이의 시간 차이 (시간)입니다.                                   |
| SessionThresholdPerCPU        | 최대 사용 시간 동안 새 세션 호스트 VM을 시작 해야 하는 시기를 결정 하는 데 사용 되는 CPU 임계값 당 최대 세션 수입니다.  |
| 이상 번호           | 사용량이 적은 사용 시간 동안 계속 실행 되는 호스트 풀 Vm의 최소 수             |
| LimitSecondsToForceLogOffUser | 사용자가 로그 아웃 하도록 강제 하기 전에 대기 하는 시간 (초)입니다. 0으로 설정 하면 사용자가 강제로 로그 아웃 되지 않습니다.  |
| LogOffMessageTitle            | 강제로 로그 아웃 하기 전에 사용자에 게 보내는 메시지의 제목입니다.                  |
| LogOffMessageBody             | 로그 아웃 하기 전에 사용자에 게 보내는 경고 메시지의 본문입니다. 예를 들어 "이 컴퓨터가 X 분 후에 종료 됩니다. 작업 내용을 저장 하 고 로그 아웃 하세요. " |

### <a name="configure-the-task-scheduler"></a>작업 스케줄러 구성

Config.xml 파일을 구성한 후에는 basicScaler 파일을 정기적으로 실행 하도록 작업 스케줄러를 구성 해야 합니다.

1. **작업 스케줄러**를 시작 합니다.
2. **작업 스케줄러** 창에서 **작업 만들기** ...를 선택 합니다.
3. **작업 만들기** 대화 상자에서 **일반** 탭을 선택 하 고 **이름** (예: "동적 RDSH")을 입력 한 다음 **사용자 로그온 여부에 관계 없이 실행** 을 선택 하 고 **가장 높은 권한으로 실행**합니다.
4. **트리거** 탭으로 이동한 다음 **새로 만들기 ...** 를 선택 합니다.
5. **새 트리거** 대화 상자의 **고급 설정**에서 **작업 반복** 을 선택 하 고 적절 한 기간 및 기간 (예: **15 분** 또는 **무기한**)을 선택 합니다.
6. **작업** 탭 및 **새로 만들기** ...를 선택 합니다.
7. **새 작업** 대화 상자에서 **프로그램/스크립트** 필드에 **powershell .exe** 를 입력 하 고 **인수 추가 (선택 사항)** 필드에 **C:\\크기 조정\\basicscale.** p s 1을 입력 합니다.
8. **조건** 및 **설정** 탭으로 이동 하 고 **확인** 을 선택 하 여 각에 대 한 기본 설정을 적용 합니다.
9. 크기 조정 스크립트를 실행 하려는 관리 계정의 암호를 입력 합니다.

## <a name="how-the-scaling-script-works"></a>크기 조정 스크립트 작동 방법

이 크기 조정 스크립트는 하루 중 피크 사용 기간의 시작과 끝을 포함 하 여 config.xml 파일에서 설정을 읽습니다.

최대 사용 시간 동안 스크립트는 현재 세션 수와 각 호스트 풀에 대해 현재 실행 중인 RDSH 용량을 확인 합니다. SessionThresholdPerCPU 파일에 정의 된 매개 변수를 기반으로 하 여 실행 중인 세션 호스트 Vm의 용량이 부족 하 여 기존 세션을 지원할 수 있는지 여부를 계산 합니다. 그렇지 않은 경우 스크립트는 호스트 풀에서 추가 세션 호스트 Vm을 시작 합니다.

사용률이 낮은 사용 시간 동안 스크립트는 config.xml 파일의 최소 번호 Ofrvsh 매개 변수를 기반으로 종료 해야 하는 세션 호스트 Vm을 결정 합니다. 이 스크립트는 호스트에 연결 된 새 세션을 방지 하기 위해 세션 호스트 Vm을 드레이닝 모드로 설정 합니다. Config.xml 파일의 **LimitSecondsToForceLogOffUser** 매개 변수를 0이 아닌 값으로 설정 하는 경우 스크립트는 현재 로그인 한 사용자에 게 작업을 저장 하도록 알리고 구성 된 시간 동안 기다린 다음 사용자가 로그 아웃 하도록 합니다. 모든 사용자 세션이 세션 호스트 VM에서 로그 오프 된 후 스크립트는 서버를 종료 합니다.

Config.xml 파일의 **LimitSecondsToForceLogOffUser** 매개 변수를 0으로 설정 하는 경우 스크립트는 호스트 풀 속성의 세션 구성 설정을 사용 하 여 사용자 세션 로그 오프를 처리 합니다. 세션 호스트 VM에 세션이 있는 경우 세션 호스트 VM이 실행 되는 상태로 유지 됩니다. 세션이 없으면 스크립트가 세션 호스트 VM을 종료 합니다.

이 스크립트는 작업 스케줄러을 사용 하 여 scaler VM 서버에서 정기적으로 실행 되도록 설계 되었습니다. 원격 데스크톱 서비스 환경의 크기에 따라 적절 한 시간 간격을 선택 하 고 가상 컴퓨터를 시작 하 고 종료 하는 데 시간이 걸릴 수 있습니다. 15 분 마다 크기 조정 스크립트를 실행 하는 것이 좋습니다.

## <a name="log-files"></a>로그 파일

크기 조정 스크립트는 두 개의 로그 파일인 **Wvdtenantscale .log** 및 **Wvdtenantscale .log**를 만듭니다. **Wvdtenantscale .log** 파일은 크기 조정 스크립트를 실행할 때마다 이벤트 및 오류 (있는 경우)를 기록 합니다.

**Wvdten앤틸리스 .log** 파일은 크기 조정 스크립트를 실행할 때마다 활성 코어 수와 활성 개수의 가상 컴퓨터를 기록 합니다. 이 정보를 사용 하 여 Microsoft Azure Vm의 실제 사용량 및 비용을 예측할 수 있습니다. 파일은 쉼표로 구분 된 값으로 형식이 지정 되며 각 항목에는 다음 정보가 포함 됩니다.

>시간, 호스트 풀, 코어, Vm

파일 이름은 .csv 확장명을 포함 하 여 Microsoft Excel로 로드 되 고 분석 되도록 수정할 수도 있습니다.
