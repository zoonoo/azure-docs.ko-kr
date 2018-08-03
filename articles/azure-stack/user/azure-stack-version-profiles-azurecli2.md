---
title: CLI 사용 하 여 Azure Stack에 연결 | Microsoft Docs
description: 플랫폼 간 명령줄 인터페이스 (CLI)를 사용 하 여 관리 하 고 Azure Stack에서 리소스를 배포 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: e5dd41b34c41c442034e0a7ccb74c8d5b6583753
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436712"
---
# <a name="use-api-version-profiles-with-azure-cli-20-in-azure-stack"></a>Azure Stack에서 Azure CLI 2.0을 사용 하 여 API 버전 프로필 사용

Linux, Mac 및 Windows 클라이언트 플랫폼에서 Azure Stack 개발 키트 리소스를 관리 하는 Azure 명령줄 인터페이스 (CLI)를 설정 하려면이 문서의 단계를 수행할 수 있습니다.

## <a name="install-cli"></a>CLI 설치

개발 워크스테이션에 로그인 하 고 CLI를 설치 합니다. Azure Stack의 Azure CLI 2.0 버전에 필요합니다. 에 설명 된 단계를 사용 하 여 설치할 수 있습니다 합니다 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 문서. 설치가 성공 했는지 여부를 확인 하려면 터미널 또는 명령 프롬프트 창을 열고 다음 명령을 실행 합니다.

```azurecli
az --version
```

Azure CLI 및 컴퓨터에 설치 된 기타 종속 된 라이브러리의 버전에 표시 됩니다.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack CA 루트 인증서를 신뢰 합니다.

1. Azure Stack CA 루트 인증서를 가져오는 [귀하가 Azure Stack 운영자](..\azure-stack-cli-admin.md#export-the-azure-stack-ca-root-certificate) 신뢰 하 고 있습니다. Azure Stack CA 루트 인증서를 신뢰 하려면 기존 Python 인증서를 추가 합니다.

1. 컴퓨터에 인증서 위치를 찾습니다. 위치는 Python 설치에 따라 달라질 수 있습니다. 해야 합니다 [pip](https://pip.pypa.io) 하며 [로](https://pypi.org/project/certifi/) 모듈을 설치 합니다. Bash 프롬프트에서 다음 Python 명령에 사용할 수 있습니다.

  ```bash  
    python -c "import certifi; print(certifi.where())"
  ```

  인증서 위치를 기록해 둡니다. 예: `~/lib/python3.5/site-packages/certifi/cacert.pem` 특정 경로 설치 된 Python의 버전 및 OS에 따라 달라 집니다.

### <a name="set-the-path-for-a-development-machine-inside-the-cloud"></a>클라우드 내에서 개발 컴퓨터에 대 한 경로 설정 합니다.

Azure Stack 환경 내에서 만든 Linux 컴퓨터에서 CLI를 실행 하는 경우 인증서의 경로 사용 하 여 다음 bash 명령을 실행 합니다.

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
```

### <a name="set-the-path-for-a-development-machine-outside-the-cloud"></a>클라우드 외부 개발 컴퓨터에 대 한 경로 설정 합니다.

컴퓨터에서 CLI를 실행 하는 경우 **외부** Azure Stack 환경:  

1. 설정 해야 합니다 [Azure Stack에 VPN 연결](azure-stack-connect-azure-stack.md)합니다.

1. Azure Stack 연산자에서 가져온 PEM 인증서를 복사 하 고 (PATH_TO_PEM_FILE) 파일의 위치를 기록해 둡니다.

1. 끝 개발 워크스테이션의 운영 체제에 따라 다음 명령을 실행 합니다.

#### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
```

#### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>가상 머신 별칭 끝점 가져오기

사용자는 CLI를 사용 하 여 가상 컴퓨터를 만들 수 있습니다, 전에 Azure Stack 운영자에 게 문의 하 고 가상 머신 별칭 끝점 URI를 가져올 되어야 합니다. Azure는 다음 URI를 사용 하는 예를 들어: https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json합니다. 클라우드 관리자는 Azure Stack marketplace에서 사용할 수 있는 이미지를 사용 하 여 Azure Stack에 대 한 유사한 끝점 설정 해야 합니다. 사용자가 끝점 URI를 전달 해야 하는 `endpoint-vm-image-alias-doc` 매개 변수를를 `az cloud register` 다음 섹션에 표시 된 대로 명령을 합니다. 
   

## <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 단계를 사용 하 여 Azure Stack에 연결 합니다.

1. Azure Stack 환경을 실행 하 여 등록 된 `az cloud register` 명령입니다.
   
   a. 등록 하는 *클라우드 관리* 환경에서 사용 하 여:

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   나. 등록 하는 *사용자* 환경에서 사용 하 여:

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

1. 다음 명령을 사용 하 여 활성 환경을 설정 합니다.

   a. 에 대 한 합니다 *클라우드 관리* 환경에서 사용 하 여:

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   나. 에 대 한 합니다 *사용자* 환경에서 사용 하 여:

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

1. Azure Stack 특정 API 버전 프로필을 사용 하도록 사용자의 환경 구성을 업데이트 합니다. 구성 값을 업데이트 하려면 다음 명령을 실행 합니다.

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

1. 사용 하 여 Azure Stack 환경에 로그인 합니다 `az login` 명령입니다. 로그인 할 수 있습니다 Azure Stack 환경에 사용자 또는으로 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects)합니다. 

   * 으로 로그인을 *사용자*: username 및 password 내에서 직접 지정할 수 있습니다는 `az login` 명령을 선택 하거나 브라우저를 사용 하 여 인증 합니다. 사용자 계정에 multi-factor authentication 사용 하는 경우에 후자를 수행 해야 합니다.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > 사용자 계정에 multi-factor authentication 사용 하는 경우 사용할 수 있습니다 합니다 `az login command` 제공 하지 않고는 `-u` 매개 변수입니다. URL 및 인증을 사용 해야 하는 코드를 제공 명령을 실행 합니다.
   
   * 으로 로그인을 *서비스 주체*: 로그인 하기 전에 [Azure portal 통해 서비스 주체 만들기](azure-stack-create-service-principals.md) 또는 CLI 역할을 할당 합니다. 이제 다음 명령을 사용 하 여 로그인 합니다.

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>연결 테스트

이제는 것은 모두 갖추어 설치 프로그램을 살펴보겠습니다 CLI를 사용 하 여 Azure Stack에서 리소스를 만듭니다. 예를 들어, 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 머신 추가 수 있습니다. 다음 명령을 사용 하 여 "myresourcegroup 이라는" 이름의 리소스 그룹을 만듭니다.

```azurecli
az group create \
  -n MyResourceGroup -l local
```

리소스 그룹 만들기를 완료 하는 경우 이전 명령은 새로 만든된 리소스의 다음 속성을 출력 합니다.

![리소스 그룹에는 출력 만들기](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>알려진 문제
Azure Stack에서 CLI를 사용 하는 경우 주의 해야 하는 몇 가지 알려진된 문제는

 - CLI 대화형 모드 즉 `az interactive` 명령은 Azure Stack에서 아직 지원 되지 않습니다.
 - Azure Stack에서 사용할 수 있는 가상 머신 이미지 목록을 사용 합니다 `az vm images list --all` 대신 명령을 `az vm image list` 명령입니다. 지정 된 `--all` 옵션을 선택 하면 Azure Stack 환경에서 사용할 수 있는 이미지만 응답 반환 하는지 확인 합니다.
 - Azure에서 사용할 수 있는 가상 머신 이미지 별칭 Azure Stack에 적용할 수 없습니다. 가상 머신 이미지를 사용 하는 경우에 전체 URN 매개 변수를 사용 해야 합니다 (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) 이미지 별칭 대신 합니다. 이 URN에서 파생 된 이미지 사양과 일치 해야 합니다는 `az vm images list` 명령입니다.

## <a name="next-steps"></a>다음 단계

[Azure CLI을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)

[Azure Stack 사용자 (운영자)에 대 한 Azure CLI를 사용 하도록 설정](..\azure-stack-cli-admin.md)

[사용자 권한 관리](azure-stack-manage-permissions.md)