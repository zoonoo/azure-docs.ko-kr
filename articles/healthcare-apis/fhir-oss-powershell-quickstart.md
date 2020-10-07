---
title: 'PowerShell: FHIR Server for Azure 배포 - Azure API for FHIR'
description: 이 빠른 시작에서는 PowerShell을 사용하여 Microsoft 오픈 소스 FHIR 서버를 배포하는 방법을 설명합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c133a309cc85ffcfb69be2ae7bbb614cbb540f2e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87847145"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>빠른 시작: PowerShell을 사용하여 오픈 소스 FHIR 서버 배포

이 빠른 시작에서는 PowerShell을 사용하여 Azure용 오픈 소스 Microsoft FHIR 서버를 배포하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

프로비저닝된 리소스를 포함할 리소스 그룹의 이름을 선택하고 만듭니다.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>FHIR 서버 템플릿 배포

Azure용 Microsoft FHIR 서버 [GitHub 리포지토리](https://github.com/Microsoft/fhir-server)에는 필요한 모든 리소스를 배포하는 템플릿이 포함되어 있습니다. 다음을 사용하여 배포합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>FHIR 서버가 실행 중인지 확인

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

서버가 처음 응답하는 데 1분 정도 소요됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 리소스 그룹을 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure용 Microsoft 오픈 소스 FHIR 서버를 구독에 배포했습니다. Postman을 사용하여 FHIR API에 액세스하는 방법을 알아보려면 Postman 자습서로 이동합니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR API에 액세스](access-fhir-postman-tutorial.md)
