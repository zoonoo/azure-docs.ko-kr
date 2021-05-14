---
title: 'Azure AD Connect: 그룹 쓰기 저장'
description: 이 문서에서는 Azure AD Connect의 그룹 쓰기 저장에 대해 설명합니다.
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
ms.openlocfilehash: 90babde79bf53cf85c6cf2bf9e583c27d7cd21b6
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162944"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect 그룹 쓰기 저장

그룹 쓰기 저장을 통해 고객은 하이브리드 요구 사항에 클라우드 그룹을 활용할 수 있습니다. Microsoft 365 그룹 기능을 사용하는 경우 이 그룹을 온-프레미스 Active Directory에 표시할 수 있습니다. 이 옵션은 Exchange가 온-프레미스 Active Directory에 있는 경우 **에만** 사용할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

그룹 쓰기 저장을 사용하도록 설정하려면 다음 필수 구성 요소를 충족해야 합니다.
- 테넌트에 대한 Azure Active Directory Premium 라이선스입니다.
- Exchange 온-프레미스 조직과 Microsoft 365 간의 하이브리드 배포를 구성하고 제대로 작동하는지 확인했습니다.
- 지원되는 Exchange 온-프레미스 버전 설치
- Azure Active Directory Connect를 사용하여 Single Sign On 구성

## <a name="enable-group-writeback"></a>그룹 쓰기 저장 사용

그룹 쓰기 저장을 사용하려면 다음 단계를 수행하십시오.

1. Azure AD Connect 마법사를 열고 **구성** 을 선택한 후 **다음** 을 클릭합니다.
2. **동기화 옵션 사용자 지정** 을 선택하고, **다음** 을 클릭합니다.
3. **Azure AD에 연결** 페이지에서 자격 증명을 입력합니다. **다음** 을 클릭합니다.
4. **선택적 기능** 페이지에서 이전에 구성한 옵션이 여전히 선택되어 있는지 확인합니다.
5. **그룹 쓰기 저장** 을 선택한 후 **다음** 을 클릭합니다.
6. **쓰기 저장 페이지** 에서, Microsoft 365에서 온-프레미스 조직으로 동기화된 개체를 저장할 Active Directory OU(조직 구성 단위)를 선택하고 **다음** 을 클릭합니다.
7. 구성 **준비** 페이지에서 **구성** 을 클릭합니다.
8. 마법사가 완료되면 구성 완료 페이지에서 **끝내기** 를 클릭합니다.
9. Azure Active Directory Connect 서버에서 관리자 권한으로 Windows PowerShell을 열고 다음 명령을 실행합니다.

```powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

Microsoft 365 그룹을 구성하는 방법에 대한 자세한 내용은 [온-프레미스 Exchange 하이브리드를 사용하여 Microsoft 365 그룹 구성](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect)을 참조하세요.

## <a name="disabling-group-writeback"></a>그룹 쓰기 저장 사용 안 함

그룹 쓰기 저장을 사용하지 않으려면 다음 단계를 수행하십시오.

1. Azure Active Directory Connect 마법사를 시작하고 추가 작업 페이지로 이동합니다. **동기화 옵션 사용자 지정** 을 작업을 선택하고 **다음** 을 클릭합니다.
2. **선택적 기능** 페이지에서 그룹 쓰기 저장을 선택 취소합니다.  그룹이 삭제된다는 것을 알리는 경고가 표시됩니다.  **예** 를 클릭합니다.
   > [!IMPORTANT]
   > 그룹 쓰기 저장을 사용하지 않도록 설정하면 이전에 이 기능으로 만든 모든 그룹이 다음 동기화 주기에 로컬 Active Directory에서 삭제됩니다.

   ![확인란 선택 취소](media/how-to-connect-group-writeback/group2.png)

3. **다음** 을 클릭합니다.
4. **Configure** 를 클릭합니다.

 > [!NOTE]
 > 그룹 쓰기 저장을 사용하지 않도록 설정하면 Azure Active Directory Connector에서 전체 가져오기 및 전체 동기화 플래그를 ' true'로 설정되므로 규칙 변경 내용이 다음 동기화 주기에 전파되어 이전에 Active Directory에 쓰기 저장된 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
