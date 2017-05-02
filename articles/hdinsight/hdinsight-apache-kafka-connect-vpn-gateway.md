---
title: "가상 네트워크를 사용하여 HDInsight의 Kafka에 연결 -Azure | Microsoft Docs"
description: "kafka-python 클라이언트를 사용하여 HDInsight의 Kafka에 원격으로 연결하는 방법을 알아봅니다. 이 문서의 구성에서는 Azure Virtual Network 내에서 HDInsight를 사용합니다. 원격 클라이언트는 지점-사이트 간 VPN Gateway를 통해 가상 네트워크에 연결합니다."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 5da21de1f7b51a6ca38a3fe443531bab4091dd60
ms.lasthandoff: 04/20/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>Azure Virtual Network를 통해 HDInsight(미리 보기)의 Kafka에 연결

Azure Virtual Network를 사용하여 HDInsight의 Kafka에 연결하는 방법을 알아봅니다. Kafka 클라이언트(생산자 및 소비자)는 HDInsight에서 직접 또는 원격 시스템에서 실행할 수 있습니다. 원격 클라이언트는 Azure Virtual Network를 통해 HDInsight의 Kafka에 연결해야 합니다. 이 문서의 정보의 정보를 사용하여 원격 클라이언트가 Azure Virtual Network를 사용하여 HDInsight에 연결하는 방법을 이해합니다.

> [!IMPORTANT]
> 이 문서에 설명된 구성 중 일부는 Windows, macOS 또는 Linux 클라이언트에서 사용할 수 있습니다. 그러나 포함된 지점 - 사이트 간 예제는 Windows용 VPN 클라이언트만 제공합니다.
>
> 또한 이 예제에서는 Python 클라이언트([kafka-python](http://kafka-python.readthedocs.io/en/master/))를 사용하여 HDInsight의 Kafka와의 통신을 확인합니다.

## <a name="architecture-and-planning"></a>아키텍처 및 계획

HDInsight 클러스터는 Azure Virtual Network 내에서 보호되며 들어오는 SSH 및 HTTPS 트래픽만 허용합니다. 트래픽은 공용 게이트웨이 통해 도착합니다. 이러한 게이트웨이는 Kafka 클라이언트의 트래픽을 라우팅하지 않습니다. 원격 클라이언트에서 Kafka에 액세스하려면 VPN(가상 사설망) Gateway를 제공하는 Azure Virtual Network를 만들어야 합니다. 가상 네트워크 및 게이트웨이를 구성한 후 가상 네트워크에 HDInsight를 설치하고 VPN Gateway를 사용하여 연결합니다.

![클라이언트가 VPN을 통해 연결된 Azure Virtual Network 내 HDInsight의 다이어그램](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

다음 목록에는 가상 네트워크에서 HDInsight의 Kafka를 사용하는 프로세스에 대한 정보가 나와 있습니다.

1. 가상 네트워크를 만듭니다. HDInsight와 함께 Azure Virtual Network를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](hdinsight-extend-hadoop-virtual-network.md) 문서를 참조하세요.

2. (선택 사항) 가상 네트워크 내에 Azure Virtual Network를 만들고 사용자 지정 DNS 서버를 설치합니다. 이 DNS 서버를 사이트 간 또는 VNet 간 구성에서 원격 클라이언트에 대한 이름 확인을 사용하도록 설정하는 데 사용됩니다. 자세한 내용은 [VM 및 Cloud Services의 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요.

3. 가상 네트워크에 대한 VPN Gateway를 만듭니다. VPN Gateway 구성에 대한 자세한 내용은 [VPN Gateway 정보](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

4. 가상 네트워크 내에 HDInsight를 만듭니다. 네트워크에 대한 사용자 지정 DNS 서버를 구성한 경우 HDInsight가 이 서버를 사용하도록 자동으로 구성됩니다.

5. (선택 사항) 사용자 지정 DNS 서버를 사용하지 않았으며 클라이언트와 가상 네트워크 간에 이름 확인이 수행되지 않으면 IP 보급을 위해 Kafka를 구성해야 합니다. 자세한 내용은 이 문서의 [IP 보습을 위해 Kafka 구성](#configure-kafka-for-ip-advertising) 섹션을 참조하세요.

## <a name="create-using-powershell"></a>만들기: PowerShell 사용

이 섹션의 단계에서는 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)을 사용하여 다음 구성을 만듭니다.

* Azure 가상 네트워크
* 지점 - 사이트 간 VPN Gateway
* Azure Storage 계정(HDInsight에서 사용)
* HDInsight의 Kafka

1. [지점 및 사이트 간 연결에 대한 자체 서명된 인증서로 작업](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)의 단계에 따라 게이트웨이에 필요한 인증서를 만듭니다.

2. PowerShell 프롬프트를 열고 다음 코드를 사용하여 Azure 구독에 로그인합니다.

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. 다음 코드를 사용하여 구성 정보를 포함하는 변수를 만듭니다.

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. 다음 코드를 사용하여 Azure 리소스 그룹 및 가상 네트워크를 만듭니다.

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > 이 프로세스는 완료하는 데 몇 분 정도 걸릴 수 있습니다.

5. 다음 코드를 사용하여 Azure Storage 계정 및 Blob 컨테이너를 만듭니다.

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. 다음 코드를 사용하여 HDInsight 클러스터를 만듭니다.

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > 이 프로세스를 완료하는 데 약 20분이 걸립니다.

8. 다음 cmdlet을 사용하여 가상 네트워크에서 Windows VPN 클라이언트의 URL을 검색합니다.

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Windows VPN 클라이언트를 다운로드하려면 웹 브라우저에서 반환된 URI를 사용합니다.

## <a name="configure-kafka-for-ip-advertising"></a>IP 보습을 위해 Kafka 구성

기본적으로 Zookeeper는 클라이언트에 대한 Kafka 브로커의 도메인 이름을 반환합니다. 이 구성은 가상 네트워크의 엔터티에 대해 이름 확인을 사용할 수 없으므로 VPN 클라이언트에 작동하지 않습니다. 다음 단계에 따라 도메인 이름 대신 IP 주소를 보급하도록 HDInsight의 Kafka를 구성합니다.

1. 웹 브라우저를 사용하여 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. __CLUSTERNAME__을 HDInsight 클러스터의 Kafka 이름으로 바꿉니다.

    클러스터의 HTTPS 사용자 이름 및 암호를 입력하라는 메시지가 표시되면 입력합니다. 클러스터에 대한 Ambari Web UI가 표시됩니다.

2. Kafka에 대한 정보를 보려면 왼쪽 목록에서 __Kafka__를 선택합니다. 

    ![Kafka가 강조 표시된 서비스 목록](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Kafka 구성을 보려면 위쪽 가운데에서 __Configs__를 선택합니다.

    ![Kafka에 대한 링크 구성](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. __kafka-env__ 구성을 찾으려면 오른쪽 위에 있는 __필터__ 필드에 `kafka-env`를 입력합니다.

    ![Kafka 구성, kafka-env 관련](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. IP 주소를 보급하도록 Kafka를 구성하려면 __kafka-env-template__ 맨 아래에 다음 텍스트를 추가합니다.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka에서 수신 대기하는 인터페이스를 구성하려면 오른쪽 위의 __필터__ 필드에 `listeners`를 입력합니다.

7. 모든 네트워크 인터페이스에서 수신 대기하도록 Kafka를 구성하려면 __수신기__ 필드의 값을 `PLAINTEXT://0.0.0.0:92092`로 변경합니다.

8. 구성 변경 내용을 저장하려면 __저장__ 단추를 사용합니다. 변경 내용을 설명하는 텍스트 메시지를 입력합니다. 변경 내용이 저장되면 __확인__을 선택합니다.

    ![구성 저장 단추](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. Kafka를 다시 시작할 때 오류를 방지하려면 __서비스 작업__ 단추를 사용하여 __유지 관리 모드 켜기__를 선택합니다. 확인을 선택하여 이 작업을 완료합니다.

    ![서비스 작업, 유지 관리 모드가 강조 표시됨](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Kafka를 다시 시작하려면 __다시 시작__ 단추를 사용하고 __영향 받은 모든 항목 다시 시작__을 선택합니다. 다시 시작을 확인하고 작업이 완료되면 __확인__ 단추를 사용합니다.

    ![영향 받은 모든 항목 다시 시작이 강조 표시된 다시 시작 단추](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. 유지 관리 모드를 사용하지 않도록 설정하려면 __서비스 작업__ 단추를 사용하고 __유지 관리 모드 켜기__를 선택합니다. **확인**을 선택하여 이 작업을 완료합니다.

## <a name="connect-to-the-vpn-gateway"></a>VPN Gateway에 연결

__Windows 클라이언트__에서 VPN Gateway에 연결하려면 [지점- 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameconnectapart-7---connect-to-azure) 문서의 __Azure에 연결__ 섹션을 사용합니다.

## <a name="remote-kafka-client"></a>원격 Kafka 클라이언트

클라이언트 컴퓨터에서 Kafka에 연결하려면 Kafka 브로커 또는 Zookeeper 노드(둘 중 클라이언트에 필요한 항목)의 IP 주소를 사용해야 합니다. 다음 단계에 따라 Kafka 브로커의 IP 주소를 검색한 다음 Python 응용 프로그램에서 사용합니다.

1. 다음 스크립트를 사용하여 클러스터에 있는 노드의 IP 주소를 검색합니다.

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    이 스크립트에서는 `$resourceGroupName`을 가상 네트워크가 포함된 Azure 리소스 그룹의 이름으로 가정합니다. 이 스크립트의 출력은 다음 텍스트와 비슷합니다.

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > Kafka 클라이언트가 Kafka 브로커 대신 Zookeeper 노드를 사용하는 경우 PowerShell 스크립트에서 `*workernode*`를 `*zookeepernode*`로 바꿉니다.

    > [!WARNING]
    > 클러스터 크기를 조정하려고 하거나 노드가 실패하여 대체되면 IP 주소가 변경될 수 있습니다. 현재 HDInsight 클러스터의 노드에 대해 특정 IP 주소를 미리 할당하는 방법은 없습니다.

2. 다음을 사용하여 [kafka-python](http://kafka-python.readthedocs.io/) 클라이언트를 설치합니다.

        pip install kafka-python

3. Kafka로 데이터를 전송하려면 다음 Python 코드를 사용합니다.

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    `'ip_address'` 항목을 이 섹션의 1단계에서 반환된 주소로 바꿉니다.
    
    > [!NOTE]
    > 이 코드는 문자열 `test message`를 토픽 `testtopic`으로 전송합니다. HDInsight에서 Kafka의 기본 구성은 토픽이 없을 때 만드는 것입니다.

4. Kafka에서 메시지를 검색하려면 다음 Python 코드를 사용합니다.

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    `'ip_address'` 항목을 이 섹션의 1단계에서 반환된 주소로 바꿉니다. 출력에는 이전 단계에서 생산자에게 전송된 테스트 메시지가 포함됩니다.

## <a name="troubleshooting"></a>문제 해결

가상 네트워크에 연결하는 데 문제가 있거나 네트워크를 통해 HDInsight에 연결하는 데 문제가 있는 경우 [가상 네트워크 게이트웨이 및 연결 문제 해결](../network-watcher/network-watcher-troubleshoot-manage-powershell.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

지점 - 사이트 간 VPN Gateway를 사용하여 Azure Virtual Network를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Portal을 사용하여 지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Azure PowerShell을 사용하여 지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

HDInsight에서 Kafka를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Kafka 시작](hdinsight-apache-kafka-get-started.md)
* [HDInsight에서 Kafka에 미러링 사용](hdinsight-apache-kafka-mirroring.md)

