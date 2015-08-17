<properties 
	pageTitle="Azure Multi-Factor Authentication 보고서" 
	description="Azure Multi-Factor Authentication 기능 - 보고서를 사용하는 방법을 설명합니다." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication에서 보고서

Azure Multi-Factor Authentication은 사용자 및 사용자의 조직에서 사용할 수 있는 다양한 보고서를 제공합니다. 이러한 보고서는 Multi-Factor Authentication 관리 포털을 통해 액세스할 수 있습니다. 다음은 사용 가능한 보고서의 목록입니다.

Azure 관리 포털을 통해 보고서에 액세스할 수 있습니다.

이름| 설명
:------------- | :------------- | 
사용 현황 | 사용 현황 보고서는 전반적인 사용 현황, 사용자 요약 및 사용자 세부 내용에 대한 정보를 표시합니다.
서버 상태|이 보고서는 계정에 연결된 Multi-Factor Authentication 서버의 상태를 표시합니다.
차단된 사용자 기록|이러한 보고서는 사용자 차단 또는 차단 해제 요청 기록을 보여 줍니다.
무시된 사용자 기록|사용자의 전화번호에 대한 Multi-Factor Authentication 무시 요청 기록을 보여 줍니다.
사기 행위 경고|지정한 날짜 범위 동안 제출된 사기 행위 경고 기록을 보여 줍니다.
Queued|처리 및 해당 상태에 대해 대기 중인 보고서가 나열되어 있습니다. 보고서가 완료되면 보고서를 다운로드하거나 볼 링크가 제공됩니다.

## 보고서를 보려면

1. [http://azure.microsoft.com](http://azure.microsoft.com)으로 로그온합니다.
2. 왼쪽에서 Active Directory를 선택합니다.
3. 위쪽에서 Multi-Factor Auth 공급자를 선택합니다. 그러면 Multi-Factor Auth 공급자의 목록이 표시됩니다.
4. 둘 이상의 Multi-Factor Auth 공급자를 사용하도록 설정한 경우 사기 행위 경고 보고서를 보려는 하나를 선택하고 페이지의 아래에서 관리를 클릭합니다. 하나만 있는 경우 관리를 클릭합니다. 그러면 Azure Multi-Factor Authentication 관리 포털이 열립니다.
5. Azure Multi-Factor Authentication 관리 포털의 왼쪽에서 보고서 보기를 찾을 수 있습니다. 여기에서 위에 설명된 보고서를 선택할 수 있습니다.


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**추가 리소스**

* [사용자](multi-factor-authentication-end-user.md)
* [MSDN에서 Azure Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=August15_HO6-->