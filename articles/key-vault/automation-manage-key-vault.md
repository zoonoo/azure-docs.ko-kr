<properties
	pageTitle="Azure 자동화를 사용하여 Azure 주요 자격 증명 모음 관리 | Microsoft Azure"
	description="Azure 자동화 사버시를 사용하여 Azure 주요 자격 증명 모음을 관리하는 방법에 대해 알아봅니다."
	services="Key-Vault, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="csand"/>



#Azure 자동화를 사용하여 Azure 주요 자격 증명 모음 관리

이 가이드에서는 Azure 자동화 서비스 및 이 서비스를 사용하여 Azure 주요 자격 증명 모음에서 키 및 암호 관리를 간소화하는 방법에 대해 소개합니다.

## Azure 자동화 정의

[Azure 자동화](http://azure.microsoft.com/services/automation/)는 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure 자동화, 수동, 빈번하게 반복되는, 장기 실행 및 오류가 발생하기 쉬운 작업을 사용하면 조직의 안정성, 효율성 및 가치 창출 시간을 개선하도록 자동화할 수 있습니다.

Azure 자동화는 요구 사항에 맞게 크기가 조정되는 매우 안정적이며 가용성이 높은 워크플로 실행 엔진을 제공합니다. Azure 자동화에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure 자동화에서 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT 및 DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.


## Azure 자동화를 통해 Azure 주요 자격 증명 모음을 쉽게 관리하려면 어떻게 해야 하나요?

[Azure PowerShell 도구](https://msdn.microsoft.com/library/azure/jj156055.aspx)에서 사용할 수 있는 [Azure 주요 자격 증명 모음 cmdlet](https://msdn.microsoft.com/library/azure/dn868052.aspx)을 통해 Azure 자동화에서 주요 자격 증명 모음을 관리할 수 있습니다. Azure 자동화에서는 이러한 cmdlet을 기본적으로 사용할 수 있으므로 서비스 내에서 주요 자격 증명 모음 관리 작업을 수행할 수 있습니다. Azure 자동화에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

Azure 주요 자격 증명 모음 cmdlet 사용하면 키 만들기 또는 가져오기, 암호 만들기 또는 업데이트, 키 특성 업데이트, 키 또는 암호 가져오기, 키 또는 암호 삭제 등의 작업을 수행할 수 있습니다


## 다음 단계

Azure 자동화의 기본 사항과 이를 사용하여 Azure 주요 자격 증명 모음을 관리하는 방법을 알아보았으므로, 이제 다음 링크에 따라 Azure 자동화에 대해 자세히 알아보세요.

* Azure 자동화 [시작 자습서](../automation-create-runbook-from-samples.md)를 참조하세요.
* [Azure 주요 자격 증명 모음 PowerShell 스크립트](https://gallery.technet.microsoft.com/scriptcenter/Azure-Key-Vault-Powershell-1349b091)를 참조하세요.

<!---HONumber=Oct15_HO3-->