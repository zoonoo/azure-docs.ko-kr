---
title: Azure AD Connect 동기화 V2 엔드포인트 공개 미리 보기 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect 동기화 v2 엔드포인트 API에 대한 업데이트를 다룹니다.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f4eba1b48b651c8efe9e9d737e226727cb244fb
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662474"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect 동기화 V2 엔드포인트 API(공개 미리 보기) 
Microsoft는 Azure AD Connect에 대한 새 엔드포인트(API)를 배포하여 Azure Active Directory에 대한 동기화 서비스 작업의 성능을 향상시켰습니다. 새 V2 엔드포인트를 활용하면 Azure AD로 내보내기 및 가져오기에 대한 성능이 크게 향상됩니다. 이 새 엔드포인트는 다음을 지원합니다.
    
 -  최대 250k 멤버가 포함된 그룹 동기화
 - Azure AD로 내보내기 및 가져오기 성능 향상
 
> [!NOTE]
> 현재 새 끝점에는 다시 작성 된 Microsoft 365 그룹에 대 한 그룹 크기 제한이 구성 되어 있지 않습니다. 이는 Active Directory 및 동기화 주기 대기 시간에 영향을 미칠 수 있습니다. 그룹 크기를 점진적으로 늘리는 것이 좋습니다.  


## <a name="pre-requisites"></a>필수 구성 요소  
새 V2 엔드포인트를 사용하려면 [Azure AD Connect 버전 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 이상을 사용하고 아래 제공되는 배포 단계에 따라 Azure AD Connect 서버에 대해 V2 엔드포인트를 사용하도록 설정해야 합니다.   

>[!NOTE]
>현재 이 공개 미리 보기는 Azure 글로벌 클라우드에서만 사용할 수 있으며 [국가별 클라우드](../develop/authentication-national-cloud.md)에는 사용할 수 없습니다.

### <a name="public-preview-limitations"></a>공용 미리 보기 제한 사항  
이 릴리스는 광범위한 테스트를 거쳤지만 여전히 문제가 발생할 수 있습니다. 이 공개 미리 보기 릴리스의 목표 중 하나는 이러한 문제를 찾아서 수정하는 것입니다.  

>[!IMPORTANT]
> 이 공개 미리 보기 릴리스에 대해 지원이 제공되지만, 발생할 수 있는 모든 문제를 Microsoft가 즉시 해결할 수 있는 것은 아닙니다. 따라서 프로덕션 환경에 이 릴리스를 배포하기 전에 신중히 판단하는 것이 좋습니다. 

## <a name="deployment-guidance"></a>배포 지침 
V2 엔드포인트를 사용하려면 [Azure AD Connect 버전 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 이상을 배포해야 합니다. 제공된 링크를 사용하여 다운로드합니다. 

환경에 새 엔드포인트를 롤아웃하려면 [스윙 마이그레이션](./how-to-upgrade-previous-version.md#swing-migration) 방법을 따르는 것이 좋습니다. 이렇게 하면 중요한 롤백이 필요한 상황에 대비한 명확한 대체 계획을 확보할 수 있습니다. 다음 예에서는 이 시나리오에서 스윙 마이그레이션을 사용하는 방법을 보여 줍니다. 스윙 마이그레이션 배포 방법에 대한 자세한 내용은 제공된 링크를 참조하세요. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>V2 엔드포인트 배포를 위한 스윙 마이그레이션
다음 단계는 스윙 방법을 사용하여 v2 엔드포인트를 배포하는 과정을 안내 합니다.

1. 현재 준비 서버에 V2 엔드포인트를 배포합니다. 이 서버는 다음 단계에서 **V2 서버** 라고 합니다. 현재 활성 서버는 V1 엔드포인트를 사용하여 계속해서 프로덕션 워크로드를 처리합니다. 이 엔드포인트는 아래에서 **V1 서버**로 지칭됩니다.
1. **V2 서버**가 예상한 대로 가져오기를 계속 처리하고 있는지 확인합니다. 이 단계에서 큰 그룹은 Azure AD 또는 온-프레미스 AD에 프로비저닝되지 않지만 업그레이드로 인해 기존 동기화 프로세스에 예기치 않은 다른 영향이 발생하지 않았음을 확인할 수 있습니다. 
2. 유효성 검사가 완료되면 **V2 서버**를 활성 서버로, **V1 서버**를 준비 서버로 전환합니다. 이 시점에서 동기화 되는 범위에 있는 규모가 많은 그룹은 Azure AD에 프로 비전 되 고, 그룹 쓰기 저장을 사용 하도록 설정 된 경우 대량 Microsoft 365 통합 그룹이 AD에 프로 비전 됩니다.
3. **V2 서버**가 정상 작동하면서 큰 그룹을 성공적으로 처리하고 있는지 확인합니다. 일정 기간 동안 이 단계에 머물면서 동기화 프로세스를 모니터링하는 것이 좋습니다.
  >[!NOTE]
  > 이전 구성으로 다시 전환해야 하는 경우 **V2 서버**에서 **V1 서버**로 다시 스윙 마이그레이션을 수행할 수 있습니다. V1 엔드포인트는 멤버가 50k를 초과하는 그룹을 지원하지 않으므로 Azure AD 또는 온-프레미스 AD에서 Azure AD Connect에 의해 프로비저닝된 모든 큰 그룹은 이후 삭제됩니다. 
4. V2 엔드포인트 사용에 대해 확실이 들면 V2 엔드포인트 사용을 시작하도록 **V1 서버**를 업그레이드합니다. 
 

## <a name="expectations-of-performance-impact"></a>성능 영향에 대한 기대  
V2 엔드포인트를 사용하는 경우 성능 향상은 동기화되는 그룹의 수, 해당 그룹의 크기 및 그룹 변동(사용자를 그룹 멤버로 추가하고 제거하는 과정에서 발생하는 작업)에 따라 달라집니다. 동기화되는 그룹의 수, 크기 또는 변동을 늘리지 않고 새 엔드포인트를 사용하면 Azure AD로의 내보내기와 가져오기 시간이 단축됩니다. 
 
그러나 큰 그룹을 동기화할 때 필요한 부가적인 처리로 인해 성능 향상이 무효화될 수 있습니다. 동기화 프로세스에 큰 그룹을 너무 많이 추가하면 전체 동기화 시간이 늘어날 수 있습니다.  

새 그룹 추가가 동기화 성능에 미치는 영향을 더 잘 파악하려면 처음에는 멤버 수가 100k 미만인 소수의 큰 그룹만 동기화하는 것이 좋습니다. 그런 다음, OU, 특성 또는 최대 그룹 크기 필터링을 통해 더 많은 그룹을 범위로 가져와서 그룹의 수와 크기를 늘릴 수 있습니다. 성능 개선은 온-프레미스 AD 커넥터가 아닌 Azure AD 커넥터에 대한 내보내기 및 가져오기 작업에서 실현됩니다. 

## <a name="deployment-step-by-step"></a>단계별 배포 
다음 세 단계는 새 V2 엔드포인트 배포의 심층적인 예제입니다.  이 단계를 배포 지침으로 사용하세요.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>1 단계 - Azure AD Connect 설치 및 유효성 검사 
먼저 [Azure AD Connect 버전 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 이상으로 설치 또는 업그레이드하는 단계를 수행하고, 2단계로 이동해 V2 엔드포인트를 사용하도록 설정하기 전에 동기화 프로세스의 유효성을 검사하는 것이 좋습니다. Azure AD Connect 서버에서 다음을 수행합니다. 


1. [선택 사항] 데이터베이스 백업 
2. [Azure AD Connect version 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) 이상 설치 또는 업그레이드
3. 설치 유효성 검사 

### <a name="phase-2--enable-the-v2-endpoint"></a>2 단계 – V2 엔드포인트 사용 설정 
다음 단계는 V2 엔드포인트를 사용 설정하는 것입니다. 

> [!NOTE]
> 서버에 대해 V2 엔드포인트를 사용하도록 설정하면 기존 워크로드에 대한 몇 가지 성능 향상을 확인할 수 있습니다. 그러나 멤버가 50K를 초과하는 그룹은 아직 동기화할 수 없습니다. 

V2 엔드포인트로 전환하려면 다음 단계를 사용합니다. 

1. 관리자 권한으로 PowerShell 프롬프트를 엽니다. 
2. 동기화 작업이 실행되고 있지 않은지 확인한 다음, 동기화 스케줄러를 사용하지 않도록 설정합니다. 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. 새 모듈을 가져옵니다. 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  V2 엔드포인트로 전환합니다.

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
이제 서버에 대해 V2 엔드포인트를 사용 설정했습니다. V2 엔드포인트를 사용 설정한 후, 다음 단계로 이동하여 그룹 크기 제한을 늘리기 전에 일정 시간 동안 예상치 못한 결과가 없는지 확인합니다. 
>[!NOTE]
>파일/모듈 경로는 Azure AD Connect를 설치할 때 제공된 설치 경로에 따라 다른 드라이브 문자를 사용할 수 있습니다. 


### <a name="phase-3--increase-the-group-membership-limit"></a>3 단계 – 그룹 멤버 자격 한도 증가 
예기치 않은 결과 없이 서비스가 실행되는지 확인했다면 계속 진행하여 그룹 멤버 자격 한도를 늘릴 수 있습니다. 먼저 멤버 자격 한도를 약간 높게, 예를 들어 75K 멤버 정도로 늘려 더 큰 그룹이 Azure AD와 동기화되는 것을 확인하는 것이 좋습니다. 결과가 만족스러우면 멤버 한도를 더 높일 수 있습니다.  

최대 한도는 그룹당 250K 멤버입니다. 

다음 단계를 사용하여 멤버 자격 한도를 늘릴 수 있습니다.  

1. Azure AD 동기화 규칙 편집기를 엽니다. 
2. 편집기에서 방향에 **아웃바운드**를 선택합니다. 
3. **AAD로 나가기 – 그룹 조인** 동기화 규칙을 클릭합니다. 
4. **편집** 단추를 클릭합니다. ![동기화 규칙 편집](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. **예** 단추를 클릭하여 기본 규칙을 사용하지 않도록 설정하고 편집 가능한 복사본을 만듭니다.
 ![동기화 규칙 편집](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. **설명** 페이지의 팝업 창에서 우선 순위를 1에서 99 사이의 사용 가능한 값으로 설정합니다. ![동기화 규칙 편집](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. **변환** 페이지에서 **멤버** 변환에 대한 **원본** 값을 업데이트하여 '50000'을 50001에서 250000 사이의 값으로 바꿉니다. 이렇게 바꾸면 Azure AD와 동기화되는 그룹의 최대 멤버 자격 크기가 늘어납니다. 큰 그룹 동기화가 동기화 성능에 미치는 영향을 파악하기 위해 먼저 100k로 시작하는 것이 좋습니다. 
 
 **예제** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![동기화 규칙 편집](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. 저장을 클릭합니다. 
10. 관리자 PowerShell 프롬프트를 엽니다. 
11. 동기화 스케줄러를 다시 사용하도록 설정합니다. 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Azure AD Connect Health가 사용 설정되지 않은 경우 Windows 애플리케이션 이벤트 로그 설정을 변경하여 로그를 덮어쓰지 않고 보관하도록 합니다. 로그는 향후 문제 해결 활동에 사용될 수 있습니다. 

>[!NOTE]
> 새 엔드포인트를 사용 설정한 후 AAD 커넥터에서 이름이 'dn-attributes-failure'인 내보내기 오류가 표시될 수 있습니다. ID가 6949인 각 오류에 해당하는 이벤트 로그 항목이 있습니다. 오류에서 정보를 얻을 수 있습니다. 오류는 설치 문제를 나타내지는 않고, 멤버 개체 자체가 Azure AD와 동기화되지 않았기 때문에 동기화 프로세스에서 특정 멤버를 Azure AD의 그룹에 추가할 수 없음을 나타냅니다. 

새 V2 엔드포인트 코드에서 일부 내보내기 오류 유형을 처리하는 방법은 V1 코드와는 약간 다릅니다.  V2 엔드포인트를 사용하는 경우 정보 제공을 위한 오류 메시지가 더 많이 표시될 수 있습니다. 

>[!NOTE]
> Azure AD Connect를 업그레이드하는 경우 업그레이드 프로세스에서 변경 내용이 유지되지 않으므로 2단계의 작업을 다시 실행해야 합니다. 

이후 **AAD로 나가기 – 그룹 조인** 동기화 규칙의 그룹 멤버 한도를 늘릴 때는 전체 동기화는 필요 없으므로 PowerShell에서 다음 명령을 실행하여 전체 동기화를 억제할 수 있습니다. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> 구성원이 50k 이상인 통합 그룹 Microsoft 365 있는 경우 그룹을 Azure AD Connect으로 읽어 그룹 쓰기 저장을 사용 하도록 설정 하면 온-프레미스 AD에 기록 됩니다. 

## <a name="rollback"></a>롤백 
V2 엔드포인트를 사용 설정한 상황에서 롤백해야 하는 경우 다음 단계에 따릅니다. 

1. Azure AD Connect 서버에서 다음을 수행합니다. [선택 사항] 데이터베이스 백업 
2. 관리자 PowerShell 프롬프트를 엽니다.
3. 동기화 작업이 실행되고 있지 않은지 확인한 다음, 동기화 스케줄러를 사용하지 않도록 설정합니다.
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. V1 엔드포인트로 전환합니다.* 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Azure AD 동기화 규칙 편집기를 엽니다. 
6. 편집 가능한 **AAD로 나가기 – 그룹 조인** 동기화 규칙 복사본을 삭제합니다. 
7. **AAD로 나가기 – 그룹 조인** 동기화 규칙 기본 복사본을 사용 설정합니다. 
8. 관리자 PowerShell 프롬프트를 엽니다. 
9. 동기화 스케줄러를 다시 사용하도록 설정합니다. 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> V 2에서 V1 끝점으로 다시 전환 하는 경우, Azure Microsoft 365 AD에 프로 비전 된 AD 그룹과 AD에 프로 비전 된 통합 그룹 모두에 대해 전체 동기화를 실행 한 후에는 전체 동기화를 실행 한 후에 50 개 이상의 구성원과 동기화 된 그룹이 삭제 됩니다. 

## <a name="frequently-asked-questions"></a>질문과 대답  
**Q: 고객이 프로덕션에서 이 기능을 사용할 수 있나요?**   
</br>예, 앞에서 설명한 주의 사항을 참고하여 프로덕션 환경에서 사용할 수 있습니다.
 
**Q: 문제가 발생할 경우 고객은 누구에게 연락하나요?**   
</br>이 기능을 사용하면서 지원이 필요한 경우 지원 사례를 열어야 합니다. 
 
**Q: 공개 미리 보기는 자주 업데이트될 예정인가요?**   
</br>공개 미리 보기 동안에는 제한된 수준의 지속적인 변화가 발생합니다. 프로덕션에 공개 미리 보기 기능을 배포하는 경우 이에 따르는 위험을 감안해야 합니다.  
 
**Q: 다음 마일스톤까지 얼마나 걸리나요?**   
</br>추가 마일스톤에 도달하기 전에 공개 미리 보기 기능은 철회되고 다시 설계될 수 있습니다.  
 
## <a name="next-steps"></a>다음 단계

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)