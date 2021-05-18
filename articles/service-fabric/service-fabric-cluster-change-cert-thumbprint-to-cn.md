---
title: 인증서 일반 이름을 사용하도록 클러스터 업데이트
description: Azure Service Fabric 클러스터 인증서를 지문 기반 선언에서 일반 이름으로 변환하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: daf8d55e156f30b1f9e9ec5c50d60714e6f17884
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308016"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>클러스터 인증서를 지문 기반 선언에서 일반 이름으로 변환

인증서의 서명(일반적으로 지문이라고 함)은 고유합니다. 지문으로 선언된 클러스터 인증서는 인증서의 특정 인스턴스를 나타냅니다. 이 구체화로 인해 인증서 롤오버가 수행되고 관리가 일반적으로 어렵지만 분명해집니다. 변경할 때마다 클러스터 및 기본 컴퓨팅 호스트의 업그레이드 오케스트레이션이 필요합니다.

지문 기반의 Azure Service Fabric 클러스터의 인증서 선언을 인증서의 주체 CN(일반 이름)을 기반으로 하는 선언으로 변환하면 관리가 크게 간소화됩니다. 특히 인증서를 롤오버하는 데 더 이상 클러스터 업그레이드가 필요하지 않습니다. 이 문서에서는 가동 중지 시간 없이 기존 클러스터를 CN 기반 선언으로 변환하는 방법을 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>인증 기관 서명된 인증서로 이동

포함된 인증서가 지문으로 선언된 클러스터의 보안은 다른 서명과 동일한 서명으로 인증서를 위조하는 것이 불가능하거나 계산상 실행할 수 없다는 사실을 기반으로 합니다. 이 경우 인증서의 출처는 별로 중요하지 않으므로 자체 서명된 인증서가 적절합니다.

이와 달리, 포함된 인증서가 CN으로 선언된 클러스터의 보안은 클러스터 소유자가 인증서 공급자에 대해 가지는 암시적 신뢰를 기반으로 합니다. 공급자는 인증서를 발급한 PKI(공개 키 인프라) 서비스입니다. 신뢰는 무엇보다 PKI의 인증 사례, 운영 보안이 다른 신뢰할 수 있는 당사자에 의해 감사 및 승인되는지 여부 등을 기반으로 합니다.

클러스터 소유자는 해당 인증서를 발급하고 있는 CA(인증 기관)를 자세히 알고 있어야 합니다. 이는 주체별 인증서 유효성 검사의 기본적인 측면이기 때문입니다. 자체 서명된 인증서가 이 목적에 전혀 적합하지 않음을 의미하기도 합니다. 누구든지 지정된 주체를 사용하여 인증서를 생성할 수 있습니다.

CN으로 선언된 인증서는 일반적으로 다음 경우에 유효한 것으로 간주합니다.

* 해당 체인을 성공적으로 빌드할 수 있습니다.
* 주체에 필요한 CN 요소가 있습니다.
* 유효성 검사를 수행하는 에이전트에서 에이전트가 해당 발급자(체인에서 해당 수준 이상)를 신뢰합니다.

Service Fabric은 다음 두 가지 방법으로 CN을 통한 인증서 선언을 지원합니다.

* ‘암시적’ 발급자 사용: 체인이 트러스트 앵커에서 종료되어야 함을 의미합니다.
* 지문으로 선언된 발급자 사용: 발급자 고정이라고 합니다.

자세한 내용은 [일반 이름 기반 인증서 유효성 검사 선언](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)을 참조하세요.

지문으로 선언된 자체 서명된 인증서를 사용하여 클러스터를 CN으로 변환하려면 먼저 대상 CA 서명된 인증서를 지문을 통해 클러스터에 도입해야 합니다. 이렇게 해야 지문에서 CN으로 변환할 수 있습니다.

테스트 목적으로 자체 서명된 인증서는 발급자가 자체 지문으로 고정된 경우에만 CN으로 ‘선언할 수 있습니다’. 보안 관점에서 이 작업은 지문으로 동일한 인증서를 선언하는 것과 거의 동일합니다. 이와 같은 변환 성공이 CA 서명된 인증서를 사용하여 지문에서 CN으로 변환에 성공할 것임을 보장하지는 않습니다. 적절한 CA 서명된 인증서를 사용하여 변환을 테스트하는 것이 좋습니다. 이 테스트를 위한 무료 옵션이 있습니다.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>인증서를 업로드하고 확장 집합에 설치

Azure에서 인증서를 가져오고 프로비저닝하는 데 권장되는 메커니즘에는 Azure Key Vault 및 관련 도구가 포함됩니다. 클러스터 인증서 선언과 일치하는 인증서는 클러스터를 구성하는 가상 머신 확장 집합의 모든 노드에 프로비저닝되어야 합니다. 자세한 내용은 [가상 머신 확장 집합의 비밀](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-securely-ship-a-certificate-to-the-vm-)을 참조하세요.

클러스터의 인증서 선언에서 변경하기 전에 클러스터의 모든 노드 형식에 해당하는 현재 및 대상 클러스터 인증서를 둘 다 가상 머신에 설치하는 것이 중요합니다. 인증서 발급부터 Service Fabric 노드에 프로비저닝까지 경험은 [인증서 경험](cluster-security-certificate-management.md#the-journey-of-a-certificate)에서 자세히 설명합니다.

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>클러스터를 최적 시작 상태로 전환

인증서 선언을 지문 기반에서 CN 기반으로 변환하면 다음에 영향을 줍니다.

- 클러스터의 각 노드가 해당 자격 증명을 찾아서 다른 노드에 제공하는 방법.
- 보안 연결을 설정할 때 각 노드가 대응 노드에서 자격 증명의 유효성 검사하는 방법.

진행하기 전에 [양쪽 구성의 프레젠테이션 및 유효성 검사 규칙](cluster-security-certificates.md#certificate-configuration-rules)을 검토합니다. 지문-CN 변환을 수행할 때 가장 중요한 고려 사항은 업그레이드된 노드 및 아직 업그레이드되지 않은 노드(다른 업그레이드 도메인에 속하는 노드)가 업그레이드하는 동안 언제든지 성공적인 상호 인증을 수행할 수 있어야 한다는 것입니다. 이 동작을 달성하는 권장 방법은 초기 업그레이드에서 지문으로 대상 또는 목표 인증서를 선언하는 것입니다. 그런 다음, 후속 업그레이드에서 CN으로 전환을 완료합니다. 클러스터가 이미 권장되는 시작 상태인 경우 이 섹션을 건너뛸 수 있습니다.

변환에 유효한 시작 상태는 여러 가지가 있습니다. 고정은 클러스터가 CN으로 업그레이드를 시작할 때 이미 지문으로 선언된 대상 인증서를 사용하고 있다는 것입니다. 이 문서에서는 `GoalCert`, `OldCert1` 및 `OldCert2`를 살펴봅니다.

#### <a name="valid-starting-states"></a>유효한 시작 상태

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, 여기서 `GoalCert`에는 `OldCert1`의 날짜보다 이후인 `NotBefore` 날짜가 있습니다.
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, 여기서 `GoalCert`에는 `OldCert1`의 날짜보다 이후인 `NotBefore` 날짜가 있습니다.

> [!NOTE]
> 버전 7.2.445(7.2 CU4) 이전 Service Fabric은 가장 먼 만료 인증서(가장 먼 ‘NotAfter’ 속성이 있는 인증서)를 선택했으므로 7.2 CU4 이전인 위 시작 상태는 GoalCert에 `OldCert1`보다 이후인 `NotAfter` 날짜가 포함되어야 합니다.

클러스터가 이전에 설명한 유효한 상태 중 하나가 아닌 경우 이 문서의 끝에 있는 섹션에서 해당 상태 달성에 관한 내용을 참조하세요.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>원하는 CN 기반 인증서 유효성 검사 체계 선택

이전에 설명한 대로 Service Fabric은 암시적 트러스트 앵커를 사용하거나 발급자 지문을 명시적으로 고정하여 CN을 통한 인증서 선언을 지원합니다. 자세한 내용은 [일반 이름 기반 인증서 유효성 검사 선언](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)을 참조하세요.

선택하는 메커니즘의 차이점 및 의미를 잘 이해해야 합니다. 구문적으로 이 차이점 또는 선택 사항은 `certificateIssuerThumbprintList` 매개 변수 값에 따라 결정됩니다. 비워 두면 신뢰할 수 있는 루트 CA(트러스트 앵커)가 사용되지만 지문 세트는 클러스터 인증서의 허용되는 직접 발급자를 제한합니다.

   > [!NOTE]
   > `certificateIssuerThumbprint` 필드를 사용하여 주체 CN으로 선언된 인증서의 필요한 직접 발급자를 지정할 수 있습니다. 허용되는 값은 하나 이상의 쉼표로 구분된 SHA1 지문입니다. 이 작업은 인증서 유효성 검사를 강화합니다.
   >
   > 발급자를 지정하지 않거나 목록이 비어 있으면 체인을 빌드할 수 있는 경우 인증서를 인증에 사용할 수 있습니다. 인증서는 유효성 검사기가 신뢰할 수 있는 루트에서 종료됩니다. 하나 이상의 발급자 지문이 지정된 경우 체인에서 추출된 직접 발급자의 지문이 이 필드에 지정된 값과 일치하면 인증서를 사용할 수 있습니다. 루트를 신뢰할 수 있는지 여부에 관계없이 인증서를 사용할 수 있습니다.
   >
   > PKI는 지정된 주체로 인증서에 서명하는 데 다른 인증 기관(‘발급자’라고도 함)을 사용할 수 있습니다. 이 이유로, 해당 주체에 필요한 모든 발급자 지문을 지정하는 것이 중요합니다. 즉, 인증서 갱신으로는 갱신되는 인증서와 동일한 발급자가 서명하는 것이 보장되지 않습니다.
   >
   > 발급자 지정은 모범 사례로 간주합니다. 발급자 생략은 신뢰할 수 있는 루트에 연결하는 인증서에 대해 계속 작동하지만, 이 동작은 제한 사항이 있으며 가까운 미래에 단계적으로 중단될 수 있습니다. Azure에 배포되고, 프라이빗 PKI에서 발급된 X509 인증서로 보호되고, 주체에 의해 선언된 클러스터는 Service Fabric에서 유효성을 검사할 수 없습니다(클러스터-서비스 통신의 경우). 유효성 검사를 수행하려면 PKI 인증서 정책이 검색 가능하고, 사용 가능하며, 액세스 가능해야 합니다.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>클러스터의 Azure Resource Manager 템플릿 업데이트 및 배포

ARM(Azure Resource Manager) 템플릿을 사용하여 Service Fabric 클러스터를 관리합니다. JSON 아티팩트도 사용하는 대안은 [Azure Resource Explorer(미리 보기)](https://resources.azure.com)입니다. 지금은 Azure Portal에서 해당 환경을 사용할 수 없습니다.

기존 클러스터에 해당하는 원래 템플릿을 사용할 수 없는 경우 Azure Portal에서 해당 템플릿을 가져올 수 있습니다. 클러스터가 포함된 리소스 그룹으로 이동하고 왼쪽의 **자동화** 메뉴에서 **템플릿 내보내기** 를 선택합니다. 그런 다음, 원하는 리소스를 선택합니다. 최소한 가상 머신 확장 집합 및 클러스터 리소스를 각각 내보내야 합니다. 생성된 템플릿을 다운로드할 수도 있습니다. 이 템플릿은 완전히 배포하려면 먼저 변경해야 할 수 있습니다. 템플릿은 원래 템플릿과 정확히 일치하지 않을 수도 있습니다. 클러스터 리소스의 현재 상태를 반영하는 것입니다.

필요한 변경 내용은 다음과 같습니다.

- 가상 머신 리소스에서 Service Fabric 노드 확장의 정의를 업데이트. 클러스터가 여러 노드 형식을 정의하는 경우 해당하는 각 가상 머신 확장 집합의 정의를 업데이트해야 합니다.
- 클러스터 리소스 정의 업데이트.

여기에 자세한 예제가 포함됩니다.

### <a name="update-the-virtual-machine-scale-set-resources"></a>가상 머신 확장 집합 리소스 업데이트
원본:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
대상:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>클러스터 리소스 업데이트

**Microsoft.ServiceFabric/clusters** 리소스에서 **commonNames** 설정을 사용하여 **certificateCommonNames** 속성을 추가하고 **certificate** 속성을 함께(모든 해당 설정) 제거합니다.

원본:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
대상:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

자세한 내용은 [지문 대신 인증서 일반 이름을 사용하는 Service Fabric 클러스터 배포](./service-fabric-create-cluster-using-cert-cn.md)를 참조하세요.

## <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포

변경한 후 업데이트된 템플릿을 다시 배포합니다.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>클러스터를 CN 기반 인증서 선언으로 변환하기 위해 유효한 시작 상태 달성

| 시작 상태 | 업그레이드 1 | 업그레이드 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 및 `GoalCert`에 `OldCert1`보다 이후인 `NotBefore` 날짜가 있음 | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 및 `OldCert1`에 `GoalCert`보다 이후인 `NotBefore` 날짜가 있음 | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, 여기서 `OldCert1`에는 `GoalCert`보다 이후인 `NotBefore` 날짜가 있음 | `Thumbprint: GoalCert, ThumbprintSecondary: None`으로 업그레이드 | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, 여기서 `OldCert1`에는 `GoalCert`보다 이후인 `NotBefore` 날짜가 있음 | `Thumbprint: GoalCert, ThumbprintSecondary: None`으로 업그레이드 | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | `OldCert1` 또는 `OldCert2` 중 하나를 제거하여 `Thumbprint: OldCertx, ThumbprintSecondary: None` 상태에 도달 | 새 시작 상태에서 계속 |

> [!NOTE]
> 버전 7.2.445(7.2 CU4) 이전 클러스터의 경우 이전 상태에서 `NotBefore`를 `NotAfter`로 바꿉니다.

이 업그레이드를 수행하는 방법에 관한 지침은 [Azure Service Fabric 클러스터에서 인증서 관리](service-fabric-cluster-security-update-certs-azure.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* [일반 이름으로 클러스터 인증서를 롤오버](service-fabric-cluster-rollover-cert-cn.md)하는 방법을 알아봅니다.
* [터치리스 자동 롤오버를 위해 클러스터를 구성](cluster-security-certificate-management.md)하는 방법을 알아봅니다.

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
