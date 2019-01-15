---
title: Azure Stack 사용자에 대 한 Azure CLI를 사용 하도록 설정 | Microsoft Docs
description: 플랫폼 간 명령줄 인터페이스 (CLI)를 사용 하 여 관리 하 고 Azure Stack에서 리소스를 배포 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.openlocfilehash: ba8bed71d24c1b4ed71611b5cd2dfeb7800408b8
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304026"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Azure Stack 사용자에 대 한 Azure CLI를 사용 하도록 설정

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure CLI가 개발 컴퓨터에서 사용할 수 있도록 Azure Stack의 사용자에 게 CA 루트 인증서를 제공할 수 있습니다. 사용자가 인증서를 CLI 통해 리소스를 관리 해야 합니다.

* **Azure Stack CA 루트 인증서** 필요한 경우 사용자가 Azure Stack 개발 키트 외부 워크스테이션에서 CLI를 사용 합니다.  

* **가상 머신 별칭 끝점** "UbuntuLTS" 또는 "Win2012Datacenter," Vm을 배포할 때 이미지 게시자, 제품, SKU 및 버전을 단일 매개 변수로 참조 하는 같은 별칭을 제공 합니다.  

다음 섹션에서는 이러한 값을 얻으려면 하는 방법에 설명 합니다.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure Stack CA 루트 인증서 내보내기

개발 키트에는 개발 키트 환경 내에서 실행 되는 테 넌 트 가상 머신에서 Azure Stack CA 루트 인증서를 찾을 수 있습니다. Azure Stack 루트 인증서를 PEM 형식으로 내보내려면 개발 키트 또는 테 넌 트 가상 컴퓨터에 로그인 하 고 다음 스크립트를 실행 합니다.

```powershell
$label = "<Your Azure Stack CA root certificate name>"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>가상 머신 별칭 끝점 설정

Azure Stack 운영자는 가상 컴퓨터 별칭 파일을 호스팅하는 공개적으로 액세스 가능한 끝점을 설정 해야 합니다. 가상 머신 별칭 파일이 이미지에 대 한 일반 이름을 제공 하는 JSON 파일입니다. VM이 Azure CLI 매개 변수로 배포 하는 경우에 이후에 해당 이름이 지정 됩니다.  

별칭 파일에 항목을 추가 하기 전에 확인 하면 [Azure Marketplace에서 이미지를 다운로드](azure-stack-download-azure-marketplace-item.md), 있거나 [사용자 지정 이미지를 게시](azure-stack-add-vm-image.md)합니다. 사용자 지정 이미지를 게시 하면 게시 중에 지정 된 게시자, 제품, SKU 및 버전 정보를 확인 합니다. Marketplace에서 이미지 인 경우 사용 하 여 정보를 볼 수 있습니다는 ```Get-AzureVMImage``` cmdlet.  

A [샘플 별칭 파일](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) 많은 일반적인 이미지를 사용 하 여 별칭은 사용할 수 있습니다. 시작 점으로 사용할 수 있습니다. CLI 클라이언트 연결할 수 있는 공간에서이 파일을 호스트 합니다. 한 가지 방법은 blob 저장소 계정에 파일을 호스트 URL을 사용자와 공유 하는 것입니다.

1. 다운로드 합니다 [샘플 파일](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) GitHub에서.
2. Azure Stack에서 새 저장소 계정을 만듭니다. 완료 되 면 새 blob 컨테이너를 만듭니다. 액세스 정책을 "공용"를 설정 합니다.  
3. 새 컨테이너에 JSON 파일을 업로드 합니다. 완료 되 면 blob 이름을 선택한 다음 blob 속성에서 URL을 선택 하 여 blob의 URL을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)
- [PowerShell로 연결](azure-stack-connect-powershell.md)
- [사용자 권한 관리](azure-stack-manage-permissions.md)
