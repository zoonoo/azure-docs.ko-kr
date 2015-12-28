<properties 
	pageTitle="Azure 자동화용 Runbook 및 모듈 갤러리 | Microsoft Azure"
	description="Microsoft 및 커뮤니티의 Runbook과 모듈을 Azure 자동화 환경에 설치하여 사용할 수 있습니다. 이 글에서는 이러한 리소스에 액세스하는 방법과 자신의 Runbook을 갤러리에 올리는 방법을 설명합니다."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/23/2015"
	ms.author="bwren" />


# Azure 자동화용 Runbook 및 모듈 갤러리

Azure 자동화에서 사용자 고유의 Runbook 및 모듈을 만드는 대신 Microsoft 및 커뮤니티에서 이미 구성한 다양한 솔루션을 액세스할 수 있습니다. 이러한 솔루션은 수정 없이 그대로 사용하거나, 이를 기초로 특정 요구 사항에 맞게 편집하여 사용할 수 있습니다.

Runbook은 [Runbook 갤러리](#runbooks-in-runbook-gallery)에서, 모듈은 [PowerShell 갤러리](#modules-in-powerShell-gallery)에서 가져올 수 있습니다. 또한 자신이 개발한 솔루션을 공유하여 커뮤니티에 기여할 수 있습니다.

## Runbook 갤러리의 Runbook

[Runbook 갤러리](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation)는 Azure 자동화로 가져올 수 있는 Microsoft 및 커뮤니티에서 제작한 다양한 Runbook을 제공합니다. [TechNet 스크립트 센터](http://gallery.technet.microsoft.com/)에서 호스팅되는 갤러리로부터 Runbook을 다운로드하거나, Azure 포털 및 Azure Preview 포털의 갤러리에서 Runbook을 직접 가져올 수 있습니다.

Azure 포털 또는 Azure Preview 포털을 사용하는 Runbook 갤러리에서만 직접 가져올 수 있습니다. Windows PowerShell을 사용하여 이 함수를 수행할 수 없습니다.

>[AZURE.NOTE]프러덕션 환경에서 설치 및 실행할 때는 Runbook 갤러리에서 가져올 Runbook 내용의 유효성을 검사해야 하며 세심한 주의가 필요합니다.|

### Azure 포털을 사용하여 Runbook 갤러리에서 Runbook을 가져오려면

1. Azure 관리 포털에서 **새로 만들기**, **앱 서비스**, **자동화**, **Runbook**, **갤러리에서**를 클릭합니다.
2. 관련 Runbook을 볼 범주를 선택하고 Runbook을 선택하여 세부 내용을 확인합니다. 원하는 Runbook을 선택했으면 오른쪽 화살표 단추를 클릭합니다.<br> ![Runbook 갤러리](media/automation-runbook-gallery/runbook-gallery.png)
3. Runbook의 내용을 검토하고 설명의 모든 요구 사항을 확인합니다. 마치면 오른쪽 화살표 단추를 클릭합니다.
4. Runbook의 세부 정보를 입력하고 확인 표시 단추를 클릭합니다. Runbook 이름은 이미 입력되어 있습니다.
5. 이 Runbook이 자동화 계정의**Runbook** 탭에 표시됩니다.

### Azure Preview 포털을 사용하여 Runbook 갤러리에서 Runbook을 가져오려면

1. Azure Preview 포털에서 자동화 계정을 엽니다. 
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **갤러리 찾아보기** 단추를 클릭합니다. <br> ![갤러리 찾아보기 단추](media/automation-runbook-gallery/browse-gallery-button.png)
4. 원하는 갤러리 항목을 찾아 선택하여 세부 정보를 확인합니다. <br> ![갤러리 찾아보기](media/automation-runbook-gallery/browse-gallery.png)
4. [TechNet 스크립트 센터](http://gallery.technet.microsoft.com/)의 항목을 확인하려면 **소스 프로젝트 보기**를 클릭합니다.
5. 항목을 가져오려면 해당 항목을 클릭하여 세부 정보를 확인한 다음 **가져오기** 단추를 클릭합니다.<br> ![가져오기 단추](media/automation-runbook-gallery/gallery-item-detail.png)
6. 선택적으로 Runbook의 이름을 변경한 다음 **확인**을 클릭하여 해당 Runbook을 가져옵니다.
5. 이 Runbook이 자동화 계정의**Runbook** 탭에 표시됩니다.


### Runbook 갤러리에 Runbook 추가

Microsoft에서는 다른 고객에게 유용하다고 생각하는 Runbook을 Runbook 갤러리에 추가하도록 장려하고 있습니다. 다음 세부 정보를 고려하여 [스크립트 센터에 업로드](http://gallery.technet.microsoft.com/site/upload)를 선택하면 Runbook을 추가할 수 있습니다.

- **범주**에 *Windows Azure*를, 지정 **하위 범주**에 *자동화*를 지정해야 Runbook이 마법사에 표시됩니다.  

- 단일.ps1 또는 .graphrunbook 파일을 업로드해야 합니다. Runbook에 필요한 모듈, 자식 Runbook 또는 자산이 있는 경우 Runbook의 제출 설명과 내용 섹션에 해당 항목을 나열해야 합니다. 여러 Runbook이 필요한 솔루션인 경우 각 Runbook을 개별적으로 업로드하고 설명마다 관련 Runbook의 이름을 나열합니다. 동일한 범주에 표시되도록 동일한 태그를 사용합니다. 솔루션이 작동하기 위해 다른 Runbook이 필요하다는 설명을 사용자가 읽고 알 수 있어야 합니다.

- **코드 섹션 삽입** 아이콘을 사용하여 PowerShell 또는 PowerShell 워크플로 코드 조각을 설명에 삽입합니다.

- 업로드 요약이 Runbook 갤러리 결과에 표시되므로 사용자가 해당 Runbook의 기능을 파악하는 데 도움이 되는 상세한 정보를 제공해야 합니다.

- 업로드에 1~3개의 다음 태그를 할당해야 합니다. Runbook이 마법사에서 태그와 일치하는 범주에 나열됩니다. 이 목록에 없는 태그는 마법사에서 무시됩니다. 일치하는 태그를 지정하지 않는 경우 Runbook이 기타 범주에 나열됩니다.

 - 백업
 - 용량 관리
 - 변경 제어
 - 규정 준수
 - 개발/테스트 환경
 - 재해 복구
 - 모니터링
 - 패치
 - 프로비전
 - 재구성
 - VM 수명 주기 관리


- 자동화는 갤러리를 한 시간마다 업데이트하므로 제출한 내용이 즉시 확인되지는 않습니다. 한 시간 후에도 갤러리에서 Runbook이 보이지 않으면 [Runbook 갤러리에 Runbook 추가](#AddRunbook) 섹션에서 요구 사항을 확인합니다.

## PowerShell 갤러리의 모듈

PowerShell 모듈에는 Runbook에 사용할 수 있는 cmdlet이 있으며, Azure 자동화에서 설치할 수 있는 기존 모듈을 [PowerShell 갤러리](http://www.powershellgallery.com)에서 사용할 수 있습니다. Azure Preview 포털에서 이 갤러리를 실행하여 Azure 자동화에 직접 설치하거나, 수동으로 다운로드하여 설치할 수 있습니다. Azure 포털에서 모듈을 직접 설치할 수는 없지만 다운로드하여 다른 모듈처럼 설치할 수 있습니다.

### Azure Preview 포털을 사용하여 PowerShell 갤러리에서 모듈을 가져오려면

1. Azure Preview 포털에서 자동화 계정을 엽니다. 
2. **자산** 타일을 클릭하여 자산 목록을 엽니다.
3. **모듈** 타일을 클릭하여 모듈 목록을 엽니다.
3. **PowerShell 갤러리** 단추를 클릭하여 다른 브라우저 창에 PowerShell 갤러리를 실행합니다. <br> ![PowerShell 갤러리](media/automation-runbook-gallery/powershell-gallery-button.png)
4. **모듈** 메뉴를 클릭하여 사용 가능한 모듈 목록에 액세스합니다.<br> ![PowerShell 갤러리 단추](media/automation-runbook-gallery/powershell-gallery.png)
4. 관심이 있는 모듈을 찾아 선택하여 세부 내용을 확인합니다.
5. Azure 자동화에 직접 모듈을 설치하려면 **Azure 자동화에 배포** 단추를 클릭합니다.<br> ![PowerShell 갤러리 단추](media/automation-runbook-gallery/powershell-gallery-detail.png)
6. Azure Preview 포털의 **사용자 지정 배포** 창으로 돌아갑니다. **새 또는 기존 자동화 계정** 및 **자동화 계정 이름**에서 모듈 설치 여부를 지정합니다. 기존 계정을 사용할 경우 **자동화 계정 위치**가 무시됩니다. 
7. **리소스 그룹**을 선택하고 기존 리소스 그룹을 지정하거나 모듈에 대해 새 그룹을 만듭니다.
6. **약관**을 선택하고 **구입**을 클릭해야 합니다. 이 단추의 이름과는 달리 모듈 설치에 실제 청구되는 비용은 없습니다.
7. **만들기**를 클릭하여 모듈을 가져옵니다. 각 활동을 추출해야 하므로 몇 분이 걸릴 수 있습니다.  
8. 모듈을 가져오는 도중과 완료 시점에 알림이 표시됩니다. 


## Runbook 또는 모듈 요청 중

[사용자 음성](http://feedback.azure.com/forums/246290-azure-automation)에 요청을 보낼 수 있습니다. Runbook을 작성하는 데 도움이 필요하거나 PowerShell에 대한 질문이 있으면 [포럼](http://social.msdn.microsoft.com/Forums/windowsazure/ko-KR/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)에 게시하세요.

## 관련된 문서

- [Azure 자동화에서 Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)
- [PowerShell 워크플로 학습](automation-powershell-workflow.md)

<!---HONumber=AcomDC_1217_2015-->