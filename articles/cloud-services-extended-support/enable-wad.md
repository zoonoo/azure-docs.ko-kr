---
title: Cloud Services에서 Windows Azure 진단 확장을 적용 합니다. (확장 지원)
description: Cloud Services에 대 한 Microsoft Azure 진단 확장 적용 (확장 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4511ad979312d58e0a1b9cce9b1280e9ca059007
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744577"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Cloud Services에서 Windows Azure 진단 확장을 적용 합니다. (확장 지원) 
클라우드 서비스의 주요 성능 메트릭을 모니터링할 수 있습니다. 모든 클라우드 서비스 역할은 최소 데이터에 해당하는 CPU 사용량, 네트워크 사용량 및 디스크 사용률을 수집합니다. 클라우드 서비스의 역할에 적용 되는 Microsoft. Azure 진단 확장이 있는 경우 해당 역할은 추가 데이터 요소를 수집할 수 있습니다. 자세한 내용은 [확장 개요](extensions.md) 를 참조 하세요.

[PowerShell](deploy-powershell.md) 또는 [ARM 템플릿을](deploy-template.md) 통해 Cloud Services (확장 지원)에 Windows Azure 진단 확장을 사용 하도록 설정할 수 있습니다.

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>PowerShell을 사용 하 여 Windows Azure 진단 확장 적용

```powershell
# Create WAD extension object
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
$extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>ARM 템플릿을 사용 하 여 Windows Azure 진단 확장 적용
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- Cloud Services (확장 지원)에 대 한 질문과 [대답](faq.md) 을 검토 합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용 하 여 클라우드 서비스 (확장 지원)를 배포 합니다.