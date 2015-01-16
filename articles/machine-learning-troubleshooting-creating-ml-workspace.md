<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="문제 해결 가이드: Azure 기계 학습 작업 영역 만들기 및 연결 | Azure" description="Azure 기계 학습 작업 영역 만들기 및 연결과 관련된 일반적인 문제에 대한 해결 방법 " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/25/2014" ms.author="garye" />


#문제 해결 가이드: Azure 기계 학습 작업 영역 만들기 및 연결

이 가이드에서는 Azure ML 작업 영역을 설정할 때 자주 발생하는 몇 가지 문제에 대한 해결 방법을 제공합니다.

##작업 영역 소유자

새 Azure 기계 학습 작업 영역을 만들 때 작업 영역 소유자 필드에 입력한 ID는 유효한 Microsoft 계정(이전 Windows Live ID)이어야 합니다(예: john-contoso@live.com 또는 john-contoso@hotmail.com). 회사 메일 계정과 같이 Microsoft 이외의 계정일 수 없습니다. 무료 Microsoft 계정을 만들려면 [www.live.com](http://www.live.com)으로 이동합니다. 

##허용되는 지역

Azure 기계 학습은 현재 미국 중남부 지역에서 공개 미리 보기로 제공됩니다. 구독에 미국 중남부가 포함되어 있지 않으면 "허용되는 지역에 구독이 없습니다. 허용되는 지역: 미국 중남부."라는 오류가 표시될 수도 있습니다. 

이 문제를 해결하려면 Azure 포털에서 "Microsoft 지원에 문의"(아래 표시됨)를 선택하고 **지원 유형**으로 **청구**를 선택하여 이 지역을 구독에 추가하도록 요청합니다. Azure 기계 학습에서 점차 여러 지역을 추가할 것입니다.

![Contact Microsoft support][screen1]

##저장소 계정
 
Azure 기계 학습 서비스에서 데이터를 저장하려면 저장소 계정이 필요합니다. 미국 중남부의 기존 저장소 계정을 사용하거나, 새 저장소 계정을 만들 수 있는 할당량이 있는 경우 새 ML 작업 영역을 만들 때 새 저장소 계정을 만들 수 있습니다. 새 저장소 계정을 만들 수 있는지 확인하려면 Azure 포털에서 **설정**, **사용**으로 차례로 이동합니다.

![Create workspace][screen2]

새 기계 학습 작업 영역이 만들어진 후 작업 영역의 소유자로 지정한 Microsoft 계정을 사용하여 기계 학습 스튜디오에 로그인할 수 있습니다. 아래 스크린샷과 유사한 "작업 영역을 찾을 수 없음" 오류가 발생할 경우 다음 단계에 따라 문제를 해결하세요.

![Workspace not found][screen3]

1. 브라우저 쿠키를 플러시합니다.

	Internet Explorer를 사용하는 경우 오른쪽 위에 있는 **도구** 단추를 클릭하고 **인터넷 옵션**을 선택합니다.  

	![Internet options][screen4]

	**일반** 탭에서 **삭제...**를 클릭합니다.

	![General tab][screen5]

	**검색 기록 삭제** 대화 상자에서 **쿠키 및 웹 사이트 데이터**가 선택되었는지 확인하고 **삭제**를 클릭합니다.

	![Delete cookies][screen6]

2. 쿠키가 플러시된 후 브라우저를 다시 시작하고 [https://studio.azureml.net](https://studio.azureml.net)으로 이동합니다. 사용자 이름과 암호를 묻는 메시지가 표시되면 작업 영역의 소유자로 지정한 계정과 동일한 Microsoft 계정을 입력합니다.

Microsoft는 Azure 기계 학습 환경을 가능한 한 매끄럽게 만들기 위해 노력하고 있습니다. 의견이나 문제가 있을 경우 더 나은 서비스를 제공할 수 있도록 아래 또는 [Azure 기계 학습 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning)에 게시해 주세요. 

[screen1]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
