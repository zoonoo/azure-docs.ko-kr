---
title: "Azure AD 사용자 계정 만들기 | Microsoft Docs"
description: "이 문서에서는 Azure Automation에서 Runbook에 대해 Azure AD 사용자 계정 자격 증명을 만들어서 Azure 및 클래식 Azure에서 인증하는 방법을 설명합니다."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: "azure active directory 사용자, azure 서비스 관리, azure ad 사용자 계정"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 4eaa3e36ededddeb5268ec4f49b9daee2f824cee
ms.contentlocale: ko-kr
ms.lasthandoff: 08/15/2017

---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Azure 클래식 배포 및 Resource Manager를 사용하여 Runbook 인증
이 문서에서는 Azure 클래식 배포 모델 또는 Azure Resource Manager 리소스에 대해 실행되는 Azure Automation Runbook에 대한 Azure AD 사용자 계정을 구성하기 위해 수행해야 하는 단계를 설명합니다.  Azure Resource Manager 기반 Runbook에 대해 이 인증 ID가 계속 지원되겠지만 Azure 실행 계정을 사용할 것을 권장합니다.       

## <a name="create-a-new-azure-active-directory-user"></a>새 Azure Active Directory 사용자 만들기
1. 관리하려는 Azure 구독의 서비스 관리자 권한으로 Azure 클래식 포털에 로그인합니다.
2. **Active Directory**를 선택한 다음 조직 디렉터리의 이름을 선택합니다.
3. **사용자** 탭을 선택한 다음 명령 영역에서 **사용자 추가**를 선택합니다.
4. **이 사용자에 대해 알리기** 페이지의 **사용자 형식**에서 **조직 내 새 사용자**를 선택합니다.
5. 사용자 이름을 입력합니다.  
6. Active Directory 페이지에서 Azure 구독과 연결된 디렉터리 이름을 선택합니다.
7. **사용자 프로필** 페이지에서 이름과 성, 별명, **역할** 목록의 사용자를 입력합니다.  **Multi-Factor Authentication 활성화**를 하지 마세요.
8. 사용자의 전체 이름 및 임시 암호를 기록해 둡니다.
9. **설정 > 관리자 > 추가**를 선택합니다.
10. 만든 사용자의 전체 사용자 이름을 입력합니다.
11. 사용자가 관리하도록 할 구독을 선택합니다.
12. Azure에서 로그한 다음 방금 만든 계정으로 다시 로그인합니다. 사용자의 암호를 변경하라는 메시지가 표시됩니다.

## <a name="create-an-automation-account-in-azure-classic-portal"></a>Azure 클래식 포털에서 Automation 계정 만들기
이 섹션에서는 다음 단계를 수행하여 Azure Portal에서 Azure 클래식 배포에서 리소스를 관리하는 데 Runbook을 사용하기 위한 Azure Automation 계정을 만듭니다.  

> [!NOTE]
> Azure 클래식 포털을 사용하여 만든 Automation 계정은 Azure 클래식 포털과 Azure Portal 및 각 cmdlet 집합에서 관리할 수 있습니다. 계정이 만들어지면 계정 내에서 리소스를 만들고 관리하는 방법에는 차이가 없습니다. Azure 클래식 포털을 계속 사용하려는 경우, Azure Portal 대신 사용하여 모든 Automation 계정을 만들어야 합니다.
> 
> 

1. 관리하려는 Azure 구독의 서비스 관리자 권한으로 Azure 클래식 포털에 로그인합니다.
2. **Automation**을 선택합니다.
3. **Automation** 페이지에서 **Automation 계정 만들기**를 선택합니다.
4. **Automation 계정 만들기** 상자에서 새 Automation 계정의 이름을 입력하고 드롭다운 목록에서 **하위 지역**을 선택합니다.  
5. **확인**을 클릭하여 설정을 수락하고 계정을 만듭니다.
6. 계정이 만들어지면 **Automation** 페이지에 표시됩니다.
7. 해당 계정을 클릭하면 대시보드 페이지로 이동됩니다.  
8. Automation 대시보드 페이지에서 **자산**을 선택합니다.
9. **자산** 페이지 하단에서 **설정 추가**를 선택합니다.
10. **설정 추가** 페이지에서 **자격 증명 추가**를 선택합니다.
11. **자격 증명 정의** 페이지의 **자격 증명 유형** 드롭다운 목록에서 **Windows PowerShell 자격 증명**을 선택하고 자격 증명의 이름을 입력합니다.
12. 이후에 나오는 **자격 증명 정의** 페이지에서는 **사용자 이름** 필드에 이전에 만든 AD 사용자 계정의 사용자 이름을 입력하고 **암호** 및 **암호 확인** 필드에 암호를 입력합니다. **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Azure Portal에서 Automation 계정 만들기
이 섹션에서는 다음 단계를 수행하여 Azure Portal에서 Azure Resource Manager 모드에서 리소스를 관리하는 데 Runbook을 사용하기 위한 Azure Automation 계정을 만듭니다.  

1. 관리하려는 Azure 구독의 서비스 관리자 권한으로 Azure Portal에 로그인합니다.
2. **Automation 계정**을 선택합니다.
3. Automation 계정 블레이드에서 **추가**를 클릭합니다.<br><br>![Automation 계정 추가](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. **Automation 계정 추가** 블레이드의 **이름** 상자에 새 Automation 계정에 대한 이름을 입력합니다.
5. 구독이 둘 이상인 경우 새 계정의 구독을 지정하고 새로운 또는 기존 **리소스 그룹** 및 Azure 데이터 센터 **위치**를 지정합니다.
6. **Azure 실행 계정 만들기** 옵션에 **예** 값을 선택하고 **만들기** 단추를 클릭합니다.  
   
    > [!NOTE]
    > **아니요** 옵션을 선택하여 실행 계정을 만들지 않는 경우 **Automation 계정 추가** 블레이드에 경고 메시지가 나타납니다.  계정이 생성되고 구독의 **참가자** 역할에 할당되는 동안에는 구독 디렉터리 서비스 내에서 해당하는 인증 ID가 없으므로 구독의 리소스에 대한 액세스 권한도 없습니다.  이렇게 하면 이 계정을 참조하는 Runbook이 Azure Resource Manager 리소스에 대해 인증되고 작업을 수행하지 못하도록 방지합니다.
    > 
    >

    <br>![Automation 계정 경고 추가](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Azure에서 Automation 계정을 만드는 동안 메뉴의 **알림**에서 진행률을 추적할 수 있습니다.

자격 증명이 생성되면 자격 증명 자산을 만들어서 Automation 계정을 앞에서 만든 AD 사용자 계정과 연결해야 합니다.  Automation 계정을 만들기만 했을 뿐, 아직 인증 ID와 연결하지는 않았다는 점을 잊지 마세요.  [Azure Automation의 자격 증명 자산](automation-credentials.md#creating-a-new-credential-asset) 문서에 설명된 단계를 수행하고 **사용자 이름** 값을 **도메인\사용자** 형식으로 입력합니다.

## <a name="use-the-credential-in-a-runbook"></a>Runbook에서 자격 증명 사용
[Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) 작업을 사용하여 Runbook에서 자격 증명을 검색한 후 [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx)와 함께 사용하여 Azure 구독에 연결할 수 있습니다. 자격 증명이 여러 Azure 구독의 관리자인 경우 [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx)을 사용하여 올바른 관리자를 지정해야 합니다. 이 작업은 아래의 Windows PowerShell 샘플(일반적으로 대부분의 Azure Automation Runbook 위쪽에 표시됨)에 나와 있습니다.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Runbook의 모든 [검사점](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) 뒤에 이러한 줄을 반복해야 합니다. Runbook이 일시 중단된 후 다른 작업자에서 다시 시작되는 경우에는 인증을 다시 수행해야 합니다.

## <a name="next-steps"></a>다음 단계
* 다음 [Azure Automation Runbook 형식](automation-runbook-types.md) 문서에서 다양한 Runbook 유형 및 고유한 Runbook을 만드는 단계를 검토합니다.


