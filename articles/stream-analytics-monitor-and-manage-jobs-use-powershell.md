<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="PowerShell | Azure를 사용하여 스트림 분석 작업 모니터링 및 관리" description="Azure PowerShell cmdlet을 사용 하 여 모니터링 하 고 스트림 분석 작업을 관리 하는 방법에 알아봅니다." metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Azure PowerShell을 사용하여 스트림 분석 작업 모니터링 및 관리

Azure PowerShell을 사용하여 Azure 스트림 분석 리소스를 관리하는 방법을 알아봅니다.

##이 문서의 내용

- [필수 조건](#prerequisites)
- [스트림 분석 PowerShell cmdlets](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [참고 항목](#seealso)

## <a name="prerequisites"></a>스트림 분석 PowerShell cmdlet을 실행하기 위한 필수 조건

1.	Azure PowerShell 설치 및 구성

	[Azure PowerShell을 설치 및 구성하는 방법][powershell-install]의 지침에 따라 Azure PowerShell을 설치합니다.

2.	Azure 모드 구성

	Azure PowerShell을 설치한 후 [Switch-AzureMode][msdn-switch-azuremode] cmdlet을 실행하여 스트림 분석 cmdlet에 액세스하는 데 적합한 Azure 모드를 설정합니다.

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Azure PowerShell을 통해 생성된 스트림 분석 작업에 모니터링을 사용할 수 없는 일시적인 제한이 있습니다.  이 문제를 해결하려면 Azure 포털에서 작업의 모니터 페이지로 이동한 다음 "사용" 단추를 클릭합니다.  

##<a name="cmdlets"></a>스트림 분석 PowerShell cmdlets
다음 표에는 Azure PowerShell을 통해 Azure 스트림 분석 작업을 모니터링 및 관리하는 데 사용할 수 있는 cmdlet이 나와 있습니다.

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Azure 구독 또는 지정한 리소스 그룹에 정의된 모든 스트림 분석 작업을 나열하거나 리소스 그룹 내의 특정 작업에 대한 작업 정보를 가져옵니다.

**예제 1**

	Get-AzureStreamAnalyticsJob

이 명령은 Azure 구독의 모든 스트림 분석 작업에 대한 정보를 반환합니다.

**예제 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
이 명령은 리소스 그룹 StreamAnalytics-Default-West-US의 모든 스트림 분석 작업에 대한 정보를 반환합니다.

**예제 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
이 명령은 리소스 그룹 StreamAnalytics-Default-West-US의 스트림 분석 작업 StreamingJob에 대한 정보를 반환합니다.

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
지정한 스트림 분석 작업에 정의된 모든 입력을 나열하거나 특정 입력에 대한 정보를 가져옵니다.

**예제 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

이 명령은 StreamingJob 작업에 정의된 모든 입력에 대한 정보를 반환합니다.

**예제 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
이 명령은 StreamingJob 작업에 정의된 EntryStream이라는 입력에 대한 정보를 반환합니다.

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
지정한 스트림 분석 작업에 정의된 모든 출력을 나열하거나 특정 출력에 대한 정보를 가져옵니다.

**예제 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
이 명령은 StreamingJob 작업에 정의된 출력에 대한 정보를 반환합니다.

**예제 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
이 명령은 StreamingJob 작업에 정의된 Output이라는 출력에 대한 정보를 반환합니다.

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
지정한 지역의 스트리밍 단위 할당량에 대한 정보를 가져옵니다.

**예제 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
이 명령은 미국 서부 지역의 스트리밍 단위 할당량 및 사용에 대한 정보를 반환합니다.

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
스트림 분석 작업에 정의된 특정 변환에 대한 정보를 가져옵니다.

**예제 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
이 명령은 StreamingJob 작업에 정의된 StreamingJob이라는 변환에 대한 정보를 반환합니다.

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
스트림 분석 작업 내에서 새 입력을 만들거나 지정한 기존 입력을 업데이트합니다.
  
.JSON 파일 또는 명령줄에서 입력의 이름을 지정할 수 있습니다.  둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 입력을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 입력을 바꿀지 여부를 묻습니다.

-Force 매개 변수와 기존 입력 이름을 지정하면 확인 없이 입력이 대체됩니다.

**예제 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
이 명령은 Input.json 파일에 새 입력을 만듭니다.  입력 정의 파일에 지정된 이름의 기존 입력이 이미 정의되어 있으면 cmdlet에서 해당 입력을 바꿀지 여부를 묻습니다.

**예제 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
이 명령은 EntryStream이라는 작업에 새 입력을 만듭니다.  이 이름의 기존 입력이 이미 정의되어 있으면 cmdlet에서 해당 입력을 바꿀지 여부를 묻습니다.

**예제 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
이 명령은 EntryStream이라는 기존 입력 소스의 정의를 파일에 있는 정의로 바꿉니다.

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Microsoft Azure에 새 스트림 분석 작업을 만들거나 지정한 기존 작업의 정의를 업데이트합니다.

.JSON 파일 또는 명령줄에서 작업의 이름을 지정할 수 있습니다.  둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 작업 이름을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 작업을 바꿀지 여부를 묻습니다.

-Force 매개 변수와 기존 작업 이름을 지정하면 확인 없이 작업 정의가 대체됩니다.

**예제 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
이 명령은 JobDefinition.json의 정의에서 새 작업을 만듭니다.  작업 정의 파일에 지정된 이름의 기존 작업이 이미 정의되어 있으면 cmdlet에서 해당 작업을 바꿀지 여부를 묻습니다.

**예제 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
이 명령은 StreamingJob에 대한 작업 정의를 바꿉니다.

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
스트림 분석 작업 내에서 새 출력을 만들거나 기존 출력을 업데이트합니다.  

.JSON 파일 또는 명령줄에서 출력의 이름을 지정할 수 있습니다.  둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 출력을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 출력을 바꿀지 여부를 묻습니다.

-Force 매개 변수와 기존 출력 이름을 지정하면 확인 없이 출력이 대체됩니다.

**예제 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
이 명령은 StreamingJob 작업에 "output"이라는 새 출력을 만듭니다.  이 이름의 기존 출력이 이미 정의되어 있으면 cmdlet에서 해당 출력을 바꿀지 여부를 묻습니다.

**예제 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
이 명령은 StreamingJob 작업에서 "output"의 정의를 바꿉니다.

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
스트림 분석 작업 내에서 새 변환을 만들거나 기존 변환을 업데이트합니다.
  
.JSON 파일 또는 명령줄에서 변환의 이름을 지정할 수 있습니다.  둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 변환을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 변환을 바꿀지 여부를 묻습니다.

-Force 매개 변수와 기존 변환 이름을 지정하면 확인 없이 변환이 대체됩니다.

**예제 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
이 명령은 StreamingJob 작업에 StreamingJobTransform이라는 새 변환을 만듭니다.  이 이름의 기존 변환이 이미 정의되어 있으면 cmdlet에서 해당 변환을 바꿀지 여부를 묻습니다.

**예제 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 이 명령은 StreamingJob 작업에서 StreamingJobTransform의 정의를 바꿉니다.

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Microsoft Azure의 스트림 분석 작업에서 특정 입력을 비동기적으로 삭제합니다.  
-Force 매개 변수를 지정하면 확인 없이 입력이 삭제됩니다.

**예제 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
이 명령은 StreamingJob 작업에서 EventStream 입력을 제거합니다.  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Microsoft Azure에서 특정 스트림 분석 작업을 비동기적으로 삭제합니다.  
-Force 매개 변수를 지정하면 확인 없이 작업이 삭제됩니다.

**예제 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
이 명령은 StreamingJob 작업을 제거합니다.  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Microsoft Azure의 스트림 분석 작업에서 특정 출력을 비동기적으로 삭제합니다.  
-Force 매개 변수를 지정하면 확인 없이 출력이 삭제됩니다.

**예제 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
이 명령은 StreamingJob 작업에서 Output 출력을 제거합니다.  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
Microsoft Azure에 스트림 분석 작업을 비동기적으로 배포하고 시작합니다.

**예제 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
이 명령은 StreamingJob 작업을 시작합니다.  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Microsoft Azure에서 실행 중인 스트림 분석 작업을 비동기적으로 중지하고 사용하던 리소스를 할당 취소합니다. 작업 정의와 메타데이터는 작업을 편집하고 다시 시작할 수 있도록 Azure 포털과 관리 API를 통해 구독 내에서 계속 사용할 수 있습니다. 중지됨 상태의 작업에 대해서는 요금이 부과되지 않습니다.

**예제 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
이 명령은 StreamingJob 작업을 중지합니다.  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
스트림 분석이 지정한 입력에 연결할 수 있는지 테스트합니다.

**예제 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
StreamingJob에서 EntryStream 입력의 연결 상태를 테스트합니다.  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
스트림 분석이 지정한 출력에 연결할 수 있는지 테스트합니다.

**예제 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
StreamingJob에서 Output 출력의 연결 상태를 테스트합니다.  


##<a name="seealso"></a>참고 항목

- [Azure 스트림 분석 소개][stream.analytics.introduction]
- [스트림 분석 시작][stream.analytics.get.started]
- [스트림 분석 미리 보기 릴리스의 제한][stream.analytics.limitations]
- [스트림 분석을 위한 개발자 가이드][stream.analytics.developer.guide]
- [스트림 분석을 위한 쿼리 언어 참조][stream.analytics.query.language.reference]
- [스트림 분석을 위한 REST API 참조][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/ko-kr/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/ko-kr/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
