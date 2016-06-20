<properties
	pageTitle="Azure Resource Manager에서 가상 컴퓨터에 대한 주요 자격 증명 모음 설정 | Microsoft Azure"
	description="Azure Resource Manager에서 사용할 주요 자격 증명 모음을 설정하는 방법"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Azure Resource Manager에서 가상 컴퓨터에 대한 주요 자격 증명 모음 설정

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델

Azure Resource Manager 스택에서 비밀/인증서는 주요 자격 증명 모음 리소스 공급자가 제공하는 리소스로 모델링됩니다. 주요 자격 증명 모음에 대한 자세한 내용을 보려면 [Azure 주요 자격 증명 모음이란?](../key-vault/key-vault-whatis.md)을 참조하세요.

## 설정
주요 자격 증명 모음을 Azure Resource Manager 가상 컴퓨터에서 사용하려면 주요 자격 증명에 대한 *EnabledForDeployment* 속성을 true로 설정해야 합니다. 아래와 같은 다양한 클라이언트에서 이 작업을 수행할 수 있습니다."

## CLI
CLI를 사용하여 주요 자격 증명 모음을 만들려면 [CLI를 사용하여 주요 자격 증명 모음 관리](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)를 참조하세요.

CLI의 경우 먼저 주요 자격 증명 모음을 만든 다음 배포 정책을 사용하도록 설정해야 합니다. 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## 템플릿
템플릿을 사용하는 동안 `enabledForDeployment` 속성을 주요 증명 모음 리소스에 대한 `true`로 설정해야 합니다.

	{
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

템플릿을 통해 주요 자격 증명 모음을 만드는 동안 구성할 수 있는 다른 옵션의 경우 [여기](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)를 참조하세요.

<!---HONumber=AcomDC_0608_2016-->