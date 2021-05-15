---
title: 템플릿을 사용하여 Azure AD DS에 Windows VM 가입 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 신규 또는 기존 Windows Server VM을 Azure Active Directory Domain Services 관리되는 도메인에 가입하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619439"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Windows Server 가상 머신을 Azure Active Directory Domain Services 관리되는 도메인에 가입

Azure VM(가상 머신)의 배포 및 구성을 자동화하기 위해 Resource Manager 템플릿을 사용할 수 있습니다. 해당 템플릿을 사용하면 매번 일관된 배포를 만들 수 있습니다. 확장을 템플릿에 포함하여 배포의 일부로 VM을 자동으로 구성할 수도 있습니다. 하나의 유용한 확장은 VM을 도메인에 가입시키며 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인과 함께 사용할 수 있습니다.

이 문서에서는 Resource Manager 템플릿을 사용하여 Windows Server VM을 만들고 Azure AD DS 관리되는 도메인에 가입하는 방법을 보여줍니다. 또한 Azure AD DS 도메인에 기존 Windows Server VM을 가입하는 방법에 대해서도 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* 관리되는 도메인의 일부인 사용자 계정

## <a name="azure-resource-manager-template-overview"></a>Azure Resource Manager 템플릿 개요

Resource Manager 템플릿을 사용하면 코드에서 Azure 인프라를 정의할 수 있습니다. 필요한 리소스, 네트워크 연결 또는 VM 구성은 모두 템플릿에서 정의할 수 있습니다. 해당 템플릿은 매번 일관되고 재현 가능한 배포를 만들며 변경 시 버전을 지정할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿 개요][template-overview]를 참조하세요.

각 리소스는 JSON(JavaScript Object Notation)을 사용하여 템플릿에 정의됩니다. 다음의 JSON 예제에서는 *Microsoft.Compute/virtualMachines/extensions* 리소스 유형을 사용하여 Active Directory 도메인 가입 확장을 설치합니다. 매개 변수는 배포 시 지정하는 용도로 사용됩니다. 확장이 배포되면 VM이 지정된 관리되는 도메인에 가입됩니다.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

이 VM 확장은 동일한 템플릿에서 VM을 만들지 않더라도 배포할 수 있습니다. 이 문서의 예제에서는 다음 두 가지 방법을 모두 보여줍니다:

* [Windows Server VM을 만들고 관리되는 도메인에 가입하기](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [관리되는 도메인에 기존 Windows Server VM 조인](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Windows Server VM을 만들고 관리되는 도메인에 가입하기

Windows Server VM이 필요한 경우 Resource Manager 템플릿을 사용하여 만들고 구성할 수 있습니다. VM이 배포되면 확장이 설치되어 관리되는 도메인에 VM을 가입합니다. 관리되는 도메인에 가입하려는 VM이 이미 있는 경우 [관리되는 도메인에 기존 Windows Server VM 가입](#join-an-existing-windows-server-vm-to-a-managed-domain)으로 건너뜁니다.

Windows Server VM을 만든 다음 관리되는 도메인에 가입하려면 다음 단계를 완료합니다:

1. [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-vm-domain-join/) 찾아보기. **Azure에 배포** 옵션을 선택합니다.
1. **사용자 지정 배포** 페이지에서 다음의 정보를 입력하여 관리되는 도메인에 Windows Server VM을 만들고 가입합니다:

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | Resource group            | VM의 리소스 그룹을 선택합니다. |
    | Location                  | VM의 위치를 선택합니다. |
    | 기존 VNET 이름        | VM을 연결할 기존 가상 네트워크의 이름(예: *myVnet*)입니다. |
    | 기존 서브넷 이름      | *워크로드* 와 같은 기존 가상 네트워크 서브넷의 이름입니다. |
    | DNS 레이블 접두사          | VM에 사용할 DNS 이름(예: *myvm*)을 입력합니다. |
    | VM 크기                   | *Standard_DS2_v2* 와 같은 VM 크기를 지정합니다. |
    | 가입할 도메인            | 관리되는 도메인의 DNS 이름(예: *aaddscontoso.com*)입니다. |
    | 도메인 사용자 이름           | VM을 관리되는 도메인에 가입하는데 사용되는 관리되는 도메인의 사용자 계정(예: `contosoadmin@aaddscontoso.com`)입니다. 이 계정은 관리되는 도메인의 일부여야 합니다. |
    | 도메인 암호           | 이전 설정에서 지정된 사용자 계정의 암호입니다. |
    | 선택적 OU 경로          | VM을 추가할 사용자 지정 OU입니다. 이 매개 변수의 값을 지정하지 않으면 VM이 기본 *AAD DC 컴퓨터* OU에 추가됩니다. |
    | VM 관리자 사용자 이름         | VM에서 만들 로컬 관리자 계정을 지정합니다. |
    | VM 관리자 암호         | VM에 대한 로컬 관리자 암호를 지정합니다. 강력한 로컬 관리자 암호를 만들어 무차별 암호 대입 공격으로부터 보호합니다. |

1. 사용 약관을 검토한 다음, **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다. 준비가 되면 **구매** 를 선택하여 관리되는 도메인에 VM을 만들고 가입합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일은 관리되는 도메인의 일부인 사용자 계정에 대한 암호를 요청합니다. 이 파일에 값을 수동으로 입력하지 말고 파일 공유 또는 기타 공유 위치에서 액세스 할 수 있도록 유지합니다.

배포가 성공적으로 완료되는 데 몇 분 정도 걸립니다. 완료되면 Windows VM이 만들어지고 관리되는 도메인에 가입됩니다. 도메인 계정을 사용하여 VM을 관리하고 로그인할 수 있습니다.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>관리되는 도메인에 기존 Windows Server VM 조인

관리되는 도메인에 가입하고자 하는 기존 VM 또는 VM 그룹이 있을 경우, Resource Manager 템플릿을 사용하여 VM 확장만 배포할 수 있습니다.

기존 Windows Server VM을 관리되는 도메인에 가입하려면 다음 단계를 완료합니다:

1. [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/) 찾아보기. **Azure에 배포** 옵션을 선택합니다.
1. **사용자 지정 배포** 페이지에서 다음 정보를 입력하여 관리되는 도메인에 VM을 가입합니다:

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | Resource group            | 기존 VM이 있는 리소스 그룹을 선택합니다. |
    | Location                  | 기존 VM의 위치를 선택합니다. |
    | VM 목록                   | *myVM1,myVM2* 와 같이 관리되는 도메인에 가입하려는 기존 VM의 쉼표로 구분된 목록을 입력합니다. |
    | 도메인 가입 사용자 이름     | VM을 관리되는 도메인에 가입하는데 사용되는 관리되는 도메인의 사용자 계정(예: `contosoadmin@aaddscontoso.com`)입니다. 이 계정은 관리되는 도메인의 일부여야 합니다. |
    | 도메인 가입 사용자 암호 | 이전 설정에서 지정된 사용자 계정의 암호입니다. |
    | 선택적 OU 경로          | VM을 추가할 사용자 지정 OU입니다. 이 매개 변수의 값을 지정하지 않으면 VM이 기본 *AAD DC 컴퓨터* OU에 추가됩니다. |

1. 사용 약관을 검토한 다음, **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다. 준비가 되면 **구매** 를 선택하여 관리되는 도메인에 VM을 가입합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일은 관리되는 도메인의 일부인 사용자 계정에 대한 암호를 요청합니다. 이 파일에 값을 수동으로 입력하지 말고 파일 공유 또는 기타 공유 위치에서 액세스 할 수 있도록 유지합니다.

배포가 성공적으로 완료되려면 몇 분 정도 걸립니다. 완료되면 지정된 Windows VM이 관리되는 도메인에 가입되며 도메인 계정을 사용하여 관리하거나 로그인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 템플릿을 사용하여 리소스를 구성하고 배포하는 Azure Portal을 사용했습니다. [Azure PowerShell][deploy-powershell] 또는 [Azure CLI][deploy-cli]를 사용하여 Resource Manager 템플릿으로 리소스를 배포할 수도 있습니다.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
