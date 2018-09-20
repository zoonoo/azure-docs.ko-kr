---
title: Azure Stack에서 명령줄을 사용 하 여 템플릿 배포 | Microsoft Docs
description: Azure Stack에 템플릿을 배포 하는 플랫폼 간 명령줄 인터페이스 (CLI)를 사용 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d40f9e8bf319f327879a71b23f9d9b82da924c2f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365437"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>명령줄을 사용하여 Azure Stack의 템플릿 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 개발 키트 환경에서 Azure Resource Manager 템플릿을 배포 하는 명령줄을 사용 합니다. Azure Resource Manager 템플릿 배포 및 조정 된 단일 작업에서 응용 프로그램에 대 한 모든 리소스를 프로 비전 합니다.

## <a name="before-you-begin"></a>시작하기 전에

 - [설치 하 고 연결](azure-stack-version-profiles-azurecli2.md) Azure CLI를 사용 하 여 Azure Stack에 있습니다.
 - 파일을 다운로드 *azuredeploy.json* 하 고 *azuredeploy.parameters.json* 에서 [저장소 계정에 대 한 예제 서식 파일 만들기](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account)합니다.
 
## <a name="deploy-template"></a>템플릿 배포

이러한 파일 다운로드 된 폴더로 이동한 템플릿을 배포 하려면 다음 명령을 실행 합니다.

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

이 명령은 리소스 그룹에 템플릿을 배포 **cliRG** Azure Stack POC 기본 위치에 있습니다.

## <a name="validate-template-deployment"></a>템플릿 배포 유효성 검사

이 리소스 그룹 및 저장소 계정을 보려면 다음 명령을 사용합니다.

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>다음 단계

템플릿 배포에 대 한 자세한 내용은 다음을 참조 하세요.

[PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)

