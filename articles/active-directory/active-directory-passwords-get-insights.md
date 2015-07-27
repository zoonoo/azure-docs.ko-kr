<properties 
	pageTitle="정보 얻기: Azure AD 암호 관리 보고서 | Microsoft Azure" 
	description="이 문서에서는 사용자의 조직에서 암호 관리 작업에 대한 정보를 얻기 위해 보고서를 사용하는 방법을 설명합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# 암호 관리 보고서와 함께 Operational Insights를 얻는 방법
이 섹션에서는 Azure Active Directory의 암호 관리 보고서를 사용하여 조직에서 사용자 암호 재설정을 사용하여 변경하는 방법을 볼 수 있는 방법을 설명합니다.

- [**암호 관리 보고서 개요**](#overview-of-password-management-reports)
- [**암호 관리 보고서를 보는 방법**](#how-to-view-password-management-reports)
- [**조직에서 암호 재설정 등록 활동 보기**](#view-password-reset-registration-activity)
- [**조직에서 암호 재설정 활동 보기**](#view-password-reset-activity)

## 암호 관리 보고서 개요
암호 재설정을 배포하면 조직에서 가장 일반적인 다음 단계 중 하나는 조직에서 사용 중인 방법을 보는 것입니다. 예를 들어, 사용자가 암호 재설정을 위해 등록되는 방법 또는 지난 몇 일 내에 수행된 암호 재설정 횟수에 대한 정보를 얻을 수 있습니다. 다음은 현재 [Azure 관리 포털](https://manage.windowsazure.com)에 있는 암호 관리 보고서로 답변할 수 있는 일반적인 질문들입니다.

- 얼마나 많은 사람들이 암호 재설정을 위해 등록합니까?
- 누가 암호 재설정을 위해 등록합니까?
- 사람들이 등록하는 데이터는 무엇입니까?
- 지난 7일 동안 얼마나 많은 사람들이 자신의 암호를 재설정했습니까?
- 사용자나 관리자가 자신의 암호를 재설정하는 가장 일반적인 방법은 무엇입니까?
- 암호 재설정을 시도할 때 사용자나 관리자가 직면하는 공통적인 문제는 무엇입니까?
- 어느 관리자가 자신의 암호를 자주 재설정합니까?
- 암호 재설정에 대해 의심스러운 활동이 있습니까?


## 암호 관리 보고서를 보는 방법
암호 관리 보고서를 찾으려면 다음 단계를 따릅니다.

1.	[Azure 관리 포털](https://manage.windowsazure.com)의 **Active Directory** 확장을 클릭합니다.
2.	포털에 표시되는 목록에서 디렉터리를 선택합니다.
3.	**보고서** 탭을 클릭합니다.
4.	**활동 로그** 섹션을 잠급니다.
5.	**암호 재설정 활동** 보고서 또는 **암호 재설정 등록 활동** 보고서 중 하나를 선택합니다.

    ![][001]

## 암호 재설정 등록 활동 보기

암호 재설정 등록 활동 보고서는 조직 내에서 발생한 모든 암호 재설정 등록을 보여줍니다. 암호 재설정 등록은 암호 재설정 등록 포털([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup))에서 인증 정보를 성공적으로 등록한 사용자에 대한 이 보고서에 표시됩니다.

- **최대 시간 범위**: 1개월
- **최대 행 수**: 무제한
- **다운로드 가능**: 예, CSV 파일을 통해

    ![][002]

### 보고서 열 설명
다음 목록에서는 각 보고서의 열에 대해 자세히 설명합니다.

- **사용자** – 암호 재설정 등록 작업을 시도한 사용자입니다.
- **역할** – 디렉터리에서 사용자의 역할입니다.
- **날짜 및 시간** – 시도한 날짜 및 시간입니다.
- **등록된 데이터** – 암호 재설정 등록 중 사용자가 제공한 인증 데이터입니다.

### 보고서 값 설명
다음 테이블에서 각 열에 대해 허용되는 다른 값을 설명합니다.

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>열</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>허용되는 값과 해당 의미</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>등록된 데이터</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>대체 전자 메일</strong> – 인증하는 데 사용자가 사용한 대체 전자 메일 또는 인증  전자 메일<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>사무실 전화</strong> – 인증하는 데 사용자가 사용한 사무실 전화<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>휴대폰</strong> – 인증하는 데 사용자가 사용한 휴대폰 또는 인증 전화<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>보안 질문</strong> – 인증하는 데 사용자가 사용한 보안 질문<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>위의 조합(예: 대체 전자 메일 + 휴대폰)</strong> – 2개의 게이트 정책이 지정되고 암호 재설정 요청을 인증하는 데 사용자가 사용한 두 메서드를 표시합니다.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## 암호 재설정 활동 보기

이 보고서는 조직 내에서 발생한 모든 암호 재설정 시도를 보여줍니다.

- **최대 시간 범위**: 1개월
- **최대 행 수**: 무제한
- **다운로드 가능**: 예, CSV 파일을 통해

    ![][003]

### 보고서 열 설명
다음 목록에서는 각 보고서의 열에 대해 자세히 설명합니다.

1. **사용자** – 암호 재설정 작업을 시도한 사용자(사용자가 암호를 재설정할 때 제공되는 사용자 ID 필드에 따라).
2. **역할** – 디렉터리에서 사용자의 역할입니다.
3. **날짜 및 시간** – 시도한 날짜 및 시간입니다.
4. **사용된 메서드** –이 재설정 작업에 사용자가 사용한 인증 메서드입니다.
5. **결과** – 암호 재설정 작업의 최종 결과입니다.
6. **세부 정보** – 암호 재설정 결과로 왜 이 값이 생성되었는지에 대한 자세한 설명입니다. 또한 예기치 않은 오류를 해결하기 위해 수행할 수 있는 완화 단계를 포함합니다.

### 보고서 값 설명
다음 테이블에서 각 열에 대해 허용되는 다른 값을 설명합니다.

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>열</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>허용되는 값과 해당 의미</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용된 메서드</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>대체 전자 메일</strong> – 인증하는 데 사용자가 사용한 대체 전자 메일 또는 인증  전자 메일<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>사무실 전화</strong> – 인증하는 데 사용자가 사용한 사무실 전화<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>휴대폰</strong> – 인증하는 데 사용자가 사용한 휴대폰 또는 인증 전화<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>보안 질문</strong> – 인증하는 데 사용자가 사용한 보안 질문<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>위의 조합(예: 대체 전자 메일 + 휴대폰)</strong> – 2개의 게이트 정책이 지정되고 암호 재설정 요청을 인증하는 데 사용자가 사용한 두 메서드를 표시합니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>결과</p>
              </td>
              <td>
                <ul>
                  <li class="unordered">
                    <strong>중단됨</strong> – 사용자가 암호 재설정을 시작하지만 완료하지 않고 중간에서 중단됩니다.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>차단됨</strong> -사용자의 계정이 암호 재설정 페이지를 사용하거나 24시간 동안 너무 여러 번 단일 암호 재설정 게이트를 사용하여 암호를 재설정할 수 없습니다.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>취소됨</strong> – 사용자가 암호 재설정을 시작했지만 취소 단추를 클릭하여 도중 세션을 취소했습니다. <br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>연결된 관리자</strong> – 세션 중 사용자가 해결할 수 없는 문제가 생겨 사용자가 암호 재설정 작업을 완료하는 대신 “관리자에게 문의” 링크를 클릭했습니다.<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>실패함</strong> – 사용자가 기능을 사용하도록 구성되지 않았기 때문에 암호를 재설정할 수 없습니다(라이선스 없음, 누락된 인증 정보, 암호 관리 온-프레미스지만 쓰기 저장이 꺼져 있음).<br><br></li>
                </ul>
                <ul>
                  <li class="unordered">
                    <strong>성공함</strong> – 암호 재설정이 성공했습니다.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>세부 정보</p>
              </td>
              <td>
                <p>아래 테이블을 참조하세요.</p>
              </td>
            </tr>
          </tbody></table>

### 세부 정보 열에 대해 허용되는 값
다음은 암호 재설정 활동 보고서를 사용하는 경우 예상할 수 있는 결과 형식 목록입니다.

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>세부 정보</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>결과 형식</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 전자 메일 확인 옵션을 완료한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 모바일 SMS 확인 옵션을 완료한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 모바일 음성 통화 확인 옵션을 완료한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 사무실 음성 통화 확인 옵션을 완료한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 보안 질문 옵션을 완료한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 사용자 ID를 입력한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 전자 메일 확인 옵션을 시작한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 모바일 SMS 확인 옵션을 시작한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 모바일 음성 통화 확인 옵션을 시작한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 사무실 음성 통화 확인 옵션을 시작한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 보안 질문 옵션을 시작한 후 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 새 암호를 선택하기 전에 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 새 암호를 선택하는 중에 중단함</p>
              </td>
              <td>
                <p>Abandoned</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 너무 많은 잘못된 전자 메일 확인 코드를 입력하여 24시간 동안 차단됨</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 너무 많은 잘못된 SMS 확인 코드를 입력하여 24시간 동안 차단됨</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 너무 많이 모바일 음성 인증을 시도하여 24시간 동안 차단됨</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 너무 많이 사무실 음성 인증을 시도하여 24시간 동안 차단됨</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 너무 많이 보안 질문에 답변을 시도하여 24시간 동안 차단됨</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 너무 많이 전화번호 확인을 시도하여 24시간 동안 차단됨</p>
              </td>
              <td>
                <p>Blocked</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 필수 인증 방법을 전달하기 전에 취소함</p>
              </td>
              <td>
                <p>Cancelled</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 새 암호를 전송하기 전에 취소함</p>
              </td>
              <td>
                <p>Cancelled</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자 전자 메일 확인 옵션을 시도한 후 관리자에 문의함</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 모바일 SMS 확인 옵션을 시도한 후 관리자에 문의함</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 모바일 음성 통화 확인 옵션을 시도한 후 관리자에 문의함</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 사무실 음성 통화 확인 옵션을 시도한 후 관리자에 문의함</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 보안 질문 확인 옵션을 시도한 후 관리자에 문의함</p>
              </td>
              <td>
                <p>Contacted admin</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>이 사용자에 대한 암호 재설정이 사용되지 않습니다. 이를 해결하려면 구성 탭에서 암호 재설정 사용합니다.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자에게 라이선스가 없습니다. 이를 해결하기 위해 사용자에게 라이선스를 추가할 수 있습니다.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 쿠키를 사용하지 않고 장치에서 다시 설정하려고 시도함</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자의 계정에 정의된 인증 메서드가 부족합니다. 이를 해결하려면 인증 정보를 추가합니다.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자의 암호는 관리 대상 온-프레미스입니다. 이를 해결하려면 암호 쓰기 저장을 사용하도록 설정할 수 있습니다.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>온-프레미스 암호 재설정 서비스에 접근할 수 없습니다. 동기화 컴퓨터의 이벤트 로그를 확인합니다.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자의 온-프레미스 암호를 재설정하는 중에 문제가 발생했습니다. 동기화 컴퓨터의 이벤트 로그를 확인합니다.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>이 사용자는 암호 재설정 사용자 그룹의 멤버가 아닙니다. 이를 해결하려면 해당 그룹에 이 사용자를 추가합니다.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>암호 재설정은 이 테넌트에 대해 완전히 비활성화되었습니다. 이를 해결하려면, <a href="http://aka.ms/ssprtroubleshoot">http://aka.ms/ssprtroubleshoot</a>를 참조하세요.</p>
              </td>
              <td>
                <p>Failed</p>
              </td>
            </tr>
            <tr>
              <td>
                <p>사용자가 성공적으로 암호를 재설정함</p>
              </td>
              <td>
                <p>Succeeded</p>
              </td>
            </tr>
          </tbody></table>

<br/> <br/> <br/>

**추가 리소스**


* [암호 관리 정의](active-directory-passwords.md)
* [암호 관리의 작동 원리](active-directory-passwords-how-it-works.md)
* [암호 관리 시작](active-directory-passwords-getting-started.md)
* [암호 관리 사용자 지정](active-directory-passwords-customize.md)
* [암호 관리 모범 사례](active-directory-passwords-best-practices.md)
* [암호 관리 FAQ](active-directory-passwords-faq.md)
* [암호 관리 문제 해결](active-directory-passwords-troubleshoot.md)
* [자세한 정보](active-directory-passwords-learn-more.md)
* [MSDN의 암호 관리](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"
 

<!---HONumber=July15_HO3-->