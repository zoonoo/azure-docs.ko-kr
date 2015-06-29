<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Azure CLI 사용

다음 단계에서는 최신 버전 및 적절한 구독에서 Azure CLI를 쉽게 사용할 수 있도록 도와줍니다. 먼저 Azure CLI를 설치하고 계정에 연결해야 하는 경우 [Azure CLI(Azure 명령줄 인터페이스)](xplat-cli.md)를 참조하세요.

### 1단계: Azure CLI 버전 업데이트

서비스 관리 모드에서 명령에 Azure CLI를 사용하려면 가급적 최신 버전이 있어야 합니다. 사용 중인 버전을 확인하려면 `azure --version`을 입력합니다. 다음과 유사한 결과가 표시됩니다.

    $ azure --version
    0.8.17 (node: 0.10.25)

Azure CLI의 버전을 업데이트하려면 [Azure CLI](https://github.com/Azure/azure-xplat-cli)를 참조하세요.

### 2단계: Azure 계정 및 구독 설정

사용하려는 계정에 Azure CLI를 연결한 후 둘 이상의 구독이 있을 수 있습니다. 이 경우 `azure account list`를 입력하여 계정에 사용 가능한 구독을 검토한 다음 `azure account set <subscription id or name> true`(여기서 _subscription id or name_은 현재 세션에서 사용할 구독 ID 또는 구독 DLFMA)를 입력하여 사용할 구독을 선택해야 합니다. 다음과 유사한 출력이 표시됩니다.

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE]Azure 계정이 아직 없지만 MSDN 구독이 있는 경우 [여기에서 MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하여 무료 Azure 크레딧을 받거나 무료 계정을 사용할 수 있습니다. 둘 중 하나가 Azure 액세스에 작동합니다.

<!---HONumber=58_postMigration-->