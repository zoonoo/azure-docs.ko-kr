---
title: Azure CLI 스크립트 예제 - 자산 게시 | Microsoft Docs
description: Azure CLI 스크립트를 사용하여 자산을 게시합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 1ce85c18b93f22e9078e551deda74c6e6ff0b28b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614925"
---
# <a name="cli-example-publish-an-asset"></a>CLI 예: 자산 게시

이 문서의 Azure CLI 스크립트는 스트리밍 로케이터를 만들고 스트리밍 URL을 다시 가져오는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건 

- CLI를 로컬로 설치하여 사용하기 위해 이 문서에는 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

    현재 일부 [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) 명령만 Azure Cloud Shell에서 작동합니다. CLI를 로컬로 사용하는 것이 좋습니다.

- [Media Services 계정 만들기](../create-account-cli-how-to.md)

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure CLI 예](../cli-samples.md)를 참조하세요.
