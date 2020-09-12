---
title: 'Azure AD Connect: 그룹 쓰기 저장'
description: 이 문서에서는 Azure AD Connect의 그룹 쓰기 저장에 대해 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2e24246c749978cd2bbb5b3d0821eea6d7dfb4b
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660875"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect 그룹 쓰기 저장

그룹 쓰기 저장은 고객이 하이브리드 요구에 맞게 클라우드 그룹을 활용할 수 있도록 합니다. Microsoft 365 그룹 기능을 사용 하는 경우 이러한 그룹이 온-프레미스 Active Directory에 표시 되도록 할 수 있습니다. 이 옵션은 온-프레미스 Active Directory에 Exchange가 있는 경우에 **만** 사용할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소
그룹 쓰기 저장을 사용 하도록 설정 하려면 다음 필수 구성 요소를 충족 해야 합니다.
- 테 넌 트에 대 한 라이선스를 Azure Active Directory Premium 합니다.
- Exchange 온-프레미스 조직과 Microsoft 365 간의 하이브리드 배포를 구성 하 고 제대로 작동 하는지 확인 했습니다.
- 지원 되는 Exchange 온-프레미스 버전을 설치 합니다.
- Azure Active Directory Connect를 사용 하 여 Single Sign-On 구성 

## <a name="enable-group-writeback"></a>그룹 쓰기 저장 사용
그룹 쓰기 저장을 사용 하도록 설정 하려면 다음 단계를 사용 합니다.

1. Azure AD Connect 마법사를 열고 **구성** 을 선택한 후 **다음**을 클릭 합니다.
2. **동기화 옵션 사용자 지정** 을 선택 하 고 **다음**을 클릭 합니다.
3. **AZURE AD에 연결** 페이지에서 자격 증명을 입력 합니다. **다음**을 클릭합니다.
4. **선택적 기능** 페이지에서 이전에 구성한 옵션이 여전히 선택 되어 있는지 확인 합니다.
5. **그룹 쓰기 저장** 을 선택 하 고 **다음**을 클릭 합니다.
6. **쓰기 저장 (Writeback) 페이지**에서 Microsoft 365에서 온-프레미스 조직으로 동기화 된 개체를 저장할 OU (조직 구성 단위) Active Directory 선택 하 고 **다음**을 클릭 합니다.
7. 구성 **준비 완료** 페이지에서 **구성**을 클릭 합니다.
8. 마법사가 완료 되 면 구성 완료 페이지에서 **끝내기** 를 클릭 합니다.
9. Azure Active Directory Connect 서버에서 관리자 권한으로 Windows PowerShell을 열고 다음 명령을 실행 합니다.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

Microsoft 365 그룹을 구성 하는 방법에 대 한 자세한 내용은 [온-프레미스 Exchange 하이브리드를 사용 하 여 Microsoft 365 그룹 구성](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect)을 참조 하세요.

## <a name="disabling-group-writeback"></a>그룹 쓰기 저장 사용 안 함
그룹 쓰기 저장을 사용 하지 않도록 설정 하려면 다음 단계를 사용 합니다. 


1. Azure Active Directory Connect 마법사를 시작 하 고 추가 작업 페이지로 이동 합니다. **동기화 옵션 사용자 지정** 작업을 선택 하 고 **다음**을 클릭 합니다.
2. **선택적 기능** 페이지에서 그룹 쓰기 저장을 선택 취소 합니다.  그룹이 삭제 된다는 것을 알리는 경고가 표시 됩니다.  **예**를 클릭합니다.
   >[!IMPORTANT]
   > 그룹 쓰기 저장을 사용 하지 않도록 설정 하면 이전에이 기능으로 만든 모든 그룹이 다음 동기화 주기에서 로컬 Active Directory에서 삭제 됩니다. 

   ![확인란 선택 취소](media/how-to-connect-group-writeback/group2.png)
  
3. **다음**을 클릭합니다.
4. **구성**을 클릭합니다.

 >[!NOTE]
 > 그룹 쓰기 저장을 사용 하지 않도록 설정 하면 Azure Active Directory 커넥터에서 전체 가져오기 및 전체 동기화 플래그를 ' t r u e '로 설정 하 여 다음 동기화 주기에서 규칙 변경 내용을 전파 하 여 이전에 Active Directory에 다시 쓴 그룹을 삭제 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.