<properties
	pageTitle="기본 구성 변경에 대한 모범 사례 | Microsoft Azure"
	description="Azure AD Connect 동기화의 기본 구성을 변경의 모범 사례를 제공합니다."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 동기화: 기본 구성 변경에 대한 모범 사례

이 항목에서는 Azure AD Connect 동기화에 지원되거나 지원되지 않는 변경사항을 설명합니다.

Azure AD Connect에 의해 만들어진 구성은 온-프레미스 Active Directory와 Azure AD를 동기화하는 환경의 대부분을 위해 “있는 그대로” 작동합니다. 그러나 일부 경우에는, 특정 필요 또는 요구 사항을 충족시키기 위해 구성에 일부 변경 내용을 적용할 필요가 있습니다.

## 서비스 계정의 변경 내용
Azure AD Connect 동기화는 설치 마법사에서 만든 서비스 계정에서 실행 중입니다. 이 서비스 계정은 동기화에 의해 사용되는 데이터베이스에 대한 암호화 키를 보유합니다. 127자의 긴 암호로 만들어지며, 만료되지 않도록 설정됩니다.

- 서비스 계정의 암호는 변경 또는 초기화가 **지원되지 않습니다**. 이렇게 하면 암호화 키는 파기되고 서비스는 데이터베이스에 액세스할 수 없으며, 서비스를 시작할 수 없습니다.

## 스케줄러에 대한 변경 사항
Azure AD Connect 동기화는 ID 데이터를 3시간마다 동기화하도록 설정됩니다. 설치하는 동안 동기화 서버를 작동할 수 있는 권한이 있는 서비스 계정에서 실행되는 예약된 작업이 생성됩니다.

- 예약된 작업의 변경을 **지원하지 않습니다**. 서비스 계정의 암호는 알 수 없습니다. [서비스 계정 변경](#changes-to-the-service-account)을 참조하십시오.
- 기본3시간 보다 더 자주 동기화하는 기능은 **지원하지 않습니다**.

## 동기화 규칙 변경

설치 마법사는 가장 일반적인 시나리오에 대한 작업으로 간주되는 구성을 제공합니다. 구성을 변경해야 할 경우에도 여전히 지원되는 구성이 포함된 이 규칙을 따라야 합니다.

- 기본 동기화 규칙에 유일하게 지원되는 변경사항은 사용하지 않도록 설정하는 것입니다. 다른 모든 변경사항은 업그레이드하는 동안 손실될 수 있습니다.
- 기본 규칙을 변경해야 할 경우 기본 규칙의 복사본을 만든 다음 원래 규칙을 사용하지 않도록 설정합니다. 동기화 규칙 편집기에서 프롬프트를 표시하여 이 작업을 수행하는 것을 도와줍니다.
- 동기화 규칙 편집기를 사용하여 사용자 지정 동기화 규칙을 내보냅니다. 이렇게 하면 재해 복구 시나리오의 경우에 쉽게 다시 만드는 데 사용할 수 있는 PowerShell 스크립트를 얻게 됩니다.

>[AZURE.WARNING]기본 동기화 규칙에는 지문이 포함되어 있습니다. 이러한 규칙을 변경한 경우 지문이 더 이상 일치하지 않으며 나중에 Azure AD Connect의 새 릴리스를 적용하려 할 때 문제가 발생할 수 있습니다. 이 문서에 유일하게 변경하는 방법이 설명되어 있습니다.

### 원치 않는 동기화 규칙 삭제
기본 동기화 규칙을 삭제하지 마십시오; 다음 업그레이드 작업을 하는 동안 다시 만들어집니다.

일부 경우에는 설치 마법사에서 토폴로지에 대해 작동하지 않는 구성을 생성했습니다. 예를 들어, 계정 리소스 포리스트 토폴로지를 가지고 있지만 계정 포리스트의 스키마를 Exchange 스키마로 확장한 경우 Exchange에 대한 규칙은 계정 포리스트뿐만 아니라 리소스 포리스트에 대해서도 생성됩니다. 이런 경우 Exchange에 대한 동기화 규칙을 사용하지 않음으로 설정해야 합니다.

![비활성화된 동기화 규칙](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

위의 그림에서 설치 마법사가 계정 포리스트의 이전 버전 Exchange 2003 스키마를 발견했습니다. 이는 리소스 포리스트가 Fabrikam의 환경에 도입되기 전에 추가되었습니다. 동기화된 이전 버전의 Exchange 구현에 대한 특성이 없는지 확인하려면 동기화 규칙을 보여지는 것처럼 사용하지 않도록 설정해야 합니다.

### 기본 규칙 변경
기본 규칙을 변경해야 할 경우 기본 규칙의 복사본을 만든 다음 원래 규칙을 사용하지 않도록 설정해야 합니다. 그런 다음 복제된 규칙을 변경합니다. 동기화 규칙 편집기가 이 작업을 수행하는 것을 도와줍니다. 기본 규칙을 열면 이 대화 상자가 나타납니다:

![경고 받은 기본 규칙](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

**예**를 선택하여 규칙의 복사본을 만듭니다. 그런 다음 복제된 규칙이 열립니다.

![복제된 규칙](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

이 복제된 규칙에서 범위, 조인 및 변환에 필요한 사항을 변경합니다.

### 특성을 전달하지 않습니다.
특성을 전달하지 않는 방법에는 다음 두 가지가 있습니다. 첫 번째 방법은 설치 마법사를 사용하여 [선택한 특성을 제거합니다](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). 이 옵션은 전에 특성을 동기화하지 않은 경우에만 작동합니다. 하지만 이 특성을 동기화한 다음 이후에 이 기능을 사용하여 제거한 경우 동기화 엔진은 이 특성을 관리하는 것을 중지하고 Azure AD에 기존 값이 남아 있게 됩니다.

특성 값을 제거하고 나중에 이 값이 전달되지 않는지 확인하려면 대신 사용자 지정 규칙을 생성해야 합니다.

Fabrikam에서는 클라우드에 동기화된 특성 중 일부라도 클라우드에 남아 있지 않아야 합니다. Azure AD에서 이들 특성이 제거되었는지 확인하려 합니다.

![확장 특성](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- 새 인바운드 동기화 규칙 만들기 및 설명 ![설명](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png) 채우기
- 원본 **AuthoritativeNull**을 포함하는 형식 **식**의 특성 흐름을 만듭니다. 리터럴 **AuthoritativeNull**은 심지어 우선 순위가 더 낮은 동기화 규칙이 그 값을 채우려고 해도 MV에서 그 값이 비어 있어야 함을 나타냅니다. ![확장 특성](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- 해당 동기화 규칙을 저장합니다. **동기화 서비스**를 시작하여 커넥터를 찾고 **실행**을 선택한 다음 **전체 동기화**를 선택합니다. 그러면 모든 특성 흐름을 다시 계산합니다.
- 의도한 변경 내용을 커넥터 공간을 검색하여 내보낼 수 있는지 확인합니다. ![단계적 삭제](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## 다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=Nov15_HO3-->