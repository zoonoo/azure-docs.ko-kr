<properties
	pageTitle="Azure Active Directory B2C: 사용자 인터페이스(UI) 사용자 지정 | Microsoft Azure"
	description="Azure Active Directory B2C에서 UI(사용자 인터페이스) 사용자 지정 기능의 항목"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: Azure AD B2C UI(사용자 인터페이스)를 사용자 지정

사용자 환경은 소비자 지향 응용 프로그램에서 가장 중요합니다. 좋은 응용 프로그램과 훌륭한 응용 프로그램, 그저 활발한 소비자와 진정으로 참여하는 소비자 간의 차이점입니다. Azure AD(Azure Active Directory) B2C를 사용하면 픽셀을 완벽하게 제어하여 소비자 등록, 로그인(*아래 참고를 참조*), 프로필 편집 및 암호 재설정 페이지를 사용자 지정할 수 있습니다.

> [AZURE.NOTE]
현재 로컬 계정 로그인 페이지, 해당 동반 암호 재설정 페이지 및 확인 메일은 이 문서에서 설명하는 메커니즘이 아닌 [회사 브랜딩 기능](../active-directory/active-directory-add-company-branding.md)을 사용해야만 사용자 지정할 수 있습니다.

이 문서에서는 다음에 대해 읽습니다.

- 페이지 UI(사용자 인터페이스) 사용자 지정 기능
- 샘플 콘텐츠를 사용하여 페이지 UI 사용자 지정 기능을 테스트하는 데 도움이 되는 도구입니다.
- 페이지의 각 형식에서 코어 UI 요소입니다.
- 이 기능을 실행할 때 모범 사례입니다.

## 페이지 UI 사용자 지정 기능

페이지 UI 사용자 지정 기능을 사용하면 [정책](active-directory-b2c-reference-policies.md)을 구성하여 소비자 등록, 로그인, 암호 재설정 및 프로필 편집 페이지의 모양과 느낌을 사용자 지정할 수 있습니다. 소비자가 Azure AD B2C에 의해 제공된 응용 프로그램과 페이지 간에 이동하는 경우 원활한 환경을 유지합니다.

UI 옵션이 제한되거나 API를 통해서만 사용할 수 있는 다른 서비스와 달리 Azure AD B2C는 페이지 UI 사용자 지정에 최신(및 간단한) 접근 방법을 사용합니다.

작동 방식은 다음과 같습니다. Azure AD B2C는 소비자의 브라우저에서 코드를 실행하고 [크로스-원본 자원 공유 (CORS)](http://www.w3.org/TR/cors/)을 호출하는 최신 방법을 사용하여 정책에서 지정한 URL에서 콘텐츠를 로드합니다. 다른 페이지에 다른 URL을 지정할 수 있습니다. 코드는 Azure AD B2C의 UI 요소를 해당 URL에서 로드된 콘텐츠에 병합하고 소비자에게 페이지를 표시합니다. 꼭 해야 할 일:

1. `<body>` 어딘가에 있는 `<div id="api"></div>` 요소(빈 요소이어야 함)를 사용하여 잘 구성된(Well-Formed) HTML5 콘텐츠를 만듭니다. 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 표시합니다.
2. (허용된 CORS로)HTTPS 끝점의 콘텐츠를 호스트합니다.
3. Azure AD B2C가 삽입하는 UI 요소의 스타일을 만듭니다.

## UI 사용자 지정 기능 테스트하기

샘플 HTML 및 CSS 콘텐츠를 사용하여 UI 사용자 지정 기능을 시도해 보고자 할 경우 Azure Blob 저장소에 샘플 콘텐츠를 업로드 및 구성하는 [간단한 도우미 도구](active-directory-b2c-reference-ui-customization-helper-tool.md)를 제공합니다.

> [AZURE.NOTE]
웹 서버, CDN, AWS S3, 파일 공유 시스템 등 어느 곳에나 UI 콘텐츠를 호스트할 수 있습니다. 콘텐츠가 공개 사용 가능한 HTTPS 끝점(CORS 허용됨)에 호스트된다면 계속 진행하세요. Azure Blob 저장소를 설명 목적으로만 사용합니다.

### 테스트를 위한 가장 기본적인 HTML 콘텐츠

이 기능을 테스트하는 데 사용할 수 있는 가장 기본적인 HTML 콘텐츠는 아래와 같습니다. 앞서 제공된 동일한 도우미 도구를 사용 하여 이 콘텐츠를 Azure Blob 저장소에 업로드하고 구성합니다. 그런 다음 각 페이지의 양식화되지 않은 기본 단추 및 양식 필드가 표시되고 작동하는지 확인할 수 있습니다.

```HTML

<!DOCTYPE html>
<html>
	<head>
		<title>!Add your title here!</title>
	</head>
	<body>
		<div id="api"></div>	<!-- IMP: This element is intentionally empty; don't enter anything here -->
	</body>
</html>

```

## 페이지의 각 형식에서 코어 UI 요소

다음 섹션에는 Azure AD B2C가 콘텐츠의 `<div id="api"></div>` 요소에 병합하는 HTML5 조각의 예가 있습니다. **HTML 5 콘텐츠에 이러한 조각을 삽입하지 마십시오.** Azure AD B2C 서비스는 런타임에 삽입합니다. 이러한 예제를 사용하여 사용자 고유의 스타일 시트를 디자인합니다.

### "ID 공급자 선택 페이지"에 삽입되는 Azure AD B2C 콘텐츠

이 페이지는 등록 또는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. Facebook, Google+ 또는 로컬 계정(메일 주소 또는 사용자 이름 기반)과 같은 소셜 ID 공급자입니다.

```HTML

<div id="api" data-name="IdpSelections">
	<div class="intro">
         <p>Sign up</p>
	</div>

	<div>
		<ul>
			<li>
				<button class="accountButton" id="FacebookExchange">Facebook</button>
			</li>
			<li>
				<button class="accountButton" id="GoogleExchange">Google+</button>
			</li>
			<li>
				<button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
			</li>
		</ul>
	</div>
</div>

```

### "로컬 계정 등록 페이지"에 삽입되는 Azure AD B2C 콘텐츠

이 페이지는 메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정을 등록하는 경우 사용자가 작성해야 하는 등록 양식을 포함합니다. 양식은 텍스트 입력 상자, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같은 다른 입력 제어를 포함할 수 있습니다.

```HTML

<div id="api" data-name="SelfAsserted">
	<div class="intro">
		<p>Create your account by providing the following details</p>
	</div>

	<div id="attributeVerification">
		<div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
		<div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
		<div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
		<div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
		<div class="attr" id="attributeList">
			<ul>
				<li>
					<div class="attrEntry validate">
						<div>
							<div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
							<div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
							<div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
							<div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
							<div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
							<div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
							<div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
						</div>

					<div class="helpText show">This information is required</div>
						<label>Email</label>
						<input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

					<div class="buttons verify" claim_id="email">
						<div id="email_ver_wait" class="working" style="display: none;"></div>
							<label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
							<input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
							<button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
							<button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
							<button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
							<button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
							<button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
						</div>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
						<label>Enter password</label>
						<input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"> This information is required</div>
						<label>Reenter password</label>
						<input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Name</label>
						<input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Gender</label>
						<input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
						<label for="extension_Gender_F">Female</label>
						<input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
						<label for="extension_Gender_M">Male</label>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Loyalty number</label>
						<input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>State</label>
						<select class="dropdown_single" id="state">
							<option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
							<option value="WA">Washington</option>
							<option value="NY">New York</option>
							<option value="CA">California</option>
						</select>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li>
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Zip code</label>
						<input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
					</div>
				</li>
			</ul>
		</div>
		<div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
	</div>
	<div class="verifying-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="verifying_blurb"></div>
	</div>
</div>

```

### "소셜 계정 등록 페이지"에 삽입되는 Azure AD B2C 콘텐츠

이 페이지는 Facebook 또는 Google+와 같은 소셜 ID 공급자에서 기존 계정을 사용하여 등록하는 경우 소비자가 작성해야 하는 등록 양식을 포함합니다. 이 페이지는 암호 입력 필드를 제외하고 로컬 계정 등록 페이지와 유사합니다.(이전 섹션에 표시됨)

### "통합 등록 또는 로그인 페이지"에 삽입되는 Azure AD B2C 콘텐츠

이 페이지는 Facebook 또는 Google+ 또는 로컬 계정과 같은 소셜 ID 공급자를 사용할 수 있는 고객의 등록 및 로그인을 모두 다룹니다.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### "Multi-Factor Authentication 페이지"에 삽입되는 Azure AD B2C 콘텐츠

이 페이지에서 등록 또는 로그인하는 동안 사용자가 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다.

```HTML

<div id="api" data-name="Phonefactor">
	<div id="phonefactor_initial">
		<div class="intro">
			<p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
		</div>
		<div class="errorText" id="errorMessage" style="display:none"></div>
		<div class="phoneEntry" id="phoneEntry">
			<div class="phoneNumber">
				<select id="countryCode" style="display:inline-block">
					<option value="+93">Afghanistan (+93)</option>
					<!-- Not all country codes listed -->
					<option value="+44">United Kingdom (+44)</option>
					<option value="+1" selected="">United States (+1)</option>
					<!-- Not all country codes listed -->
				</select>
			</div>
			<div class="phoneNumber">
				<input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
			</div>
		</div>
		<div id="codeVerification" style="display:none">
			<div>
				<p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
				<input type="text" id="verificationCode" placeholder="Verification code">
			</div>
		</div>
		<div class="buttons">
			<button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
			<button id="verifyPhone" style="display:inline-block">Call Me</button>
			<button id="cancel" style="display:inline-block">Cancel</button>
		</div>
	</div>
	<div class="dialing-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="dialing_blurb"></div><div id="dialing_number"></div>
	</div>
</div>

```

### "오류 페이지"에 삽입되는 Azure AD B2C 콘텐츠

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
	<h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
	<div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
	<div class="error-page-messagedetails">
		<div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
		<div>Timestamp:2015-09-14 23:22:35Z</div>
		<div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
	</div>
</div>

```

## 고유의 콘텐츠를 작성할 때 유의할 점

페이지 UI 사용자 지정 기능을 사용하려는 경우 다음 모범 사례를 검토합니다.

- Azure AD B2C의 기본 콘텐츠를 복사하거나 수정하려고 하지 마세요. 처음부터 HTML5 콘텐츠를 작성하고 기본 콘텐츠를 참조로 사용하는 것이 가장 좋습니다.
- 로그인, 등록 및 프로필 편집 정책에 의해 처리되는 모든 페이지(오류 페이지 제외)에서 사용자가 제공하는 스타일 시트는 <head> 조각에서 이들 페이지에 추가한 기본 스타일 시트를 재정의해야합니다. 등록 또는 로그인 및 암호 재설정 정책, 모든 정책의 오류 페이지에 의해 처리되는 모든 페이지에서 사용자는 모든 스타일을 직접 제공해야 합니다.
- 보안상의 이유로 콘텐츠에 JavaScript를 포함할 수 없습니다. 대부분의 필요한 것을 즉시 사용할 수 있어야 합니다. 그렇지 않으면 [사용자 의견](http://feedback.azure.com/forums/169401-azure-active-directory)을 통해 새로운 기능을 요청합니다.
- 지원되는 브라우저 버전:
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (제한됨)
	- Internet Explorer 8 (제한됨)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=AcomDC_0727_2016-->