---
title: 'Azure AD Connect 단일 개체 동기화 '
description: 문제 해결을 위해 한 개체를 Active Directory에서 Azure AD로 동기화 하는 방법을 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726065"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect 단일 개체 동기화 

Azure AD Connect 단일 개체 동기화 도구는 Active Directory에서 Azure Active Directory으로 개별 개체를 동기화 하는 데 사용할 수 있는 PowerShell cmdlet입니다. 생성 된 보고서를 사용 하 여 개체 동기화 문제를 조사 하 고 문제를 해결할 수 있습니다. 

> [!NOTE]
> 이 도구는 Active Directory에서 Azure Active Directory로의 동기화를 지원 합니다. Azure Active Directory에서 Active Directory로의 동기화는 지원 하지 않습니다. 
>
> 이 도구는 개체 수정 추가 및 업데이트 동기화를 지원 합니다. 개체 수정 삭제는 동기화를 지원 하지 않습니다. 

## <a name="how-it-works"></a>작동 방법
단일 개체 동기화 도구에는 가져올 원본 커넥터와 파티션을 찾기 위한 입력으로 Active Directory 고유 이름이 필요 합니다. Azure Active Directory에 대 한 변경 내용을 내보냅니다. 도구는 **provisioningObjectSummary** 리소스 형식과 비슷한 JSON 출력을 생성 합니다. 

단일 개체 동기화 도구는 다음 단계를 수행 합니다. 

 1. 동기화 범위에서 개체의 (원본) 도메인 (Active Directory 커넥터 및 파티션)을 확인 합니다. 
 2. 동기화 범위에서 개체의 (대상) 도메인 (Azure Active Directory 커넥터 및 파티션)을 확인 합니다. 
 3. 개체의 조직 구성 단위가 동기화 범위에 있는지 확인 합니다. 
 4. 커넥터 계정 자격 증명을 사용 하 여 개체에 액세스할 수 있는지 확인 합니다. 
 5. 동기화 범위에서 개체의 형식이 있는지 확인 합니다. 
 6. 그룹 필터링을 사용 하는 경우 개체가 동기화 범위에 있는지 확인 합니다. 
 7. Active Directory에서 Active Directory 커넥터 공간으로 개체를 가져옵니다. 
 8. Azure Active Directory에서 Azure Active Directory 커넥터 공간으로 개체를 가져옵니다. 
 9. Active Directory 커넥터 공간에서 개체를 동기화 합니다. 
 10. Azure Active Directory 커넥터 공간에서 Azure Active Directory 개체를 내보냅니다. 

JSON 출력 외에도 도구는 동기화 작업에 대 한 모든 세부 정보를 포함 하는 HTML 보고서를 생성 합니다. HTML 보고서는 **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> .htm** 에 있습니다. 필요한 경우이 HTML 보고서를 지원 팀과 공유 하 여 추가 문제 해결을 수행할 수 있습니다. 

HTML 보고서에는 다음이 포함 됩니다. 

|탭|Description|
|-----|-----|
|단계|개체를 동기화 하는 데 필요한 단계를 간략하게 설명 합니다. 각 단계에는 문제 해결에 대 한 세부 정보가 포함 되어 있습니다. 가져오기, 동기화 및 내보내기 단계에는 이름, 다중값, 유형, 값, 값 추가, 값 삭제, 작업, 동기화 규칙, 매핑 유형 및 데이터 원본과 같은 추가 특성 정보가 포함 됩니다.| 
|& 권장 사항 문제 해결|오류 코드 및 이유를 제공 합니다. 오류 정보는 오류가 발생 하는 경우에만 사용할 수 있습니다.| 
|수정된 속성|이전 값과 새 값을 표시 합니다. 이전 값이 없거나 새 값이 삭제 된 경우 해당 셀은 비어 있습니다. 다중값 특성의 경우 개수를 표시 합니다. 특성 이름은 단계 탭에 대 한 링크입니다. Azure Active Directory 커넥터 공간에서 Azure Active Directory으로 개체 내보내기: 이름, 다중값, 유형, 값, 값 추가, 값 삭제, 작업, 동기화 규칙, 매핑 유형 및 데이터 원본 등 특성에 대 한 추가 세부 정보를 포함 하는 특성 정보를 포함 하는 특성 정보입니다.| 
|요약|원본 및 대상 시스템에서 개체에 대해 발생 하는 작업과 식별자에 대 한 개요를 제공 합니다.| 

## <a name="prerequisites"></a>필수 조건 

단일 개체 동기화 도구를 사용 하려면 Azure AD Connect 이상 버전의 2021 년 3 월 릴리스를 사용 해야 합니다. 

### <a name="run-the-single-object-sync-tool"></a>단일 개체 동기화 도구 실행 

단일 개체 동기화 도구를 실행 하려면 다음 단계를 수행 합니다. 

 1. [관리자 권한으로 실행] 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다. 

 2. [실행 정책을](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) RemoteSigned 또는 무제한으로 설정 합니다. 

 3. 동기화 작업이 실행 되 고 있지 않은지 확인 한 후 동기화 스케줄러를 사용 하지 않도록 설정 합니다. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. AdSync 진단 모듈 가져오기 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. 단일 개체 동기화 cmdlet을 호출 합니다. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. 동기화 스케줄러를 다시 사용 하도록 설정 합니다. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|단일 개체 동기화 입력 매개 변수|Description| 
|-----|----|
|DistinguishedName|이 매개 변수는 필수 문자열 매개 변수입니다. </br></br>동기화 및 문제 해결이 필요한 Active Directory 개체의 고유 이름입니다.| 
|StagingMode|이 매개 변수는 선택적 스위치 매개 변수입니다.</br></br>이 매개 변수를 사용 하 여 Azure Active Directory 변경 내용을 내보내지 않도록 방지할 수 있습니다.</br></br>**참고**: cmdlet이 동기화 작업을 커밋합니다. </br></br>**참고**: Azure AD Connect 준비 서버는 Azure Active Directory 변경 내용을 내보내지 않습니다.|
|NoHtmlReport|이 매개 변수는 선택적 스위치 매개 변수입니다.</br></br>이 매개 변수를 사용 하 여 HTML 보고서 생성을 방지할 수 있습니다. 

## <a name="single-object-sync-throttling"></a>단일 개체 동기화 제한 

단일 개체 동기화 도구는 개체 동기화 문제를 조사 하 고 문제를 해결 하기 위한 **것입니다.** 스케줄러에 의해 실행 되는 동기화 주기를 대체 하기 위한 것이 **아닙니다** . Azure Active Directory에서 가져오기 및 Azure Active Directory로 내보내기에는 제한 제한이 적용 됩니다. 제한 한도에 도달 하면 5 분 후에 다시 시도 하세요. 

## <a name="next-steps"></a>다음 단계
- [개체 동기화 문제 해결](tshoot-connect-objectsync.md)
- [동기화 되지 않는 개체 문제 해결](tshoot-connect-object-not-syncing.md)
- [Azure AD Connect 개체 및 특성에 대 한 종단 간 문제 해결](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
