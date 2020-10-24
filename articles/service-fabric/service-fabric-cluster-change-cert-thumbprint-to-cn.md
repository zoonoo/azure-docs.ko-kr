---
title: 인증서 일반 이름을 사용 하도록 클러스터 업데이트
description: Azure Service Fabric 클러스터 인증서를 지문 기반 선언에서 일반 이름으로 변환 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 013b8190390a4b05791b0a56072487f249956ec5
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495201"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>클러스터 인증서를 지문 기반 선언에서 일반 이름으로 변환

인증서의 서명 (일반적으로 지문 이라고 함)은 고유 합니다. 지 문으로 선언 된 클러스터 인증서는 특정 인증서 인스턴스를 참조 합니다. 이 특이성는 인증서 롤오버와 일반적이 고 어렵고 명시적인 관리를 수행 합니다. 각 변경에는 클러스터 및 기본 컴퓨팅 호스트의 오케스트레이션 업그레이드가 필요 합니다.

인증서의 주체 CN (일반 이름)을 기반으로 하는 Azure Service Fabric 클러스터의 인증서 선언을 지문 기반에서 선언으로 변환 하면 관리가 훨씬 간단해 집니다. 특히 인증서를 롤백하는 경우 클러스터 업그레이드가 더 이상 필요 하지 않습니다. 이 문서에서는 가동 중지 시간 없이 기존 클러스터를 CN 기반 선언으로 변환 하는 방법을 설명 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>인증 기관 서명 된 인증서로 이동

지문이 해당 인증서를 선언 하는 클러스터의 보안은 불가능 하거나 계산 작업 다른 서명을 사용 하는 인증서를 위조 하는 것을 말합니다. 이 경우 인증서의 provenance는 중요 하지 않으므로 자체 서명 된 인증서가 적절 합니다.

이와 반대로 인증서를 발급 하는 클러스터의 보안은 클러스터 소유자가 해당 인증서 공급자에 있는 암시적 트러스트를 통해 전송 됩니다. 공급자는 인증서를 발급 한 PKI (공개 키 인프라) 서비스입니다. 신뢰는 PKI의 인증 방법에 따라 운영 보안이 아직 다른 신뢰할 수 있는 당사자에 의해 감사 및 승인 되는지 여부를 기반으로 합니다.

또한 클러스터 소유자에 게는 인증서를 발급 하는 Ca (인증 기관)에 대 한 세부 정보가 있어야 합니다 .이는 주체 별로 인증서 유효성 검사의 기본적인 측면입니다. 이는 자체 서명 된 인증서가이 목적에 전적으로 적합 하지 않음을 의미 합니다. 누구 든 지 지정 된 주체를 사용 하 여 인증서를 생성할 수 있습니다.

CN으로 선언 된 인증서는 일반적으로 다음과 같은 경우 유효한 것으로 간주 됩니다.

* 체인을 성공적으로 빌드할 수 있습니다.
* 주체에 필요한 CN 요소가 있습니다.
* 유효성 검사를 수행 하는 에이전트에서 발급자 (체인의 바로 이상)를 신뢰할 수 있습니다.

Service Fabric는 다음과 같은 두 가지 방법으로 CN에서 인증서 선언을 지원 합니다.

* *암시적* 발급자를 사용 하 여 체인이 트러스트 앵커에서 종료 되어야 함을 의미 합니다.
* 지문이 선언한 발급자 (발급자 고정 이라고 함)

자세한 내용은 [일반 이름 기반 인증서 유효성 검사 선언](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)을 참조 하세요.

지문이 지 문으로 선언 된 자체 서명 된 인증서를 사용 하 여 클러스터를 변환 하려면 먼저 대상 CA 서명 인증서를 지 문으로 클러스터에 추가 해야 합니다. 그러면 지문에서 CN으로의 변환만 가능 합니다.

테스트 목적으로 자체 서명 된 인증서는 CN에서 선언할 수 있지만 발급자가 자체 지문에 고정 되어 있는 경우에 *만 가능 합니다* . 보안 관점에서이 작업은 지문을 통해 동일한 인증서를 선언 하는 것과 거의 동일 합니다. 이러한 종류를 성공적으로 변환 하면 CA 서명 인증서를 사용 하 여 지문을 CN으로 성공적으로 변환할 수 없습니다. 적절 한 CA 서명 인증서를 사용 하 여 변환을 테스트 하는 것이 좋습니다. 이 테스트에 대 한 무료 옵션이 있습니다.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>인증서를 업로드하고 확장 집합에 설치

Azure에서 인증서를 가져오고 프로 비전 하는 데 권장 되는 메커니즘에는 Azure Key Vault와 도구가 포함 됩니다. 클러스터 인증서 선언과 일치 하는 인증서는 클러스터를 구성 하는 가상 머신 확장 집합의 모든 노드에 프로 비전 되어야 합니다. 자세한 내용은 [가상 머신 확장 집합에 대 한 암호](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm)를 참조 하세요.

클러스터의 인증서 선언에서 변경 하기 전에 클러스터의 모든 노드 형식에 대 한 가상 컴퓨터에 현재 및 대상 클러스터 인증서를 모두 설치 하는 것이 중요 합니다. Service Fabric 노드에 프로 비전 하기 위한 인증서 발급에서의 경험은 [인증서의](cluster-security-certificate-management.md#the-journey-of-a-certificate)경험에 자세히 설명 되어 있습니다.

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>클러스터를 최적의 시작 상태로 전환

인증서 선언을 지문 기반에서 CN 기반 영향으로 변환 합니다.

- 클러스터의 각 노드가 해당 자격 증명을 찾아서 다른 노드에 표시 하는 방법을 보여 줍니다.
- 보안 연결을 설정할 때 각 노드가 대응 하는 자격 증명의 유효성을 검사 하는 방법입니다.

진행 하기 전에 [두 구성에 대 한 프레젠테이션 및 유효성 검사 규칙](cluster-security-certificates.md#certificate-configuration-rules) 을 검토 합니다. 지문 대 CN 변환을 수행할 때 가장 중요 한 고려 사항은 업그레이드 되 고 아직 업그레이드 되지 않은 노드 (즉, 다른 업그레이드 도메인에 속하는 노드)는 업그레이드 하는 동안 언제 든 지 성공적인 상호 인증을 수행할 수 있어야 한다는 것입니다. 이 동작을 수행 하는 권장 방법은 초기 업그레이드에서 지 문으로 대상 또는 목표 인증서를 선언 하는 것입니다. 그런 다음 이후에 CN으로의 전환을 완료 합니다. 클러스터가 이미 권장 되는 시작 상태 이면이 섹션을 건너뛸 수 있습니다.

변환에 유효한 시작 상태가 여러 개 있습니다. 고정은 클러스터가 CN으로 업그레이드를 시작할 때 이미 지문에 의해 선언 된 대상 인증서를 사용 하 고 있다는 것입니다. `GoalCert` `OldCert1` 이 문서에서는, 및을 고려 `OldCert2` 합니다.

#### <a name="valid-starting-states"></a>유효한 시작 상태

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`입니다. 여기에 `GoalCert` `NotAfter` 는의 이후 날짜가 있습니다. `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`입니다. 여기에 `GoalCert` `NotAfter` 는의 이후 날짜가 있습니다. `OldCert1`

클러스터가 앞에서 설명한 유효한 상태 중 하나가 아닌 경우이 문서의 끝에 있는 섹션에서 해당 상태를 달성 하는 방법에 대 한 정보를 참조 하세요.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>원하는 CN 기반 인증서 유효성 검사 체계를 선택 합니다.

앞에서 설명한 것 처럼 Service Fabric는 CN에서 암시적 트러스트 앵커를 사용 하 여 인증서를 선언 하거나 발급자 지문을 명시적으로 고정 하는 것을 지원 합니다. 자세한 내용은 [일반 이름 기반 인증서 유효성 검사 선언](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)을 참조 하세요.

이러한 메커니즘 중 하나를 선택 하는 것과 차이점을 잘 이해 하 고 있는지 확인 합니다. 구문적으로이 차이점과 choice는 매개 변수의 값에 따라 결정 됩니다 `certificateIssuerThumbprintList` . 비어 있는 경우 신뢰할 수 있는 루트 CA (트러스트 앵커)를 사용 하는 것을 의미 하는 반면, 지문 집합은 클러스터 인증서의 허용 된 직접 발급자를 제한 합니다.

   > [!NOTE]
   > 이 `certificateIssuerThumbprint` 필드를 사용 하 여 주체 CN에서 선언 된 인증서의 예상 직접 발급자를 지정할 수 있습니다. 허용 되는 값은 하나 이상의 쉼표로 구분 된 SHA1 지문입니다. 이 작업은 인증서 유효성 검사를 강화 합니다.
   >
   > 발급자를 지정 하지 않거나 목록이 비어 있는 경우 체인을 구축할 수 있는 경우 인증서가 인증을 받을 수 있습니다. 그러면 인증서가 유효성 검사기에서 신뢰 하는 루트에 종료 됩니다. 하나 이상의 발급자 지문이 지정 된 경우 체인에서 추출 된 직접 발급자의 지문이이 필드에 지정 된 값과 일치 하면 인증서가 허용 됩니다. 루트를 신뢰할 수 있는지 여부에 관계 없이 인증서가 허용 됩니다.
   >
   > PKI는 지정 된 주체를 사용 하 여 인증서에 서명 하는 데 다른 인증 기관 ( *발급자*라고도 함)을 사용할 수 있습니다. 이러한 이유로 해당 주체에 대해 예상 되는 모든 발급자 지문을 지정 하는 것이 중요 합니다. 즉, 인증서 갱신은 갱신 되는 인증서와 동일한 발급자가 서명 하는 것이 보장 되지 않습니다.
   >
   > 발급자 지정은 모범 사례로 간주 됩니다. 발급자를 생략 하면 신뢰할 수 있는 루트에 연결 하는 인증서에 대해 계속 작동 하지만,이 동작에는 제한이 있으며 가까운 장래에 사용할 수 있습니다. Azure에 배포 되 고, 개인 PKI에서 발급 된 X509 인증서로 보호 되 고, 주체에 의해 선언 된 클러스터는 Service Fabric (클러스터-서비스 통신의 경우)에서 유효성을 검사할 수 없습니다. 유효성 검사를 수행 하려면 PKI 인증서 정책을 검색 가능 하 고, 사용 가능 하며, 액세스할 수 있어야 합니다.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>클러스터의 Azure Resource Manager 템플릿 업데이트 및 배포

ARM (Azure Resource Manager) 템플릿을 사용 하 여 Service Fabric 클러스터를 관리 합니다. JSON 아티팩트를 사용 하는 대체 항목은 [Azure Resource Explorer (미리 보기)](https://resources.azure.com)입니다. 지금은 Azure Portal에서 해당 하는 환경을 사용할 수 없습니다.

기존 클러스터에 해당 하는 원본 템플릿을 사용할 수 없는 경우 Azure Portal에서 해당 하는 템플릿을 가져올 수 있습니다. 클러스터가 포함 된 리소스 그룹으로 이동 하 고 왼쪽의 **자동화** 메뉴에서 **템플릿 내보내기** 를 선택 합니다. 그런 다음 원하는 리소스를 선택 합니다. 최소한 가상 머신 확장 집합 및 클러스터 리소스를 내보내야 합니다. 생성 된 템플릿을 다운로드할 수도 있습니다. 이 템플릿은 완전히 배포 가능 하기 전에 변경 해야 할 수 있습니다. 템플릿은 원래 템플릿과 정확히 일치 하지 않을 수도 있습니다. 클러스터 리소스의 현재 상태를 반영 하는 것입니다.

필요한 변경 내용은 다음과 같습니다.

- 가상 컴퓨터 리소스에서 Service Fabric 노드 확장의 정의를 업데이트 하는 중입니다. 클러스터가 여러 노드 유형을 정의 하는 경우 해당 하는 각 가상 머신 확장 집합의 정의를 업데이트 해야 합니다.
- 클러스터 리소스 정의를 업데이트 하는 중입니다.

자세한 예제는 여기에 포함 되어 있습니다.

### <a name="update-the-virtual-machine-scale-set-resources"></a>가상 머신 확장 집합 리소스를 업데이트 합니다.
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

**ServiceFabric/클러스터** 리소스에서 **commonNames** 설정을 사용 하 여 **certificatecommonnames** 속성을 추가 하 고 **인증서** 속성을 완전히 제거 합니다 (모든 설정).

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

자세한 내용은 [지문 대신 인증서 일반 이름을 사용 하는 Service Fabric 클러스터 배포](./service-fabric-create-cluster-using-cert-cn.md)를 참조 하세요.

## <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포

변경을 수행한 후 업데이트 된 템플릿을 다시 배포 합니다.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>클러스터를 CN 기반 인증서 선언으로 변환 하기 위한 유효한 시작 상태 얻기

| 시작 상태 | 업그레이드 1 | 업그레이드 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 및 `GoalCert` 의 이후 `NotAfter` 날짜 `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 및 `OldCert1` 의 이후 `NotAfter` 날짜 `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`입니다. 여기에는 `OldCert1` 이후 날짜가 있습니다. `NotAfter``GoalCert` | 업그레이드 대상 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`입니다. 여기에는 `OldCert1` 이후 날짜가 있습니다. `NotAfter``GoalCert` | 업그레이드 대상 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | `OldCert1`상태를 가져오려면 또는 중 하나를 제거 `OldCert2` 합니다.`Thumbprint: OldCertx, ThumbprintSecondary: None` | 새 시작 상태에서 계속 |

이러한 업그레이드를 수행 하는 방법에 대 한 지침은 [Azure Service Fabric 클러스터에서 인증서 관리](service-fabric-cluster-security-update-certs-azure.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아봅니다.
* [일반 이름으로 클러스터 인증서를 롤오버](service-fabric-cluster-rollover-cert-cn.md)하는 방법에 대해 알아봅니다.
* [Touchless autorollover에 대 한 클러스터를 구성](cluster-security-certificate-management.md)하는 방법에 대해 알아봅니다.

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
