---
title: "Azure Virtual Machine Scale Sets에 대한 FAQ | Microsoft Docs"
description: "Virtual Machine Scale Sets에 대한 FAQ(질문과 대답)에 대해 알아봅니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 850459a79c723de0eb3249cfe9ea19bc988ca318
ms.lasthandoff: 03/18/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>Azure Virtual Machine Scale Sets에 대한 FAQ

이 문서에는 크기 집합에 대해 자주 묻는 질문과 대답이 포함되어 있습니다.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Azure 자동 크기 조정에 대한 모범 사례는 무엇입니까?

예. https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices를 참조하세요.

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>호스트 기반 메트릭을 사용하는 자동 크기 조정에 대한 메트릭 이름은 어디서 찾을 수 있습니까?

https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>서비스 버스 토픽과 큐 길이를 기반으로 하는 자동 크기 조정의 예제가 있습니까?

예. 다음을 참조하세요.

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/

서비스 버스 큐의 경우:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

저장소 큐의 경우:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

이러한 샘플 값은 적절한 리소스 URI로 바꿉니다.


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>호스트 기반 메트릭을 사용하여 자동 크기 조정하거나 진단 확장을 사용해야 합니까?

VM에 자동 크기 조정 설정을 만들면 호스트 수준 메트릭을 사용하거나 게스트 OS 기반 메트릭을 사용할 수 있습니다.

지원되는 메트릭 목록은 https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics를 참조하세요. 다음은 크기 집합의 전체 샘플입니다(여기서는 호스트 수준 CPU 메트릭 및 메시지 수 메트릭을 사용함).

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>크기 집합에 경고 규칙을 설정하려면 어떻게 해야 합니까?

PS 또는 CLI를 통해 크기 집합의 메트릭에 대한 경고를 만들 수 있습니다. 다음을 참조하세요.

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts

크기 집합의 TargetResourceId는 /subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname과 같습니다.

경고할 메트릭으로 다음과 같은 VM 성능 카운터를 모두 선택할 수 있습니다.

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>PowerShell을 사용하여 크기 집합에 자동 크기 조정을 설정하려면 어떻게 해야 합니까?

https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/을 참조하세요.




## <a name="certificates"></a>인증서

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>VM에 인증서를 안전하게 배달하려면 어떻게 합니까?  인증서 구성에서 크기 집합을 프로비전하여 웹 사이트의 SSL이 안전하게 배달되는 웹 사이트를 실행하는 예제가 있습니까?  일반적인 인증서 회전 작업은 구성 업데이트 작업에 해당합니다.

고객 인증서를 키 자격 증명 모음의 Windows 인증서 저장소에 직접 설치하는 것을 지원합니다.

크기 집합의 컨텍스트에서 다음과 같습니다.

https://msdn.microsoft.com/library/mt589035.aspx

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

Windows 및 Linux를 모두 지원합니다.

### <a name="self-signed-certificate-example"></a>자체 서명된 인증서 예제:

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>KeyVault에 자체 서명된 인증서 만들기

KeyVault에 자체 서명된 인증서를 만드는 한 가지 방법은 https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/에 있는 Service Fabric 문서의 지침을 사용하는 것입니다.

PowerShell 명령:

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

위의 명령은 Resource Manager 템플릿에 대한 입력을 제공합니다.

#### <a name="change-resource-manager-template"></a>Resource Manager 템플릿 변경

Resource 크기 집합의 일부로 "virtualMachineProfile"에 다음 속성을 추가합니다.

```json 
"osProfile": {
            "computerNamePrefix": "[variables('namingInfix')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                  {
                    "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                    "certificateStore": "My"
                  }
                ]
              }
            ]
          }
```
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>Resource Manager 템플릿에서 Linux 크기 집합으로 SSH 인증에 사용하려는 SSH 키 쌍을 지정하는 방법이 있습니까?  

osProfile의 REST API는 다음의 일반적인 VM 사례와 비슷합니다.
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration
 
다음 예제와 같이 템플릿에 `osProfile`을 포함합니다.

```json 
"osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUserName')]",
          "linuxConfiguration": {
            "disablePasswordAuthentication": "true",
            "ssh": {
              "publicKeys": [
                {
                  "path": "[variables('sshKeyPath')]",
                  "keyData": "[parameters('sshKeyData')]"
                }
              ]
            }
          }
        }
```
 
이 JSON 블록은 다음의 빠른 시작 템플릿에서 사용됩니다.
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
또한 다음 템플릿의 OS 프로필도 살펴보겠습니다.
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json

### <a name="how-do-i-remove-deprecated-certificates"></a>사용되지 않는 인증서를 제거하려면 어떻게 합니까? 

자격 증명 모음 인증서 목록에서 이전 인증서를 제거해야 하지만, 컴퓨터에서 유지하려는 인증서는 모두 그대로 두어야 합니다. 이렇게 하면 모든 VM에서 해당 인증서가 제거되지는 않지만, 크기 집합에 만든 새 VM에 이 인증서가 추가되지도 않습니다. 기존 VM에서 인증서를 제거하려면 인증서 저장소에서 인증서를 수동으로 제거하는 사용자 지정 스크립트 확장을 작성해야 합니다.
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>기존 SSH 공개 키를 가져와서 프로비전하는 동안 크기 집합 SSH 계층에 삽입하려면 어떻게 합니까?  SSH 공개 키 값을 Azure Key Vault에 저장하고 이를 내 Resource Manager 템플릿에서 활용하고 싶습니다.

VM에 공개 ssh 키만 제공하는 경우 공개 키가 비밀이 아니기 때문에 해당 공개 키를 키 자격 증명 모음에 두지 않아도 됩니다.
 
Linux VM을 만들 때 일반 텍스트로 SSH 공개 키를 제공할 수 있습니다.
예제는 다음에서 찾을 수 있습니다.

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
구체적으로 살펴보면 다음과 같습니다.

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
linuxConfiguration 요소 이름 | 필수 | 형식 | 설명
--- | --- | --- | --- |  ---
ssh | 아니요 | 컬렉션 | Linux OS용 ssh 키 구성을 지정합니다.
path | 예 | string | ssh 키 또는 인증서를 배치해야 하는 Linux 파일 경로를 지정합니다.
keyData | 예 | 문자열 | base64로 인코딩된 ssh 공개 키를 지정합니다.
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>동일한 KeyVault에서 둘 이상의 인증서 다음에 Update-AzureRmVmss를 실행하면 다음과 같은 오류가 발생합니다.
 
Update-AzureRmVmss: 허용되지 않는 /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev의 반복된 인스턴스를 포함하고 있는 비밀을 나열합니다. 동일한 KeyVault에서 두 개의 인증서를 추가할 수 없는 이유는 무엇입니까?
 
이 문제는 기존 sourceVault에 대해 새 vaultCertificate 대신 동일한 자격 증명 모음을 두 번 추가하려는 경우에 발생할 수 있습니다. 추가 비밀을 추가할 때는 Add-AzureRmVmssSecret이 제대로 작동하지 않습니다.
 
동일한 키 자격 증명 모음에서 더 많은 비밀을 추가하려면 $vmss.properties.osProfile.secrets[0].vaultCertificates 목록을 업데이트해야 합니다.
 
예상되는 입력 구조는 https://msdn.microsoft.com/library/azure/mt589035.aspx에서 확인할 수 있습니다.
 
키 자격 증명 모음을 포함하고 있는 크기 집합 개체에서 비밀을 찾아야 합니다. 그런 다음 인증서 참조(비밀 저장소 이름 및 URL)를 자격 증명 모음과 관련된 목록에 추가해야 합니다.

참고: 크기 집합 API를 통해 VM에서 인증서를 제거하는 기능은 현재 지원되지 않습니다.
 
새 VM에는 이전 인증서가 없지만, 이미 배포된 인증서에는 이전 인증서가 여전히 남아 있습니다.
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>인증서가 현재 SecretStore에 있을 때 암호를 제공하지 않고 인증서를 크기 집합에 푸시할 수 있는 방법이 있습니까?

스크립트에서 암호를 하드 코딩할 필요가 없습니다. 배포 스크립트를 사용하여 실행하는 권한으로 암호를 동적으로 검색할 수 있습니다. 인증서를 비밀 저장소에서 키 자격 증명 모음으로 이동하는 스크립트가 있는 경우 secret store get certificate 명령은 pfx 파일의 암호도 출력합니다.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>크기 집합의 virtualMachineProfile.osProfile에 있는 secrets 속성은 어떻게 작동합니까? certificateUrl이 있는 인증서에 절대 URI를 지정해야 하는 경우 sourceVault가 왜 필요합니까? 

Win RM 인증서 참조가 OS 프로필의 secrets 속성에 있어야 합니다. 

원본 자격 증명 모음을 나타내는 것은 CSM에 있는 ACL 정책을 적용할 수 있기 위한 것입니다. 원본 자격 증명 모음을 지정하지 않으면 키 자격 증명 모음에 비밀을 배포하고 액세스할 수 있는 권한이 없는 사용자는 CRP를 통해 배포하고 액세스할 수 있습니다. ACL은 존재하지 않는 리소스에 대해서도 존재합니다.

잘못된 sourceVault ID를 제공했지만 유효한 키 자격 증명 모음 URL을 제공한 경우 작업을 폴링할 때 오류가 보고됩니다.
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>기존 크기 집합에 비밀을 추가하는 경우 해당 비밀을 기존 인스턴스 또는 새로 만든 인스턴스에 삽입해야 합니까? 

인증서는 기존 VM을 포함하여 모든 VM에 추가됩니다. 크기 집합 upgradePolicy 속성을 "Manual"로 설정하면 VM에서 수동 업데이트를 수행할 때 인증서가 해당 VM에 추가됩니다.
 
### <a name="where-do-certificates-go-for-linux-vms"></a>Linux VM에 대한 인증서는 어디에 있습니까?

https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/를 참조하세요.
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>새 자격 증명 모음 인증서는 새 인증서 개체에 추가하려면 어떻게 합니까?

비밀 개체 하나만 있는 기존 비밀에 자격 증명 모음 인증서를 추가하려는 경우 다음 PowerShell 예제와 같이 수행할 수 있습니다.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>VM을 이미지로 다시 설치하면 인증서는 어떻게 됩니까?

VM을 이미지로 다시 설치하면 전체 OS 디스크 전체를 삭제하기 때문에 인증서가 나타나지 않습니다. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>키 자격 증명 모음에서 인증서를 삭제하면 어떻게 됩니까?

키 자격 증명 모음에서 비밀을 삭제하고 모든 VM의 할당 취소를 중지한 다음 다시 시작하면 오류가 발생합니다. 이 오류는 CRP에서 Key Vault의 비밀을 검색해야 하지만 검색할 수 없기 때문입니다. 이 시나리오에서는 크기 집합 모델에서 인증서를 삭제할 수 있습니다. 

CRP 구성 요소는 고객의 비밀을 유지하지 않습니다. 크기 집합의 모든 VM 할당 취소를 중지하면 캐시가 삭제됩니다. 이 시나리오에서는 키 자격 증명 모음에서 비밀을 검색합니다.

캐시된 비밀 복사본이 패브릭에 있기 때문에(적어도 단일 패브릭 테넌트 모델인 경우) 이 문제는 확장에 맞지 않습니다.
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/에서 설명하는 대로 인증서 URL의 정확한 위치를 지정해야 하는 이유는 무엇입니까? 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
 
Key Vault 설명서에 따르면, 버전이 지정되지 않은 경우 get-secret REST API는 최신 버전의 암호를 반환해야 합니다.
 
메서드 | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

{secret-name}을 검색하려는 이름 및 비밀의 버전을 포함한 {secret-version}으로 바꿉니다. 현재 버전을 검색하는 경우에는 비밀 버전을 제외할 수 있습니다.
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>키 자격 증명 모음을 사용할 때 인증서 버전을 지정해야 하는 이유는 무엇입니까?

VM에 배포된 인증서를 사용자에게 분명하게 알리기 위해 이 요구 사항이 필요합니다.

VM을 만든 다음 키 자격 증명 모음에서 비밀을 업데이트하면 새 인증서가 VM에 다운로드되지 않습니다. 그러나 VM에서 새 인증서를 참조하는 것처럼 보일 것이며, 새 VM에서 새 비밀을 가져오게 됩니다. 이러한 혼동을 피하기 위해 명시적인 비밀 버전을 참조해야 합니다.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>여기서는 .cer 공개 키로 배포되는 여러 인증서로 작업합니다. 이러한 인증서를 크기 집합에 배포하는 경우 권장되는 방법은 무엇입니까?

.cer 파일만 포함하고 X509ContentType = Pfx인 pfx 파일을 생성하면 됩니다. 예를 들어 C# 또는 PowerShell에서 https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx의 메서드를 호출하는 x509Certificate2 개체로 .cer 파일을 로드합니다.

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>사용자가 인증서를 대부분의 다른 리소스 공급자에서 제공하는 base64 문자열로 전달하는 옵션이 표시되지 않습니다.

Resource Manager 템플릿 내에서 최신 버전 URL을 추출하여 설명하는 동작을 에뮬레이트할 수 있습니다. Resource Manager 템플릿에 다음 JSON 속성을 포함할 수 있습니다.

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>키 자격 증명 모음의 JSON 개체에서 인증서를 래핑해야 합니까?

이는 크기 집합/VM 요구 사항입니다. 또한 application/x-pkcs12 콘텐츠 형식도 지원합니다. 지침은 http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/에서 확인할 수 있습니다.
 
현재 .cer 파일을 지원하지 않으므로 .cer 파일을 pfx 컨테이너로 내보내야 합니다.





## <a name="compliance"></a>규정 준수

### <a name="are-scale-sets-pci-compliant"></a>크기 집합은 PCI 규격을 준수합니까?

크기 집합은 CRP(Compute Resource Provider)의 위쪽에 있는 얇은 API 계층이며, Azure 서비스 트리의 "Compute 플랫폼" 영역에 속합니다.

따라서 규정 준수 관점에서 볼 때 크기 집합은 Azure Compute 플랫폼의 기본 요소입니다. 그렇기 때문에 CRP 자체와 동일한 팀, 도구, 프로세스, 배포 방법, 보안 제어, JIT, 모니터링, 경고 등을 공유합니다.  현재 CRP가 PCI DSS 증명의 일부이므로 크기 집합은 PCI 규격을 준수합니다.

자세한 내용은 [https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI)를 참조하세요.






## <a name="extensions"></a>확장

### <a name="how-do-you-delete-a-scale-set-extension"></a>크기 집합 확장을 삭제하려면 어떻게 합니까?

다음은 PowerShell을 사용하는 예제입니다.

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
extensionName은 `$vmss`에 있습니다.
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>OMS와 통합되는 크기 집합 템플릿 예제가 있습니까?

여기서 다음 두 번째 예제를 살펴보겠습니다.

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>확장이 크기 집합에서 병렬로 실행되어 내 사용자 지정 스크립트 확장이 실패합니다. 이 문제를 해결하려면 어떻게 해야 합니까?

https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/를 참조하세요. 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>크기 집합 VM의 암호를 다시 설정하려면 어떻게 합니까?

VM 액세스 확장을 사용합니다.

다음은 PowerShell을 사용하는 예제입니다.

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>크기 집합의 모든 VM에 확장을 추가하려면 어떻게 합니까?

- 업데이트 정책이 자동으로 설정된 경우 템플릿을 새 확장 속성으로 다시 배포하면 모든 VM이 업데이트됩니다.
- 업데이트 정책이 수동으로 설정된 경우 확장을 업데이트한 다음 모든 인스턴스에서 manualUpdate를 수행해야 합니다.
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>기존 크기 집합과 연결된 확장을 업데이트하면 기존 VM에 영향을 줍니까? (즉, VM이 크기 집합 모델과 일치하지 않는 것으로 나타납니까?) 또는 무시하겠습니까? 기존 컴퓨터에서 서비스 복구, 이미지로 다시 설치 등을 수행할 때 현재 크기 집합에 구성된 스크립트를 실행하거나 컴퓨터에서 처음 만들었을 때 구성된 스크립트를 사용합니까?

- 크기 집합 모델의 확장 정의를 업데이트할 때 upgradePolicy가 자동으로 설정되어 있는 경우 VM을 업데이트하고, 그렇지 않고 수동으로 설정되어 있는 경우 모델과 일치하지 않는다는 플래그가 지정됩니다. 

- 기존 VM이 서비스 복구되면 다시 부팅하는 것처럼 보이며 확장이 다시 실행되지 않습니다. 이미지로 다시 설치되면 OS 드라이브를 원본 이미지로 바꾸고 특수화된 최신 모델(예: 확장)이 실행됩니다.
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>AD 도메인에 가입할 크기 집합을 얻으려면 어떻게 합니까?

예를 들어 JsonADDomainExtension을 사용하여 다음과 같이 확장을 정의할 수 있습니다.
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
                                    "settings": {
                                        "Name": "[parameters('domainName')]",
                                        "OUPath": "[variables('ouPath')]",
                                        "User": "[variables('domainAndUsername')]",
                                        "Restart": "true",
                                        "Options": "[variables('domainJoinOptions')]"
                                    },
                                    "protectedsettings": {
                                        "Password": "[parameters('domainJoinPassword')]"
                                    }
                                }
                            }
                        ]
                    }
```
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>내 크기 집합 확장에서 다시 부팅해야 하는 항목을 설치하려고 합니다. 예: "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

DSC 확장을 사용할 수 있습니다. OS가 2012 R2이면 Azure에서 WMF5.0 설치 프로그램을 가져오고 다시 부팅한 다음 구성을 계속합니다. 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>크기 집합에서 맬웨어 방지 프로그램을 사용하도록 설정하려면 어떻게 해야 합니까?

다음은 PowerShell 예제입니다.

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>개인 저장소 계정에서 호스팅되는 사용자 지정 스크립트를 실행해야 합니다. 저장소가 공개되어도 문제가 없지만 SAS(공유 액세스 서명)를 사용하려고 하면 "올바른 공유 액세스 서명에 대한 필수 매개 변수가 없음"이라는 오류 메시지와 함께 실패합니다. 해당 링크를 알고 있고 내 로컬 브라우저에서 SAS가 제대로 작동하고 있습니다.

이 시나리오가 작동하려면 저장소 계정 키와 이름으로 보호된 설정을 설정해야 합니다. https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings를 참조하세요.







## <a name="networking"></a>네트워킹
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>동일한 구독 및 동일한 지역에서 크기 집합에 대해 VIP 교환을 수행하려면 어떻게 합니까?

https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/를 참조하세요. 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for"></a>NIC에 대한 resourceGuid 속성은 무엇입니까?

고유 ID입니다. 하위 계층에서 나중에 이 ID를 기록할 것입니다. 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>정적 개인 IP 주소를 할당하기 위해 개인 IP 주소의 범위를 지정하려면 어떻게 합니까?

IP는 사용자가 지정한 서브넷에서 선택됩니다. 

크기 집합 IP의 할당 메서드는 항상 "Dynamic"입니다. 이는 이러한 IP가 변경될 수 있음을 의미하는 것이 아니라 PUT 요청에서 IP를 지정하지 않는다는 것을 의미할 뿐입니다. 즉 서브넷을 사용하여 정적 집합을 지정합니다. 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>기존 VNET에 크기 집합을 배포하려면 어떻게 합니까? 

https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet을 참조하세요. 

### <a name="how-do-i-add-a-scale-sets-first-vms-ip-address-to-the-output-of-a-template"></a>크기 집합의 첫 번째 VM IP 주소를 템플릿의 출력에 추가하려면 어떻게 해야 하나요?

참조: http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips



## <a name="scale"></a>확장

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>VM이 2개 미만인 크기 집합을 만드는 이유는 무엇입니까?

한 가지 이유는 크기 집합의 탄력적인 속성을 사용하는 것입니다. 예를 들어 VM 실행 비용을 지불하지 않고 인프라를 정의하기 위해 VM이 없는 크기 집합을 배포할 수 있습니다. 그런 다음 VM을 배포할 준비가 되면 크기 집합의 "용량"을 프로덕션 인스턴스 수로 늘려 VM을 배포할 수 있습니다.

또 다른 이유는 별도의 VM으로 구성된 가용성 집합을 사용하는 것과 같은 의미로 가용성을 고려하지 않는 크기 집합으로 어떤 작업을 수행하는 경우입니다. 크기 집합은 대체할 수 있는 미분화된 계산 단위로 작업하는 방법을 추가합니다. 이 일관성은 크기 집합과 가용성 집합에 대한 주요 차별성이기도 합니다. 많은 상태 비저장 워크로드에서는 개별 장치를 고려하지 않으며, 워크로드가 감소하면 하나의 계산 단위로 축소한 다음 워크로드가 증가할 때 많은 계산 단위로 다시 확장할 수 있습니다.

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>크기 집합의 VM 수를 변경하려면 어떻게 합니까?

https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/를 참조하세요.

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>특정 임계값에 도달하는 경우 사용자 지정 경고를 정의하려면 어떻게 해야 합니까?

경고를 처리하는 방법에는 약간의 유연성이 있습니다. 예를 들어 Resource Manager 템플릿에서 다음 예제와 같이 사용자 지정 웹후크를 정의할 수 있습니다.
```json
   {
         "type": "Microsoft.Insights/autoscaleSettings",
           "apiVersion": "[variables('insightsApi')]",
                 "name": "autoscale",
                   "location": "[parameters('resourceLocation')]",
                     "dependsOn": [
                         "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
                 ],
                 "properties": {
                         "name": "autoscale",
                     "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                     "enabled": true,
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

이 예제에서는 임계값에 도달하면 경고가 Pagerduty로 이동합니다.



## <a name="troubleshooting"></a>문제 해결

### <a name="how-do-i-enable-boot-diagnostics"></a>부팅 진단을 사용하도록 설정하려면 어떻게 합니까?

저장소 계정을 만들고 다음 JSON 블록을 virtualMachineProfile 크기 집합에 배치하고 해당 크기 집합을 업데이트합니다.
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

그런 다음 새 VM이 만들어지면 VM의 InstanceView에서 스크린샷 등에 대한 세부 정보를 표시합니다. 예:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>업데이트

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>내 크기 집합을 새 이미지로 업데이트하고 패치를 관리하는 방법은 무엇입니까?

https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set를 참조하세요.

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>이미지로 다시 설치 작업을 사용하여 이미지를 변경하지 않고 VM을 다시 설정할 수 있습니까? (즉 VM을 새 이미지가 아닌 출하 시 설정으로 다시 설정합니까?)

예. https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set를 참조하세요.

그러나 크기 집합이 version = "latest"인 플랫폼 이미지를 참조하면 이미지로 다시 설치 작업을 호출할 때 VM에서 최신 OS 이미지로 업데이트할 수 있습니다.







## <a name="vm-properties"></a>VM 속성

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>여러 번 호출하지 않고도 각 VM에 대한 속성 정보를 얻으려면 어떻게 합니까? 예를 들어 100개의 크기 집합에서 각 VM의 장애 도메인을 가져옵니까?

다음 리소스 URI에서 `GET` REST API를 수행하여 ListVMInstanceViews를 호출할 수 있습니다.

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>크기 집합의 다른 VM에 다른 확장 인수를 전달하는 방법이 있습니까?

아니요. 확장은 컴퓨터 이름과 같이 실행 중인 VM의 고유한 속성에 따라 작동할 수 있습니다. 또한 확장은 http://169.254.169.254에 있는 인스턴스 메타데이터를 쿼리하여 자세한 정보를 가져올 수 있습니다.

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>크기 집합 VM 컴퓨터 이름과 VM ID 사이에 간격이 있는 이유는 무엇입니까? 예를 들어 0, 1, 3...입니다.

크기 집합 과도 프로비전 속성이 true(기본값)로 설정되어 있기 때문에 간격이 있습니다. 과도 프로비전이 true이면 요청한 것보다 많은 VM이 만들어지고, 그 다음에 추가 VM이 삭제됩니다. 연속된 이름 지정 및 연속된 NAT 규칙 대신 배포 안정성이 향상된다는 이점을 얻을 수 있습니다. 이 속성은 false로 설정할 수 있으며, 작은 크기 집합의 경우 배포 안정성에 별다른 차이가 없습니다.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>크기 집합에서 VM 삭제와 VM 할당 취소 간의 차이점은 무엇입니까? 언제 다른 하나를 선택해야 합니까?

주요 차이점은 할당 취소에서 VHD를 삭제하지 않으므로 할당 취소 중지와 관련된 저장소 비용이 있다는 것입니다. 다른 것을 사용하는 이유는 다음과 같습니다.

- Compute 지불은 중지하지만 VM의 디스크 상태는 유지하려고 합니다.
- 크기 집합을 확장하는 것보다 빠르게 VM 집합을 시작하려고 합니다.
  - 이 시나리오와 관련하여 사용자 고유의 자동 크기 조정 엔진을 만들고 더 빠른 종단 간 확장을 원했습니다.
  - VM을 선택적으로 삭제하거나 VM을 과도 프로비전 후에 삭제하기 때문에 FD/UD에서 균등하게 분산된 크기 집합이 있습니다. 할당 취소 중지에 이어 크기 집합에서 시작하면 VM이 FD/UD에서 균등하게 분산합니다.








 
   

