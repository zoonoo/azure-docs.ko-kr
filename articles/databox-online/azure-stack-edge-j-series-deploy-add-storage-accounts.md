---
title: 자습서 - Azure Stack Edge Pro GPU를 사용하여 스토리지 계정에 데이터 전송 | Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스에서 로컬 및 Edge 스토리지 계정을 추가하고 연결하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: c24970b30e522d593a491194dbbabb0421ad7f4b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891057"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>자습서: Azure Stack Edge Pro GPU를 사용하여 스토리지 계정을 통해 데이터 전송 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 자습서에서는 Azure Stack Edge Pro 디바이스에서 스토리지 계정을 추가하고 연결하는 방법에 대해 설명합니다. 스토리지 계정이 추가되면 Azure Stack Edge Pro에서 데이터를 Azure로 전송할 수 있습니다.

이 절차를 완료하는 데 약 30분이 걸릴 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 스토리지 계정 추가
> * 스토리지 계정에 연결

 
## <a name="prerequisites"></a>필수 조건

스토리지 계정을 Azure Stack Edge Pro에 추가하려면 먼저 다음 사항을 확인합니다.

- [Azure Stack Edge Pro 설치](azure-stack-edge-gpu-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.

- [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에서 설명한 대로 물리적 디바이스를 활성화했습니다.


## <a name="add-an-edge-storage-account"></a>Edge 스토리지 계정 추가

Edge 스토리지 계정을 만들려면 다음 절차를 수행합니다.

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Edge 스토리지 계정에 연결

이제 *http* 또는 *https*를 통해 Edge 스토리지 REST API에 연결할 수 있습니다.

- *https*는 안전하고 추천되는 방법입니다.
- *http*는 신뢰할 수 있는 네트워크를 통해 연결할 때 사용됩니다.

## <a name="connect-via-http"></a>http를 통해 연결

http를 통해 Edge 스토리지 REST API에 연결하려면 다음 단계를 수행해야 합니다.

- Azure 일치 서비스 VIP 및 Blob 서비스 엔드포인트를 원격 호스트에 추가
- 연결 확인 

이러한 각 단계는 다음 섹션에 설명되어 있습니다.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>디바이스 IP 주소 및 Blob 서비스 엔드포인트를 원격 클라이언트에 추가

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>연결 확인

연결을 확인하려면 일반적으로 이전 단계에서 수집한 다음 정보(다를 수 있음)가 필요합니다.

- Storage 계정 이름
- 스토리지 계정 액세스 키
- Blob 서비스 엔드포인트

스토리지 계정 이름과 Blob 서비스 엔드포인트가 이미 있습니다. Azure PowerShell 클라이언트를 사용하여 Azure Resource Manager를 통해 디바이스에 연결하여 스토리지 계정 액세스 키를 가져올 수 있습니다.

[Azure Resource Manager를 통해 디바이스에 연결](azure-stack-edge-j-series-connect-resource-manager.md)의 단계를 따릅니다. Azure Resource Manager를 통해 로컬 디바이스 API에 로그인하면 디바이스의 스토리지 계정 목록을 가져옵니다. 다음 cmdlet을 실행합니다.

`Get-AzureRMStorageAccount`

디바이스의 스토리지 계정 목록에서 액세스 키가 필요한 스토리지 계정을 식별합니다. 스토리지 계정 이름 및 리소스 그룹을 적어 둡니다.

샘플 출력은 다음과 같습니다.

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

액세스 키를 가져오려면 다음 cmdlet을 실행합니다.

`Get-AzureRmStorageAccountAccessKey`

샘플 출력은 다음과 같습니다.

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

이 키를 복사하여 저장합니다. 이 키를 사용하여 Azure Storage Explorer를 통해 연결을 확인합니다.

연결이 성공적으로 설정되었는지 확인하려면 Storage Explorer를 사용하여 외부 스토리지 계정에 연결합니다. Storage Explorer가 없는 경우 [Storage Explorer를 다운로드](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409)하세요.

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>https를 통해 연결

https를 통해 Azure Blob 스토리지 REST API에 연결하려면 다음 단계를 수행해야 합니다.

- Blob 엔드포인트 인증서 가져오기
- 클라이언트 또는 원격 호스트의 인증서 가져오기
- 클라이언트 또는 원격 호스트에 디바이스 IP 및 Blob 서비스 엔드포인트 추가
- 연결 구성 및 확인

이러한 각 단계는 다음 섹션에 설명되어 있습니다.

### <a name="get-certificate"></a>인증서 가져오기

HTTPS를 통해 Blob 스토리지에 액세스하려면 디바이스에 대한 SSL 인증서가 필요합니다. 또한 이 인증서를 프라이빗 키가 연결된 *.pfx* 파일로 Azure Stack Edge Pro 디바이스에 업로드합니다. 테스트 및 개발 목적으로만 이러한 인증서를 만들고 Azure Stack Edge Pro 디바이스에 업로드하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Blob 엔드포인트 인증서 만들기](azure-stack-edge-j-series-manage-certificates.md#create-certificates-optional)
- [Blob 엔드포인트 인증서 업로드](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)
- [디바이스에 액세스하는 클라이언트에서 인증서 가져오기](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)

### <a name="import-certificate"></a>인증서 가져오기

Azure Storage Explorer를 사용하여 디바이스의 스토리지 계정에 연결하는 경우 PEM 형식의 인증서를 Storage Explorer로 가져와야 합니다. Windows 환경에서 Base-64로 인코딩된 *.cer*는 PEM 형식과 동일합니다.

다음 단계를 수행하여 Azure Storage Explorer에서 인증서를 가져옵니다.

1. Azure Storage Explorer에서 Azure Stack API를 대상으로 하는지 확인합니다. **편집 > 대상 Azure Stack API**로 차례로 이동합니다. 메시지가 표시되면 Storage Explorer를 다시 시작하여 변경 내용을 적용합니다.

2. SSL 인증서를 가져오려면 **편집 > SSL 인증서 > 인증서 가져오기**로 차례로 이동합니다.

  
   ![Storage Explorer로 인증서 가져오기](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. 서명 체인과 Blob 인증서를 탐색하여 제공합니다. 서명 체인과 Blob 인증서는 모두 Windows 시스템에서 Base64로 인코딩된 형식과 동일한 PEM 형식이어야 합니다. 인증서를 성공적으로 가져왔다는 알림이 표시됩니다.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>디바이스 IP 주소 및 Blob 서비스 엔드포인트 추가

[*http*를 통해 연결할 때 디바이스 IP 주소 및 Blob 서비스 엔드포인트 추가](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client)와 동일한 단계를 따릅니다.

### <a name="configure-and-verify-connection"></a>연결 구성 및 확인

[*http*를 통해 연결하는 동안 사용한 연결을 구성하고 확인](#verify-connection)하는 단계를 수행합니다. 유일한 차이점은 *http 옵션 사용*을 선택하지 않은 상태로 두어야 한다는 것입니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Azure Stack Edge Pro 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 스토리지 계정 추가
> * 스토리지 계정에 연결

Azure Stack Edge Pro를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro를 사용하여 데이터 변환](./azure-stack-edge-j-series-deploy-configure-compute.md)


