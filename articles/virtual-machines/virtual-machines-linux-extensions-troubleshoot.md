<properties
   pageTitle="Linux VM 확장 오류 문제 해결 | Microsoft Azure"
   description="Azure Linux VM 확장 오류 문제 해결에 대해 자세히 알아봅니다."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Azure Linux VM 확장 오류 문제 해결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## 확장 상태 보기
Azure CLI에서 Azure Resource Manager 템플릿을 실행할 수 있습니다. 템플릿을 실행한 후 Azure 리소스 탐색기 또는 명령줄 도구에서 확장 상태를 볼 수 있습니다.

다음은 예제입니다.

Azure CLI:

      azure vm get-instance-view


샘플 출력은 다음과 같습니다.

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## 확장 오류 문제 해결:

### VM에서 확장 다시 실행

사용자 지정 스크립트 확장을 사용하여 VM에서 스크립트를 실행하는 경우 때때로 VM이 성공적으로 만들어졌지만 스크립트가 실패하는 오류가 발생할 수 있습니다. 이러한 조건에서 이 오류를 복구하는 권장 방법은 확장을 제거하고 템플릿을 다시 실행하는 것입니다. 참고: 이후에는 확장을 제거할 필요가 없도록 이 기능이 향상될 예정입니다.

#### Azure CLI에서 확장 제거

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

여기서 "publsher-name"은 "azure vm get-instance-view" 출력의 확장 형식에 해당하고 name은 템플릿의 확장 리소스 이름입니다.

확장이 제거되면 템플릿을 다시 실행하여 VM에서 스크립트를 실행할 수 있습니다.

<!---HONumber=AcomDC_0420_2016-->