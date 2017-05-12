> [!NOTE]
> 이러한 경험을 개선하고 다음 단계를 사용하지 않을 계획입니다.

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트에서 관리자 자격 증명을 만듭니다.

다음 섹션을 수행하려면 Azure AD B2C 테넌트의 도메인을 사용하는 자격 증명을 사용해야 합니다. 이렇게 하려면 이러한 자격 증명으로 관리자 계정을 만들어야 합니다. 이렇게 하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure AD B2C 테넌트의 컨텍스트로 전환하고 Azure AD B2C 블레이드를 엽니다. [방법 표시.](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)
1. **사용자 및 그룹**을 선택합니다.
1. **모든 사용자**를 선택합니다.
1. **+ 새 사용자**를 클릭합니다.
    * **이름** = `Admin`을 설정합니다.
    * **사용자 이름** = `admin@{tenantName}.onmicrosoft.com`을 설정합니다. 여기서 `{tenantName}`은 Azure AD B2C 테넌트의 이름입니다.
1. **디렉터리 역할**에서 **전역 관리자**를 선택하고 **확인**을 누릅니다.
1. **만들기**를 클릭하여 관리 사용자를 만듭니다.
1. **암호 표시**를 선택하고 암호를 복사합니다.

### <a name="set-up-the-key-container"></a>키 컨테이너 설정

키 컨테이너는 키를 저장하는 데 사용됩니다. 키 컨테이너를 설정하려면:

1. 새 powershell 명령 프롬프트를 엽니다.  여는 방법 중 하나는 **Windows 로고 키 + R** 키를 누르고, `powershell`을 입력하고, Enter 키를 누르는 것입니다.
1. powershell ExploreAdmin 도구를 다운로드하려면 이 재현을 다운로드합니다.

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. ExploreAdmin 도구를 사용하여 폴더로 전환합니다.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. powershell로 ExploreAdmin 도구를 가져옵니다.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. `b2c_1a_TokenSigningKeyContainer`가 아직 없는지 확인합니다.  `{tenantName}`을 테넌트 이름으로 바꿉니다.

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a. 로그인 프롬프트가 나타나면 이전 섹션에서 만든 관리자 계정을 사용합니다.

    b. 메시지가 표시되면 전화 번호를 입력하여 Multi-Factor Authentication을 설정해야 합니다.

    c. 메시지가 표시되면 암호를 변경합니다.

    d. **오류가 예상됩니다!**  `b2c_1a_TokenSigningKeyContainer`를 찾을 수 없다는 오류가 표시될 것입니다.  오류가 발생하지 않으면 이미 이 단계를 완료한 것이므로 이 섹션의 나머지 단계를 건너뜁니다.

1. `b2c_1a_TokenSigningKeyContainer`를 만듭니다.  `{tenantName}`을 테넌트 이름으로 바꿉니다.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. `b2c_1a_TokenEncryptionKeyContainer`를 만듭니다.  `{tenantName}`을 테넌트 이름으로 바꿉니다.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. `b2c_1a_FacebookSecret`를 만듭니다.  `{tenantName}`을 테넌트 이름으로 바꿉니다.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
