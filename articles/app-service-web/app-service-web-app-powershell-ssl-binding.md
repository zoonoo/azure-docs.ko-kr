---
title: "PowerShell을 사용하여 SSL 인증서 바인딩"
description: "PowerShell을 사용하여 웹앱에 SSL 인증서를 바인딩하는 방법을 알아봅니다."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 8ce32508-e982-48d3-b878-0e526afda537
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: ahmedelnably
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e0302c4e4e63383a2b19bd6f02f3be8d83f5ff98


---
# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>PowerShell을 사용하여 Azure 앱 서비스 SSL 인증서 바인딩
새 cmdlet이 추가된 Microsoft Azure PowerShell 버전 1.1.0이 출시되어 사용자는 기존 또는 새 SSL 인증서를 기존 웹앱에 바인딩할 수 있습니다.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Azure Resource Manager 기반 Azure PowerShell cmdlet을 사용하여 [Azure 웹앱용 Azure Resource Manager 기반 PowerShell 명령](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>새 SSL 인증서 업로드 및 바인딩
시나리오: 사용자가 SSL 인증서를 웹앱 중 하나에 바인딩하려고 합니다.

웹앱, 웹앱 이름, 인증서, 사용자 컴퓨터의 .pfx 파일 경로, 인증서 암호 및 사용자 지정 호스트 이름을 포함한 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 해당 SSL 바인딩을 만들 수 있습니다.

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

SSL 바인딩을 웹앱에 추가하기 전에 이미 구성된 호스트 이름(사용자 지정 도메인)이 있어야 합니다. 호스트 이름이 구성되지 않은 경우 New-AzureRmWebAppSSLBinding을 실행하는 동안 'hostname'가 존재하지 않는다는 오류가 발생합니다. 포털에서 또는 Azure PowerShell을 사용하여 직접 호스트 이름을 추가할 수 있습니다. 다음 PowerShell 조각은 New-AzureRmWebAppSSLBinding을 실행하기 전에 호스트 이름을 구성할 수 있습니다.   

    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   

Set-AzureRmWebApp cmdlet이 웹앱에 대한 호스트 이름을 덮어쓴다는 것을 이해하는 것이 중요합니다. 따라서 위의 PowerShell 조각은 웹앱에 대한 호스트 이름의 기존 목록에 추가됩니다.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>기존 SSL 인증서 업로드 및 바인딩
시나리오: 사용자가 이전에 업로드한 SSL 인증서를 웹앱 중 하나에 바인딩하려고 합니다.

다음 명령을 사용하여 특정 리소스 그룹에 이미 업로드된 인증서 목록을 가져올 수 있습니다.

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

인증서는 특정 위치 및 리소스 그룹에 로컬이며 구성된 웹앱이 필요한 인증서가 아닌 리소스 그룹 및 다른 위치에 있으면 사용자는 인증서를 다시 업로드해야 합니다. 

웹앱, 웹앱 이름, 인증서 지문 및 사용자 지정 호스트 이름이 포함된 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 해당 SSL 바인딩을 만들 수 있습니다.

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>기존 SSL 바인딩 삭제
시나리오: 사용자가 기존 SSL 바인딩을 삭제하려고 합니다.

웹앱, 웹앱 이름, 사용자 지정 호스트 이름이 포함된 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 해당 SSL 바인딩을 제거할 수 있습니다.

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

제거한 SSL 바인딩이 해당 위치에서 해당 인증서를 사용한 마지막 바인딩인 경우 기본적으로 인증서는 삭제되고 사용자가 인증서를 유지하려면 DeleteCertificate 옵션을 사용하여 인증서를 유지할 수 있습니다.

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>참조
* [Azure 웹앱용 Azure Resource Manager 기반 PowerShell 명령](app-service-web-app-azure-resource-manager-powershell.md)
* [앱 서비스 환경 소개](app-service-app-service-environment-intro.md)
* [Azure 리소스 관리자로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)




<!--HONumber=Nov16_HO3-->


