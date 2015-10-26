<properties
	pageTitle="액세스 및 사용 보고서 보기 | Microsoft Azure"
	description="액세스 및 사용 보고서를 사용하여 조직 디렉터리의 무결성 및 보안을 보는 방법에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="kenhoff;Justinha;curtand"/>

# 액세스 및 사용 보고서 보기

Azure Active Directory의 액세스 및 사용 보고서를 사용하여 조직 디렉터리의 무결성 및 보안을 볼 수 있습니다 이 정보를 사용하면 디렉터리 관리자는 가능한 보안 위험이 발생할 수 있는 위치를 보다 잘 결정하여 이러한 위험을 적절하게 완화할 수 있습니다.

Azure 관리 포털에서 보고서는 다음과 같은 방식으로 분류되어 있습니다.

- 비정상 보고서 – 비정상으로 확인된 로그인 이벤트가 포함됩니다. 이러한 활동을 인식하고 이벤트가 의심스러운지 확인할 수 있게 해 줍니다.
- 통합 응용 프로그램 보고서 – 클라우드 응용 프로그램이 조직에서 사용되는 방식을 파악할 수 있게 해 줍니다. Azure Active Directory는 수천 개의 클라우드 응용 프로그램과 통합을 제공합니다.
- 오류 보고서 – 외부 응용 프로그램에 계정을 프로비전할 때 발생할 수 있는 오류를 나타냅니다.
- 사용자별 보고서 – 특정 사용자에 대한 장치/로그인 활동 데이터를 표시합니다.
- 활동 로그 - 최근 24시간, 최근 7일 또는 최근 30일 이내에 감사된 모든 이벤트의 레코드와 그룹 활동 변경 사항, 암호 재설정 및 등록 활동이 포함됩니다.

> [AZURE.NOTE]
>
- 일부 고급 비정상 보고서 및 리소스 사용 보고서는 [Azure Active Directory Premium](active-directory-get-started-premium.md)을 사용하도록 설정하는 경우에만 사용할 수 있습니다. 고급 보고서는 액세스 보안을 향상하고, 잠재적 위협에 대응하고, 장치 액세스 및 응용 프로그램 사용에 대한 진단에 액세스하는 데 도움이 됩니다.
- 중국 고객의 경우 전 세계의 Azure Active Directory 인스턴스를 사용하여 Azure Active Directory Premium 및 Basic 버전을 사용할 수 있습니다. Azure Active Directory Premium 및 Basic 버전은 현재 중국 21Vianet이 운영하는 Microsoft Azure에서 지원되지 않습니다. 자세한 내용은 [Azure Active Directory 포럼](http://feedback.azure.com/forums/169401-azure-active-directory)을 통해 문의하세요.

## 보고서

|	보고서 |	설명 |
|	------												|	-----																						|
|	**비정상적인 작업 보고서**
|	[알 수 없는 원본에서 로그인](active-directory-reporting-sign-ins-from-unknown-sources.md) |	추적 없이 로그인하려고 한 경우를 나타낼 수 있습니다. |
|	[여러 번의 실패 후 로그인](active-directory-reporting-sign-ins-after-multiple-failures.md) |	성공적인 무작위 공격을 나타낼 수 있습니다. |
|	[여러 지역에서의 로그인](active-directory-reporting-sign-ins-from-multiple-geographies.md) |	여러 사용자가 같은 계정으로 로그인함을 나타낼 수 있습니다. |
|	[의심스러운 활동을 포함하는 IP 주소의 로그인](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |	지속적인된 침입 시도 후에 성공적인 로그인을 나타낼 수 있습니다. |
|	[감염 가능성이 있는 장치에서의 로그인](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |	감염 가능성이 있는 장치에서 로그인하려고 한 경우를 나타낼 수 있습니다. |
|	[비정상적인 로그인 활동](active-directory-reporting-irregular-sign-in-activity.md) |	사용자의 로그인 패턴에 대한 비정상적인 이벤트를 나타낼 수 있습니다. |
|	[비정상적인 로그인 활동을 포함하는 사용자 보고서](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |	손상된 계정을 가진 사용자를 나타냅니다. |
|	자격 증명이 손실된 사용자 |	자격 증명이 손실된 사용자 |
|	**활동 로그**
|	감사 보고서 |	디렉토리에서 감사된 이벤트 |
|	암호 재설정 활동 |	조직에서 발생하는 암호 재설정의 세부 정보 보기를 제공합니다. |
|	암호 재설정 등록 활동 |	조직에서 발생하는 암호 재설정 등록의 세부 정보 보기를 제공합니다. |
|	셀프 서비스 그룹 활동 |	사용자 디렉토리의 모든 그룹 자체 서비스 활동에 대한 활동 로그를 제공합니다. |
|	**통합된 응용 프로그램**
|	응용 프로그램 사용 현황 |	디렉토리와 통합하는 모든 SaaS 응용 프로그램에 대한 사용 현황 요약을 제공합니다. |
|	계정 프로비전 활동 |	외부 응용 프로그램에 계정을 프로비전하려는 시도의 기록을 제공합니다. |
|	암호 롤오버 상태 |	SaaS 응용 프로그램의 자동 암호 롤오버 상태의 자세한 개요를 제공합니다. |
|	계정 프로비전 오류 |	외부 응용 프로그램에 대한 사용자의 액세스에 대한 영향을 나타냅니다. |
|	**권한 관리**
|	RMS 사용 현황 |	권한 관리 사용 현황에 대한 요약을 제공합니다. |
|	가장 활동적인 RMS 사용자 |	RMS로 보호된 파일에 액세스하는 상위 1000명의 활성 사용자를 나열합니다. |
|	RMS 장치 사용 |	RMS로 보호된 파일에 액세스하는 데 사용된 장치를 나열합니다. |
|	RMS 사용 응용 프로그램 사용 현황 |	RMS 사용 응용 프로그램의 사용 현황을 제공합니다. |

## 보고서 버전

|	보고서 |	무료 |	Basic |	Premium |
|	------												|	----	|	-----	|	--------	|
|	**비정상적인 작업 보고서**
|	알 수 없는 원본에서 로그인 |	✓ |	✓ |	✓ |
|	여러 번의 실패 후 로그인 |	✓ |	✓ |	✓ |
|	여러 지역에서의 로그인 |	✓ |	✓ |	✓ |
|	의심스러운 활동을 포함하는 IP 주소의 로그인 | | |	✓ |
|	감염 가능성이 있는 장치에서의 로그인 | | |	✓ |
|	비정상적인 로그인 활동 | | |	✓ |
|	비정상적인 로그인 활동을 포함하는 사용자 보고서 | | |	✓ |
|	자격 증명이 손실된 사용자 | | |	✓ |
|	**활동 로그**
|	감사 보고서 |	✓ |	✓ |	✓ |
|	암호 재설정 활동 | | |	✓ |
|	암호 재설정 등록 활동 | | |	✓ |
|	셀프 서비스 그룹 활동 | | |	✓ |
|	**통합된 응용 프로그램**
|	응용 프로그램 사용 현황 | | |	✓ |
|	계정 프로비전 활동 |	✓ |	✓ |	✓ |
|	암호 롤오버 상태 | | |	✓ |
|	계정 프로비전 오류 |	✓ |	✓ |	✓ |
|	**권한 관리**
|	RMS 사용 현황 | | |	RMS만 해당 |
|	가장 활동적인 RMS 사용자 | | |	RMS만 해당 |
|	RMS 장치 사용 | | |	RMS만 해당 |
|	RMS 사용 응용 프로그램 사용 현황 | | |	RMS만 해당 |



## 비정상적인 활동 보고서
<p>비정상적인 로그인 활동 보고서는 Office365, Azure 관리 포털, Azure AD 액세스 패널, Sharepoint Online, Dynamics CRM Online 및 기타 Microsoft 온라인 서비스에 대한 의심스러운 로그인 활동에 플래그를 지정합니다.</p>
<p>"여러 번의 실패 후 로그인" 보고서를 제외하고 이러한 모든 보고서는 페더레이션 공급자에 관계없이 앞서 언급한 서비스에 대한 의심스러운 <i>페더레이션</i> 로그인에도 플래그를 지정합니다. </p>
<p>다음과 같은 보고서를 사용할 수 있습니다. </p><ul> <li>[알 수 없는 원본에서 로그인](active-directory-reporting-sign-ins-from-unknown-sources.md)</li> <li>[여러 번의 실패 후 로그인](active-directory-reporting-sign-ins-after-multiple-failures.md)</li> <li>[여러 지역에서의 로그인](active-directory-reporting-sign-ins-from-multiple-geographies.md)</li> <li>[의심스러운 활동을 포함하는 IP 주소의 로그인](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)</li> <li>[비정상적인 로그인 활동](active-directory-reporting-irregular-sign-in-activity.md)</li> <li>[감염 가능성이 있는 장치에서의 로그인](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)</li> <li>[비정상적인 로그인 활동을 포함하는 사용자](active-directory-reporting-users-with-anomalous-sign-in-activity.md)</li> <li>자격 증명이 손실된 사용자</li></ul>










## 활동 로그

### 감사 보고서

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 최근 24시간, 최근 7일 또는 최근 30일 내에 감사된 모든 이벤트의 레코드를 보여 줍니다. <br /> 자세한 내용은 [Azure Active Directory 감사 보고서 이벤트](active-directory-reporting-audit-events.md)를 참조하세요. | 디렉터리 > 보고서 탭 |

![감사 보고서](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### 암호 재설정 활동

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 조직 내에서 발생한 모든 암호 재설정 시도를 보여 줍니다. | 디렉터리 > 보고서 탭 |

![암호 재설정 활동](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### 암호 재설정 등록 활동

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 조직 내에서 발생한 모든 암호 재설정 등록을 보여 줍니다. | 디렉터리 > 보고서 탭 |

![암호 재설정 등록 활동](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### 셀프 서비스 그룹 활동

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 디렉터리의 셀프 서비스 관리 그룹에 대한 모든 활동을 보여 줍니다. | 디렉터리 > 사용자 > <i>사용자</i> > 장치 탭 |

![셀프 서비스 그룹 활동](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## 통합된 응용 프로그램 보고서

### 응용 프로그램 사용: 요약

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 디렉터리에서의 모든 SaaS 응용 프로그램에 대한 사용 현황을 확인하려는 경우 이 보고서를 사용합니다. 이 보고서는 액세스 패널에서 해당 응용 프로그램을 클릭한 횟수를 기반으로 작성됩니다. | 디렉터리 > 보고서 탭 |

![응용 프로그램 사용 요약](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### 응용 프로그램 사용: 세부

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 특정 SaaS 응용 프로그램이 어느 정도 사용되고 있는지 확인하려는 경우 이 보고서를 사용합니다. 이 보고서는 액세스 패널에서 해당 응용 프로그램을 클릭한 횟수를 기반으로 작성됩니다. | 디렉터리 > 보고서 탭 |

### 응용 프로그램 대시보드

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 이 보고서는 선택한 시간 간격 동안 조직 사용자의 누적 응용 프로그램 로그인을 나타냅니다. 대시보드 페이지의 차트는 해당 응용 프로그램의 모든 사용 추세를 식별하는 데 도움이 됩니다. | 디렉터리 > 응용 프로그램 > 대시보드 탭 |

## 오류 보고서

### 계정 프로비전 오류

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 이 보고서를 사용하면 SaaS 응용 프로그램에서 Azure Active Directory로의 계정 동기화 중에 발생한 오류를 모니터링할 수 있습니다. | 디렉터리 > 보고서 탭 |

![계정 프로비전 오류](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## 사용자별 보고서

### 장치

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 특정 사용자가 Azure Active Directory에 액세스하는 데 사용한 장치의 IP 주소 및 지리적 위치를 확인하려는 경우 이 보고서를 사용합니다. | 디렉터리 > 사용자 > <i>사용자</i> > 장치 탭 |

### 작업

| 설명 | 보고서 위치 |
| :-------------     | :-------        |
| 사용자에 대한 로그인 활동을 보여 줍니다. 보고서에는 로그인한 응용 프로그램, 사용한 장치, IP 주소, 위치 등의 정보가 포함됩니다. Microsoft 계정으로 로그인하는 사용자에 대한 기록은 수집되지 않습니다. | 디렉터리 > 사용자 > <i>사용자</i> > 활동 탭 |

#### 사용자 활동 보고서에 포함된 로그인 이벤트

특정 유형의 로그인 이벤트만 사용자 활동 보고서에 표시됩니다.

| 이벤트 유형 | 포함 여부 |
| ----------------------								| ---------		|
| [액세스 패널](http://myapps.microsoft.com/)에 로그인 | 예 |
| [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인 | 예 |
| [Microsoft Azure 포털](http://portal.azure.com/)에 로그인 | 예 |
| [Office 365 포털](http://portal.office.com/)에 로그인 | 예 |
| Outlook 같은 네이티브 응용 프로그램에 로그인(아래 예외 참조) | 예 |
| Salesforce 같은 액세스 패널을 통해 페더레이션/프로 비전된 응용 프로그램에 로그인 | 예 |
| Twitter 같은 액세스 패널을 통해 암호 기반 응용 프로그램에 로그인 | 예 |
| 디렉터리에 추가된 사용자 지정 비즈니스 응용 프로그램에 로그인 | 아니요(포함 예정) |
| 디렉토리에 추가된 Azure AD 응용 프로그램 프록시 앱에 로그인 | 아니요(포함 예정) |

> 참고: 이 보고서의 노이즈를 줄이기 위해 [Microsoft Online Services 로그인 도우미](http://community.office365.com/en-us/w/sso/534.aspx)를 통한 로그인은 표시되지 않습니다.










## 보안 위반이 의심될 때 고려할 사항

사용자 계정이 손상되었다고 의심되거나 클라우드에서 디렉터리 데이터의 보안 위반을 초래할 수 있는 의심스러운 사용자 활동이 있는 경우 다음 작업 중 하나 이상을 고려하는 것이 좋습니다.

- 사용자에게 연락하여 활동 확인
- 사용자 암호 재설정
- 추가 보안을 위해 [다단계 인증](http://go.microsoft.com/fwlink/?linkid=335774) 사용

## 보고서 보기 또는 다운로드

1. Azure 관리 포털에서 **Active Directory**를 클릭하고, 조직의 디렉터리의 이름을 클릭하고, **보고서**를 클릭합니다.
2. 보고서 페이지에서 보거나 다운로드하려는 보고서를 클릭합니다.

    > [AZURE.NOTE]처음으로 Azure Active Directory의 보고 기능을 사용하는 경우 옵트인하라는 메시지가 표시됩니다. 동의하는 경우 계속하려면 확인 표시 아이콘을 클릭합니다.

3. 간격 옆의 드롭다운 메뉴를 클릭한 후 이 보고서를 생성할 때 사용해야 하는 다음 시간 범위 중 하나를 선택합니다.
    - 최근 24시간
    - 최근 7일
    - 최근 30일
4. 확인 표시 아이콘을 클릭하여 보고서를 실행합니다.
	- 최대 1000개의 이벤트가 Azure 관리 포털에 표시됩니다.
5. 해당하는 경우 **다운로드**를 클릭하여 오프라인으로 보거나 보관하기 위해 CSV(쉼표로 구분된 값) 형식의 압축된 파일로 보고서를 다운로드합니다.
	- 최대 75,000개의 이벤트가 다운로드한 파일에 포함됩니다.
	- 더 많은 데이터는 [Azure AD Reporting API](active-directory-reporting-api-getting-started.md)를 확인하세요.

## 이벤트 무시

비정상 보고서를 본다면 관련 보고서에 표시된 다양한 이벤트를 무시할 수 있다는 것을 알 수 있습니다. 이벤트를 무시하려면 보고서에서 해당 이벤트를 강조 표시한 후 **무시**를 클릭합니다. **무시** 단추를 클릭하면 강조 표시된 이벤트가 영구적으로 보고서에서 제거되며 허가받은 전역 관리자만 사용할 수 있습니다.

## 자동 전자 메일 알림

Azure AD의 보고 알림에 대한 자세한 내용은 [Azure Active Directory 보고 알림](active-directory-reporting-notifications.md)을 확인하세요.

## 다음 단계

- [Azure Active Directory Premium 시작](active-directory-get-started-premium.md)
- [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](active-directory-add-company-branding.md)

<!---HONumber=Oct15_HO3-->