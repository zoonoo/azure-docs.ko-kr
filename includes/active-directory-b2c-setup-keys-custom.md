
### <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>사용자 지정 정책에서 사용하기 위해 B2C 테넌트에 서명 및 암호화 키 추가

1. Azure AD B2C 테넌트 설정에서 ID 경험 프레임워크 블레이드로 이동합니다.
1. `Policy Keys`을 선택하여 테넌트에 사용 가능한 키를 봅니다. `B2C_1A_TokenSigningKeyContainer`이 있으면 이 키를 건너뜁니다.
1. `TokenSigningKeyContainer`을 만듭니다.  
 * `+Add`
 * 을 클릭합니다.옵션 > `Generate`
 * 이름 > `TokenSigningKeyContainer`B2C_1A_ 접두사를 자동으로 추가할 수 있습니다.
 * 키 유형 > `RSA`
 * 날짜 - 기본값 사용
 * 키 사용 > `Signature`
1. `Create`을 클릭합니다.
1. `B2C_1A_TokenEncryptionKeyContainer`이라는 키가 있으면 이 키를 건너뜁니다.
1. `TokenEncryptionKeyContainer`를 만듭니다.
 * 옵션 > `Generate`
 * 이름 > `TokenSigningKeyContainer`B2C_1A_ 접두사를 자동으로 추가할 수 있습니다.
 * 키 유형 > `RSA`
 * 날짜 - 기본값 사용
 * 키 사용 > `Encryption`
1. `Create`을 클릭합니다.


[!TIP]
최종 사용자에게 소셜 ID 공급자 또는 페더레이션된 ID 공급자를 제공하려는 경우 다음 단계는 선택 사항입니다.  Facebook은 사용자 지정 정책을 사용하여 Azure AD B2C에서 외부 ID 공급자에 대해 자세히 알아보기 위한 좋은 출발점입니다.

1. `FacebookSecret`를 만듭니다.  선택적이지만 외부에서 페더레이션하는 기능을 테스트하기 위해 이 단계가 권장됩니다.  이렇게 하면 다른 ID 공급자와 함께 정책을 한층 더 발전시키는 든든한 시작점을 만들게 됩니다.
 * `+Add`을 클릭합니다.
 * 옵션 > `Manual`
 * 이름 > `FacebookSecret`B2C_1A_ 접두사를 자동으로 추가할 수 있습니다.
 * 암호 > developers.facebook.com에서 FacebookSecret을 입력합니다.  *Facebook 앱 ID가 아닙니다.*
 * 키 사용 > 서명
1. `Create`을 클릭하고 생성되었는지 확인하고 이름을 적어둡니다.

[!NOTE]
Azure AD B2C 기본 제공 정책을 사용하는 경우 기본 제공 및 사용자 지정 정책에 대해 일반적으로 동일한 암호를 사용합니다. 
