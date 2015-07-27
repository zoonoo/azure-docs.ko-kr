<properties
   pageTitle="Azure PowerShell 명령을 사용하여 빈 클라우드 서비스 컨테이너를 만드는 방법"
   description="이 문서에서는 PowerShell 스크립트를 사용하여 클라우드 서비스 컨테이너를 만들고 클라우드 서비스 관련 관리 작업을 수행하는 방법에 대해 설명합니다."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="bscholl" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="06/19/2015"
   ms.author="cawa"/>

# Azure PowerShell 명령을 사용하여 빈 클라우드 서비스 컨테이너를 만드는 방법
1. Microsoft Azure PowerShell cmdlet을 [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) 다운로드에서 설치합니다. Azure PowerShell cmdlet 설치 및 Azure 구독에 연결에 대한 추가 지침은 [Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

2. **New-AzureService**는 빈 클라우드 서비스 컨테이너를 만드는 cmdlet입니다.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Cmdlet을 호출하는 예제입니다. ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Azure 클라우드 서비스 만들기에 대한 자세한 내용은 다음을 실행합니다.```
Get-help New-AzureService
```

4. 다음 단계:

   - 클라우드 서비스 배포를 관리하려면 [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) 및 [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) 명령을 참조하세요. Azure에서 [클라우드 서비스](cloud-services-how-to-configure.md)를 구성하는 방법에 대해 알아봅니다.

    - 클라우드 서비스 프로젝트를 Azure에 게시하려면, **PublishCloudService.ps1** 코드 예제를 [Azure에서 클라우드 서비스에 대한 지속적인 전송](cloud-services-dotnet-continuous-delivery.md)에서 참조하세요.
 

<!---HONumber=July15_HO3-->