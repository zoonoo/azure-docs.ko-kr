---
title: "Azure AD Connect: 지원되는 토폴로지 | Microsoft Docs"
description: "이 항목은 Azure AD Connect에 대해 지원되고 지원되지 않는 토폴로지에 대해 자세히 설명합니다."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 11/01/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: e5983496cda061b28418dcf86c38fbe80c611503


---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect에 대한 토폴로지
이 항목의 목적은 주요 통합 솔루션인 Azure AD Connect Sync와 함께 여러 온-프레미스 및 Azure AD 토폴로지에 대해 설명하는 것입니다. 지원되거나 지원되지 않는 구성에 대해 설명합니다.

문서 내 그림에 대한 범례:

| 설명 | 아이콘 |
| --- | --- |
| 온-프레미스 Active Directory 포리스트 |![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| 필터링된 가져오기를 사용한 Active Directory |![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Azure AD Connect Sync 서버 |![동기화](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Azure AD Connect Sync 서버 “스테이징 모드" |![동기화](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| FIM2010 또는 MIM2016로 GALSync |![동기화](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Azure AD Connect Sync 서버, 자세히 설명됨 |![동기화](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD Directory |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| 지원되지 않는 시나리오 |![지원되지 않음](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest-single-azure-ad-tenant"></a>단일 포리스트, 단일 Azure AD 테넌트
![단일 포리스트 단일 테넌트](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

가장 일반적인 토폴로지는 단일 포리스트 온-프레미스, 하나 또는 여러 도메인, 단일 Azure AD 테넌트입니다. Azure AD 인증을 위해 암호 동기화가 사용됩니다. Azure AD Connect의 빠른 설치는 이 토폴로지만 지원합니다.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>단일 포리스트, 여러 동기화 서버를 하나의 Azure AD 테넌트로
![지원되지 않는 필터링된 단일 포리스트](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

동일한 Azure AD 테넌트에 여러 Azure AD Connect 동기화 서버를 연결하는 것은 지원되지 않습니다([준비 서버](#staging-server)제외). 상호 배타적 개체 집합을 동기화하도록 구성된 경우에도 지원되지 않습니다. 단일 서버 포리스트의 모든 도메인에 연결할 수 없는 경우 이를 고려하거나 여러 서버에 부하를 분산해야 합니다.

## <a name="multiple-forests-single-azure-ad-tenant"></a>여러 포리스트, 단일 Azure AD 테넌트
![다중 포리스트 단일 테넌트](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

많은 조직에는 다중 온-프레미스 Active Directory 포리스트가 있는 환경이 있습니다. 둘 이상의 온-프레미스 Active Directory 포리스트가 있는 이유는 여러 가지가 있습니다. 일반적인 예로 계정 리소스 포리스트로 디자인 및 합병 또는 인수 후의 결과로서의 디자인이 있습니다.

여러 포리스트가 있을 경우에는 단일 Azure AD Connect Sync 서버에서 모든 포리스트에 연결할 수 있어야 합니다. 서버를 도메인에 가입시킬 필요가 없습니다. 필요한 경우 서버를 네트워크 DMZ에 배치하여 모든 포리스트에 연결할 수 있습니다.

Azure AD Connect 설치 마법사는 여러 포리스트에 나타난 사용자를 통합하는 몇 가지 옵션을 제공합니다. 사용자가 Azure AD에서 한 번만 나타나도록 하기 위한 것입니다. 설치 마법사에서 사용자 지정 설치 경로에 구성할 수 있는 몇 가지 일반적인 토폴로지가 있습니다. **사용자를 고유하게 식별**페이지에서 토폴로지를 나타내는 해당 옵션을 선택합니다. 통합은 사용자에 대해서만 구성됩니다. 중복된 그룹은 기본 구성과 통합되지 않습니다.

일반적인 토폴로지는 [토폴로지 분리](#multiple-forests-separate-topologies), [전체 메시](#multiple-forests-full-mesh-with-optional-galsync) 및 [계정 리소스](#multiple-forests-account-resource-forest) 섹션에서 설명됩니다.

Azure AD Connect 동기화의 기본 구성에서 다음 사항을 가정합니다.

1. 사용자는 하나의 계정만 사용할 수 있으며 이 계정이 위치한 포리스트는 사용자를 인증하는 데 사용됩니다. 이 가정은 암호 동기화 및 페더레이션에 모두 해당됩니다. UserPrincipalName 및 sourceAnchor/immutableID는 이 포리스트에서 제공됩니다.
2. 사용자에게는 하나의 사서함만 있습니다.
3. 사용자의 사서함을 호스트하는 포리스트는 Exchange GAL(전체 주소 목록)에 표시되는 특성에 대해 최상의 데이터 품질을 가집니다. 사용자에 대해 사서함이 없는 경우 아무 포리스트나 사용하여 이러한 특성 값에 기여할 수 있습니다.
4. 연결된 사서함이 있다면 로그인에 사용되는 다른 포리스트에도 다른 계정이 있습니다.

사용자 환경이 이러한 가정과 일치하지 않는 경우 다음이 수행됩니다.

* 둘 이상의 활성 계정 또는 둘 이상의 사서함이 있으면 동기화 엔진은 하나를 선택하고 다른 하나는 무시합니다.
* 다른 활성 계정이 없으면 연결된 사서함을 Azure AD로 내보내지 않습니다. 임의 그룹의 구성원으로 사용자 계정이 표시되지 않습니다. DirSync에 연결된 사서함은 항상 일반 사서함으로 표시됩니다. 이 변경은 의도적으로 다중 포리스트 시나리오 지원을 향상시키는 다른 동작입니다.

자세한 내용은 [기본 구성 이해](active-directory-aadconnectsync-understanding-default-configuration.md)에서 확인할 수 있습니다.

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>여러 포리스트, 여러 동기화 서버를 하나의 Azure AD 테넌트로
![지원되지 않는 다중 포리스트 다중 동기화](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

둘 이상의 Azure AD Connect Sync 서버를 단일 Azure AD 테넌트에 연결하는 것을 지원하지 않습니다. [준비 서버](#staging-server)사용은 예외입니다.

### <a name="multiple-forests--separate-topologies"></a>다중 포리스트 – 별도 토폴로지
**사용자는 모든 디렉터리에서 한 번만 표시됩니다**

![다중 포리스트 사용자 한 번](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![다중 포리스트 별도 토폴로지](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

이 환경에서는 모든 포리스트 온-프레미스가 별개의 엔터티로 취급되며 다른 포리스트에 사용자가 없습니다.
각 포리스트에는 자체 Exchange 조직이 있으며 포리스트 사이에는 GALSync가 없습니다. 이 토폴로지는 합병/인수 후 또는 각 사업부가 서로 격리되어 운영되는 조직 내의 상황일 수 있습니다. 이러한 포리스트가 Azure AD의 동일한 조직 내에 있으며 통합된 GAL과 함께 표시됩니다.
이 그림에서는 모든 포리스트에 있는 각 개체가 메타버스에 한 번 표시되며 대상 Azure AD 테넌트에서 집계됩니다.

### <a name="multiple-forests--match-users"></a>다중 포리스트 – 사용자 일치
**사용자 ID는 여러 디렉터리에 존재합니다**

일반적으로 이러한 모든 시나리오에서는 배포 및 보안 그룹이 다양한 사용자, 연락처 및 FSP(외부 보안 주체)를 포함할 수 있습니다.

FSP는 ADDS에서 사용되어 보안 그룹 내 다른 포리스트에 있는 멤버를 나타냅니다. 모든 FSP는 Azure AD에서 실제 개체로 확인됩니다.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>다중 포리스트 – GALSync 선택 사항이 제공되는 전체 메시
**사용자 ID는 여러 디렉터리에 존재합니다. 다음을 사용하여 일치: Mail 특성**

![다중 포리스트 사용자 메일](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![다중 포리스트 전체 메시](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

완전한 메시 토폴로지는 사용자와 리소스가 아무 포리스트에나 위치할 수 있게 지원하며 일반적으로 포리스트 사이에 양방향 트러스트가 있습니다.

Exchange가 둘 이상의 포리스트에 있다면 온-프레미스 GALSync 솔루션이 선택적으로 제공될 수 있습니다. 모든 사용자는 다른 모든 포리스트의 연락처로 나타납니다. GALSync는 일반적으로 Forefront Identity Manager 2010 또는 Microsoft Identity Manager 2016을 사용하여 구현됩니다. Azure AD Connect를 온-프레미스 GALSync에 사용할 수 없습니다.

이 시나리오에서는 id 개체가 메일 특성을 사용하여 조인됩니다. 하나의 포리스트에 사서함이 있는 사용자는 다른 포리스트의 연락처와 조인됩니다.

### <a name="multiple-forests--account-resource-forest"></a>다중 포리스트 – 계정 리소스 포리스트
**사용자 ID는 여러 디렉터리에 존재합니다. 다음을 사용하여 일치: ObjectSID 및 msExchMasterAccountSID 특성**

![다중 포리스트 사용자 ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![다중 포리스트 AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

계정 리소스 포리스트 토폴로지에는 활성 사용자 계정이 있는 하나 이상의 계정 포리스트가 있습니다. 또한 비활성화된 계정이 있는 하나 이상의 리소스 포리스트가 있습니다.

이 시나리오에는 하나 이상의 **리소스 포리스트**가 모든 **계정 포리스트**를 신뢰합니다. 리소스 포리스트에는 일반적으로 Exchange 및 Lync와 확장된 AD 스키마가 있습니다. 모든 Exchange 및 Lync 서비스는 물론 다른 공유 서비스도 이 포리스트에 있습니다. 사용자는 이 포리스트에 사용할 수 없는 사용자 계정을 가지며 사서함이 계정 포리스트에 연결됩니다.

## <a name="office-365-and-topology-considerations"></a>Office 365 및 토폴로지 고려 사항
일부 Office 365 워크로드의 경우 지원되는 토폴로지에 특정 제한이 있습니다. 이 중 하나를 사용하려는 경우 워크로드의 지원되는 토폴로지 항목을 참조하세요.

| 워크로드 |
| --- | --- |
| Exchange Online |
| 비즈니스용 Skype |

## <a name="staging-server"></a>준비 서버
![준비 서버](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect는 **준비 모드**에서 두 번째 서버의 설치를 지원합니다. 이 모드에서 서버는 모든 연결된 디렉터리에서 데이터를 읽지만 연결된 디렉터리에는 아무 것도 쓰지 않습니다. 일반 동기화 주기를 사용하므로 ID 데이터의 업데이트된 복사본을 갖게 됩니다. 주 서버가 실패하면 준비 서버로 장애 조치(failover)할 수 있습니다. Azure AD Connect 마법사에서 이 작업을 수행합니다. 기본 서버와 공유되는 인프라가 없으므로 두 번째 서버를 다른 데이터 센터에 배치하는 것이 좋습니다. 주 서버에서 적용한 모든 구성 변경 사항은 두 번째 서버에 수동으로 복사해야 합니다.

준비 서버는 데이터에 존재하는 새로운 사용자 지정 구성 및 효과를 테스트할 때에도 사용할 수 있습니다. 변경 내용을 미리보고 구성을 조정할 수 있습니다. 새 구성에 만족한다면 준비 서버를 Active Server로 만들고 이전 Active Server를 준비 모드로 설정할 수 있습니다.

이 메서드는 활성 동기화 서버를 교체하는 데도 사용할 수 있습니다. 새 서버를 준비하고 준비 모드로 설정합니다. 정상 상태인지 확인하고 활성 상태로 만들어 준비 모드를 사용하지 않도록 설정하고 현재 Active Server를 종료합니다.

여러 데이터 센터에 여러 백업을 보유하려고 할 때 둘 이상의 준비 서버를 둘 수도 있습니다.

## <a name="multiple-azure-ad-tenants"></a>여러 Azure AD 테넌트
Microsoft는 조직을 위해 Azure AD에 단일 테넌트를 보유할 것을 권장합니다.
여러 Azure AD 테넌트를 사용하도록 계획하기 전에 다음 항목에서는 단일 테넌트의 사용을 허용하는 일반적인 시나리오를 다룹니다.

| 항목 |
| --- | --- |
| 관리 단위를 사용하여 위임 |

![다중 포리스트 다중 테넌트](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Azure AD Connect Sync 서버와 Azure AD 테넌트 간에 1:1 관계가 있습니다. 각 Azure AD 테넌트에 하나의 Azure AD Connect 동기화 서버 설치가 필요합니다. Azure AD 테넌트 인스턴스는 격리되어 설계되어 있으며 한 테넌트의 사용자는 다른 테넌트에 있는 사용자를 볼 수 없습니다. 이 구분이 의도된 것이라면 지원되는 구성이지만 그렇지 않으면 단일 Azure AD 테넌트 모델을 사용해야 합니다.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Azure AD 테넌트에서 각 개체가 한 번만
![필터링된 단일 포리스트](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

이 토폴로지에서는 하나의 Azure AD Connect 동기화 서버가 각 Azure AD 테넌트에 연결됩니다. Azure AD Connect 동기화 서버가 필터링에 대해 구성되어야 하므로 각 서버에는 상호 배타적인 개체 집합이 계속 작동하고 있습니다. 예를 들어 특정 도메인 또는 OU에 각 서버의 범위를 지정할 수 있습니다. DNS 도메인은 단일 Azure AD 테넌트에만 등록될 수 있습니다. 온-프레미스 AD 내의 사용자 UPN도 역시 별도의 네임스페이스를 사용해야 합니다. 예를 들어, 위 그림에서 3개의 별도 UPN 접미사가 contoso.com, fabrikam.com 및 wingtiptoys.com의 온-프레미스 AD에 등록됩니다. 각 온-프레미스 AD 도메인 내의 사용자들은 서로 다른 네임스페이스를 사용합니다.

Azure AD 테넌트 인스턴스 사이에는 GALsync가 없습니다. Exchange Online 및 비즈니스용 Skype의 주소록은 동일한 테넌트에 있는 사용자만 보여 줍니다.

이 토폴로지는 그 외의 시나리오에 대해 다음과 같은 제한 사항이 있습니다.

* 하나의 Azure AD 테넌트만 온-프레미스 Active Directory를 사용하여 Exchange 하이브리드를 활성화할 수 있습니다.
* Windows 10 장치는 하나의 Azure AD 테넌트와만 연결할 수 있습니다.

상호 배타적인 집합 개체에 대한 요구 사항은 쓰기 저장에도 적용됩니다. 일부 쓰기 저장 기능은 이러한 특성이 온-프레미스의 단일 구성을 가정하므로 이 토폴로지에서 지원되지 않습니다.

* 기본 구성으로 쓰기 저장 그룹화
* 장치 쓰기 저장

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Azure AD 테넌트에서 각 개체가 여러 번
![지원되지 않는 단일 포리스트 다중 테넌트](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![지원되지 않는 단일 포리스트 다중 커넥터](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

* 동일한 사용자를 여러 Azure AD 테넌트에 동기화하는 것은 지원되지 않습니다.
* 하나의 Azure AD에 있는 사용자가 다른 Azure AD 테넌트 내의 연락처로 표시되도록 구성을 변경하는 것이 지원되지 않습니다.
* Azure AD Connect Sync를 여러 Azure AD 테넌트에 연결되도록 수정하는 것이 지원되지 않습니다.

### <a name="galsync-by-using-writeback"></a>쓰기 저장을 사용한 GALsync
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD 테넌트는 설계상 격리되어 있습니다.

* 다른 Azure AD 테넌트에서 데이터를 읽도록 Azure AD Connect 동기화의 구성을 변경하는 것은 지원되지 않습니다.
* Azure AD Connect Sync를 사용하여 사용자를 온-프레미스 AD에 연락처로 내보내는 것이 지원되지 않습니다.

### <a name="galsync-with-on-premises-sync-server"></a>온-프레미스 동기화 서버로 GALsync
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

두 개의 Exchange 조직 사이에서 GALsync 사용자에게 FIM2010/MIM2016 온-프레미스를 사용하도록 지원합니다. 한 조직 내의 사용자가 다른 조직에서 외부 사용자/연락처로 표시됩니다. 이제 이러한 여러 온-프레미스 AD는 각각 자체 Azure AD 테넌트에 동기화할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이러한 시나리오에 대해 Azure AD Connect를 설치하는 방법을 알아보려면 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요.

[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.




<!--HONumber=Dec16_HO3-->


