<properties
   pageTitle="PowerShell을 사용한 서비스 버스 및 이벤트 허브 리소스 관리"
   description="PowerShell을 사용한 서비스 버스 및 이벤트 허브 리소스 만들기 및 관리"
   services="service-bus"
   documentationCenter=".NET"
   authors="sethmanheim"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="sethm"/>

# PowerShell을 사용한 서비스 버스 및 이벤트 허브 리소스 관리

Microsoft Azure PowerShell은 Azure 서비스의 배포와 관리를 제어하고 자동화하는 데 사용할 수 있는 스크립팅 환경입니다. 이 문서에서는 로컬 Azure PowerShell 콘솔에서 PowerShell을 사용하여 네임스페이스, 큐, 이벤트 허브와 같은 서비스 버스 엔터티를 프로비전하고 관리하는 방법을 설명합니다.

## 필수 조건

이 작업을 수행하려면 다음이 필요합니다.

- Azure 구독. Azure는 구독 기반 플랫폼입니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][], [구성원 제공 항목][] 또는 [무료 평가판][]을 참조하세요.

- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성][]을 참조하세요.

- PowerShell 스크립트, NuGet 패키지 및 .NET Framework 전반에 대한 지식

## 서비스 버스에 대한 .NET 어셈블리의 참조 포함

서비스 버스를 관리하는 데 사용할 수 있는 PowerShell cmdlet은 제한되어 있습니다. 기존 cmdlet을 통해 노출되지 않은 엔터티를 프로비전하려면 [서비스 버스 NuGet 패키지]를 참조하여 PowerShell 내에서 서비스 버스용 .NET 클라이언트를 사용할 수 있습니다.

먼저 스크립트가 NuGet 패키지와 함께 설치되는 **Microsoft.ServiceBus.dll** 어셈블리를 찾을 수 있는지 확인합니다. 유연한 작업을 위해 스크립트는 다음 단계를 수행합니다.

1. 호출된 경로를 파악합니다.
2. 이름이 `packages`인 폴더를 찾을 때까지 경로를 탐색합니다. 이 폴더는 NuGet 패키지를 설치할 때 생성됩니다.
3. **Microsoft.ServiceBus.dll** 어셈블리에 대한 `packages` 폴더를 재귀적으로 검색합니다.
4. 해당 형식을 나중에 사용할 수 있도록 어셈블리를 참조합니다.

PowerShell 스크립트에서 이러한 단계는 다음과 같이 구현됩니다.

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## 서비스 버스 네임스페이스 프로비전

서비스 버스 네임스페이스를 사용할 때는 .NET SDK 대신 [Get-azuresbnamespace]와 [New-AzureSBNamespace]라는 두 cmdlet을 사용할 수 있습니다.

이 예제에서는 스크립트에 `$Namespace`과(와) `$Location`(이)라는 몇 가지 로컬 변수를 만듭니다.

- `$Namespace`은(는) 사용하려는 서비스 버스 네임스페이스의 이름입니다.
- `$Location`은(는) 네임스페이스를 프로비전할 데이터 센터를 식별합니다.
- `$CurrentNamespace`에는 검색하거나 만드는 참조 네임스페이스가 저장됩니다.

실제 스크립트에서 `$Namespace` 및 `$Location`은(는) 매개 변수로 전달할 수 있습니다.

스크립트의 이 부분은 다음을 수행합니다.

1. 제공된 이름의 서비스 버스 네임스페이스 검색을 시도합니다.
2. 네임스페이스가 있으면 발견된 항목을 보고합니다.
3. 네임스페이스가 없으면 만든 다음 새로 만든 네임스페이스를 검색합니다.

	``` powershell

	$Namespace = "MyServiceBusNS"
	$Location = "West US"

	# Query to see if the namespace currently exists
	$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

	# Check if the namespace already exists or needs to be created
	if ($CurrentNamespace)
	{
	    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
	}
	else
	{
	    Write-Host "The [$Namespace] namespace does not exist."
	    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```
다른 서비스 버스 엔터티를 프로비전하려면 SDK에서 `NamespaceManager` 개체의 인스턴스를 만듭니다. [Get-AzureSBAuthorizationRule] cmdlet을 사용하면 연결 문자열을 제공하는 데 사용되는 권한 부여 규칙을 검색할 수 있습니다. 이 예제에서는 `NamespaceManager` 인스턴스에 대한 참조를 `$NamespaceManager` 변수에 저장합니다. `$NamespaceManager`은(는) 스크립트에서 나중에 다른 엔터티를 프로비전하는 데 사용합니다.

	``` powershell
	$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
	# Create the NamespaceManager object to create the Event Hub
	Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
	$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
	Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
	```

## 다른 서비스 버스 엔터티 프로비전

큐, 토픽, 이벤트 허브와 같은 다른 엔터티를 프로비전하려는 경우 [서비스 버스용 .NET API]를 사용할 수 있습니다. 다른 엔터티를 비롯한 더 자세한 예제도 이 문서 끝부분에 참조용으로 나와 있습니다.

### 이벤트 허브 만들기

이 스크립트 부분에서는 로컬 변수를 4개 더 만듭니다. 이러한 변수는 `EventHubDescription` 개체를 인스턴스화하는 데 사용됩니다. 이 스크립트는 다음을 수행합니다.

1. `NamespaceManager` 개체를 사용하여 `$Path`(으)로 식별되는 이벤트 허브가 있는지 확인합니다.
2. 해당 이벤트 허브가 없으면 `EventHubDescription`을(를) 만든 다음 `NamespaceManager` 클래스의 `CreateEventHubIfNotExists` 메서드에 전달합니다.
3. 이벤트 허브를 사용할 수 있음을 확인한 후 `ConsumerGroupDescription` 및 `NamespaceManager`을(를) 사용하여 소비자 그룹을 만듭니다.

	``` powershell

	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"

	# Check if the Event Hub already exists
	if ($NamespaceManager.EventHubExists($Path))
	{
	    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
	}
	else
	{
	    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
	    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
	    $EventHubDescription.PartitionCount = $PartitionCount
	    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
	    $EventHubDescription.UserMetadata = $UserMetadata
	    $EventHubDescription.Path = $Path
	    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
	    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
	}

	# Create the consumer group if it doesn't exist
	Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
	$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
	$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
	$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
	Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
	```

### 큐 만들기

큐 또는 토픽을 만들려면 이전 섹션에서와 같이 네임스페이스를 확인합니다.

	if ($NamespaceManager.QueueExists($Path))
	{
	    Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
	    $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
	    $QueueDescription.EnableExpress = $EnableExpress
	    $QueueDescription.EnablePartitioning = $EnablePartitioning
	    $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
	    $QueueDescription.ForwardTo = $ForwardTo
	    $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    if ($LockDuration -gt 0)
	    {
	        $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
	    }
	    $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
	    $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    $QueueDescription.RequiresSession = $RequiresSession
	    if ($EnablePartitioning)
	    {
	        $QueueDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $QueueDescription.SupportOrdering = $SupportOrdering
	    }
	    $QueueDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateQueue($QueueDescription);
	}

### 토픽 만들기

	if ($NamespaceManager.TopicExists($Path))
	{
	    Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
	    $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $TopicDescription.EnableExpress = $EnableExpress
	    $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
	    $TopicDescription.EnablePartitioning = $EnablePartitioning
	    $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    if ($EnablePartitioning)
	    {
	        $TopicDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $TopicDescription.SupportOrdering = $SupportOrdering
	    }
	    $TopicDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateTopic($TopicDescription);
	}

## 다음 단계

이 문서에서는 PowerShell을 사용하여 서비스 버스 엔터티를 프로비전하는 방법에 대해 간략하게 설명했습니다. 서비스 버스 메시징 엔터티 관리에 사용할 수 있는 PowerShell cmdlet의 개수에는 제한이 있지만 Microsoft.ServiceBus.dll 어셈블리를 참조하면 .NET 클라이언트 라이브러리를 사용하여 거의 모든 작업을 수행할 수 있으며 PowerShell 스크립트에서도 수행할 수 있습니다.

다음 블로그 게시물에서 보다 자세한 설명을 확인할 수 있습니다.

- [PowerShell 스크립트를 사용하여 서비스 버스 큐, 토픽 및 구독을 만드는 방법](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [PowerShell 스크립트를 사용하여 서비스 버스 네임스페이스 및 이벤트 허브를 만드는 방법](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

즉시 사용 가능한 스크립트도 다운로드 가능합니다.

- [Service Bus PowerShell 스크립트](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[구매 옵션]: http://azure.microsoft.com/pricing/purchase-options/
[구성원 제공 항목]: http://azure.microsoft.com/pricing/member-offers/
[무료 평가판]: http://azure.microsoft.com/pricing/free-trial/
[서비스 버스 NuGet 패키지]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[서비스 버스용 .NET API]: https://msdn.microsoft.com/ko-KR/library/azure/mt419900.aspx
[Azure PowerShell 설치 및 구성]: ../install-configure-powershell.md

<!---HONumber=AcomDC_1217_2015-->