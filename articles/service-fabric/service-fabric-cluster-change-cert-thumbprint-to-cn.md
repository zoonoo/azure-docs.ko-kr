---
title: 인증서 일반 이름을 사용 하도록 클러스터 업데이트
description: Service Fabric 클러스터 인증서를 지문 기반 선언에서 일반 이름으로 변환 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368063"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>클러스터 인증서를 지문 기반 선언에서 일반 이름으로 변환
인증서의 서명 (것 구어체 ' 지문 ' 이라고 함)은 고유 합니다. 즉, 지 문으로 선언 된 클러스터 인증서는 특정 인증서 인스턴스를 참조 합니다. 이렇게 하면 일반적으로 어렵고 명시적으로 인증서 롤오버 및 관리가 수행 됩니다. 각 변경에는 클러스터 및 기본 컴퓨팅 호스트의 오케스트레이션 업그레이드가 필요 합니다. 인증서의 주체 일반 이름에 따라 지문 기반 선언에서 Service Fabric 클러스터의 인증서 선언을 변환 하면 관리가 간단해 집니다. 특히 인증서를 롤백하여 클러스터를 업그레이드할 필요가 없습니다. 이 문서에서는 가동 중지 시간 없이 기존 클러스터를 일반 이름 기반 선언으로 변환 하는 방법을 설명 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>CA (인증 기관) 서명 된 인증서로 이동
인증서가 지 문으로 선언 된 클러스터의 보안은 불가능 하다는 사실을 작업 하거나, 다른 인증서와 같은 서명을 사용 하 여 인증서를 위조 하기 위해 계산을 수행할 수 있습니다. 이 경우 인증서의 provenance는 중요 하지 않으므로 자체 서명 된 인증서가 적절 합니다. 반대로, 일반 이름으로 선언 된 인증서를 사용 하는 클러스터의 보안은 해당 인증서를 발급 한 PKI (공개 키 인프라 서비스)에서 전달 되 고, 해당 인증 방법과 같은 측면을 포함 합니다. 이러한 이유로 PKI 선택은 중요 합니다. 발급자 (인증 기관 또는 CA)에 대 한 지식이 필요 하 고 자체 서명 된 인증서는 본질적으로 쓸모가 없습니다. CN (일반 이름)으로 선언 되는 인증서는 일반적으로 체인을 성공적으로 구축할 수 있는 경우 유효한 것으로 간주 되 고, 주체에 필요한 CN 요소가 있으며, 발급자 (체인의 바로 이상)가 유효성 검사를 수행 하는 에이전트에 의해 신뢰 됩니다. Service Fabric는 CN에서 ' 암시적인 ' 발급자 (체인이 트러스트 앵커로 끝나야 함) 또는 손도장 ("발급자 고정")으로 선언 된 발급자를 사용 하 여 인증서 선언을 지원 합니다. 자세한 내용은이  [문서](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) 를 참조 하세요. 지문에 의해 선언 된 자체 서명 된 인증서를 사용 하 여 클러스터를 일반 이름으로 변환 하려면 대상 CA 서명 인증서를 지문을 통해 클러스터에 먼저 추가 해야 합니다. 그러면 TP에서 CN으로의 변환만 가능 합니다.

테스트를 위해 자체 서명 된 인증서를 CN으로 선언 하 여 발급자를 자체 지문에 고정할 수 있습니다. 보안 관점에서이는 TP로 동일한 인증서를 선언 하는 것과 거의 동일 합니다. 그러나 이러한 종류의 성공적인 변환은 CA 서명 인증서를 사용 하 여 TP에서 CN으로 성공적으로 변환 하는 것을 보장 하지 않습니다. 따라서 적절 한 CA 서명 인증서를 사용 하 여 변환을 테스트 하는 것이 좋습니다 (무료 옵션 있음).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>인증서를 업로드하고 확장 집합에 설치
Azure에서 인증서를 가져오고 프로 비전 하는 데 권장 되는 메커니즘에는 Azure Key Vault 서비스와 도구가 포함 됩니다. 클러스터 인증서 선언과 일치 하는 인증서는 클러스터를 구성 하는 가상 머신 확장 집합의 모든 노드에 프로 비전 되어야 합니다. 자세한 내용은 [가상 머신 확장 집합의 암호](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) 를 참조 하세요. 클러스터의 인증서 선언에 변경 내용을 적용 하기 전에 클러스터의 모든 노드 형식에 대 한 Vm에 현재 및 대상 클러스터 인증서를 모두 설치 하는 것이 중요 합니다. Service fabric 노드에 프로 비전 하기 위한 인증서 발급에서의 경험을 자세히 설명 합니다 [.](cluster-security-certificate-management.md#the-journey-of-a-certificate)

## <a name="bring-cluster-to-an-optimal-starting-state"></a>클러스터를 최적의 시작 상태로 전환
인증서 선언을 지문 기반에서 일반 이름 기반으로 변환 하는 것이 둘 다에 영향을 줍니다.

- 클러스터의 각 노드가 해당 자격 증명을 찾아서 다른 노드에 표시 하는 방법
- 보안 연결을 설정할 때 각 노드가 대응 하는 자격 증명의 유효성을 검사 하는 방법  

계속 하기 전에 [두 구성에 대 한 프레젠테이션 및 유효성 검사 규칙](cluster-security-certificates.md#certificate-configuration-rules) 을 검토 합니다. 지문 대 일반 이름 변환을 수행할 때 가장 중요 한 고려 사항은 업그레이드 되 고 아직 업그레이드 되지 않은 노드 (즉, 다른 업그레이드 도메인에 속하는 노드)는 업그레이드 하는 동안 언제 든 지 성공적인 상호 인증을 수행할 수 있어야 한다는 것입니다. 이를 위해서는 초기 업그레이드에서 지 문으로 대상/목표 인증서를 선언 하 고, 다음에 일반 이름으로의 전환을 완료 하는 것이 좋습니다. 클러스터가 이미 권장 되는 시작 상태 이면이 섹션을 건너뛸 수 있습니다.

변환에 유효한 시작 상태가 여러 개 있습니다. 고정은 클러스터가 일반 이름으로 업그레이드를 시작할 때 이미 지문에 의해 선언 된 대상 인증서를 사용 하 고 있다는 것입니다. , 다음을 고려 `GoalCert` 합니다 `OldCert1` `OldCert2` .

#### <a name="valid-starting-states"></a>유효한 시작 상태
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`입니다. 여기에 `GoalCert` `NotAfter` 는의 이후 날짜가 있습니다. `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`입니다. 여기에 `GoalCert` `NotAfter` 는의 이후 날짜가 있습니다. `OldCert1`

클러스터가 위에서 설명한 유효한 상태 중 하나가 아닌 경우이 문서의 끝에서 해당 상태를 달성 하는 방법에 대 한 부록을 참조 하세요.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>원하는 일반 이름 기반 인증서 유효성 검사 체계를 선택 합니다.
앞에서 설명한 것 처럼 Service Fabric는 CN에서 암시적 트러스트 앵커를 사용 하 여 인증서 선언을 지원 하거나 발급자 지문을 명시적으로 고정 하는 것을 지원 합니다. 자세한 내용은 [이 문서](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) 를 참조 하 고 차이점을 이해 하 고 두 가지 메커니즘을 선택 하는 것에 대 한 영향을 확인 하세요. 구문적으로이 차이/선택은 매개 변수의 값에 따라 결정 됩니다 `certificateIssuerThumbprintList` . empty는 신뢰할 수 있는 루트 CA (트러스트 앵커)에 의존 하는 반면, 지문 집합은 클러스터 인증서의 허용 되는 직접 발급자를 제한 합니다.

   > [!NOTE]
   > ' CertificateIssuerThumbprint ' 필드를 사용 하면 주체 일반 이름으로 선언 된 인증서의 예상 직접 발급자를 지정할 수 있습니다. 허용 되는 값은 하나 이상의 쉼표로 구분 된 SHA1 지문입니다. 인증서 유효성 검사를 강화 하는 것입니다. 발급자가 지정 되지 않은 경우 목록이 비어 있으면 인증서가 해당 체인을 빌드할 수 있는 경우 인증을 받을 수 있으며, 유효성 검사기에서 신뢰 하는 루트에서 끝납니다. 하나 이상의 발급자 지문이 지정 된 경우 체인에서 추출 된 직접 발급자의 지문이 루트의 신뢰 여부와 관계 없이이 필드에 지정 된 값과 일치 하는 경우 인증서가 허용 됩니다. PKI는 지정 된 주체를 사용 하 여 인증서에 서명 하는 데 다른 인증 기관 (' 발급자 ')을 사용할 수 있으므로 해당 주체에 대해 필요한 모든 발급자 지문을 지정 하는 것이 중요 합니다. 즉, 인증서 갱신은 갱신 되는 인증서와 동일한 발급자가 서명 하는 것이 보장 되지 않습니다.
   >
   > 발급자를 지정하는 것은 모범 사례로 간주됩니다. 이를 생략하더라도 신뢰할 수 있는 루트에 연결된 인증서의 경우 작업은 계속 진행되지만 이러한 동작에는 제한이 있으며 가까운 시일 내에 단계적으로 중단될 수 있습니다. 또한 PKI의 인증서 정책을 검색할 수 없고 사용할 수 없고 액세스할 수 없는 경우, Azure에 배포되고 프라이빗 PKI에서 발급된 X509 인증서로 보안되고 제목별로 선언된 클러스터는 Azure Service Fabric 서비스(클러스터-서비스 통신용)에서 유효성을 검사하지 못할 수 있습니다. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>클러스터의 ARM (Azure 리소스 관리) 템플릿 업데이트 및 배포
ARM 템플릿을 사용 하 여 Azure Service Fabric 클러스터를 관리 하는 것이 좋습니다. json 아티팩트를 사용 하는 대안은 [Azure Resource Explorer (미리 보기)](https://resources.azure.com)입니다. 지금은 Azure Portal에서 해당 하는 환경을 사용할 수 없습니다. 기존 클러스터에 해당 하는 원본 템플릿을 사용할 수 없는 경우 클러스터를 포함 하는 리소스 그룹으로 이동 하 고, **자동화** 왼쪽 메뉴에서 **템플릿 내보내기** 를 선택 하 고, 원하는 리소스를 추가로 선택 하 여 Azure Portal에서 해당 하는 템플릿을 가져올 수 있습니다. 최소한 가상 머신 확장 집합 및 클러스터 리소스를 내보내야 합니다. 생성 된 템플릿을 다운로드할 수도 있습니다. 이 템플릿은 완전히 배포 가능 하기 전에 변경 해야 할 수 있으며 원래 이름과 정확히 일치 하지 않을 수 있습니다 .이는 클러스터 리소스의 현재 상태를 반영 하는 것입니다.

필요한 변경 내용은 다음과 같습니다.
    - 가상 컴퓨터 리소스에서 Service Fabric 노드 확장의 정의를 업데이트 하는 중입니다. 클러스터가 여러 노드 유형을 정의 하는 경우 해당 하는 각 가상 머신 확장 집합의 정의를 업데이트 해야 합니다.
    - 클러스터 리소스 정의를 업데이트 하는 중

자세한 예는 아래에 포함 되어 있습니다.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>가상 머신 확장 집합 리소스를 업데이트 하는 중
From
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
대상
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

### <a name="updating-the-cluster-resource"></a>클러스터 리소스를 업데이트 하는 중
**ServiceFabric/클러스터** 리소스에서 **commonNames** 설정을 사용 하 여 **certificatecommonnames** 속성을 추가 하 고 **인증서** 속성을 완전히 제거 합니다 (모든 설정).

From
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
대상
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

자세한 내용은 [지문 대신 인증서 일반 이름을 사용 하는 Service Fabric 클러스터 배포](./service-fabric-create-cluster-using-cert-cn.md) 를 참조 하세요.

## <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포
변경 후 업데이트된 템플릿을 다시 배포합니다.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>부록: 클러스터를 CN 기반 인증서 선언으로 변환 하기 위한 유효한 시작 상태 얻기

| 시작 상태 | 업그레이드 1 | 업그레이드 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 및 `GoalCert` 의 이후 `NotAfter` 날짜 `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 및 `OldCert1` 의 이후 `NotAfter` 날짜 `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`입니다. 여기에는 `OldCert1` 이후 날짜가 있습니다. `NotAfter``GoalCert` | 업그레이드 대상 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`입니다. 여기에는 `OldCert1` 이후 날짜가 있습니다. `NotAfter``GoalCert` | 업그레이드 대상 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | `OldCert1`상태를 가져오려면 또는 중 하나를 제거 `OldCert2` 합니다.`Thumbprint: OldCertx, ThumbprintSecondary: None` | 새 시작 상태에서 계속 |

이러한 업그레이드를 수행 하는 방법에 대 한 지침은 [이 문서](service-fabric-cluster-security-update-certs-azure.md)를 참조 하세요.


## <a name="next-steps"></a>다음 단계
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아봅니다.
* [일반 이름으로 클러스터 인증서를 롤오버](service-fabric-cluster-rollover-cert-cn.md) 하는 방법 알아보기
* [Touchless autorollover에 대 한 클러스터를 구성](cluster-security-certificate-management.md) 하는 방법 알아보기

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
