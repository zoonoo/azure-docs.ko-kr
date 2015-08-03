<properties 
	pageTitle="문제 해결: 기계 학습 작업 영역 만들기 및 연결 | Microsoft Azure" 
	description="Azure 기계 학습 작업 영역 만들기 및 연결과 관련된 일반적인 문제에 대한 해결 방법" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="garye"/>


#문제 해결 가이드: 기계 학습 작업 영역 만들기 및 연결

이 가이드에서는 Azure 기계 학습 작업 영역을 설정할 때 자주 발생하는 몇 가지 문제에 대한 해결 방법을 제공합니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##작업 영역 소유자

새 기계 학습 작업 영역을 만들 때 작업 영역 소유자 필드에 입력하는 ID는 유효한 Microsoft 계정(이전의 Windows Live ID)이어야 합니다(예: john-contoso@live.com 또는 john-contoso@hotmail.com). 회사 전자 메일 계정과 같은 타사 계정을 사용할 수 없습니다. 무료 Microsoft 계정을 만들려면 [www.live.com](http://www.live.com)으로 이동합니다.

작업 영역을 만들기 위해 Azure 포털에 로그인하는 데 사용한 계정에는 해당 계정을 소유자로 지정하지 않는 한 작업 영역을 *열* 수 있는 권한이 자동으로 부여되지 않습니다. 기계 학습 스튜디오에서 작업 영역을 열려면 작업 영역의 소유자로 정의된 Microsoft 계정으로 로그인하거나 작업 공간 소유자의 참가 초대를 받아야 합니다. 그러나 Azure 포털에서 소유자를 변경하고 액세스를 구성하는 등 작업 영역을 *관리*할 수 있습니다.

작업 영역을 관리하는 방법에 대한 자세한 내용은 [Azure 기계 학습 작업 영역 관리]를 참조하세요.

[Azure 기계 학습 작업 영역 관리]: machine-learning-manage-workspace.md

##허용되는 지역

기계 학습은 현재 미국 중남부 지역에서 사용할 수 있습니다. 구독에 미국 중남부가 포함되어 있지 않으면 "허용되는 지역에 구독이 없습니다. 허용되는 지역: 미국 중남부"라는 오류 메시지가 표시될 수 있습니다.

이 문제를 해결하려면 다음 스크린샷에 표시된 대로 Azure 포털에서 **Microsoft 지원에 문의**를 선택하고 **지원 유형**으로 **청구**를 선택하여 이 지역을 구독에 추가하도록 요청합니다.

![Microsoft 지원에 문의][screen1]

##저장소 계정
 
기계 학습 서비스에서 데이터를 저장하려면 저장소 계정이 필요합니다. 미국 중남부 지역의 기존 저장소 계정을 사용하거나, 새 저장소 계정을 만들 수 있는 할당량이 있는 경우 새 ML 작업 영역을 만들 때 새 저장소 계정을 만들 수 있습니다. 새 저장소 계정을 만들 수 있는지 확인하려면 관리 포털에서 **설정**으로 이동하여 **사용**을 클릭합니다.

![작업 영역 만들기][screen2]

새 기계 학습 작업 영역이 만들어진 후 작업 영역의 소유자로 지정한 Microsoft 계정을 사용하여 기계 학습 스튜디오에 로그인할 수 있습니다. "작업 영역을 찾을 수 없음"(다음 스크린샷과 유사) 오류 메시지가 나타나는 경우 다음 단계를 사용하여 브라우저 쿠키를 삭제하세요.

![작업 영역을 찾을 수 없음][screen3]

**브라우저 쿠키를 삭제하려면**

	If you use Internet Explorer, click the **Tools** button in the upper-right corner and select **Internet options**.  

	![Internet options][screen4]

	Under the **General** tab, click **Delete…**

	![General tab][screen5]

	In the **Delete Browsing History** dialog box, make sure **Cookies and website data** is selected, and click **Delete**.

	![Delete cookies][screen6]

쿠키를 삭제한 후 브라우저를 다시 시작하고 [Microsoft Azure 기계 학습](https://studio.azureml.net) 페이지로 이동합니다. 사용자 이름과 암호를 묻는 메시지가 표시되면 작업 영역의 소유자로 지정한 계정과 동일한 Microsoft 계정을 입력합니다.

Microsoft는 기계 학습 환경을 가능한 한 원활하게 만들기 위해 노력하고 있습니다. 의견이나 문제가 있을 경우 더 나은 서비스를 제공할 수 있도록 [Azure 기계 학습 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)에 게시해 주세요.

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
 

<!---HONumber=July15_HO4-->