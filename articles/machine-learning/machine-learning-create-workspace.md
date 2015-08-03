<properties 
	pageTitle="기계 학습 작업 영역 만들기 | Microsoft Azure" 
	description="Azure 기계 학습 스튜디오의 작업 영역을 만드는 방법" 
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


# Azure 기계 학습 작업 영역 만들기 

Azure 기계 학습 스튜디오를 사용하려면 기계 학습 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 작업 영역을 만들려면

1. Microsoft Azure 계정에 로그인합니다.
2. Microsoft Azure 서비스 패널에서 **기계 학습**을 클릭합니다.

    ![기계 학습 서비스][1]

3. 창 아래쪽에서 **+새로 만들기**를 클릭합니다.
4. **데이터 서비스**, **기계 학습**, **빠른 생성**을 차례로 클릭합니다.

	![새 작업 영역의 빠른 생성][3]

5. 작업 영역에 대한 **작업 영역 이름**을 입력하고 **작업 영역 소유자**를 지정합니다. 작업 영역 소유자는 유효한 Microsoft 계정(예: name@outlook.com))이어야 합니다.

    > [AZURE.NOTE]나중에 작업 영역에 다른 사용자를 초대하여 작업 중인 실험을 공유할 수 있습니다. 기계 학습 스튜디오의 **SETTINGS** 페이지에서 이 작업을 할 수 있습니다. 각 사용자에 대한 Microsoft 계정 또는 조직 계정만 있으면 됩니다.

6. Azure **위치**를 지정하고 기존 Azure **저장소 계정**을 입력하거나 **새 저장소 계정 만들기**를 클릭하여 새 저장소 계정을 만듭니다.
7. **기계 학습 작업 영역 만들기**를 클릭합니다.

기계 학습 작업 영역이 만들어진 후에는 **기계 학습** 페이지에 나열됩니다.

작업 영역을 관리하는 방법에 대한 자세한 내용은 [Azure 기계 학습 작업 영역 관리]를 참조하세요. 작업 영역을 만드는 데 문제가 발생한 경우 [문제 해결 가이드: 기계 학습 작업 영역 만들기 및 연결]을 참조하세요.

[Azure 기계 학습 작업 영역 관리]: machine-learning-manage-workspace.md
[문제 해결 가이드: 기계 학습 작업 영역 만들기 및 연결]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->

<!---HONumber=July15_HO4-->