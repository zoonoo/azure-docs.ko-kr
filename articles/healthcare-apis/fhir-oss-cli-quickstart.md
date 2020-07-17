---
title: 'Azure CLI: Azure용 오픈 소스 FHIR 서버 배포 - Azure API for Azure'
description: 이 빠른 시작에서는 Azure용 오픈 소스 Microsoft FHIR 서버를 배포하는 방법을 설명합니다.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2020
ms.locfileid: "84819978"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 오픈 소스 FHIR 서버 배포

이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 오픈 소스 FHIR&reg; 서버를 배포하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>리소스 그룹 만들기

프로비저닝된 리소스를 포함할 리소스 그룹의 이름을 선택하고 만듭니다.

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>템플릿 배포

Azure용 Microsoft FHIR 서버 [GitHub 리포지토리](https://github.com/Microsoft/fhir-server)에는 필요한 모든 리소스를 배포하는 템플릿이 포함되어 있습니다. 다음을 사용하여 배포합니다.

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>FHIR 서버가 실행 중인지 확인

다음을 사용하여 FHIR 서버에서 기능 문을 가져옵니다.

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

서버가 처음 응답하는 데 1분 정도 소요됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure용 Microsoft 오픈 소스 FHIR 서버를 구독에 배포했습니다. Postman을 사용하여 FHIR API에 액세스하는 방법을 알아보려면 Postman 자습서로 이동합니다.
 
>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR API에 액세스](access-fhir-postman-tutorial.md)