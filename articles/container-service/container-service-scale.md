---
title: "Azure CLI로 ACS 클러스터 크기 조정 | Microsoft Docs"
description: "Azure CLI를 사용하여 Azure Container Service의 크기를 조정하는 방법"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure"
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>Azure Container Service 크기 조정
Azure CLI 도구를 사용하여 ACS(Azure Container Service)의 노드 수를 조정할 수 있습니다. Azure CLI를 사용하여 크기를 조정할 때는 도구가 컨테이너에 대한 변경 사항을 나타내는 새로운 구성 파일을 반환합니다.

## <a name="about-the-command"></a>명령 정보
Azure CLI는 Azure 컨테이너와 상호 작용할 수 있게 Azure Resource Manager 모드에 있어야 합니다. `azure config mode arm`을 호출하여 Resource Manager 모드로 전환할 수 있습니다.  `acs` 명령에는 컨테이너 서비스의 모든 크기 조정 작업을 수행하는 자식 명령인 `scale`이 있습니다. 다음과 유사한 내용을 출력하는 `azure acs scale --help`를 실행하여 scale 명령에서 사용되는 여러 매개 변수에 대한 도움말을 얻을 수 있습니다.

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>명령을 사용하여 크기 조정
컨테이너 서비스 크기를 조정하려면 먼저 **리소스 그룹**과 **ACS(Azure Container Service) 이름**을 알아야 하며 새 에이전트 수도 지정해야 합니다. 더 작거나 큰 크기를 통해 각각 축소 또는 확장할 수 있습니다.

크기 조정에 앞서 컨테이너 서비스의 현재 에이전트 수를 파악하고자 할 수 있습니다. `azure acs show <resource group> <ACS name>` 명령을 사용하여 ACS 구성을 반환합니다. <mark>Count</mark> 결과를 참고합니다.

#### <a name="see-current-count"></a>현재 개수 참조
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>새 개수로 크기 조정
이미 스스로 드러나 있으므로 `azure acs scale`을 호출하고 **리소스 그룹**, **ACS 이름** 및 **에이전트 수**를 제공하여 컨테이너 서비스 크기를 조정할 수 있습니다. 컨테이너 서비스 크기를 조절할 때는 Azure CLI가 새 에이전트 수 등, 컨테이너의 새 구성을 나타내는 JSON 문자열을 반환합니다.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>다음 단계
* [클러스터 배포](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


