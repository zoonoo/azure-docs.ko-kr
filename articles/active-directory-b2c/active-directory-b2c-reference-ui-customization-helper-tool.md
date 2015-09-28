<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 페이지 UI 사용자 지정 도우미 도구 | Microsoft Azure"
	description="Azure Active Directory B2C에서 페이지 UI 사용자 지정 기능을 설명하기 위해 사용된 도우미 도구"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 페이지 사용자 인터페이스(UI) 사용자 지정 기능을 설명하기 위해 사용된 도우미 도구

이 문서는 Azure Active Directory(AD) B2C의 페이지 사용자 인터페이스(UI) 사용자 지정 기능에 대한 [주요 문서](active-directory-b2c-reference-ui-customization)에 동반됩니다.

아래에 지정된 단계를 수행하면 샘플 콘텐츠를 사용하여 페이지 UI 사용자 지정 기능을 연습할 수 있습니다. 데모 목적으로 샘플 콘텐츠는 **정적** HTML5입니다.

1. 샘플 콘텐츠를 [Azure Blob 저장소](http://azure.microsoft.com/documentation/services/storage/)에 업로드하고 HTTPS를 통해 공개적으로 액세스할 수 있게 하며 해당 URL의 CORS를 켭니다.(크로스-원본 자원 공유)
2. 기존 등록 정책에서 페이지 UI 사용자 지정 설정을 수정하고 위의 URL을 지정합니다.

시작하기 전에

- [여기](active-directory-b2c-overview.md)에 나열된 Azure AD B2C 빠른 시작 응용 프로그램 중 하나를 빌드합니다. 이 작업의 일부로서 여기서 수정하는 등록 정책을 만듭니다.
- 도우미 도구를 [여기서](https://github.com/AzureADSamples/B2C-AzureBlobStorage-Client) 다운로드합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### 1단계: 올바른 위치에서 샘플 콘텐츠 업로드

1. [Azure Preview 포털](https://portal.azure.com/)에 로그인합니다.
2. **+새로 만들기** -> **데이터 + 저장소** -> **저장소 계정**을 클릭합니다. Azure Blob 저장소 계정을 만들려면 Azure 구독이 필요합니다. [여기서](https://azure.microsoft.com/ko-KR/pricing/free-trial/) 무료 평가판에 등록할 수 있습니다.
3. 저장소 계정에 **이름**을 제공하고(예: "contoso.core.windows.net") **가격 책정 계층**, **리소스 그룹** 및 **구독**에 적절한 선택 항목을 선택합니다. **시작 보드에 고정** 옵션이 선택되었는지 확인합니다. **만들기**를 클릭합니다.
4. 시작 보드에 돌아가서 방금 만든 저장소 계정을 클릭합니다.
5. **요약** 섹션에서 **컨테이너** 및 **+추가**를 차례로 클릭합니다.
6. 컨테이너에 **이름**을 제공하고(예: "b2c") **Blob**를 **액세스 형식**으로 선택합니다. **확인**을 클릭합니다.
7. 만든 컨테이너는 **Blob** 블레이드에서 목록에 표시됩니다. 컨테이너의 URL를 적어둡니다. 예를 들어 `https://contoso.blob.core.windows.net/b2c`와 같아야 합니다. **Blob** 블레이드를 닫습니다.
8. 저장소 계정 블레이드에서 **키**를 클릭하고 **저장소 계정 이름** 및 **기본 선택키** 필드의 값을 적어둡니다.

    > [AZURE.NOTE]**기본 선택키**는 중요한 보안 자격 증명입니다.

9. 도우미 도구를 실행하고 이전 단계에서 복사한 **저장소 계정 이름** 및 **기본 선택키** 값을 제공합니다. 샘플 콘텐츠를 저장소 계정에 업로드합니다.
10. 브라우저에서 `https://contoso.blob.core.windows.net/b2c/idp.html`에 액세스를 시도하여 콘텐츠를 제대로 업로드했는지 확인합니다. 또한 [http://test-cors.org/](http://test-cors.org/)를 사용하여 콘텐츠가 사용하도록 설정된 CORS인지 확인합니다.(결과에서 `XHR status: 200`을 찾기)

### 2단계: 등록 정책에서 페이지 UI 사용자 지정 옵션 수정

1. [Azure Preview 포털](https://portal.azure.com)의 디렉터리에 로그인하고 B2C 기능 블레이드로 이동합니다.
2. **등록 정책**을 클릭한 다음 사용자 등록 정책을 클릭합니다.(예: "B2C\_1\_SiIn")
3. **페이지 UI 사용자 지정** 및 **ID 공급자 선택 페이지**를 차례로 클릭합니다.
4. **사용자 지정 템플릿 사용** 스위치를 **예**로 설정/해제합니다. **사용자 지정 페이지 URI** 필드에서 저장소 계정에 업로드된 콘텐츠의 적절한 URL을 입력합니다. 예: `https://contoso.blob.core.windows.net/b2c/idp.html`. **확인**을 클릭합니다.
5. **로컬 계정 등록 페이지**를 클릭합니다. **사용자 지정 템플릿 사용** 스위치를 **예**로 설정/해제합니다. **사용자 지정 페이지 URI** 필드에서 저장소 계정에 업로드된 콘텐츠의 적절한 URL을 입력합니다. 예: `https://contoso.blob.core.windows.net/b2c/su.html` **확인**을 두 번 클릭합니다.
6. **Save**를 클릭합니다.
7. 블레이드 위쪽에서 **지금 실행** 명령을 클릭합니다. **응용 프로그램** 드롭다운에서 "B2C app" 및 **회신 URL/리디렉션 URI** 드롭다운에서 `https://localhost:44321/`을 선택합니다. **지금 실행** 단추를 클릭합니다.
8. 새 브라우저 탭이 열리고 준비된 새 컨텐츠로 응용 프로그램에 등록한 사용자 환경을 실행할 수 있습니다.

> [AZURE.NOTE]정책 변경 내용이 적용되려면 최대 1분이 걸립니다.

<!---HONumber=Sept15_HO3-->