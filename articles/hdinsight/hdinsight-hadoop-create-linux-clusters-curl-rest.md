<properties
   	pageTitle="cURL 및 Azure REST API를 사용하여 HDInsight에서 Linux에 Hadoop, HBase 또는 Storm 클러스터 만들기 | Microsoft Azure"
   	description="cURL, Azure 리소스 관리자 템플릿 및 Azure REST API를 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법을 알아봅니다. 클러스터 유형(Hadoop, HBase 또는 Storm)을 지정하거나 스크립트를 사용하여 사용자 지정 구성 요소를 설치할 수 있습니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/04/2015"
   	ms.author="larryfr"/>

#cURL 및 Azure REST API를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[AZURE.INCLUDE [선택기](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure REST API를 사용하면 Linux 기반 HDInsight 클러스터 등과 같은 새 리소스 생성을 포함하여 Azure 플랫폼에서 호스팅되는 관리 작업을 수행할 수 있습니다. 이 문서에서는 HDInsight 클러스터 및 관련 저장소를 구성하는 Azure 리소스 관리자 템플릿을 만들고 cURL을 사용하여 템플릿을 Azure REST API에 배치하여 새 HDInsight 클러스터를 만듭니다.

> [AZURE.IMPORTANT] 이 문서의 단계는 HDInsight 클러스터에 대해 작업자 노드 (4)의 기본 갯수를 사용합니다. 클러스터 만들기에서 또는 클러스터를 만든 후 확장하여 32개 이상의 작업자 노드를 계획하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
>
> 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

###필수 조건

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

- __Azure CLI__ Azure CLI는 서비스 주체를 만드는 데 사용됩니다. 서비스 주체는 Azure REST API에 대한 요청의 인증 토큰을 생성하는 데 사용됩니다.

    CLI를 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](../xplat-cli-install.md)를 참조하세요.

- __cURL__. 이 유틸리티는 패키지 관리 시스템을 통해서나 [http://curl.haxx.se/](http://curl.haxx.se/)에서 다운로드하여 사용할 수 있습니다.

    > [AZURE.NOTE] PowerShell을 사용하여 이 문서의 명령을 실행하는 경우 먼저 기본적으로 생성한 `curl` 별칭을 제거합니다. 이 별칭은 PowerShell 프롬프트에서 `curl` 명령을 사용할 때 cURL 대신 PowerShell cmdlet인 Invoke-WebRequest을 사용하며 이 문서에서 사용되는 많은 명령에 대해 오류를 반환합니다.
    > 
    > 이 별칭을 제거하려면 PowerShell 프롬프트에서 다음을 사용합니다.
    >
    > ```Remove-item alias:curl`
    >
    > 별칭을 제거한 후에는 시스템에 설치한 cURL 버전을 사용할 수 있어야 합니다.

##템플릿 만들기

Azure 리소스 관리 템플릿은 __리소스 그룹__과 그 안의 모든 리소스를 설명하는 JSON 문서입니다(예: HDInsight). 이 템플릿 기반 접근 방식을 사용하면 HDInsight에 필요한 모든 리소스를 하나의 템플릿에 정의하고 그룹에 변경 내용을 적용하는 __배포__를 통해 그룹에 대한 변경 내용을 전체적으로 관리할 수 있습니다.

템플릿은 보통 템플릿 그 자체와, 구성에 특정한 값으로 사용자가 입력한 매개 변수 파일 등의 두 부분으로 구성됩니다. 예를 들어 클러스터 이름, 관리자 이름, 암호가 있습니다. REST API를 직접 사용할 때는 이 두 항목을 한 파일로 결합해야 합니다. 이 JSON 문서의 형식은 다음과 같습니다.

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

예를 들어, 다음은 [https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password)의 템플릿과 매개 변수 파일의 병합기로, 암호를 사용하여 SSH 사용자 계정을 보호하는 Linux 기반 클러스터를 만듭니다.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

이 예는 이 문서의 단계에서 사용됩니다. 문서 마지막의 __매개 변수__ 섹션에서 자리표시자 _values_의 값을, 클러스터에 사용할 값으로 교체해야 합니다.

##Azure 구독에 로그인

[Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](../xplat-cli-connect.md)에서 설명된 단계를 따라 __로그인__ 방법을 사용하여 구독에 연결합니다.

##서비스 주체 만들기

> [AZURE.IMPORTANT] 아래 링크의 문서에 있는 단계를 따를 때는 다음 항목을 변경해야 합니다.
> 
> * 이 단계에서 __reader__의 값이 언급되면 대신 __owner__를 사용합니다. 그러면 구독에서 서비스에 대해 변경할 수 있는 서비스 주체가 만들어집니다. 이 항목은 HDInsight 클러스터를 만들 때 필요합니다.
>
> 또한 이 프로세스에서 사용되는 다음 정보를 저장해야 합니다.
> 
> * 구독 ID - `azure account list` 사용 시 수신
> * 테넌트 ID - `azure account list` 사용 시 수신
> * 응용 프로그램 ID - 서비스 주체를 만들 때 반환
> * 서비스 주체 암호 - 서비스 주체를 만들 때 사용

[Azure 리소스 관리자를 사용하여 서비스 주체 인증](https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/#authenticate-service-principal-with-password---azure-cli) 문서의 _암호를 사용하여 서비스 주체 인증 - Azure CLI_ 섹션에 있는 단계를 따릅니다. 그러면 클러스터 만들기 요청을 인증하는 데 사용할 수 있는 새 서비스 주체가 만들어집니다.

##인증 토큰 가져오기

다음을 사용하여 Azure에서 새 토큰을 가져옵니다. __TENANTID__, __APPLICATIONID__, 및 __PASSWORD__는 서비스 주체를 만들 때 저장한 정보로 대체합니다.

    curl -X "POST" "https://login.microsoftonline.com/TENANTID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=APPLICATIONID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=PASSWORD" \
    --data-urlencode "resource=https://management.azure.com/"

이 요청에 성공하면 200 시리즈 응답을 받게 되며 응답 본문에 JSON 문서가 포함되어 있습니다.

> [AZURE.IMPORTANT] 이 요청에서 반환한 JSON 문서는 이름이 __access\_token__인 요소를 포함하며, 이 요소의 값은 이 문서의 다음 섹션에서 사용되는 요청을 인증하는 데 필요한 액세스 토큰입니다.

##리소스 그룹 만들기

다음을 사용하여 새 리소스 그룹을 만듭니다. HDInsight 클러스터와 같은 리소스를 만들려면 먼저 그룹을 만들어야 합니다.

* __SUBSCRIPTIONID__를, 서비스 주체를 만들 때 받은 구독 ID로 바꿉니다.
* __ACCESSTOKEN__을 이전 단계에서 받은 액세스 토큰으로 바꿉니다.
* __DATACENTERLOCATION__을 리소스 그룹과 리소스를 만들려는 데이터 센터로 바꿉니다. 예를 들어 "미국 중남부"입니다. 
* __GROUPNAME__을 이 그룹에 사용하려는 이름으로 바꿉니다.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d $'{ "location": "DATACENTERLOCATION" }’

이 요청에 성공하면 200 시리즈 응답을 받게 되며 응답 본문에 그룹 정보가 담긴 JSON 문서가 포함되어 있습니다. `"provisioningState"` 요소는 `"Succeeded"` 값을 포함합니다.

##배포 만들기

다음을 사용하여 리소스 그룹에 클러스터 구성(템플릿 및 매개 변수 값)을 배포합니다.

* __SUBSCRIPTIONID__ 및 __ACCESSTOKEN__은 이전에 사용한 값으로 바꿉니다. 
* __GROUPNAME__을 이전 섹션에서 만든 리소스 그룹 이름으로 바꿉니다.
* __DEPLOYMENTNAME__을 이 배포에 사용하려는 이름으로 바꿉니다.

    curl -X "PUT" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json" \\ -d "{본문 문자열을 템플릿 및 매개 변수로 설정}"

> [AZURE.NOTE] 템플릿과 매개 변수가 담긴 JSON 문서를 파일로 저장한 경우 `-d "{ 템플릿 및 매개 변수}"' 대신 다음을 사용할 수 있습니다.
>
> ```--data-binary "@/path/to/file.json"```

이 요청에 성공하면 200 시리즈 응답을 받게 되며 응답 본문에 배포 작업 정보가 담긴 JSON 문서가 포함되어 있습니다.

> [AZURE.IMPORTANT] 이 시점에서는 배포가 제출되었으나 완료된 것은 아닙니다. 배포가 완료되려면 보통 몇 분(보통 15분 전후)이 소요됩니다.

##배포 상태 확인

배포 상태를 확인하려면 다음을 사용합니다.

* __SUBSCRIPTIONID__ 및 __ACCESSTOKEN__은 이전에 사용한 값으로 바꿉니다. 
* __GROUPNAME__을 이전 섹션에서 만든 리소스 그룹 이름으로 바꿉니다.

    curl -X "GET" "https://management.azure.com/subscriptions/SUBSCRIPTIONID/resourcegroups/GROUPNAME/providers/microsoft.resources/deployments/DEPLOYMENTNAME?api-version=2015-01-01" \\ -H "Authorization: Bearer ACCESSTOKEN" \\ -H "Content-Type: application/json"

그러면 배포 작업 관련 정보가 담긴 JSON 문서가 반환됩니다. `"provisioningState"` 요소에는 배포 상태가 포함되어 있습니다. `"Succeeded"` 값이 포함되었으면 배포가 성공적으로 완료된 것입니다. 이 시점에서 클러스터를 사용할 수 있어야 합니다.

##다음 단계

HDInsight 클러스터를 성공적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

###Hadoop 클러스터

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

###HBase 클러스터

* [HDInsight에서 HBase 시작](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 HBase용 Java 응용 프로그램 개발](hdinsight-hbase-build-java-maven-linux.md)

###Storm 클러스터

* [HDInsight에서 Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](hdinsight-storm-develop-python-topology.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0128_2016-->