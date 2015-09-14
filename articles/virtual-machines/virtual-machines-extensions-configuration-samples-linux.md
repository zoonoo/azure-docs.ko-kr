<properties
   pageTitle="Azure VM 확장에 대한 샘플 구성 | Microsoft Azure"
	description="확장으로 템플릿 작성에 대한 샘플 구성"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="kundanap"/>

# Azure Linux VM 확장 구성 샘플

이 문서에서는 Azure IaaS Linux VM에 대해 Azure VM 확장을 구성하기 위한 샘플 구성을 제공합니다. 이러한 확장에 대해 자세히 알아보려면 <a href="https://msdn.microsoft.com/ko-KR/library/azure/dn606311.aspx" target="_blank">여기</a>를 클릭하세요.

## VM 확장에 대한 샘플 템플릿 코드 조각
확장을 배포하기 위한 템플릿 코드 조각은 다음과 같습니다.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

확장을 배포하기 전에 최신 확장 버전을 확인하고 "typeHandlerVersion"을 현재 최신 버전으로 바꿉니다.

문서의 나머지 부분에서는 Linux VM 확장에 대한 샘플 구성을 제공합니다.

### CloudLink SecureVM Agent
          {
            "publisher": "CloudLinkEMC.SecureVM",
            "type": "CloudLinkSecureVMLinuxAgent",
            "typeHandlerVersion": "4.0",
            "settings": {
              "CloudLinkCenter" : "specify valid IP/FQDN to CloudLinkCenter"
            }
          }

### Linux용 CustomScript 확장
    {
        "publisher": " Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.3",
        "settings": {
            "fileUris": [
                "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
            ],
            "commandToExecute": "powershell.exe-ExecutionPolicyUnrestricted-Filestart.ps1"
        }
    }


### Datadog Agent
        {
          "publisher": "Datadog.Agent",
          "type": "DatadogLinuxAgent",
          "typeHandlerVersion": "0.4",
          "settings": {
            "api_key" : "API Key from https://app.datadoghq.com/account/settings#api"
          }
        }

### Chef Agent
        {
          "publisher": "Chef.Bootstrap.WindowsAzure",
          "type": "CentosChefClient|LinuxChefClient",
          "typeHandlerVersion": "1210.12",
          "settings": {
            "validation_key" : " Validation key",
            "client_rb" : "client_rb file",
            "runlist" : "Optional runlist"
          }
        }

### VM Access 확장(암호 재설정)
업데이트된 스키마를 보려면 <a href="https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess" target="_blank">여기</a> 링크를 참조하세요.

        {
          "publisher": "Microsoft.OSTCExtensions",
          "type": "VMAccessForLinux",
          "typeHandlerVersion": "1.2",
          "protectedSettings": {
            "username": "(required, string) the name of the user",
            "password": "(optional, string) the password of the user",
            "reset_ssh": "(optional, boolean) whether or not reset the ssh",
            "ssh_key": "(optional, string) the public key of the user, base64 encoded pem",
            "remove_user": "(optional, string) the user name to remove"
          }
        }

### OS 패치
업데이트된 스키마를 보려면 <a href="https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching" target="_blank">여기</a> 링크를 참조하세요.

        {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "OSPatchingForLinux",
        "typeHandlerVersion": "2.9",
        "Settings": {
          "disabled": false,
          "stop": false,
          "rebootAfterPatch": "RebootIfNeed|Required|NotRequired|Auto",
          "category": "Important|ImportantAndRecommended",
          "installDuration": "<hr:min>",
          "oneoff": false,
          "intervalOfWeeks": "<number>",
          "dayOfWeek": "Sunday|Monday|Tuesday|Wednesday|Thursday|Friday|Saturday|Everyday",
          "startTime": "<hr:min>",
          "vmStatusTest": {
              "local": false,
              "idleTestScript": "<path_to_idletestscript>",
              "healthyTestScript": "<path_to_healthytestscript>"
          }
        }
        }

### Docker 확장
업데이트된 스키마를 보려면 <a href="https://github.com/Azure/azure-docker-extension/blob/master/README.md#1-configuration-schema" target="_blank">여기</a> 링크를 참조하세요.

        {
          "publisher": "Microsoft.Azure.Extensions ",
          "type": "DockerExtension ",
          "typeHandlerVersion": "1.0",
          "Settings": {
            "docker":{
                "port": "2376",
                "options": ["-D", "--dns=8.8.8.8"]
            },
            "compose": {
                "cache" : {
                    "image" : "memcached",
                    "ports" : ["11211:11211"]
                },
                "blog": {
                    "image": "ghost",
                    "ports": ["80:2368"]
                }
            }
            }
        }
위의 예제에서 버전 번호를 최신 버전 번호로 바꿉니다.

<!---HONumber=September15_HO1-->