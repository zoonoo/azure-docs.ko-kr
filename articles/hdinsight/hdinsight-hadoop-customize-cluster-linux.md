<properties
	pageTitle="스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정 | Microsoft Azure"
	description="스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2015"
	ms.author="larryfr"/>

# 스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정

HDInsight는 사용자 지정 스크립트(프로비전 프로세스 중 클러스터에서 수행할 사용자 지정 정의)를 호출하는 **스크립트 작업**이라는 구성 옵션을 제공합니다. 이 스크립트를 사용하여 클러스터에 추가 소프트웨어를 설치하거나 클러스터에서 응용 프로그램의 구성을 변경할 수 있습니다.

> [AZURE.NOTE]이 문서에 있는 정보는 Linux 기반 HDInsight 클러스터에 지정됩니다. Windows 기반 클러스터에 지정된 이 문서의 버전은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정(Windows)](hdinsight-hadoop-customize-cluster.md)을 참조하세요.

## 클러스터 프로비전 프로세스의 스크립트 작업

스크립트 작업은 클러스터를 만드는 프로세스 동안에만 사용됩니다. 다음 다이어그램에서는 프로비전 프로세스 중 스크립트 작업을 실행할 때를 보여 줍니다.

![HDInsight 클러스터 사용자 지정 및 클러스터 프로비저닝 중의 단계][img-hdi-cluster-states]

HDInsight를 구성하는 동안 스크립트를 실행합니다. 이 단계에서 스크립트는 클러스터의 지정된 모든 노드에서 병렬 및 루트 권한으로 실행됩니다.

> [AZURE.NOTE]스크립트가 실행될 때 클러스터 노드에 대한 루트 권한이 있으므로 Hadoop 관련 서비스를 비롯한 서비스의 중지 및 시작과 같은 작업을 수행할 수 있습니다. 서비스를 중지하는 경우 스크립트 실행이 완료되기 전에 Ambari 서비스 및 기타 Hadoop 관련 서비스가 실행 중인지 확인해야 합니다. 클러스터가 생성되는 동안 클러스터의 상태를 확인하려면 이러한 서비스가 필요합니다.

각 클러스터에서는 지정된 순서대로 호출되는 여러 스크립트 작업을 사용할 수 있습니다. 스크립트를 헤드 노드, 작업자 노드 또는 두 노드 모두에서 실행할 수 있습니다.

> [AZURE.IMPORTANT]스크립트 동작은 15분 이내에 완료하지 않으면 시간이 초과됩니다.

## 예제 스크립트 작업 스크립트

스크립트 작업 스크립트는 Azure Preview 포털, Azure PowerShell 또는 HDInsight.NET SDK에서 사용할 수 있습니다. 이 문서에서는 포털에서 스크립트 작업을 사용하는 방법을 보여줍니다. PowerShell 및 .NET SDK로 스크립트 작업을 사용하는 방법을 알아보려면 아래 표에 나열된 샘플을 살펴봅니다.

HDInsight는 HDInsight 클러스터에서 다음 구성 요소를 설치하는 여러 스크립트를 제공합니다.

이름 | 스크립트
----- | -----
**Hue 설치** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)을 참조하세요.
**Spark 설치** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh. [HDInsight 클러스터에서 Spark 설치 및 사용](hdinsight-hadoop-spark-install-linux.md)을 참조하세요.
**R 설치** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. [HDInsight 클러스터에서 R 설치 및 사용](hdinsight-hadoop-r-scripts-linux.md)을 참조하세요.
**Solr 설치** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. [HDInsight 클러스터에서 Solr 설치 및 사용](hdinsight-hadoop-solr-install-linux.md)을 참조하세요.
**Giraph 설치** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. [HDInsight 클러스터에서 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)을 참조하세요.

##Azure Preview 포털에서 스크립트 작업 사용

1. [사용자 지정 옵션을 사용하여 클러스터를 프로비전](hdinsight-provision-clusters.md#portal)에서 설명된 대로 클러스터 프로비전을 시작합니다.

2. 아래와 같이, __선택적 구성__의 **스크립트 작업** 블레이드에 대해 **스크립트 작업 추가**를 클릭하여 스크립트 작업에 대한 세부 정보를 제공합니다.

	![스크립트 작업을 사용하여 클러스터 사용자 지정](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "스크립트 작업을 사용하여 클러스터 사용자 지정")

	| 속성 | 값 |
	| -------- | ----- |
	| 이름 | 스크립트 작업의 이름을 지정합니다. |
	| 스크립트 URI | 클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다. |
	| 헤드/작업자 | 사용자 지정 스크립트가 실행되는 노드(**헤드**, **작업자** 또는 **ZooKeeper**)를 지정합니다. |
	| 매개 변수 | 스크립트에 필요한 경우 매개 변수를 지정합니다. |

	ENTER 키를 누르고 두 개 이상의 스크립트 작업을 추가하여 클러스터에 여러 구성 요소를 설치할 수 있습니다.

3. **선택**을 클릭하여 스크립트 작업 구성을 저장하고 클러스터 프로비전을 계속합니다.

##Azure 리소스 관리자 템플릿에서 스크립트 작업 사용

이 섹션에서 Azure 리소스 관리자(ARM) 템플릿을 사용하여 HDInsight 클러스터를 프로비전하고 클러스터에서 사용자 지정 구성 요소(이 예에서는 R)를 설치하는 스크립트 작업을 사용할 수 있습니다. 이 섹션에서는 스크립트 작업을 사용하여 클러스터를 프로비전하는 샘플 ARM 템플릿을 제공합니다.

### 시작하기 전에

* HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.
* ARM 템플릿을 만드는 방법에 대한 지침은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 이전에 리소스 관리자에서 Azure PowerShell을 사용하지 않은 경우 [Azure 리소스 관리자와 함께 Azure PowerShell 사용](powershell-azure-resource-manager)을 참조하세요.

### 스크립트 동작을 사용하여 클러스터 프로비전

1. 다음 템플릿을 컴퓨터의 위치에 복사합니다. 이 템플릿은 헤드 노트에 R뿐만 아니라 클러스터에 작업자를 설치합니다. 또한 JSON 템플릿이 유효한지 확인할 수 있습니다. 템플릿 콘텐츠를 [JSONLint](http://jsonlint.com/), 즉, 온라인 JSON 유효성 검사기 도구에 붙여 넣습니다.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "hdiuser"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",

		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}



2. Azure PowerShell을 시작하고 Azure 계정에 로그인합니다. 자격 증명을 제공하면 사용자 계정에 대한 정보가 반환됩니다.

		Add-AzureAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. Azure 리소스 관리자 모듈로 전환합니다.

		Switch-AzureMode AzureResourceManager

5. 기본 리소스 그룹이 없는 경우 새 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다. 새 리소스 그룹에 대한 요약이 반환됩니다.

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. 리소스 그룹에 대한 새 배포를 만들려면 **New-azureresourcegroupdeployment** 명령을 실행하고 필요한 매개 변수를 제공합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름 및 만든 템플릿의 경로 또는 URL이 포함됩니다. 템플릿에 매개 변수가 필요한 경우 해당 매개 변수를 전달해야 합니다. 이 경우 클러스터에서 R을 설치하는 스크립트 작업은 매개 변수가 필요하지 않습니다.


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	템플릿에 정의된 매개 변수에 대한 값을 제공하라는 메시지가 표시됩니다.

7. 리소스 그룹을 배포한 경우 배포에 대한 요약이 표시됩니다.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. 배포에 실패할 경우 다음 cmdlet을 사용하여 오류에 대한 정보를 가져올 수 있습니다.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	배포 오류에 대한 자세한 내용은 다음 cmdlet을 사용합니다.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

##Azure PowerShell에서 스크립트 작업 사용

이 섹션에서는 스크립트 작업을 통해 스크립트를 호출하여 클러스터를 사용자 지정하는 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet을 사용합니다. 계속하기 전에 Azure PowerShell을 설치 및 구성했는지 확인하세요. HDInsight PowerShell cmdlet을 실행하도록 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.

다음 단계를 수행합니다.

1. Azure PowerShell 콘솔을 열고 다음 변수를 선언합니다.

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"

2. 구성 값(예: 클러스터의 노드) 및 사용할 기본 저장소를 지정합니다.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. **Add-AzureHDInsightScriptAction** cmdlet을 사용하여 스크립트를 호출합니다. 다음 예제에서는 클러스터에서 R을 설치하는 데 스크립트를 사용합니다.

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh


	**Add-AzureHDInsightScriptAction** cmdlet에서 다음과 같은 매개 변수를 사용합니다.

	| 매개 변수 | 정의 |
	| --------- | ---------- |
	| 구성 | 스크립트 작업 정보가 추가되는 구성 개체입니다. |
	| 이름 | 스크립트 작업의 이름입니다. |
	| ClusterRoleCollection | 사용자 지정 스크립트가 실행되는 노드를 지정합니다. 유효한 값은 **HeadNode**(헤드 노드에 설치),**WorkerNode**(모든 데이터 노드에 설치) 또는 **ZookeeperNode**(zookeeper 노드에 설치)입니다. 두 값 중 하나 또는 모두를 사용할 수 있습니다. |
	| 매개 변수 | 스크립트에 필요한 매개 변수입니다. |
	| Uri | 실행되는 스크립트의 URI를 지정합니다. |

4. 마지막으로 클러스터를 프로비전합니다.

		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

메시지가 나타나면 클러스터에 대한 자격 증명을 입력합니다. 클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.

##HDInsight.NET SDK에서 스크립트 작업 사용

HDInsight .NET SDK는 .NET 응용 프로그램에서 HDInsight로 더 쉽게 작업하도록 지원하는 클라이언트 라이브러리를 제공합니다. 다음 단계는 스크립트를 사용하여 HDInsight.NET SDK에서 클러스터를 사용자 지정하는 방법을 보여줍니다.


> [AZURE.IMPORTANT]먼저 자체 서명 인증서를 만들어 워크스테이션에 설치한 다음 Azure 구독에 업로드해야 합니다. 관련 지침은 [자체 서명된 인증서 만들기](http://go.microsoft.com/fwlink/?LinkId=511138)를 참조하세요.


###Visual Studio 프로젝트 만들기

1. Visual Studio 2013 또는 2015 열기

2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.

3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

	| 속성 | 값 |
	| -------- | ----- |
	| Category | Templates/Visual C#/Windows |
	| Template | Console Application |
	| 이름 | ScriptActionCluster |

4. **확인**을 클릭하여 프로젝트를 만듭니다.

5. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

6. 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	.NET 라이브러리 및 이에 대한 참조가 현재의 Visual Studio 프로젝트에 추가됩니다.

7. **솔루션 탐색기**에서 **Program.cs**를 두 번 클릭하여 엽니다.

8. 파일 맨 위에 다음 **using** 문을 추가합니다.

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;

9. **Main()** 함수에서 다음 코드를 붙여넣고 변수 값을 지정합니다.

        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };

10. **Main()** 함수에 다음 코드를 추가합니다. 이 코드는 스크립트 동작을 호출합니다. 이 예제에서 클러스터에 R을 설치하는 스크립트입니다.

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), null
            ));

11. 끝으로, 클러스터를 만듭니다.

		client.CreateCluster(clusterInfo);

11. 응용 프로그램에 대한 변경 내용을 저장하고 솔루션을 빌드합니다.

###응용 프로그램 실행

Azure PowerShell 콘솔을 열고 프로젝트를 저장한 위치로 이동하고 프로젝트 내의 \\bin\\debug 디렉터리로 이동한 후 다음 명령을 실행합니다.

	.\ScriptActionCluster <cluster-name>

클러스터 이름을 제공하고 ENTER 키를 눌러 클러스터를 프로비전합니다.

## HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Hadoop에 형성된 오픈 소스 기술의 에코시스템을 사용하여 클라우드에 빅 데이터 응용 프로그램을 빌드할 수 있는 유연한 플랫폼입니다. Microsoft Azure에서는 [Azure 지원 FAQ 웹 사이트](http://azure.microsoft.com/support/faq/)의 **지원 범위** 섹션에 설명된 대로 일반적인 수준의 오픈 소스 기술을 제공합니다. HDInsight 서비스는 아래에 설명된 일부 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

- **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어, YARN ResouceManager, Hive 쿼리 언어(HiveQL) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)에 있습니다.

- **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

기본 제공 구성 요소는 완전히 지원되며, Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.

사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 다음과 같은 여러 커뮤니티 사이트를 이용할 수 있습니다.

* [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)

* [스택 오버플로](https://stackoverflow.com)

Apache 프로젝트는 [Apache.org](https://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/) 및 [Spark](http://spark.apache.org/)).

HDInsight 서비스는 사용자 지정 구성 요소를 사용하는 여러 방법을 제공합니다. 구성 요소가 클러스터에 설치되고 사용되는 방법과 상관없이, 동일한 수준의 지원이 적용됩니다. 다음은 HDInsight 클러스터에서 사용자 지정 구성 요소를 사용할 수 있는 가장 일반적인 방법의 목록입니다.

1. 작업 제출 - 사용자 지정 구성 요소를 실행하거나 사용하는 Hadoop 또는 기타 유형의 작업을 클러스터에 제출할 수 있습니다.

2. 클러스터 사용자 지정 - 클러스터를 만들 때 클러스터 노드에 설치되는 사용자 지정 구성 요소 및 추가 설정을 지정할 수 있습니다.

3. 샘플 - 인기 있는 사용자 지정 구성 요소의 경우, Microsoft와 다른 사람들이 이러한 구성 요소를 HDInsight 클러스터에서 어떻게 사용할 수 있는지에 대한 샘플을 제공할 수 있습니다. 이러한 샘플은 지원 없이 제공됩니다.

##문제 해결

Ambari 웹 UI를 사용하여 클러스터를 프로비전하는 동안 스크립트에서 기록된 정보를 볼 수 있습니다.

1. 웹 브라우저에서 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. CLUSTERNAME은 HDInsight 클러스터 이름을 바꿉니다.

	메시지가 표시되면 클러스터의 관리자 계정 이름(관리자) 및 암호를 입력합니다. 웹 폼에서 관리자 자격 증명을 다시 입력해야 합니다.

2. 페이지 위쪽에 있는 모음에서 __작업__ 항목을 선택합니다. Ambari 통해 클러스터에서 수행된 현재 및 이전 작업의 목록이 표시됩니다.

	![선택한 작업으로 Ambari 웹 UI 모음](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. __작업__ 열에서 __run\_customscriptaction__이 있는 항목을 찾습니다. 스크립트 작업을 실행할 때 생성됩니다.

	![작업의 스크린샷](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	이 항목을 선택하고 링크를 통해 드릴다운하여 스크립트가 클러스터에서 실행되었을 때 생성된 STDOUT 및 STDERR 출력을 봅니다.

## 다음 단계

클러스터를 사용자 지정하는 스크립트를 만들고 사용하는 데 대한 정보 및 예제는 다음을 참조하세요.

- [HDInsight용 스크립트 작업 스크립트 개발](hdinsight-hadoop-script-actions-linux.md)
- [HDInsight 클러스터에서 Spark 설치 및 사용](hdinsight-hadoop-spark-install-linux.md)
- [HDInsight 클러스터에서 R 설치 및 사용](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight 클러스터에 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "클러스터 프로 비전 중의 단계"

<!---HONumber=Sept15_HO2-->