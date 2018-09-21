---
title: Azure CLI 스크립트 예제 - 파일 컨테이너에 업로드 | Microsoft Docs
description: Azure CLI 스크립트를 사용하여 로컬 파일을 스토리지 컨테이너에 업로드합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 61a5a59336878976a20a6ffd7b4e4d6d27061453
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35764032"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>CLI 예: 컨테이너에 로컬 파일 업로드 

이 문서의 Azure CLI 스크립트는 로컬 파일을 저장 컨테이너에 업로드하는 방법을 보여줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/upload-file-asset/UploadFile-Asset.sh "Upload a file")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure CLI 예](../cli-samples.md)를 참조하세요.
