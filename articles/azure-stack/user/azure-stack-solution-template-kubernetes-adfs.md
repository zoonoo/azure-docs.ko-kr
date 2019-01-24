---
title: Active Directory Federated Services (AD FS)를 사용 하 여 Azure Stack에 Kubernetes 배포 | Microsoft Docs
description: Kubernetes Active Directory Federated Services (AD FS)를 사용 하 여 Azure Stack에 배포 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 3ae0a67a8aa2d403a72b0e20471c2044dbf557bf
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389075"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Active Directory Federated Services를 사용 하 여 Azure Stack에 Kubernetes 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!Note]  
> Azure Stack에서 Kubernetes 미리 보기입니다.

배포 및 Kubernetes에 대 한 리소스를 설정 하려면이 문서의 단계를 수행할 수 있습니다. Active Directory Federated Services (AD FS)는 사용자 id 관리 서비스 하는 경우 다음이 단계를 사용 합니다.

## <a name="prerequisites"></a>필수 조건 

시작 하려면 적절 한 권한이 있고 Azure Stack에 사용할 준비가 되어 있는지에 있는지를 확인 합니다.

1. SSH 공용 및 개인 키 쌍을 Azure Stack에서 Linux VM에 로그인을 생성 합니다. 클러스터를 만들 때 공개 키가 필요 합니다.

    키 생성에 대 한 지침을 참조 하세요 [SSH 키 생성](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)합니다.

1. Azure Stack 테 넌 트 포털에서 유효한 구독이 있는 새 응용 프로그램을 추가 하려면 사용할 수 있는 주소가 충분 한 공용 IP 있는지 확인 합니다.

    Azure Stack에 클러스터를 배포할 수 없습니다 **관리자** 구독 합니다. 사용 해야 합니다는 **사용자** 구독 합니다. 

1. Marketplace에 Kubernetes 클러스터 없는 경우 Azure Stack 관리자에 게 문의 합니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

Id 솔루션으로 AD FS를 사용 하는 경우 서비스 주체를 설정 하 여 Azure Stack 관리자와 협력 해야 합니다. 서비스 주체는 Azure Stack 리소스에 응용 프로그램 액세스를 제공합니다.

1. Azure Stack 관리자에 게 인증서와 서비스 주체에 대 한 정보를 제공합니다. 이 정보는 같습니다.

    ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
    ```

2. 구독에 새 서비스 주체에 참가자 역할을 할당 합니다. 자세한 내용은 [역할 할당](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal#assign-role-to-service-principal)합니다.

3. 배포에 대 한 인증서를 저장할 key vault를 만듭니다.

    - 다음 가지 정보가 필요합니다.

        | 값 | 설명 |
        | ---   | ---         |
        | Azure Resource Manager 끝점 | Microsoft Azure 리소스 관리자는 관리자가 배포, 관리 및 Azure 리소스를 모니터링할 수 있도록 관리 합니다. Azure Resource Manager 그룹으로 대신 개별적으로 단일 작업에서 이러한 작업을 처리할 수 있습니다.<br>Azure Stack 개발 키트 (ASDK)의 끝점 상태 `https://management.local.azurestack.external/`<br>통합된 시스템에서 끝점을 다음과 같습니다. `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | 구독 ID | 합니다 [구독 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) 제품을 액세스 하는 방법에 Azure Stack에서. |
        | 사용자 이름 | 사용자 이름입니다. |
        | 리소스 그룹 이름  | 기존 리소스 그룹을 선택 또는 새 리소스 그룹의 이름입니다. 리소스 이름은 영숫자 및 소문자 있어야 합니다. |
        | Keyvault 이름 | 자격 증명 모음의 이름입니다.<br> Regex 패턴: `^[a-zA-Z0-9-]{3,24}$` |
        | 리소스 그룹 위치 | 리소스 그룹의 위치입니다. Azure Stack 설치에 대해 선택 하는 지역입니다. |

    - PowerShell을 관리자 권한 프롬프트를 엽니다. 값으로 업데이트 하는 매개 변수를 사용 하 여 다음 스크립트를 실행 합니다.

    ```PowerShell  
        $armEndpoint="<Azure Resource Manager Endpoint>"
        $subscriptionId="<Your Subscription ID>"
        $username="<your user name >"
        $resource_group_name = "<the resource group name >"
        $key_vault_name = "<keyvault name>"
        $resource_group_location="<resource group location>"
        
        # Login Azure Stack Environment
        Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
        $mycreds = Get-Credential
        Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
        # Create new Resource group and key vault
        New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
        # Note, Do not omit -EnabledForTemplateDeployment flag
        New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location local -EnabledForTemplateDeployment
        
        # Obtain the security identifier(SID) of the active directory user
        $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
        $objectSID = $adUser.SID.Value
        # Set the key vault access policy
        Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
    ```

4. Key Vault에 인증서를 업로드 합니다.

    - 다음 가지 정보가 필요합니다.

        | 값 | 설명 |
        | ---   | ---         |
        | 인증서 경로 | 인증서에 FQDN 또는 파일 경로입니다. |
        | 인증서 암호 | 인증서 암호입니다. |
        | 비밀 이름 | 암호는 이전 단계에서 생성 합니다. |
        | Keyvault 이름 | 이전 단계에서 만든 key vault의 이름입니다. |
        | Azure Resource Manager 끝점 | Azure Stack 개발 키트 (ASDK)의 끝점 상태 `https://management.local.azurestack.external/`<br>통합된 시스템에서 끝점을 다음과 같습니다. `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | 구독 ID | 합니다 [구독 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) 제품을 액세스 하는 방법에 Azure Stack에서. |

    - PowerShell을 관리자 권한 프롬프트를 엽니다. 값으로 업데이트 하는 매개 변수를 사용 하 여 다음 스크립트를 실행 합니다.

    ```PowerShell  
        
    # upload the pfx to key vault
    $tempPFXFilePath = "<certificate path>"
    $password = "<certificate password>"
    $keyVaultSecretName = "<secret name>"
    $keyVaultName = "<keyvault name>"
    $armEndpoint="<Azure Resource Manager Endpoint>"
    $subscriptionId="<Your Subscription ID>"
    # Login Azure Stack Environment
    Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
    $mycreds = Get-Credential
    Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
    $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
    $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    $jsonObject = @"
    {
    "data": "$pfxAsBase64EncodedString",
    "dataType" :"pfx",
    "password": "$password"
    }
    "@
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    ```

## <a name="deploy-kubernetes"></a>Kubernetes 배포

1. 엽니다는 [Azure Stack 포털](https://portal.local.azurestack.external)합니다.

1. 선택 **+ 리소스 만들기** > **계산** > **Kubernetes 클러스터**합니다. **만들기**를 클릭합니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. 기본 사항

1. 선택 **기본 사항** Kubernetes 클러스터를 만듭니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 선택 하면 **구독** id입니다.

1. 새 리소스 그룹의 이름을 입력 하거나 기존 리소스 그룹을 선택 합니다. 리소스 이름은 영숫자 및 소문자 있어야 합니다.

1. 선택 합니다 **위치** 리소스 그룹입니다. Azure Stack 설치에 대해 선택 하는 지역입니다.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes 클러스터 설정

1. 선택 **Kubernetes 클러스터 설정을** Kubernetes 클러스터를 만듭니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. 입력 된 **Linux VM Admin Username**합니다. Kubernetes 클러스터의 일부인 Linux Virtual Machines 및 dvm이 대 한 사용자 이름입니다.

1. 입력 된 **SSH 공개 키** dvm이 고 Kubernetes 클러스터의 일부로 생성 하는 모든 Linux 컴퓨터에 대 한 인증에 사용 합니다.

1. 입력 된 **마스터 프로필 DNS 접두사** 지역에 고유한 합니다. 같은 지역 고유 이름 이어야 합니다 `k8s-12345`합니다. 하려고 선택 리소스 그룹과 동일한 이름을 모범 사례로 좋습니다.

    > [!Note]  
    > 각 클러스터에 대 한 마스터 프로필 새롭고 고유한 DNS 접두사를 사용 합니다.

1. 선택 된 **Kubernetes 마스터 풀 프로필 수**입니다. 마스터 풀의 노드 수를 포함 하는 수 있습니다. 있을 수 있습니다 1에서 7로. 이 값은 홀수 여야 합니다.

1. 선택 **Kubernetes 마스터 vm은 VMSize**합니다.

1. 선택 된 **Kubernetes 노드 풀 프로필 수**입니다. 수는 클러스터의 에이전트 수를 포함합니다. 

1. 선택 된 **저장소 프로필**합니다. 선택할 수 있습니다 **Blob 디스크** 하거나 **Managed Disk**합니다. 이 VM 크기의 Kubernetes 노드 Vm을 지정합니다. 

1. 선택 **ADFS** 에 대 한 합니다 **Azure Stack id 시스템** Azure Stack 설치 합니다.

1. 입력 된 **서비스 주체 ClientId** Kubernetes Azure 클라우드 공급자가 사용 됩니다. Azure Stack 관리자는 서비스 주체를 만들 때 응용 프로그램 ID로 식별 하는 클라이언트 ID입니다.

1. 입력 된 **Key Vault 리소스 그룹**합니다. 

1. 입력 된 **Key Vault 이름**합니다.

1. 입력 된 **Key Vault 비밀**합니다.

1. 입력 된 **Kubernetes Azure Cloud Provider 버전이**합니다. Kubernetes Azure 공급자에 대 한 버전입니다. Azure Stack에는 각 Azure Stack 버전에 대 한 사용자 지정 Kubernetes 빌드를 해제합니다.

### <a name="3-summary"></a>3. 요약

1. 요약을 선택 합니다. 블레이드는 Kubernetes 클러스터 구성 설정에 대해 유효성 검사 메시지를 표시합니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 설정을 검토 합니다.

3. 선택 **확인** 클러스터를 배포 합니다.

> [!TIP]  
>  배포에 대 한 질문이 있으면 질문을 게시 하거나 경우 사용자에 대 한 답변이 이미 질문에 참조를 [Azure Stack 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)합니다. 

## <a name="next-steps"></a>다음 단계

[클러스터에 연결](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)