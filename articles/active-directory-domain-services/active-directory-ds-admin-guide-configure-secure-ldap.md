<properties
	pageTitle="Azure AD 도메인 서비스에서 보안 LDAP(LDAPS) 구성| Microsoft Azure"
	description="Azure AD 도메인 서비스 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성
이 문서에서는 Azure AD 도메인 서비스 관리되는 도메인에 대해 LDAPS(Secure Lightweight Directory Access Protocol)를 사용하도록 설정하는 방법을 보여 줍니다. 보안 LDAP는 'SSL(Secure Sockets Layer)/TLS(Transport Layer Security)를 통한 LDAP(Lightweight Directory Access Protocol)'라고도 합니다.

## 시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.

1. 유효한 **Azure 구독**

2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.

3. Azure AD 디렉터리에 대해 **Azure AD 도메인 서비스**를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](./active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.

4. **보안 LDAP를 사용하도록 설정하는 데 사용할 인증서**
    - **권장** - 엔터프라이즈 CA 또는 공용 인증 기관에서 인증서를 가져옵니다. 훨씬 더 안전한 구성 옵션입니다.
	- 또는 이 문서의 뒷부분에 나와 있는 것처럼[자체 서명된 인증서를 만들도록](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-1---obtain-a-certificate-for-secure-ldap) 선택할 수도 있습니다.

<br>

### 보안 LDAP 인증서에 대한 요구 사항
보안 LDAP를 사용하도록 설정하기 전에 아래 지침에 따라 유효한 인증서를 획득합니다. 유효하지 않거나/잘못된 인증서로 관리되는 도메인에 대해 보안 LDAP를 사용 하도록 설정하려는 경우 오류가 발생합니다.

1. **신뢰할 수 있는 발급자** - 인증서는 보안 LDAP를 사용하여 도메인에 연결해야 하는 컴퓨터에서 신뢰하는 기관에서 발급된 것이어야 합니다. 기관은 이러한 컴퓨터에서 신뢰하는 공용 인증 기관 또는 조직의 엔터프라이즈 인증 기관일 수 있습니다.

2. **수명** - 인증서는 다음 3-6개월 이상 동안 유효해야 합니다. 이렇게 하면 인증서가 만료될 때 관리되는 도메인에 대한 보안 LDAP 액세스가 중단되지 않습니다.

3. **주체 이름** - 인증서의 주체 이름은 관리되는 도메인에 대해 와일드카드여야 합니다. 예를 들어 도메인 이름이 'contoso100.com'인 경우 인증서의 주체 이름은 '*.contoso100.com'이어야 합니다. DNS 이름(주체 대체 이름)도 이 와일드 카드 이름으로 설정되어야 합니다.

3. **키 사용** - 디지털 서명 및 키 암호화와 같은 사례에 인증서를 구성해야 합니다.

4. **인증서 용도** - 인증서는 SSL 서버 인증에 대해 유효해야 합니다.

<br>

## 작업 1 - 보안 LDAP를 위한 인증서 가져오기
첫 번째 작업은 관리되는 도메인에 대한 보안 LDAP 액세스에 사용할 인증서를 얻는 것입니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- 조직의 엔터프라이즈 CA와 같은 인증 기관 또는 공용 인증 기관에서 인증서를 가져옵니다.

- 자체 서명된 인증서를 만듭니다.


### 옵션 A(권장) - 인증 기관에서 보안 LDAP 인증서를 가져옵니다.
조직에서 엔터프라이즈 PKI(공개 키 인프라)를 배포하는 경우 조직의 엔터프라이즈 CA(인증 기관)에서 인증서를 가져와야 합니다. 조직이 공용 인증 기관에서 해당 인증서를 가져오는 경우 공용 인증 기관에서 보안 LDAP 인증서를 가져와야 합니다.

인증서를 요청할 때는 [보안 LDAP 인증서에 대한 요구 사항](./active-directory-ds-admin-guide-configure-secure-ldap.md/#requirements-for-the-secure-ldap-certificate)에 나와 있는 요구 사항을 따라야 합니다.

보안 LDAP를 사용하여 관리되는 도메인에 연결해야 하는 클라이언트 컴퓨터는 LDAPS 인증서의 발급자를 신뢰해야 합니다.


### 옵션 B - 보안 LDAP에 대한 자체 서명된 인증서 만들기
다음과 같은 경우 보안 LDAP에 대한 자체 서명된 인증서를 만들도록 선택할 수 있습니다.

- 조직의 인증서를 엔터프라이즈 인증 기관이 발급하지 않은 경우
- 공용 인증 기관의 인증서를 사용하지 않을 것으로 예상되는 경우

**PowerShell을 사용하여 자체 서명된 인증서 만들기**

Windows 컴퓨터에서 새 자체 서명된 인증서를 만들려면 **관리자**로 새 PowerShell 창을 열고 다음 명령을 입력합니다.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

위의 예제에서 'contoso100.com'을 Azure AD 도메인 서비스 관리되는 도메인의 DNS 도메인 이름으로 대체합니다.

![Azure AD 디렉터리 선택](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

새로 만든 자체 서명된 인증서는 로컬 컴퓨터의 인증서 저장소에 배치됩니다.


## 작업 2 - 보안 LDAP 인증서를 .PFX 파일로 내보내기
이 작업을 시작하기 전에 사용자는 엔터프라이즈 인증 기관 또는 공용 인증 기관에서 보안 LDAP 인증서를 가져오거나 자체 서명된 인증서를 만들어야 합니다.

LDAPS 인증서를 .PFX 파일로 내보내기 위해서는 다음 단계를 수행합니다.

1. **시작** 단추를 누르고 **R**을 입력하여 **실행** 대화 상자를 표시합니다. **mmc**를 입력하고 **확인**을 클릭합니다.

    ![MMC 콘솔 시작](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. **사용자 계정 컨트롤** 프롬프트에서 **예**를 클릭하고 관리자 권한으로 MMC(Microsoft Management Console)를 시작합니다.

3. **파일** 메뉴에서 **스냅인 추가/제거...**를 클릭합니다.

    ![MMC 콘솔에 스냅인 추가](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. **스냅인 추가/제거** 대화 상자에서 **인증서** 스냅인을 선택하고 **추가 >** 단추를 클릭합니다.

    ![MMC 콘솔에 인증서 스냅인 추가](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. **인증서 스냅인** 마법사에서 **컴퓨터 계정**을 선택하고 **다음**을 클릭합니다.

    ![컴퓨터 계정에 대한 인증서 스냅인 추가](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. **컴퓨터 선택** 페이지에서 **로컬 컴퓨터: (이 콘솔이 실행되고 있는 컴퓨터)**를 선택하고 **마침**을 클릭합니다.

    ![인증서 스냅인 추가 - 컴퓨터 선택](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. **스냅인 추가/제거** 대화 상자에서 **확인**을 클릭하고 인증서 스냅인을 MMC에 추가합니다.

    ![MMC에 인증서 스냅인 추가 - 완료](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. MMC 창에서 **콘솔 루트**를 클릭하여 확장합니다. 인증서 스냅인이 로드되는 것으로 표시됩니다. 확장할 **인증서(로컬 컴퓨터)**를 클릭합니다. **개인** 노드, **인증서** 노드를 차례로 클릭하여 확장합니다.

    ![개인 인증서 저장소 열기](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. 위에서 볼 수 있듯이, 방금 만든 자체 서명된 인증서가 표시됩니다. 인증서의 속성을 검사하여 지문이 인증서를 만들 때 PowerShell 창에 보고된 것과 일치하는지 확인할 수 있습니다.

10. 자체 서명된 인증서를 선택하고 **마우스 오른쪽 단추로 클릭**합니다. 오른쪽 클릭 메뉴에서 **모든 작업**을 선택하고 **내보내기...**를 선택합니다.

    ![인증서 내보내기](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. **인증서 내보내기 마법사**에서 **다음**을 클릭합니다.

    ![인증서 내보내기 마법사](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. **개인 키 내보내기** 페이지에서 **예, 개인 키를 내보냅니다.**를 선택하고 **다음**을 클릭합니다.

    ![인증서 개인 키 내보내기](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] 인증서와 함께 개인 키를 내보내야 합니다. 인증서에 대한 개인 키가 없는 PFX를 제공하면 관리되는 도메인에 대한 보안 LDAP 설정에 실패합니다.

13. **파일 내보내기 형식** 페이지에서 내보낸 인증서에 대한 파일 형식으로 **개인 정보 교환 – PKCS #12(.PFX)**를 선택합니다.

    ![인증서 내보내기 파일 형식](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

	> [AZURE.NOTE] .PFX 파일 형식만 지원됩니다. 인증서를 .CER 파일 형식으로 내보내지 마세요.

14. **보안** 페이지에서 **암호** 옵션을 선택하고 .PFX 파일을 보호할 암호를 입력합니다. 다음 작업에 필요하므로 이 암호를 기억해 둡니다. **다음**을 클릭하여 진행합니다.

    ![인증서 내보내기 암호 지정](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

	> [AZURE.NOTE] 이 암호를 기록해 둡니다. [작업 3 - 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-3---enable-secure-ldap-for-the-managed-domain)에서 이 관리되는 도메인에 대해 보안 LDAP를 설정하는 동안 필요합니다.

15. **내보낼 파일** 페이지에서 파일 이름 및 인증서를 내보낼 위치를 지정합니다.

    ![인증서 내보내기 경로 지정](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. 다음 페이지에서 **마침**을 클릭하여 인증서를 PFX 파일로 내보냅니다. 인증서를 내보내면 확인 대화 상자가 표시됩니다.

    ![인증서 내보내기 완료](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## 작업 3 - 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정
보안 LDAP를 사용하도록 설정하려면 다음 구성 단계를 수행합니다.

1. **[Azure 클래식 포털](https://manage.windowsazure.com)**로 이동합니다.

2. 왼쪽 창에서 **Active Directory** 노드를 선택합니다.

3. Azure AD 도메인 서비스를 사용하도록 설정한 Azure AD 디렉터리('테넌트'라고도 함)를 선택합니다.

    ![Azure AD 디렉터리 선택](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. **구성** 탭을 클릭합니다.

    ![디렉터리의 탭 구성](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. **도메인 서비스** 섹션까지 아래로 스크롤합니다. 아래 스크린샷에 표시된 것처럼 **보안 LDAP(LDAPS)** 옵션이 표시됩니다.

    ![도메인 서비스 구성 섹션](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. **인증서 구성...** 단추를 클릭하면 **보안 LDAP에 대한 인증서 구성** 대화 상자가 표시됩니다.

    ![보안 LDAP를 위한 인증서 구성](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. **인증서가 있는 PFX 파일** 아래 폴더 아이콘을 클릭하여 관리되는 도메인에 대한 보안 LDAP 액세스에 사용할 인증서가 들어 있는 PFX 파일을 지정합니다. 또한 인증서를 PFX 파일로 내보낼 때 지정한 암호도 입력합니다. 완료되면 아래쪽에 있는 완료 단추를 클릭합니다.

    ![보안 LDAP PFX 파일 및 암호 지정](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. **구성** 탭의 **도메인 서비스** 섹션이 회색 표시되고 몇 분 동안 **보류 중...** 상태가 됩니다. 이 기간 중에 LDAPS 인증서의 정확도가 확인되고 관리되는 도메인에 대해 보안 LDAP가 구성됩니다.

    ![보안 LDAP - 보류 중 상태](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 약 10~15분이 소요됩니다. 제공된 보안 LDAP 인증서가 필수 조건과 일치하지 않는 경우(예:. 도메인 이름이 잘못되었거나 인증서가 만료되었거나 곧 만료되는 경우 등) 해당 디렉터리에 대해 보안 LDAP가 사용하도록 설정되지 않고 오류가 표시됩니다.

9. 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 성공하면 **보류 중...** 메시지가 사라집니다. 표시된 인증서의 지문이 나타납니다.

    ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## 작업 4 - 인터넷을 통해 보안 LDAP 액세스 사용
**선택적 작업** - 인터넷을 통해 LDAPS를 사용하여 관리되는 도메인에 액세스하지 않으려면 이 작업을 건너뜁니다.

이 작업을 시작하기 전에 [작업 3](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-3---enable-secure-ldap-for-the-managed-domain)에 나와 있는 단계를 완료해야 합니다.

1. **구성** 페이지의 **도메인 서비스** 섹션에서 **인터넷을 통해 보안 LDAP 액세스 사용** 옵션이 표시됩니다. 보안 LDAP를 통한 관리되는 도메인에 대한 인터넷 액세스가 기본적으로 사용되지 않으므로 기본적으로 **아니요**로 설정됩니다.

    ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. **인터넷을 통해 보안 LDAP 액세스 사용**을 **예**로 토글합니다. 아래쪽 패널에서 **저장** 단추를 클릭합니다. ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. **구성** 탭의 **도메인 서비스** 섹션이 회색 표시되고 몇 분 동안 **보류 중...** 상태가 됩니다. 이 기간 동안 보안 LDAP 통한 관리되는 도메인에 대한 인터넷 액세스가 사용하도록 설정됩니다.

    ![보안 LDAP - 보류 중 상태](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] 관리되는 도메인에 대해 보안 LDAP를 통한 인터넷 액세스를 사용하도록 설정하는 데 약 10분이 소요됩니다.

4. 인터넷을 통한 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하는 데 성공하면 **보류 중...** 메시지가 사라집니다. LDAPS를 통해 디렉터리에 액세스하는 데 사용할 수 있는 외부 IP 주소가 **LDAPS 액세스를 위한 외부 IP 주소** 필드에 표시됩니다.

    ![보안 LDAP 사용](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## 작업 5 - 인터넷에서 관리되는 도메인에 액세스할 DNS 구성
**선택적 작업** - 인터넷을 통해 LDAPS를 사용하여 관리되는 도메인에 액세스하지 않으려면 이 작업을 건너뜁니다.

이 작업을 시작하기 전에 [작업 4](./active-directory-ds-admin-guide-configure-secure-ldap.md/#task-4---enable-secure-ldap-access-over-the-internet)에 나와 있는 단계를 완료해야 합니다.

관리되는 도메인에 대한 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정했으면 클라이언트 컴퓨터가 관리되는 도메인을 찾을 수 있도록 DNS를 업데이트 야 합니다. 작업 4의 끝에 외부 IP 주소가 **LDAPS 액세스를 위한 외부 IP 주소**의 **구성** 탭에 표시됩니다.

관리되는 도메인의 DNS 이름(예: 'contoso100.com')이 이 외부 IP 주소를 가리키도록 외부 DNS 공급자를 구성합니다. 이 예에서는 다음과 같은 DNS 항목을 만들어야 합니다.

    contoso100.com  -> 52.165.38.113

이것으로 끝입니다. 이제 인터넷을 통해 보안 LDAP를 사용하여 관리되는 도메인에 연결할 준비가 되었습니다.

> [AZURE.WARNING] 클라이언트 컴퓨터가 LDAPS를 사용하여 관리되는 도메인에 성공적으로 연결하기 위해서는 LDAPS 인증서의 발급자를 신뢰해야 한다는 점을 유의하세요. 엔터프라이즈 인증 기관 또는 공개적으로 신뢰할 수 있는 인증 기관을 사용하는 경우 클라이언트 컴퓨터가 이러한 인증서 발급자를 신뢰하므로 문제가 되지 않습니다. 자체 서명된 인증서를 사용하는 경우 자체 서명된 인증서의 공개 부분(즉, 개인 키 없이 내보낸 부분)을 클라이언트 컴퓨터의 신뢰할 수 있는 인증서 저장소에 설치해야 합니다.

<br>

## 관련 콘텐츠

- [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)

<!---HONumber=AcomDC_0518_2016-->