---
title: Azure Stack 1710 업데이트 (빌드 20171020.1) | Microsoft Docs
description: Azure Stack 용 업데이트 1710의에서 새로운 기능 알아보기 시스템의 알려진된 문제 및 업데이트를 다운로드 하는 위치를 통합 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cf870551a3dbd9b5ea0ef6f886dc6451e43b2c25
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377193"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure Stack 1710 업데이트 (빌드 20171020.1)

*적용 대상: Azure Stack 통합 시스템*

이 문서는 향상 된 기능에 설명 하 고 알려진 문제가이 릴리스 및 업데이트를 다운로드 하는 위치에 대 한이 업데이트 패키지의 수정. 알려진 문제는 알려진된 문제 나뉩니다 직접 프로세스와 관련 된 업데이트를 하 고 알려진 문제가 빌드 (설치 후).

> [!IMPORTANT]
> Azure Stack 통합 시스템에만이 업데이트 패키지가 됩니다. Azure Stack 개발 키트에는이 업데이트 패키지를 적용 되지 않습니다.

## <a name="improvements-and-fixes"></a>향상된 기능 및 수정 프로그램

이 업데이트는 Azure Stack에 대 한 다음 품질 향상 및 수정을 포함 합니다.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 향상 된 기능 및 수정

- Windows Server 2016에 대 한 업데이트: 2017 년 10 월 10 일-KB4041691 (OS 빌드 14393.1770입니다. 참조 [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) 자세한 내용은 합니다.

### <a name="additional-quality-improvements-and-fixes"></a>추가 품질 향상 및 수정

- 문제를 해결 하 고 시간 NTP (Network Protocol) 서버를 업데이트 하는 데 PowerShell cmdlet 권한이 부여 된 끝점을 추가 합니다.
- 권한 있는 끝점 관리 JEA (Just Enough) 끝점 모듈 및 cmdlet은 허용 목록 업데이트에 대 한 지원이 추가 되었습니다. 
- 권한 있는 끝점에서 로컬 언어 오류를 수정 했습니다.
- 게이트웨이 자격 증명을 순환 하는 기능을 추가 합니다.
- CBLocalAdmin 로컬 관리자 계정을 제거 합니다. 
- 하트 비트 경고 템플릿 콘텐츠를 고정 해야 하트 비트 수 있도록 경고 작업 올바르게 업데이트 후 합니다.
- FRU 작업 중 처리 제한 시간을 사용 하 여 패브릭 리소스 공급자를 수정 했습니다. 
- 클라우드 개발자가 Azure Stack에서 Azure Resource Manager API 프로필을 사용 하는 기능을 추가 합니다.
- 가상 컴퓨터의 배포 (dvm이)는 Windows 업데이트 서비스를 사용 하지 않도록 설정 합니다. 
- 사용자 인터페이스에서 노드 전원 켜기/끄기 동작을 제거 합니다.
- 다양 한 다른 성능 및 안정성 수정 되었습니다. 
 
## <a name="known-issues-with-the-update-process"></a>업데이트 프로세스를 사용 하 여 알려진된 문제

이 섹션에는 1710 업데이트를 설치 하는 동안 발생할 수 있는 알려진된 문제가 포함 됩니다.

> [!IMPORTANT]
> 업데이트가 실패 하는 경우 나중에 시도할 때 사용 해야 하는 업데이트를 다시 시작을 `Resume-AzureStackUpdate` 권한 있는 끝점에서 cmdlet. 관리자 포털을 사용 하 여 업데이트를 다시 시작 되지 않습니다. (이 릴리스에서 알려진된 문제입니다.) 자세한 내용은 [권한 있는 끝점을 사용 하 여 Azure Stack의 업데이트 모니터링](azure-stack-monitor-update.md)합니다.

| 증상  | 원인  | 해결 방법 |
|---------|---------|---------|
|다음과 유사한 오류가 업데이트를 수행 하는 경우<br>"저장소 호스트를 다시 시작 저장소 노드" 단계 중에 발생할 수 있습니다.<br> 업데이트 작업 계획 합니다.<br><br>**{"name": "다시 시작 저장소 호스트", "description": "다시 시작<br> 저장소 호스트.","errorMessage": "'다시 시작' 역할의 입력<br> 'BareMetal' 발생 예외: \n\n 컴퓨터<br> HostName-05를 건너뜁니다. 해당 LastBootUpTime를 검색 하지 못했습니다<br> 다음과 같은 오류 메시지를 사용 하 여 WMI 서비스를 통해:<br> RPC 서버를 사용할 수 없습니다.<br> (HRESULT의 예외: 0x800706BA). \nat 다시 호스트** | 이 문제는 일부 구성에 잠재적인 문제가 있는 드라이버에서 발생 합니다. | 1. 베이스 보드 관리 컨트롤러 (BMC) 웹 인터페이스에 로그인 하 고 오류 메시지에서 식별 되는 호스트를 다시 시작 합니다.<br><br>2. 권한 있는 끝점을 사용 하 여 업데이트를 다시 시작 합니다. |
| 업데이트 프로세스를 중지 된 것 처럼 표시 되는 업데이트를 수행 하면<br> 단계를 수행한 후 진행률을 확인 하지 "단계: 단계 2.4-설치를 실행 합니다.<br> 업데이트 작업 계획의 "업데이트 합니다.<br><br>이 단계는 그 뒤에 일련의.nupkg의 복사 프로세스<br> 내부 인프라 파일 공유에 파일입니다. 예: <br><br>**content\PerfCollector\VirtualMachines에서 1 파일을 복사 <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>또는 메시지를 표시 합니다.<br><br>**WarningMessage:Task: 호출 'LiveUpdate' 인터페이스의<br> 실패 ' Cloud\Fabric\VirtualMachines' 역할의:<br> 발생 하는 ' VirtualMachines' 역할의 'LiveUpdate' 입력을<br> 예외: 디스크에 공간이 부족 .**  | 로그 파일에는 인프라 가상 컴퓨터 및 문제에 Windows Server 스케일 아웃 파일 서버 (SOFS) 후속 업데이트에서 전달 되는 디스크 가득 문제 때문일 수 있습니다. | Microsoft 고객 서비스 및 지원 (CSS)에 문의 하세요. | 
| 다음과 유사한 오류가 업데이트를 수행 하는 경우<br> 단계에서 발생할 수 있습니다 "단계: 단계 2.13.2-업데이트를 실행 합니다.<br> *VM_Name*"의 업데이트 작업 계획입니다. (가상 머신<br> 이름이 다를 수 있습니다.)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: 호출의 'LiveUpdate' 인터페이스<br> 역할 'Cloud\Fabric\WAS'가 실패 했습니다: 'LiveUpdate' 역할의 입력<br> 예외를 발생 시 '가': 중 오류 발생 저장소<br> 초기화: API를 확인 하는 동안 오류가 발생 했습니다<br> Microsoft Storage 서비스에 대 한 호출: {"Message": "시간 제한이<br> Service Fabric을 사용 하 여 통신 하는 동안 오류가 발생 했습니다.<br> 예외 형식: TimeoutException 합니다.<br> 예외 메시지: 작업 시간이 초과 되었습니다. "}**  | 후속 업데이트에서 수정 될 Windows Server의 경우 I/O 시간 초과 문제 때문일 수 있습니다. | Microsoft CSS 지원에 문의 하세요.
| 다음과 유사한 오류가 업데이트를 수행 하는 경우<br> 단계 중 "단계 21 SQL server Vm 다시 시작 합니다." 발생할 수 있습니다.<br><br>**발생 하는 ' VirtualMachines' 역할의 'LiveUpdateRestart' 입력을<br> 예외: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> VM MachineName 쿼리-Sql01.-2017 년 10 월 13 오후 5시 11분: 50 VerboseMessage: [VirtualMachines: LiveUpdateRestart]<br> HighlyAvailable.-2017 년 10 월 13 VM은 표시 오후 5시 11분: 50<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart]에서<br>MS. Internal.ServerClusters.ExceptionHelp.Build<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(부울 force)에서 Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>Microsoft.FailoverClusters.PowerShell에서 ProcessRecord().<br> FCCmdlet.ProcessRecord()-2017 년 10 월 13 오후 5시 11분: 50 경고<br>메시지: 작업: 호출의 'LiveUpdateRestart' 인터페이스<br> 역할 'Cloud\Fabric\VirtualMachines'가 실패 했습니다.** | 가상 컴퓨터를 다시 시작 하지 못했습니다.이 문제가 발생할 수 있습니다. | Microsoft CSS 지원에 문의 하세요.
| 업데이트를 수행 하면 다음과 유사한 오류가 발생할 수 있습니다.<br><br>**2017-10-역할 'SQL' 유형의 '종료' 22T01:37:37.5369944Z<br> 예외가 발생: 오류가 노드 일시 중지<br> ' s45r1004-Sql01'에서 중지-SQL C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> 에서 542 CloudDeployment\Roles\SQL\SQL.psm1:line<br> 종료, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>응용 프로그램 \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: 시 50 개 줄 < ScriptBlock&#62;를<br> <No file>:에서 18 번 줄 < ScriptBlock&#62;, < 없는 파일&#62;: 16 줄** | 이 문제는 가상 컴퓨터 역할을 드레이닝 하도록 일시 중단 된 상태를 넣을 수 없습니다 경우에 발생할 수 있습니다. | Microsoft CSS 지원에 문의 하세요.
| 업데이트를 수행 하면 다음 오류 중 하나가 발생할 수 있습니다.<br><br>**역할 'ADFS' 'Validate' 유형의 예외가 발생: 유효성 검사<br> ADFS/그래프 역할 오류로 인해 실패 했습니다: 오류 검사 ADFS<br> 프로브 끝점 *endpoint_URI*: 예외 호출<br> " "0" 인수를 사용 하 여 GetResponse":" 원격 서버<br> 오류를 반환 했습니다: (503) 서버 사용할 수 없습니다. "에서 Invoke-<br>ADFSGraphValidation**<br><br>**역할 'ADFS' 'Validate' 유형의 예외가 발생: 유효성 검사<br> ADFS/그래프 역할 오류로 인해 실패 했습니다: 오류 페치<br> ADFS 속성: 연결할 수 없습니다 <br>net.tcp://localhost: 1500/정책입니다. 연결 시도가 지속<br> 00:00:02.0498923의 시간 범위에 대 한 합니다. TCP 오류 코드<br> 10061: 없습니다 연결할 수 있으므로 대상<br> 컴퓨터 연결을 거부 했으므로 127.0.0.1:1500.<br> Invoke ADFSGraphValidation에서** | 업데이트 작업 계획 Active Directory Federation Services (AD FS) 상태를 확인할 수 없습니다. | Microsoft CSS 지원에 문의 하세요.

## <a name="known-issues-post-installation"></a>알려진된 문제 (사후 설치)

이 섹션에서는 알려진 문제가 20171020.1 빌드 후 설치 합니다.

### <a name="portal"></a>포털

- 관리자 포털에서 계산 또는 저장소 리소스를 볼 수 없습니다. 이 업데이트를 설치 하는 동안 오류가 발생 하 고는 업데이트를 잘못 보고 성공으로 나타냅니다. 이 문제가 발생 하는 경우 Microsoft CSS에 문의 하세요.
- 포털에서 빈 대시보드를 볼 수 있습니다. 대시보드를 복구 하려면 포털의 오른쪽 위 모서리에서 기어 아이콘을 선택 하 고 선택한 **기본 설정 복원**합니다.
- 리소스 그룹의 속성을 볼 때 합니다 **이동** 단추가 비활성화 됩니다. 이는 정상적인 동작입니다. 리소스 그룹 구독 간의 이동 현재 지원 되지 않습니다.
- 드롭다운 목록에서 구독, 리소스 그룹 또는 위치 선택 되는 위치는 모든 워크플로에 대 한 다음 문제 중 하나 이상이 발생할 수 있습니다.

   - 목록의 맨 위에 있는 빈 행을 볼 수 있습니다. 여전히 예상 대로 항목을 선택할 수 있어야 합니다.
   - 드롭다운 목록에서 항목 목록이 짧은 경우에 수 항목 이름을 볼 수 없습니다.
   - 여러 구독이 있는 사용자, 리소스 그룹 드롭 다운 목록 비어 있을 수 있습니다. 

   마지막으로 두 가지 문제를 해결 하려면 구독 또는 리소스 그룹 (알고) 하는 경우의 이름을 입력할 수 있습니다 또는 PowerShell을 대신 사용할 수 있습니다.
- 분리 된 리소스에서 사용자 구독 결과 삭제합니다. 대 안으로 사용자 리소스 또는 전체 리소스 그룹을 삭제 하 고 사용자 구독을 삭제 합니다.
- Azure Stack 포털을 사용 하 여 구독에 대 한 권한을 볼 수 없는 합니다. 대 안으로, PowerShell을 사용 하 여 사용 권한을 확인할 수 있습니다.
- 합니다 **서비스 상태** 블레이드가 로드 하지 못했습니다. 관리자 또는 사용자 포털, Azure Stack에서에서 서비스 상태 블레이드를 열 때 오류를 표시 하 고 정보를 로드 하지 않습니다. 이는 정상적인 동작입니다. 선택 하 고 서비스 상태를 열 수 있지만이 기능은 아직 제공 되지 되지만 Azure Stack의 이후 버전에서 구현 됩니다.
 

### <a name="backup"></a>Backup

- 인프라 백업에서는 사용 하지 마십시오 합니다 **인프라 백업** 블레이드입니다.

### <a name="health-and-monitoring"></a>상태 및 모니터링

- 인프라 역할 인스턴스를 다시 부팅 하면 다시 부팅에 실패 했음을 나타내는 메시지가 표시 될 수 있습니다. 그러나 다시 부팅 실제로 성공 했습니다.

### <a name="marketplace"></a>Marketplace
- Azure Stack marketplace를 사용 하 여 항목을 추가 하려는 경우는 **Azure에서 추가** 옵션을 일부 항목 다운로드에 대 한 표시 될 수 있습니다.
- 사용자를 구독 하지 않고 전체 마켓플레이스를 찾아볼 수 및 계획 및 제품은 같은 관리 항목을 볼 수 있습니다. 이러한 항목은 사용자에 게 기능입니다.

### <a name="compute"></a>컴퓨팅
- 사용자가 지역 중복 저장소를 사용 하 여 가상 컴퓨터를 만드는 옵션이 제공 됩니다. 이렇게이 구성 하면 가상 머신 만들기 실패 합니다.
- 가상 컴퓨터 가용성 1의 장애 도메인이 집합과 하나의 업데이트 도메인을 구성할 수 있습니다.
- 가상 머신 확장 집합을 만드는 marketplace 환경은 없습니다. 템플릿을 사용 하 여 확장 집합을 만들 수 있습니다.
- 가상 머신 확장 집합에 대 한 크기 조정 설정을 포털에서 사용할 수 없는 경우 대 안으로 사용할 수 있습니다 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)합니다. PowerShell 버전 차이로 인해 사용 해야 합니다 `-Name` 매개 변수 대신 `-VMScaleSetName`합니다.
 
### <a name="networking"></a>네트워킹
- 포털을 사용 하 여 부하 분산 장치 공용 IP 주소를 만들 수 없습니다. 해결 방법으로 부하 분산 장치를 만들려면 PowerShell을 사용할 수 있습니다.
- 네트워크 부하 분산 장치를 만들 때 네트워크 주소 변환 (NAT) 규칙을 만들어야 합니다. 를 지정 하지 않으면 부하 분산 장치를 만든 후에 NAT 규칙을 추가 하려고 할 때 오류를 받게.
- VM 생성 및 해당 IP 주소와 연관 된 후 가상 컴퓨터 (VM)에서 공용 IP 주소를 분리할 수 없습니다. 연결 해제 하려면 나타나지만 이전에 할당된 된 공용 IP 주소 유지 원래 VM과 연결 됩니다. 새 VM에 IP 주소를 다시 할당 하는 경우에이 문제가 발생 (일반적으로 라고 한 *VIP 교체*). 앞으로의 모든 새 아닌 원래 연결 된 VM에 연결에서이 IP 주소 결과 통해 연결 하려고 시도 합니다. 현재 새 VM 만들기에 대 한 새 공용 IP 주소 사용만 해야 합니다.
 
### <a name="sqlmysql"></a>SQL/MySQL
- 새 SQL 또는 MySQL SKU에서 테 넌 트 데이터베이스를 만들 수 전에 한 시간까지 걸릴 수 있으므로 합니다. 
- SQL 및 MySQL 리소스 공급자에 의해 수행 되지 않은 서버를 호스트에서 직접 항목의 생성은 지원 되지 않으며 일치 하지 않는 상태가 될 수 있습니다.
 
### <a name="app-service"></a>App Service
- 사용자는 구독에 해당 첫 번째 Azure Function 만들기 전에 저장소 리소스 공급자를 등록 해야 합니다.
 
### <a name="field-replaceable-unit-fru-procedures"></a>필드 교체 장치 (FRU) 절차

- 업데이트 실행 중 오프 라인 이미지 패치가 적용 되지 됩니다. 배율 단위 노드를 교체 해야 할 경우 대체 노드 최신 패치 수준이 되도록 OEM 하드웨어 공급 업체를 사용 하 여 작동 합니다.

## <a name="download-the-update"></a>업데이트 다운로드

1710 업데이트 패키지를 다운로드할 수 있습니다 [여기](https://aka.ms/azurestackupdatedownload)합니다.

## <a name="next-steps"></a>다음 단계

- Azure Stack의 업데이트 관리 개요를 참조 하세요 [Azure Stack 개요에 대 한 업데이트 관리](azure-stack-updates.md)합니다.
- 업데이트를 적용 하는 방법에 대 한 자세한 내용은 [Azure Stack의 업데이트 적용](azure-stack-apply-updates.md)합니다.
