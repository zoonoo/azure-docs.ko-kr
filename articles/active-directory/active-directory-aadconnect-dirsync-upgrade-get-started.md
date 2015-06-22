<properties 
   pageTitle="Microsoft Azure AD Connect - Microsoft Azure AD 동기화 도구(DirSync)에서 업그레이드" 
   description="DirSync에서 Azure AD Connect로 업그레이드하는 방법을 알아봅니다. 이 문서에서는 현재 Microsoft Azure AD 동기화 도구(DirSync)에서 Azure AD Connect로 업그레이드하는 단계를 설명합니다." 
   services="active-directory" 
   documentationCenter="" 
   authors="shoatman" 
   manager="terrylanfear" 
   editor="billmath"/>

<tags 
   ms.service="active-directory" 
   ms.workload="identity" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="05/26/2015" 
   ms.author="shoatman"/>

# Microsoft Azure Active Directory 동기화(DirSync)를 Azure Active Directory Connect로 업그레이드

다음 설명서를 참조하여 기존 DirSync 설치를 Azure AD Connect로 업그레이드할 수 있습니다.

## Azure AD Connect 다운로드

Azure AD Connect를 사용하여 시작하려면 다음을 사용하여 최신 버전을 다운로드하면 됩니다. [Azure AD Connect 공개 미리 보기 다운로드](http://connect.microsoft.com/site1164/program8612)

## Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하고 DirSync에서 업그레이드하기 전에 필요한 사항이 몇 가지 있습니다.

- Azure AD 인스턴스에 대한 기존 전역 관리자 계정의 암호(설치 시 이 계정이 어느 것인지 알려 주는 메시지가 나타남)
- 로컬 Active Directory에 대한 엔터프라이즈 관리자 계정
- 선택 사항: 정식 버전의 SQL Server를 사용하도록 DirSync를 구성한 경우에는 해당 데이터베이스 인스턴스에 대한 정보

### 병렬 배포

현재 5만 개가 넘는 개체를 동기화 중인 경우 병렬 배포를 수행하는 옵션이 주어집니다. 병렬 배포에는 별도의 서버나 서버 집합이 필요합니다(SQL Server에 대해 별도의 서버가 필요한 경우). 병렬 배포의 이점은 동기화 가동 중지 시간을 방지할 수 있는 기회입니다. Azure AD Connect 설치는 원하는 가동 중지 시간을 예측하려고 합니다. 하지만 과거에 DirSync를 업그레이드한 적이 있다면 자신만의 경험이 최선의 가이드일 수 있습니다.

## Azure AD Connect 설치

Azure AD Connect를 다운로드하여 기존 DirSync 서버에 복사합니다.

1. AzureADConnect.msi를 찾아 두 번 클릭합니다.
2. 마법사를 단계별로 시작합니다.

바로 업그레이드의 경우 다음과 같은 높은 수준의 단계가 수행됩니다.

1. Azure AD Connect 시작
2. 현재 DirSync 구성 분석
3. Azure AD 전역 관리자 암호 수집
4. 엔터프라이즈 관리자 계정에 대한 자격 증명 수집(Azure AD Connect 설치 도중에만 사용됨)
5. AAD Connect  설치
    * DirSync 제거
	* AAD Connect 설치
	* (선택 사항) 동기화 시작

다음과 같은 경우에 추가 단계/정보가 필요합니다.

* 현재 로컬 또는 원격으로 전체 SQL Server를 사용 중임
* 동기화 범위에 5만 개가 넘는 개체가 있음

## 바로 업그레이드 - 개체 5만 개 미만 - SQL Express(연습)

0. Azure AD Connect 설치 관리자(MSI) 시작

1. 사용 조건 및 개인 정보 알림을 검토하고 동의합니다.

![Azure AD 시작](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)

2. 다음을 클릭하여 기존 DirSync 설치를 분석합니다.

![기존 디렉터리 동기화 설치 분석](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)

3. 분석이 완료되면 진행 방법에 대해 권장 사항을 안내합니다. SQL Express를 사용하는 개체가 5만 개 미만인 이 시나리오에서는 다음과 같은 화면이 표시됩니다.

![분석이 완료되어 DirSync에서 업그레이드할 준비가 됨](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)

4. Azure AD에 연결하는 데 현재 사용하는 계정의 암호를 제공합니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)

5. Active Directory에 대한 엔터프라이즈 관리자 계정을 제공합니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)

6. 이제 구성할 준비가 되었습니다. 다음을 클릭하면 DirSync가 제거되고 Azure AD Connect가 구성되어 동기화를 시작합니다.  

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## 바로 업그레이드 - 개체 5만 개 초과
동기화 범위에 있는 개체가 5만 개가 넘는 경우, 3단계에서 다른 메시지가 표시됩니다. 다음과 비슷한 화면이 표시됩니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

이 경우 별도 서버에 병렬 업그레이드를 고려하는 것이 좋습니다. 이렇게 권장하는 이유는, 로컬 Active Directory의 변경 사항이 Azure AD/Office 365에 반영되는 속도와 관련하여 조직의 규모에 따라 바로 업그레이드가 비즈니스의 서비스 수준 계약에 영향을 미칠 수 있기 때문입니다. Azure AD Connect를 사용한 첫 번째 동기화에 소요될 수 있는 시간을 추정하려고 합니다. 위에서 설명했듯이 DirSync 원본 설치 또는 DirSync로 업그레이드와 관련된 자신만의 경험이 최선의 지표일 수 있습니다.

병렬 배포에는 별도의 서버가 필요합니다(Azure AD Connect에서 별도 서버에 SQL Server를 실행해야 하는 경우). 이런 이유로 인해, 조직 내 영향을 피하는 방법으로 예약할 수 있는 경우에는 바로 업그레이드를 고려하는 것이 합당합니다.

바로 업그레이드를 진행하려면 “이 컴퓨터에서 계속 DirSync 업그레이드” 메시지 옆에 있는 확인란을 클릭합니다.

## 바로 업그레이드 - 전체 SQL Server

DirSync 설치가 로컬 또는 원격 처음 사용자용 SQL Server를 사용 중인 경우, 3단계에서 다른 메시지가 표시됩니다. 다음과 비슷한 화면이 표시됩니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)

DirSync에서 사용 중인 기존 SQL Server 데이터베이스 서버와 관련된 정보는 표시됩니다. 필요한 경우, 적절하게 조정합니다. "다음"을 클릭하여 설치를 계속합니다.

## 병렬 배포 - 개체 5만 개 초과

개체가 5만 개를 넘는 경우, 3단계에서 Azure AD Connect 설치 시 병렬 배포가 권장됩니다. Azure AD Connect의 바로 배포 또는 병렬 배포를 선택하는 데 대한 자세한 내용은 위의 "바로 업그레이드 - 개체 5만 개 초과"를 참조하세요. 다음과 비슷한 화면이 표시됩니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

병렬 배포를 진행하려는 경우 다음 단계를 수행해야 합니다.

- "설정 내보내기" 단추를 클릭합니다. 별도 서버에 Azure AD Connect를 설치하면 이 설정을 가져와 현재 DirSync에서 새  AAD Connect 설치로 설정이 마이그레이션됩니다.

설정을 성공적으로 내보내고 나면 DirSync 서버에서 Azure AD Connect 마법사를 종료할 수 있습니다.

### 별도 서버에 Azure AD Connect 설치

새 서버에 Azure AD Connect를 설치할 때 DirSync가 발견되지 않으면 Azure AD Connect 새로 설치를 수행한다고 가정합니다. 여기에는 몇 가지의 특별한 단계가 있습니다.

1. Azure AD Connect 설치 관리자(MSI)를 실행합니다.
2. "Azure AD Connect 시작" 화면이 표시됩니다. 창 오른쪽 위에 있는 모서리에서 "X"를 클릭하여 마법사를 종료합니다.
3. 명령 프롬프트를 엽니다.
4. Azure AD Connect 위치에서(기본값: C:\Program Files\Microsoft Azure Active Directory Connect) 다음 명령을 실행합니다.
    * AzureADConnect.exe /migrate

Azure AD Connect가 연결되고 다음 UI가 표시됩니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)

5. DirSync 설치에서 내보낸 설정 파일을 선택합니다.
6. 다음을 포함한 고급 옵션을 구성합니다.
    * Azure AD Connect에 대한 사용자 지정 설치 위치
	* 기존 SQL Server 인스턴스(기본값: Azure AD Connect는 SQL Server 2012 Express를 설치함)
	* SQL Server에 연결하는 데 사용되는 서비스 계정(SQL Server 데이터베이스가 원격인 경우에는 이 계정이 도메인 서비스 계정이어야 함)

다음 UI에 이러한 옵션이 표시됩니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)

7. 다음을 클릭합니다. 
8. "구성 준비 완료" 페이지에서 "초기 구성이 완료되자마자 동기화 프로세스를 시작합니다."의 선택을 취소합니다.
8. "설치"를 클릭합니다.

[AZURE.NOTE]DirSync가 여전히 설치되어 있고 실행되며 Azure AD Connect가 동시에 AAD에 쓰기를 시도하지 않도록, 동기화 시작 확인란의 선택을 취소합니다.

### Azure AD Connect가 동기화를 시작할 준비가 되었는지 확인

Azure AD Connect가 DirSync로부터 인수할 준비가 되었는지 여부를 확인하려면 Azure AD Connect Synchronization Service Manager를 열어야 합니다. Windows 시작 메뉴에서 "동기화"를 사용하여 검색 하면 이 응용 프로그램이 표시됩니다.

응용 프로그램 내에서 "작업" 탭을 확인해야 합니다. 이 탭에서 원하는 다음 작업이 완료되었는지 확인합니다.

- AD 관리 에이전트에서 가져오기
- Azure AD 관리 에이전트에서 가져오기
- AD 관리 에이전트에 대한 전체 동기화
- Azure AD 관리 에이전트에 대한 전체 동기화

이 4가지 작업이 완료되면 DirSync를 제거하고 Azure AD Connect 동기화를 사용하도록 설정할 수 있습니다.

### DirSync 제거(이전 서버)

- "프로그램 추가/제거" 내에서 "Microsoft Azure Active Directory 동기화 도구"를 찾습니다.
- "Microsoft Azure Active Directory 동기화 도구"를 제거합니다.

### Azure AD Connect 열기(새 서버)
설치 후 Azure AD Connect를 다시 열면 구성 환경이 제공됩니다. Azure AD Connect를 엽니다.

다음이 표시되어야 합니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* "준비 모드 구성"을 선택합니다.
    * 내보낸 설정을 사용하여 DirSync에서 업그레이드하면 Azure AD Connect가 자동으로 준비 모드로 전환됩니다. 준비 모드는 기본적으로 Azure AD Connect 내에서 동기화가 이루어지는 것을 의미하지만, 변경 사항이 Azure AD나 AD로 내보내지지는 않습니다.
* "준비 모드 사용" 확인란의 선택을 취소하여 준비를 해제합니다.

![Azure AD 자격 증명 입력](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* 설치 단추를 클릭합니다.

축하합니다. 병렬 배포를 사용하여 Azure AD Connect로 마이그레이션했습니다.

## Azure AD Connect 지원 구성 요소

다음은 Azure AD Connect를 설정한 서버에 Azure AD Connect가 설치되는 데 필요한 필수 조건 및 지원 구성 요소의 목록입니다. 이 목록은 기본 Express 설치에 해당합니다. 동기화 서비스 설치 페이지에서 다른 SQL Server를 사용하도록 선택하는 경우, 다음 목록에 나온 SQL Server 2012 구성 요소는 설치되지 않습니다.

- Forefront Identity Manager Azure Active Directory Connector
- Microsoft SQL Server 2012 명령줄 유틸리티
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell용 Azure Active Directory 모듈
- IT 전문가를 위한 Microsoft Online Services 로그인 도우미
- Microsoft Visual C++ 2013 재배포 패키지


**추가 리소스**

* [클라우드에서 온-프레미스 ID 인프라 사용](active-directory-aadconnect.md)
* [Azure AD Connect 작동 방법](active-directory-aadconnect-how-it-works.md)
* [Azure AD Connect의 다음 단계](active-directory-aadconnect-whats-next.md)
* [자세한 정보](active-directory-aadconnect-learn-more.md)
* [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58--> 