---
title: Azure Files에서 사용하기 위한 Linux의 P2S(지점 및 사이트 간) VPN 구성 | Microsoft Docs
description: Azure Files에서 사용하기 위한 Linux의 P2S(지점 및 사이트 간) VPN을 구성하는 방법
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126469"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Azure Files에서 사용하기 위한 Linux의 P2S(지점 및 사이트 간) VPN 구성
P2S(지점 및 사이트 간) VPN 연결을 사용하여 포트 445을 열지 않고 Azure 외부에서 SMB를 통해 Azure 파일 공유를 탑재할 수 있습니다. 지점 및 사이트 간 VPN 연결은 Azure와 개별 클라이언트 간의 VPN 연결입니다. Azure Files에서 P2S VPN 연결을 사용하려면 연결 하려는 각 클라이언트에 대해 P2S VPN 연결을 구성해야 합니다. 온-프레미스 네트워크에서 Azure 파일 공유에 연결해야 하는 클라이언트가 많은 경우에는 각 클라이언트에 대해 지점 및 사이트 간 연결 대신 S2S(사이트 간) VPN 연결을 사용할 수 있습니다. 자세한 내용은 [Azure Files에서 사용하기 위한 사이트 간 VPN 구성](storage-files-configure-s2s-vpn.md)을 참조하세요.

이 방법 문서를 계속하기 전에 [Azure Files 네트워킹 개요](storage-files-networking-overview.md) 읽어보면서 Azure Files에 사용할 수 있는 네트워킹 옵션에 대한 전체 설명을 파악하는 것이 좋습니다.

이 문서에서는 Linux에서 지점 및 사이트 간 VPN을 구성하여 Azure 파일 공유를 온-프레미스에 직접 탑재하는 단계를 자세히 설명합니다. VPN을 통해 Azure 파일 동기화 트래픽을 라우팅하는 경우 [Azure 파일 동기화 프록시 및 방화벽 설정 구성](storage-sync-files-firewall-and-proxy.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
- 최신 버전의 Azure CLI. Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하고 해당 운영 체제를 선택합니다. Linux에서 Azure PowerShell 모듈을 사용하려는 경우에는 Azure CLI에 대한 아래 지침이 제공될 수 있습니다.

- 온-프레미스에 탑재하려는 Azure 파일 공유입니다. 지점 및 사이트 간 VPN에서 [표준](storage-how-to-create-file-share.md) 또는 [프리미엄 Azure 파일 공유](storage-how-to-create-premium-fileshare.md)를 사용할 수 있습니다.

## <a name="install-required-software"></a>필수 소프트웨어 설치
Azure 가상 네트워크 게이트웨이는 IPsec 및 OpenVPN을 비롯한 여러 VPN 프로토콜을 사용하는 VPN 연결을 제공할 수 있습니다. 이 가이드에서는 IPsec 사용 방법을 보여주며 strongSwan 패키지를 사용하여 Linux에서 지원을 제공합니다. 

> Ubuntu 18.10에서 확인됩니다.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>가상 네트워크 배포 
지점 및 사이트 간 VPN을 통해 온-프레미스에서 Azure 파일 공유 및 기타 Azure 리소스에 액세스하려면 가상 네트워크 또는 VNet을 만들어야 합니다. 자동으로 생성되는 P2S VPN 연결은 온-프레미스 Linux 머신과 이 Azure 가상 네트워크 간의 브리지입니다.

다음 스크립트는 세 개의 서브넷을 사용하여 Azure 가상 네트워크를 만듭니다. 하나는 스토리지 계정의 서비스 엔드포인트를 위한 것이고, 다른 하나는 변경될 수 있는 스토리지 계정의 공용 IP에 대한 사용자 지정 라우팅을 생성하지 않고 온-프레미스에서 스토리지 계정에 액세스해야 하는 스토리지 계정의 프라이빗 엔드포인트입니다. 마지막은 VPN 서비스를 제공하는 가상 네트워크 게이트웨이를 위한 것입니다. 

`<region>`, `<resource-group>` 및 `<desired-vnet-name>`을 사용자 환경에 적합한 값으로 바꾸어야 합니다.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>가상 네트워크에 대한 스토리지 계정 제한
기본적으로 스토리지 계정을 만들 때는 요청을 인증할 수 있는 수단이 있는 한, 세계 어디서든 액세스할 수 있습니다(예: Active Directory ID 또는 스토리지 계정 키 사용). 방금 만든 가상 네트워크에서 이 스토리지 계정에 대한 액세스를 제한하려면 가상 네트워크 내 액세스는 허용하고 다른 모든 액세스는 거부하는 네트워크 규칙 세트를 만들어야 합니다.

스토리지 계정을 가상 네트워크로 제한하려면 서비스 엔드포인트를 사용해야 합니다. 서비스 엔드포인트는 가상 네트워크 내에서만 공용 DNS/공용 IP에 액세스할 수 있는 네트워킹 구문입니다. 공용 IP 주소가 동일하게 유지된다고 보장할 수 없으므로, 궁극적으로 스토리지 계정에 대한 서비스 엔드포인트 대신 프라이빗 엔드포인트를 사용하려고 합니다. 하지만, 서비스 엔드포인트 또한 노출되지 않으면 스토리지 계정을 제한할 수 없습니다.

`<storage-account-name>`을 액세스하려는 스토리지 계정으로 바꾸어야 합니다.

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>프라이빗 엔드포인트 만들기(미리 보기)
스토리지 계정에 대한 프라이빗 엔드포인트를 만들면 스토리지 계정에 가상 네트워크의 IP 주소 공간 내에 있는 IP 주소가 제공됩니다. 이 개인 IP 주소를 사용하여 온-프레미스에서 Azure 파일 공유를 탑재하면 VPN 설치 시 자동으로 정의되는 라우팅 규칙이 VPN을 통해 스토리지 계정에 탑재 요청을 라우팅합니다. 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>VPN 인증에 대한 인증서 만들기
온-프레미스 Linux 머신에서 VPN 연결을 인증하여 가상 네트워크에 액세스할 수 있도록 하려면 두 개의 인증서를 만들어야 합니다. 하나는 가상 머신 게이트웨이에 제공되는 루트 인증서이고, 다른 하나는 루트 인증서로 서명되는 클라이언트 인증서입니다. 다음 스크립트에서는 필수 인증서를 만듭니다.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>가상 네트워크 게이트웨이 배포
Azure 가상 네트워크 게이트웨이는 온-프레미스 Linux 머신에서 연결할 서비스입니다. 이 서비스를 배포하려면 두 가지 기본 구성 요소가 필요합니다. 즉, 전 세계에 있는 클라이언트에 대한 게이트웨이를 식별하는 공용 IP와 클라이언트를 인증하는 데 사용되는 이전에 만든 루트 인증서가 필요합니다.

`<desired-vpn-name-here>`를 이러한 리소스에 대해 원하는 이름으로 바꾸어야 합니다.

> [!Note]  
> Azure 가상 네트워크 게이트웨이를 배포하는 데 최대 45분이 걸릴 수 있습니다. 이 리소스가 배포되는 동안 이 bash 스크립트는 배포가 완료 될 때까지 차단됩니다. 예상된 동작입니다.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>VPN 클라이언트 구성
Azure 가상 네트워크 게이트웨이는 온-프레미스 Linux 머신에서 VPN 연결을 초기화하는 데 필요한 구성 파일이 포함된 다운로드 가능한 패키지를 만듭니다. 다음 스크립트는 적합한 지점에서 생성된 인증서를 저장하고 다운로드 가능한 패키지의 구성 파일에서 올바른 값을 포함하는 `ipsec.conf` 파일을 구성합니다.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Azure 파일 공유 탑재
지점 및 사이트 간 VPN을 설정했으므로 Azure 파일 공유를 탑재할 수 있습니다. 다음 예제에서는 영구적이지 않은 공유를 탑재합니다. 영구적으로 탑재하려면 [Linux에서 Azure 파일 공유 사용](storage-how-to-use-files-linux.md)을 참조하세요. 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>참고 항목
- [Azure Files 네트워킹 개요](storage-files-networking-overview.md)
- [Azure Files에서 사용하기 위한 Windows의 P2S(지점 및 사이트 간) VPN 구성](storage-files-configure-p2s-vpn-windows.md)
- [Azure Files에서 사용하기 위한 Windows의 S2S(사이트 간) VPN 구성](storage-files-configure-s2s-vpn.md)