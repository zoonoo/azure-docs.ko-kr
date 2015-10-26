<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 사용자 인터페이스(UI) 사용자 지정 | Microsoft Azure"
	description="Azure Active Directory B2C에서 사용자 인터페이스(UI) 사용자 지정 기능의 항목"
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
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: Azure AD B2C 사용자 인터페이스(UI)를 사용자 지정하는 방법

사용자 환경은 소비자 지향 응용 프로그램에서 가장 중요합니다. 좋은 응용 프로그램과 훌륭한 응용 프로그램, 그저 활발한 소비자와 진정으로 참여하는 소비자 간의 차이점입니다. Azure Active Directory(AD) B2C를 사용하면 픽셀을 완벽하게 제어하여 소비자 등록, 로그인(*아래 참고를 참조*) 및 프로필 편집 페이지를 사용자 지정할 수 있습니다.

> [AZURE.NOTE]현재 로컬 계정 로그인 페이지, 확인 전자 메일 및 셀프 서비스 암호 재설정 페이지는 이 문서에서 설명하는 메커니즘이 아닌 [회사 브랜딩 기능](./active-directory/active-directory-add-company-branding.md)을 사용하여 사용자 지정할 수 있습니다.

이 문서에서는 다음에 대해 읽습니다.

- 페이지 사용자 인터페이스(UI) 사용자 지정 기능의 개요입니다.
- 샘플 콘텐츠를 사용하여 페이지 UI 사용자 지정 기능을 테스트하는 데 도움이 되는 도우미 도구입니다.
- 페이지의 각 형식에서 코어 UI 요소입니다.
- 이 기능을 실행할 때 모범 사례입니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 페이지 사용자 인터페이스(UI) 사용자 지정 기능

페이지 UI 사용자 지정 기능을 사용하면 소비자 등록, 로그인 및 프로필 편집 페이지의 모양과 느낌을 사용자 지정할 수 있습니다.([정책](active-directory-b2c-reference-policies.md)을 구성하여) 소비자가 Azure AD B2C 서비스에 의해 제공된 응용 프로그램과 페이지 간에 이동하는 경우 일관된 환경을 유지합니다.

옵션이 제한되거나 API를 통해서만 사용할 수 있는 다른 서비스와 달리 Azure AD B2C는 페이지 UI 사용자 지정에 최신(및 간단한) 접근 방법을 사용합니다. 작동 방식은 다음과 같습니다. Azure AD B2C는 소비자의 브라우저에서 코드를 실행하고 [크로스-원본 자원 공유 (CORS)](http://www.w3.org/TR/cors/)을 호출하는 최신 방법을 사용하여 정책에서 지정한 URL에서 콘텐츠를 로드합니다. 다른 페이지에 다른 URL을 지정할 수 있습니다. 코드는 Azure AD B2C의 콘텐츠(UI 요소라고 함) 및 해당 URL에서 로드된 콘텐츠를 병합하고 소비자에게 페이지를 표시합니다. `<body>` 어딘가에 있는 `<div id="api"></div>` 요소를 사용하여 올바른 형식의 HTML5 콘텐츠를 만들기만 하면 됩니다. 여기서 Azure AD B2C의 콘텐츠가 병합합니다. (허용된 CORS로)HTTPS 끝점의 콘텐츠를 호스트합니다. 또한 Azure AD B2C UI 요소의 스타일을 완전히 만들 수 있습니다.

## UI 사용자 지정 기능 사용해 보기

Azure Blob 저장소에서 호스팅되는 샘플 HTML 및 CSS 콘텐츠를 사용하여 UX 사용자 지정 기능을 사용해 보는 경우를 위해, 정적 콘텐츠를 업로드 및 구성하는 [간단한 도우미 도구](active-directory-b2c-reference-ui-customization-helper-tool.md)를 제공합니다.

## 페이지의 각 형식에서 코어 UI 요소

아래 섹션에는 Azure AD B2C가 콘텐츠의 <div id="api"></div> 요소에 병합하는 각 페이지 형식에 대한 HTML5 조각의 예가 있습니다. 사용자 고유의 스타일시트를 사용하여 이러한 UI 요소를 사용자 지정할 수 있습니다. 이러한 스타일시트는 <head> 조각에서 해당 페이지에 추가하는 기본 스타일시트를 재정의해야 합니다.

> [AZURE.IMPORTANT]미리 보기를 하는 동안 여러분의 의견에서 학습하고 적용한 대로 변경할 정확한 UI 요소가 필요합니다. 항상 기본 페이지의 소스 코드에서 최신 업데이트를 검사합니다. 실제로 고려되는 첫 번째 변경 내용은 기본 스타일시트 제거입니다. 즉, 콘텐츠에서 이러한 UI 요소에 대한 사용자 고유의 스타일시트를 항상 제공해야 합니다.

## ID 공급자 선택 페이지

이 페이지는 등록 또는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. Facebook, Google+ 또는 로컬 계정과 같은 소셜 ID 공급자입니다.(전자 메일 주소 또는 사용자 이름 기반)

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

## 로컬 계정 등록 페이지

이 페이지는 전자 메일 주소 또는 사용자 이름 기반 로컬 계정을 사용하여 등록하는 경우 사용자가 작성해야 하는 등록 양식을 포함합니다. 양식은 텍스트 입력 상자, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 및 다중 선택 확인란과 같은 다른 입력 제어를 포함할 수 있습니다.

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

## 소셜 계정 등록 페이지

이 페이지는 Facebook 또는 Google+와 같은 소셜 ID 공급자에서 기존 계정을 사용하여 등록하는 경우 소비자가 작성해야 하는 등록 양식을 포함합니다. 이 페이지는 암호 입력 필드를 제외하고 로컬 계정 등록 페이지와 유사합니다.(이전 섹션에 표시됨)

## Multi-factor Authentication 페이지

이 페이지를 사용하면 등록 또는 로그인하는 동안 사용자가 전화 번호를 확인할 수 있습니다.(텍스트 또는 음성 사용)

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

## 오류 페이지


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

페이지 UI 사용자 지정 기능을 사용하려는 경우 다음 모범 사례를 검토하세요.

- Azure AD B2C의 기본 템플릿을 덮어 쓰거나 수정하려 하지 마세요. 처음부터 HTML5 콘텐츠를 작성하고 기본 템플릿을 참조로 사용하는 것이 가장 좋습니다.
- 보안상의 이유로 콘텐츠에 JavaScript를 포함할 수 없습니다. 필요한 것은 대부분 독창적으로 사용 가능해야 합니다. 그렇지 않은 경우 [사용자 음성](http://feedback.azure.com/forums/169401-azure-active-directory)을 사용하여 새로운 기능을 요청합니다.
- 지원되는 브라우저 버전:
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (제한됨)
	- Internet Explorer 8 (제한됨)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=Oct15_HO3-->