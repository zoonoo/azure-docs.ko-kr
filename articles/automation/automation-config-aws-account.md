---
title: Amazon Web Services를 사용하여 Azure Automation Runbook 인증
description: 이 문서에서는 Amazon Web Services를 사용하여 Runbook을 인증하는 방법을 알아봅니다.
keywords: aws 인증, aws 구성
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837191"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Amazon Web Services를 사용하여 Runbook 인증

AWS(Amazon 웹 서비스)의 리소스를 사용하여 일반 작업을 자동화하는 일은 Azure의 Automation Runbook으로 할 수 있습니다. Azure의 리소스와 마찬가지로 Automation Runbook을 사용하여 AWS에서 많은 작업을 자동화할 수 있습니다. 인증을 하려면 Azure 구독이 있어야 합니다.

## <a name="obtain-aws-subscription-and-credentials"></a>AWS 구독 및 자격 증명 획득

AWS에서 인증하려면 AWS 구독을 획득하고 Azure Automation에서 실행 중인 Runbook을 인증하도록 AWS 자격 증명을 지정해야 합니다. 필요한 특정 자격 증명은 AWS 액세스 키 및 비밀 키입니다. [AWS 자격 증명 사용](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)을 참조하세요.

## <a name="configure-automation-account"></a>Automation 계정 구성

기존 Automation 계정을 사용하여 AWS를 인증할 수 있습니다. 또는 AWS 리소스를 대상으로 하는 Runbook용 계정을 지정할 수 있습니다. 이 경우 새 [Automation 계정](automation-create-standalone-account.md)을 만듭니다.  

## <a name="store-aws-credentials"></a>AWS 자격 증명 저장

AWS 자격 증명을 Azure Automation에서 자산으로 저장해야 합니다. 액세스 키 및 비밀 키 만들기에 대한 지침은 [AWS 계정에 대한 액세스 키 관리](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)를 참조하세요. 키를 사용할 수 있는 경우 액세스 키 ID와 비밀 키 ID를 안전한 장소에 복사합니다. 키 파일을 다운로드하여 안전한 위치에 저장할 수 있습니다.

## <a name="create-credential-asset"></a>자격 증명 자산 만들기

AWS 보안 키를 만들고 복사한 후 Automation 계정을 사용하여 자격 증명 자산을 만들어야 합니다. 자산을 사용하면 AWS 키를 안전하게 저장하고 Runbook에서 참조할 수 있습니다. [Azure Portal을 사용하여 새 자격 증명 자산 만들기](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal)를 참조하세요. 제공된 필드에 다음의 AWS 정보를 입력합니다.
    
* **Name** - **AWScred** 또는 본인의 명명 기준에 따라 적합한 값을 입력합니다.
* **사용자 이름** - 액세스 ID
* **암호** - 비밀 키의 이름 

## <a name="next-steps"></a>다음 단계

* AWS에서 작업을 자동화하는 Runbook을 만드는 방법에 대한 자세한 내용은 [Runbook을 사용하여 Amazon Web Services VM 배포](automation-scenario-aws-deployment.md)를 참조하세요.