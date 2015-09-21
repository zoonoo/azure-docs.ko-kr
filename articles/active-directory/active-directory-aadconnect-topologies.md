<properties
   pageTitle="Azure AD Connect에 대한 토폴로지 | Microsoft Azure"
   description="이 항목은 Azure AD Connect에 대해 지원되고 지원되지 않는 토폴로지에 대해 자세히 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/08/2015"
   ms.author="andkjell"/>

# Azure AD Connect에 대한 토폴로지

이 항목의 목적은 Azure AD Connect Sync가 주요 통합 솔루션인 여러 온-프레미스 및 Azure AD 토폴로지에 대해 설명하는 것입니다. 지원되거나 지원되지않는 구성에 대해 설명합니다.

문서 내 그림에 대한 범례:

| 설명 | 아이콘 |
|-----|-----|
| 온-프레미스 Active Directory 포리스트 | ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)|
| 필터링된 가져오기를 사용한 Active Directory | ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)|
| Azure AD Connect Sync 서버 | ![동기화](./media/active-directory-aadconnect-topologies/LegendSync1.png)|
| Azure AD Connect Sync 서버 “스테이징 모드" | ![동기화](./media/active-directory-aadconnect-topologies/LegendSync2.png)|
| FIM2010 또는 MIM2016로 GALSync | ![동기화](./media/active-directory-aadconnect-topologies/LegendSync3.png)|
| Azure AD Connect Sync 서버, 자세히 설명됨 |![동기화](./media/active-directory-aadconnect-topologies/LegendSync4.png)|
| Azure AD Directory |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)|
| 지원되지 않는 시나리오 | ![지원되지 않음](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)



## 단일 포리스트, 단일 Azure AD Directory
![SingleForestSingleDirectory](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

가장 일반적인 토폴로지는 단일 포리스트 온-프레미스, 하나 또는 여러 도메인, 단일 Azure AD Directory(테넌트)입니다. Azure AD 인증이 암호 동기화와 함께 수행됩니다. 이것은 Azure AD Connect의 빠른 설치로 지원되는 토폴로지입니다.

### 단일 포리스트, 여러 동기화 서버를 하나의 Azure AD 디렉터리로
![SingleForestFilteredUnsupported](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

여러 Azure AD Connect Sync 서버가 상호 배타적인 개체 집합을 동기화하도록 구성되었다고 해도 동일한 Azure AD 디렉터리에 대한 연결은 지원되지 않습니다([스테이징 서버](#staging-server)는 제외). 이것은 공통 네트워크 위치에서 프리스트 내 하나의 도메인에 연결할 수 없거나 여러 서버에 걸쳐 동기화 부하를 분산하기 위해 시도될 수 있습니다.

## 여러 포리스트, 단일 Azure AD Directory
![MultiForestSingleDirectory](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

많은 조직에는 여러 온-프레미스 Active Directory 포리스트가 포함된 환경이 있습니다. 둘 이상의 온-프레미스 Active Directory 포리스트가 배포된 이유는 여러 가지가 있습니다. 일반적인 예로 계정 리소스 포리스트, 합병 및 인수 관련 포리스트 또는 데이터를 아웃소싱하는 데 사용하는 포리스트 설계가 있습니다.

여러 포리스트가 있을 경우에는 단일 Azure AD Connect Sync 서버에서 모든 포리스트에 연결할 수 있어야 합니다. 서버는 도메인에 연결되지 않아도 되며 필요한 경우 네트워크 DMZ에 배치하면 모든 포리스트에 연결할 수 있습니다.

Azure AD Connect 마법사는 사용자를 통합하는 방법에 몇 가지 옵션을 제공하므로, 동일한 사용자가 여러 포리스트에 여러 번 나타나더라도 해당 사용자는 Azure ID에 한 번만 나타납니다. 아래에 일부 일반 토폴로지에 대해 설명되어 있습니다. 설치 마법사에서 사용자 지정 설치 경로를 사용하여 어떤 토폴로지를 보유하고 있는지 구성하고 "고유하게 사용자 식별" 페이지에서 해당되는 옵션을 선택합니다. 통합은 사용자에 대해서만 발생합니다. 그룹이 중복된 경우에는 이들은 기본 구성과 통합되지 않습니다.

일반적인 토폴로지는 다음 섹션에서 설명됩니다. [토폴로지 분리](#multiple-forests-separate-topologies), [전체 메시](#multiple-forests-full-mesh-with-optional-galsync) 및 [계정 리소스](#multiple-forests-account-resource-forest).

Azure AD Connect Sync에 의해 제공된 기본 구성에서는 다음과 같이 가정됩니다. 1 사용자는 하나의 계정만 사용할 수 있으며 이 계정이 위치한 포리스트는 사용자를 인증하는 데 사용됩니다. 이것은 암호 동기화 및 페더레이션 두 가지용입니다. userPrincipalName 및 sourceAnchor/immutableID이 이 포리스트로부터 제공됩니다. 2. 사용자에게는 하나의 사서함만 있습니다. 3. 사용자의 사서함을 호스트하는 포리스트에는 Exchange 전체 주소 목록(GAL)에 표시되는 특성에 대해 최상의 데이터 품질을 가집니다. 사용자에 대해 사서함이 없는 경우 아무 포리스트나 사용하여 이러한 특성 값에 기여할 수 있습니다. 4. 연결된 사서함이 있다면 로그인에 사용되는 여러 포리스트에도 다른 계정이 있습니다.

사용자의 환경이 이러한 가정에 일치하지 않으면 다음이 발생합니다. 하나 이상의 활성 계정 또는 하나 이상의 사서함이 있다면 동기화 엔진이 이 중 하나를 선택하고 다른 하나는 무시합니다. - 연결된 사서함만 있고 다른 계정이 없다면 이들 계정은 Azure AD에 내보내기되지 않고 사용자는 어떤 그룹의 멤버도 되지 않습니다. DirSync에서는 연결된 사서함이 일반 사서함으로 나타나므로 이것은 여러 포리스트 시나리오의 지원을 향상시키기 위한 의도적인 다른 동작입니다.

### 여러 포리스트, 여러 동기화 서버를 하나의 Azure AD 디렉터리로
![MultiForestMultiSyncUnsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

둘 이상의 Azure AD Connect Sync 서버를 단일 Azure AD Directory에 연결하는 것은 지원되지 않습니다([스테이징 서버](#staging-server) 제외).

### 다중 포리스트 – 별도 토폴로지
"사용자는 모든 디렉터리에서 한 번만 표시됩니다"

![MultiForestUsersOnce](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![MultiForestSeperateTopologies](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

이 환경에서는 모든 포리스트 온-프레미스가 별개의 엔터티로 취급되며 다른 포리스트에 사용자가 없습니다. 각 포리스트에는 자체 Exchange 조직이 있으며 포리스트 사이에는 GALSync가 없습니다. 이것은 합병/인수 후 또는 각 사업부가 서로 격리되어 운영되는 조직 내의 상황일 수 있습니다. Azure AD에서는 이러한 포리스트가 동일한 조직에 있으며 통합된 GAL과 함께 표시됩니다. 이 그림에서는 모든 포리스트에 있는 각 개체가 메타 버스에 한 번 표시되며 대상 Azure AD Directory에서 집계됩니다.

### 다중 포리스트 – 사용자 일치
"사용자 ID가 여러 디렉터리에 존재" 아래에 있는 옵션 중 하나를 선택하는 모든 다중 포리스트 시나리오에 대해 공통적인 사실은 배포 및 보안 그룹이 모든 포리스트 내에 있으며 사용자, 연락처 및 FSP(외부 보안 주체) 혼합을 포함할 수 있다는 사실입니다.

FSP는 ADDS에서 사용되어 보안 그룹 내 다른 포리스트에 있는 멤버를 나타냅니다. 동기화 엔진은 실제 사용자에게 FSP를 확인시키고 모든 FSP가 실제 개체에 대해 확인된 Azure AD 내 보안 그룹을 나타냅니다.

### 다중 포리스트 – GALSync 선택 사항이 제공되는 전체 메시
"사용자 ID는 여러 디렉터리 존재합니다. 다음을 사용하여 일치: 메일 특성"

![MultiForestUsersMail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![MultiForestFullMesh](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

완전한 메시 토폴로지는 사용자와 리소스가 아무 포리스트에나 위치할 수 있게 지원하며 일반적으로 포리스트 사이에 양방향 트러스트가 있습니다.

Exchange가 1개 이상의 포리스트에 있다면 1개의 포리스트 내의 사용자를 두 포리스트마다 한 번씩 연락처로 표현하는 온-프레미스 GALSync 솔루션이 선택적으로 제공될 수 있습니다. GALSync는 일반적으로 Forefront Identity Manager 2010 또는 Microsoft Identity Manager 2016을 사용하여 구현됩니다. Azure AD Connect를 온-프레미스 GALSync에 사용할 수 없습니다.

이 시나리오에서는 id 개체가 메일 특성을 사용하여 조인됩니다. 따라서 하나의 포리스트에 사서함이 있는 사용자는 다른 포리스트의 연락처와 조인됩니다.

### 다중 포리스트 – 계정 리소스 포리스트
"사용자 ID는 여러 디렉터리 존재합니다. 다음을 사용하여 일치: ObjectSID 및 msExchMasterAccountSID attributes”

![MultiForestUsersObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![MultiForestAccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

계정 리소스 포리스트 토폴로지에는 활성 사용자 계정이 있는 하나 이상의 계정 포리스트가 있습니다.

이 시나리오에는 모든 계정 포리스트를 신뢰하는 하나의 포리스트가 포함됩니다. 이 포리스트에는 일반적으로 Exchange 및 Lync와 확장된 AD 스키마가 있습니다. 모든 Exchange 및 Lync 서비스는 물론 다른 공유 서비스도 이 포리스트에 있습니다. 사용자는 이 포리스트에 사용할 수 없는 사용자 계정을 가지며 사서함이 계정 포리스트에 연결됩니다.

## Office 365 및 토폴로지 고려 사항
일부 Office 365 워크로드의 경우 지원되는 토폴로지에 특정 제한이 있습니다. 이 중 하나를 사용하고자 한다면 각 워크로드의 지원되는 토폴로지 페이지를 참조하십시오.

| 워크로드 | |
| --------- | --------- |
| Exchange Online |	둘 이상의 Exchange 조직 온-프레미스의 경우(즉, Exchange가 둘 이상 포리스트에 배포된 경우) Exchange 2013 SP1 이상을 사용해야 합니다. 자세한 내용은 여기에서 찾을 수 있습니다. [여러 Active Directory 포리스트가 있는 하이브리드 배포](https://technet.microsoft.com/ko-KR/library/jj873754.aspx) |
| 비즈니스용 Skype | 다중 포리스트 온-프레미스를 사용하는 경우 계정 리소스 포리스트 토폴로지만 지원됩니다. 지원되는 토폴로지에 대한 세부 정보는 다음에 있습니다. [Business Server 2015용 Skype에 대한 환경 요구사항](https://technet.microsoft.com/ko-KR/library/dn933910.aspx) |

## 스테이징 서버
![StagingServer](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect는 “스테이징 모드"에서 두 번째 서버의 설치를 지원합니다. 이 모드의 서버는 연결된 모든 디렉터리에서만 데이터를 읽으며 따라서 ID 데이터의 업데이트된 복사본을 갖게 됩니다. 기본 서버가 실패한 지점에서 재해 발생 시 Azure AD Connect 마법사를 사용하면 쉽게 두 번째 서버에 수동으로 재해 복구할 수 있습니다. 기본 서버와 공유되는 인프라가 없으므로 두 번째 서버를 다른 데이터 센터에 배치하는 것이 좋습니다. 기본 서버에 적용한 모든 구성 변경 사항은 두 번째 서버에 복사해야 합니다.

새로운 사용자 지정 구성 및 효과를 테스트할 때도 스테이징 서버를 사용할 수 있습니다. 변경 내용을 미리보고 구성을 조정할 수 있습니다. 새 구성에 만족한다면 스테이징 서버를 활성 서버로 만들고 기존의 활성 서버를 스테이징 모드로 설정할 수 있습니다.

이 메서드는 또한 현재 활성 서버를 종료 하기 전에 새 동기화 서버를 교체하고 새로운 서버를 준비하고자 할 때 사용할 수 있습니다.

여러 데이터 센터에 여러 백업을 보유하기 위해 둘 이상의 스테이징 서버를 둘 수도 있습니다.

## 여러 Azure AD 디렉터리
Microsoft는 조직을 위해 Azure AD에 단일 디렉터리를 보유할 것을 권장합니다. 여러 Azure AD Directory의 사용을 계획하기 전에 이들 항목이 단일 디렉터리의 사용을 지원하는 일반적인 시나리오를 다룹니다.

| 항목 | |
| --------- | --------- |
| 관리 단위를 사용하여 위임 | [Azure AD 에서 관리 단위 관리](active-directory-administrative-units-management.md)

![MultiForestMultiDirectory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Azure AD Connect Sync 서버와 Azure AD Directory 간에 1:1 관계가 있습니다. 각 Azure AD Directory에 대해 하나의 Azure AD Connect Sync 서버 설치가 필요합니다. Azure AD Directory 인스턴스는 설계상 격리되어 있으며 한 디렉터리 내의 사용자는 다른 디렉터리에 있는 사용자를 볼 수 없습니다. 이것의 의도된 것이라면 지원되는 구성이지만 그렇지 않으면 위에서 설명한 단일 Azure AD Directory 모델을 사용해야 합니다.

### Azure AD 디렉터리에서 각 개체가 한 번만
![SingleForestFiltered](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

이 토폴로지에서는 하나의 AAD Connect Sync 서버가 각 Azure AD Directory에 연결됩니다. Azure AD Connect Sync 서버가 각각 상호 배타적인 개체 집합을 통해 계속 작동하기 위해서는 필터링에 대해 구성되어야 합니다. 예를 들어, 각 서버의 범위를 특정 도메인 또는 OU에 지정합니다. DSN 도메인은 단일 Azure AD Directory에만 등록될 수 있으므로, 온-프레미스 AD 내의 사용자 UPN도 역시 별도의 네임스페이스를 사용해야 합니다. 예를 들어, 위 그림에서 3개의 별도 UPN 접미사가 contoso.com, fabrikam.com 및 wingtiptoys.com의 온-프레미스 AD에 등록됩니다. 각 온-프레미스 AD 도메인 내의 사용자들은 서로 다른 네임스페이스를 사용합니다.

이 토폴로지에서는 Azure AD Directory 인스턴스 사이에 "GALsync"가 없으므로 Exchange Online 및 비즈니스용 Skype 내 주소록에서는 동일한 디렉터리에 있는 사용자만 표시됩니다.

이 토폴로지를 통해 하나의 Azure AD Directory만 온-프레미스 Active Directory와 함께 Exchange 하이브리드를 활성화할 수 있습니다.

상호 배타적인 집합 개체에 대한 요구사항은 쓰기 저장에도 적용됩니다. 이렇게 하면 단일 구성 온-프레미스로 가정되므로 일부 쓰기 저장 기능이 이 토폴로지로 지원되지 않습니다. 여기에는 다음이 포함됩니다. - 기본 구성을 사용한 그룹 쓰기 저장 - 장치 쓰기 저장

### Azure AD 디렉터리에서 각 개체가 여러 번
![SingleForestMultiDirectoryUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![SingleForestMultiConnectorsUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

동일한 사용자를 여러 Azure AD 디렉터리에 동기화하는 것은 지원되지 않습니다. 또한 하나의 Azure AD에 있는 사용자가 다른 Azure AD Directory 내의 연락처로 표시되도록 구성하는 것도 지원되지 않습니다. 또한 Azure AD Connect Sync를 여러 Azure AD 디렉터리에 연결되도록 수정하는 것도 지원되지 않습니다.

### 쓰기 저장을 사용한 GALsync
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD 디렉터리는 설계상 격리되어 있습니다. 디렉터리 간에 일반적이고 통합된 GAL을 구축하기 위한 시도로 다른 Azure AD Directory에서 데이터를 읽도록 Azure AD Connect Sync의 구성을 변경하는 것은 지원되지 않습니다. 또한 Azure AD Connect Sync를 사용하여 사용자를 온-프레미스 AD에 연락처로 내보내는 것도 지원되지 않습니다.

### 온-프레미스 동기화 서버로 GALsync
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

두 개의 Exchange 조직 사이에서 GALsync 사용자에게 FIM2010/MIM2016 온-프레미스를 사용할 수 있습니다. 한 조직 내의 사용자가 다른 조직에서 외부 사용자/연락처로 표시됩니다. 이러한 여러 온-프레미스 AD를 각각 자체 Azure AD 디렉터리에 동기화할 수 있습니다.

## 다음 단계
이러한 시나리오에 대해 Azure AD Connect를 설치하는 방법을 알아보려면 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요. Azure AD Connect Sync를 위한 구성에 대해 자세한 내용을 보려면 [Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md)를 참조하세요.

<!---HONumber=Sept15_HO2-->