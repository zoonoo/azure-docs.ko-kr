<properties
   pageTitle="Azure 자동화 구성"
   description="초기 사용을 위해 Azure 자동화를 구성하는 데 필요한 단계를 설명합니다."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/02/2015"
   ms.author="bwren" />

# Azure 자동화 구성

이 문서에서는 Azure 자동화를 처음 사용하기 위해 수행해야 하는 작업에 대해 설명합니다.

## 자동화 계정

Azure 자동화를 처음 시작하려면 자동화 계정을 하나 이상 만들어야 합니다. 자동화 계정을 사용하면 다른 자동화 계정에 포함된 자동화 리소스에서 사용자의 자동화 리소스(Runbook, 자산, 구성)를 격리할 수 있습니다. 자동화 계정을 사용하여 자동화 리소스를 별도의 논리적 환경으로 구분할 수 있습니다. 예를 들어 개발 및 프로덕션에 서로 다른 계정을 사용할 수 있습니다.

각 자동화 계정의 자동화 리소스는 단일 Azure 지역과 연결되지만 자동화 계정에서 모든 지역의 Azure 서비스를 관리할 수 있습니다. 여러 지역에서 자동화 계정을 만드는 주된 이유는 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있을 수 있기 때문입니다.

>[AZURE.NOTE]Azure Preview 포털에서 작성된 자동화 계정 및 자동화 계정이 포함하는 리소스는 Azure 포털에서 액세스할 수 없습니다. 이러한 계정 또는 해당 리소스를 Windows PowerShell을 사용하여 관리하려는 경우 Azure 리소스 관리자 모듈을 사용해야 합니다.
>
>Azure 포털을 사용하여 만든 자동화 계정을 전체 포털 및 각 cmdlet 집합에서 관리할 수 있습니다. 계정이 만들어지면 계정 내에서 리소스를 만들고 관리하는 방법에는 차이가 없습니다. Azure 포털을 계속 사용하려는 경우, Azure Preview 포털 대신 사용하여 모든 자동화 계정을 만들어야 합니다.


Azure 계정에 지불 연체와 같은 문제가 있는 경우 자동화 계정이 일시 중단될 수 있습니다. 이 경우 계정에 액세스할 수 없으며, 실행 중인 모든 작업이 일시 중단되고, 모든 일정이 사용할 수 없도록 설정됩니다. 계정을 볼 수 있지만 해당 계정의 리소스는 볼 수 없습니다. 문제를 해결하고 자동화 계정을 사용할 수 있게 되면 일정을 사용하도록 설정하고 일시 중단된 모든 Runbook을 다시 시작해야 합니다.


## Azure 리소스에 대한 인증 구성

[Azure cmdlet](http://msdn.microsoft.com/library/azure/jj554330.aspx)을 사용하여 Azure 리소스에 액세스하는 경우 Azure 구독에 대한 인증을 제공해야 합니다. Azure 자동화에서 이 작업은 구독에 대한 관리자로 구성한 Azure Active Directory의 조직 계정을 통해 수행됩니다. 그런 다음 이 사용자 계정에 대한 [자격 증명](http://msdn.microsoft.com/library/dn940015.aspx)을 만들고 Runbook에서 [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx)와 함께 사용할 수 있습니다.

>[AZURE.NOTE]Microsoft 계정(이전의 LiveID)은 Azure 자동화에서 사용할 수 없습니다.

## Azure 구독을 관리할 새 Azure Active Directory 사용자 만들기

1. 관리하려는 Azure 구독의 서비스 관리자 권한으로 Azure 포털에 로그인합니다.
2. **Active Directory**를 선택합니다.
3. Azure 구독과 연결된 디렉터리 이름을 선택합니다. 필요한 경우 **설정 > 구독 > 디렉터리 편집**에서 이 연결을 변경할 수 있습니다.
4. [새 Active Directory 사용자를 만듭니다](http://msdn.microsoft.com/library/azure/hh967632.aspx). **조직 내 새 사용자**를 **사용자 유형**으로 선택하고 **다단계 인증 사용**을 선택하지 않습니다.
5. 사용자의 전체 이름 및 임시 암호를 기록해 둡니다.
7. **설정 > 관리자 > 추가**를 선택합니다.
8. 만든 사용자의 전체 사용자 이름을 입력합니다.
9. 사용자가 관리하도록 할 구독을 선택합니다.
10. Azure에서 로그한 다음 방금 만든 계정으로 다시 로그인합니다. 사용자의 암호를 변경하라는 메시지가 표시됩니다.
11. 만든 사용자 계정에 대한 새 [Azure 자동화 자격 증명 자산](http://msdn.microsoft.com/library/dn940015.aspx)을 만듭니다. **자격 증명 형식**은 **Windows PowerShell 자격 증명**이어야 합니다.


## Runbook에서 자격 증명 사용

[Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) 작업을 사용하여 Runbook에서 자격 증명을 검색한 후 [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx)와 함께 사용하여 Azure 구독에 연결할 수 있습니다. 자격 증명이 여러 Azure 구독의 관리자인 경우 [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx)을 사용하여 올바른 관리자를 지정해야 합니다. 이 작업은 아래의 Windows PowerShell 샘플(일반적으로 대부분의 Azure 자동화 Runbook 위쪽에 표시됨)에 나와 있습니다.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

Runbook의 모든 [검사점](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) 뒤에 이러한 줄을 반복해야 합니다. Runbook이 일시 중단된 후 다른 작업자에서 다시 시작되는 경우에는 인증을 다시 수행해야 합니다.

## 관련된 문서
- [Azure 자동화: Azure Active Directory를 사용하여 Azure에 인증](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 

<!---HONumber=Nov15_HO3-->