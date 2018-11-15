---
title: Azure Service Fabric Mesh에 대한 일반적인 질문 | Microsoft Docs
description: Azure Service Fabric Mesh에 대한 일반적인 질문과 대답을 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: f80f61cbfc1f7b719e73d7d29c6948bebe84aa6c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278313"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Service Fabric Mesh에 대한 일반적인 질문
Azure Service Fabric Mesh는 개발자가 가상 머신, 저장소 또는 네트워킹을 관리하지 않고 마이크로 서비스 응용 프로그램을 배포할 수 있는 완전히 관리되는 서비스입니다. 이 문서에는 일반적인 질문에 대한 답변이 있습니다.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>문제를 보고하거나 질문을 하려면 어떻게 하나요?

질문을 하고, Microsoft 엔지니어의 답변을 받은 다음, [service-fabric-mesh-preview GitHub 리포지토리](https://aka.ms/sfmeshissues)에서 문제를 보고합니다.

## <a name="quota-and-cost"></a>할당량 및 비용

**미리 보기에 참여하는 비용은 얼마인가요?**

현재 Mesh 미리 보기에 애플리케이션 또는 컨테이너를 배포하기 위한 요금은 없습니다. 하지만 테스트를 적극적으로 진행하지 않는 경우 배포된 리소스를 삭제하고 더 이상 실행하지 않는 것이 좋습니다.

**코어 수와 RAM의 할당량 제한이 있나요?**

예, 각 구독의 할당량은 다음과 같이 설정됩니다.

- 애플리케이션 수 - 5 
- 애플리케이션당 코어 수 - 12 
- 응용 프로그램당 총 RAM - 48GB 
- 네트워크 및 수신 엔드포인트 수 - 5  
- 연결할 수 있는 Azure 볼륨 - 10 
- 서비스 복제본 수 - 3 
- 배포할 수 있는 최대 컨테이너는 4코어, 16GB RAM으로 제한됩니다.
- 최대 6개 코어까지 코어 0.5개 증분으로 부분 코어를 컨테이너에 할당할 수 있습니다.

**애플리케이션을 배포한 후 얼마나 오래 두어도 되나요?**

현재 애플리케이션 수명은 2일로 제한되어 있습니다. 미리 보기에 할당된 체험용 코어의 사용률을 최대화하기 위한 조치입니다. 결과적으로, 주어진 배포를 연속 48시간 동안 실행할 수 있으며, 이 시간이 지나면 시스템에서 배포를 종료합니다. 이 경우 Azure CLI에서 `az mesh app show` 명령을 실행하고 `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 메시지가 반환되는지 확인하여 시스템에서 배포를 종료한 것인지 확인할 수 있습니다. 

예:  

```cli
chackdan@Azure:~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

동일한 애플리케이션을 메시에 계속 배포하려면 애플리케이션에 연결된 리소스 그룹을 삭제하거나, 애플리케이션 및 모든 관련 메시 리소스(네트워크 포함)를 개별적으로 제거해야 합니다. 

리소스 그룹을 삭제하려면 `az group delete <nameOfResourceGroup>` 명령을 사용합니다. 

## <a name="supported-container-os-images"></a>지원되는 컨테이너 OS 이미지
서비스를 배포할 때 다음 컨테이너 OS 이미지를 사용할 수 있습니다.

- Windows - windowsservercore 및 nanoserver
    - Windows Server 2016
    - Windows Server 버전 1709
- Linux
    - 알려진 제한 사항 없음

## <a name="features-gaps-and-known-issues"></a>기능 간격 및 알려진 문제

**내 응용 프로그램을 배포한 후 응용 프로그램과 관련된 네트워크 리소스가 IP 주소를 받지 못하는 것 같음**

현재, IP 주소가 지연 후에 표시되는 알려진 문제가 있습니다. 관련 IP 주소를 보려면 몇 분 후에 네트워크 리소스의 상태를 확인하세요.

**내 응용 프로그램이 올바른 네트워크/볼륨 리소스에 액세스하지 못함**

응용 프로그램 모델에서 관련 리소스에 액세스할 수 있으려면 네트워크 및 볼륨의 전체 리소스 ID를 사용해야 합니다. 빠른 시작 샘플에서 이 ID는 다음과 같이 표시됩니다.

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**현재 응용 프로그램 모델에서 내 비밀을 암호화하는 방법을 지원하지 않는 것 같음**

예, 비밀 암호화는 현재 비공개 미리 보기에서 지원되지 않습니다. 

**DNS가 Service Fabric 개발 클러스터와 Mesh에서 동일한 방식으로 작동하지 않음**

로컬 개발 클러스터와 Azure Mesh에서 서비스를 다르게 참조해야 하는 알려진 문제가 있습니다. 로컬 개발 클러스터에서는 {serviceName}.{applicationName}을 사용합니다. Azure Service Fabric Mesh에서는 {servicename}을 사용합니다. Azure Mesh는 현재 응용 프로그램 간의 DNS 확인을 지원하지 않습니다.

Windows 10에서 Service Fabric 개발 클러스터를 실행하는 경우와 관련해서 다른 알려진 DNS 문제가 있는 경우, [Windows 컨테이너 디버그](/azure/service-fabric/service-fabric-how-to-debug-windows-containers)를 참조하세요.

**CLI 모듈을 사용할 때 다음 오류가 표시됩니다. ImportError: cannot import name ‘sdk_no_wait’(ImportError: ‘sdk_no_wait’ 이름을 가져올 수 없습니다)**

2.0.30 이전의 CLI 버전을 사용하는 경우, 이 오류가 표시될 수 있습니다.

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**CLI 확장 패키지를 설치할 때 배포 이름 불일치 오류가 표시됨**

확장이 설치되지 않았음을 의미하는 것은 아닙니다. 문제 없이 CLI 명령을 계속 사용할 수 있습니다.

**규모를 확장할 때 실행 중인 컨테이너를 포함하여 내 컨테이너가 모두 영향을 받음**

이는 버그이며, 다음 런타임 새로 고침에서 수정됩니다.

## <a name="next-steps"></a>다음 단계

Service Fabric Mesh에 대한 자세한 내용은 [개요](service-fabric-mesh-overview.md)를 참조하세요.
