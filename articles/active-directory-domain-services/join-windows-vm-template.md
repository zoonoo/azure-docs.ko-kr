---
title: 템플릿을 사용하여 Windows VM을 Azure AD DS에 조인 | 마이크로 소프트 문서
description: Azure 리소스 관리자 템플릿을 사용하여 새 Windows 서버 또는 기존 Windows 서버 VM을 Azure Active Directory 도메인 관리 도메인에 조인하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 19e4033b462cda9511ee45420bd4c1a76cc36b92
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518949"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 Windows Server 가상 시스템에 Azure Active Directory 도메인 서비스 관리 도메인에 가입

Azure 가상 시스템(VM)의 배포 및 구성을 자동화하려면 리소스 관리자 템플릿을 사용할 수 있습니다. 이러한 템플릿을 사용하면 매번 일관된 배포를 만들 수 있습니다. 확장은 배포의 일부로 VM을 자동으로 구성하는 템플릿에 포함될 수도 있습니다. 유용한 확장 중 하나는 VM을 도메인에 조인하며 Azure Active Directory 도메인 서비스(Azure AD DS) 관리 도메인과 함께 사용할 수 있습니다.

이 문서에서는 리소스 관리자 템플릿을 사용하여 Windows 서버 VM을 Azure AD DS 관리 도메인에 만들고 조인하는 방법을 보여 주며, 이 문서에서는 또한 기존 Windows 서버 VM을 Azure AD DS 도메인에 조인하는 방법에 대해서도 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD DS 관리 도메인의 일부인 사용자 계정입니다.

## <a name="azure-resource-manager-template-overview"></a>Azure 리소스 관리자 템플릿 개요

리소스 관리자 템플릿을 사용하면 코드에서 Azure 인프라를 정의할 수 있습니다. 필요한 리소스, 네트워크 연결 또는 VM 구성은 모두 템플릿에서 정의할 수 있습니다. 이러한 템플릿은 매번 일관되고 재현 가능한 배포를 만들며 변경할 때 버전이 작성될 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 템플릿 개요를 참조하세요.][template-overview]

각 리소스는 JSON(자바스크립트 개체 표기이)을 사용하는 템플릿에 정의됩니다. 다음 JSON 예제에서는 *Microsoft.Compute/가상 머신/확장* 리소스 형식을 사용하여 Active Directory 도메인 조인 확장을 설치합니다. 매개 변수는 배포 시 지정하는 데 사용됩니다. 확장이 배포되면 VM이 지정된 Azure AD DS 관리 도메인에 조인됩니다.

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

이 VM 확장은 동일한 템플릿에서 VM을 만들지 않는 경우에도 배포할 수 있습니다. 이 문서의 예제에서는 다음 두 가지 방법을 모두 보여 주며 다음과 같은 방법을 모두 보여 주며 있습니다.

* [Windows 서버 VM을 만들고 관리되는 도메인에 가입](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [관리되는 도메인에 기존 Windows Server VM 조인](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Windows 서버 VM을 만들고 관리되는 도메인에 가입

Windows 서버 VM이 필요한 경우 리소스 관리자 템플릿을 사용하여 Windows 서버 VM을 만들고 구성할 수 있습니다. VM이 배포되면 확장이 설치되어 VM을 Azure AD DS 관리 도메인에 조인합니다. Azure AD DS 관리 도메인에 가입하려는 VM이 이미 있는 경우 [기존 Windows 서버 VM을 관리되는 도메인에 조인하려면](#join-an-existing-windows-server-vm-to-a-managed-domain)건너뜁니다.

Windows 서버 VM을 만든 다음 Azure AD DS 관리 도메인에 조인하려면 다음 단계를 완료합니다.

1. [빠른 시작 템플릿으로](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)이동합니다. **Azure에 배포할**옵션을 선택합니다.
1. 사용자 **지정 배포** 페이지에서 다음 정보를 입력하여 Windows 서버 VM을 Azure AD DS 관리 도메인에 만들고 조인합니다.

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | Resource group            | VM에 대한 리소스 그룹을 선택합니다. |
    | 위치                  | VM의 위치를 선택합니다. |
    | 기존 VNET 이름        | vM을 연결하는 기존 가상 네트워크의 이름(예: *myVnet)* |
    | 기존 서브넷 이름      | *워크로드와*같은 기존 가상 네트워크 서브넷의 이름입니다. |
    | DNS 레이블 접두사          | *myvm과*같은 VM에 사용할 DNS 이름을 입력합니다. |
    | VM 크기                   | *Standard_DS2_v2*와 같은 VM 크기를 지정합니다. |
    | 가입할 도메인            | Azure AD DS 관리 도메인 DNS 이름(예: *aaddscontoso.com.* |
    | 도메인 사용자 이름           | 와 같이 `contosoadmin@aaddscontoso.com`관리되는 도메인에 VM을 조인하는 데 사용해야 하는 Azure AD DS 관리 도메인의 사용자 계정입니다. 이 계정은 Azure AD DS 관리 도메인의 일부여야 합니다. |
    | 도메인 암호           | 이전 설정에서 지정한 사용자 계정의 암호입니다. |
    | OU 경로 옵션          | VM을 추가할 사용자 지정 OU입니다. 이 매개 변수에 대한 값을 지정하지 않으면 VM이 기본 *AAD DC 컴퓨터* OU에 추가됩니다. |
    | VM 관리자 사용자 이름         | VM에서 만들 로컬 관리자 계정을 지정합니다. |
    | VM 관리자 암호         | VM에 대한 로컬 관리자 암호를 지정합니다. 강력한 로컬 관리자 암호를 만들어 암호 무차별 암호 대입 공격으로부터 보호합니다. |

1. 이용 약관을 검토한 다음 **위에 명시된 이용 약관에 동의하는지 확인란을**선택합니다. 준비가 되면 **구매를** 선택하여 VM을 만들고 Azure AD DS 관리 도메인에 조인합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일은 Azure AD DS 관리 도메인의 일부인 사용자 계정에 대한 암호를 요청합니다. 이 파일에 값을 수동으로 입력하고 파일 공유 또는 기타 공유 위치에 액세스할 수 있도록 두지 마십시오.

배포가 성공적으로 완료되는 데 몇 분 정도 걸립니다. 작업이 완료되면 Windows VM이 만들어지고 Azure AD DS 관리 도메인에 조인됩니다. VM은 도메인 계정을 사용하여 관리하거나 로그인할 수 있습니다.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>관리되는 도메인에 기존 Windows Server VM 조인

Azure AD DS 관리 도메인에 가입하려는 기존 VM 또는 VM 그룹이 있는 경우 리소스 관리자 템플릿을 사용하여 VM 확장을 배포할 수 있습니다.

기존 Windows 서버 VM을 Azure AD DS 관리 도메인에 가입하려면 다음 단계를 완료하십시오.

1. [빠른 시작 템플릿으로](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)이동합니다. **Azure에 배포할**옵션을 선택합니다.
1. 사용자 **지정 배포** 페이지에서 VM을 Azure AD DS 관리 도메인에 조인하려면 다음 정보를 입력합니다.

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | Resource group            | 기존 VM을 사용 하 여 리소스 그룹을 선택 합니다. |
    | 위치                  | 기존 VM의 위치를 선택합니다. |
    | VM 목록                   | *myVM1, myVM2*와 같은 Azure AD DS 관리 도메인에 가입하려면 기존 VM의 쉼표로 구분된 목록을 입력합니다. |
    | 도메인 가입 사용자 이름     | 와 같이 `contosoadmin@aaddscontoso.com`관리되는 도메인에 VM을 조인하는 데 사용해야 하는 Azure AD DS 관리 도메인의 사용자 계정입니다. 이 계정은 Azure AD DS 관리 도메인의 일부여야 합니다. |
    | 도메인 가입 사용자 암호 | 이전 설정에서 지정한 사용자 계정의 암호입니다. |
    | OU 경로 옵션          | VM을 추가할 사용자 지정 OU입니다. 이 매개 변수에 대한 값을 지정하지 않으면 VM이 기본 *AAD DC 컴퓨터* OU에 추가됩니다. |

1. 이용 약관을 검토한 다음 **위에 명시된 이용 약관에 동의하는지 확인란을**선택합니다. 준비가 되면 **구매를** 선택하여 VM을 Azure AD DS 관리 도메인에 조인합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일은 Azure AD DS 관리 도메인의 일부인 사용자 계정에 대한 암호를 요청합니다. 이 파일에 값을 수동으로 입력하고 파일 공유 또는 기타 공유 위치에 액세스할 수 있도록 두지 마십시오.

배포가 성공적으로 완료되는 데 몇 분 정도 걸립니다. 완료되면 지정된 Windows VM이 Azure AD DS 관리 도메인에 조인되며 도메인 계정을 사용하여 관리하거나 로그인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 포털을 사용하여 템플릿을 사용하여 리소스를 구성하고 배포했습니다. [Azure PowerShell][deploy-powershell] 또는 [Azure CLI를][deploy-cli]사용하여 리소스 관리자 템플릿을 사용하여 리소스를 배포할 수도 있습니다.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
