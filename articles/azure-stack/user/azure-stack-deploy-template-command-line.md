---
title: "Azure 스택에서 명령줄을 사용 하 여 템플릿을 배포 | Microsoft Docs"
description: "크로스 플랫폼 명령줄 인터페이스 (CLI)를 사용 하 여 Azure 스택에 템플릿을 배포 하는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: d58d80d89bb2544c4d4a34f608177a96760406ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>명령줄을 사용하여 Azure Stack의 템플릿 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 개발 키트를 Azure 리소스 관리자 템플릿을 배포 하는 명령줄을 사용 합니다. Azure 리소스 관리자 템플릿 배포 및 조정를 단일 작업에서 응용 프로그램에 대 한 모든 리소스를 프로 비전 합니다.

## <a name="before-you-begin"></a>시작하기 전에
 - [설치 하 고 연결](azure-stack-connect-cli.md) Azure CLI와 Azure 스택
 - 파일을 다운로드 *azuredeploy.json* 및 *azuredeploy.parameters.json* 에서 [저장소 계정에 대 한 예제 서식 파일 만들기](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account)합니다.
 
## <a name="deploy-template"></a>템플릿 배포
폴더로 이동 하 여 이러한 파일 된 다운로드 하 고는 서식 파일을 배포 하려면 다음 명령을 실행 합니다.

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

이 명령은 서식 파일을 리소스 그룹에 배포 **cliRG** Azure 스택 POC 기본 위치에 있습니다.

## <a name="validate-template-deployment"></a>템플릿 배포 유효성 검사
이 리소스 그룹 및 저장소 계정을 보려면 다음 명령을 사용합니다.

    azure group list

    azure storage account list




