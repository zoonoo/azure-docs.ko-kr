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
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 261efda18b7cecc6370743c604622a8884ff8364
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732306"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Azure Stack에서 Azure CLI를 사용 하 여 API 버전 프로필 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Linux, Mac 및 Windows 클라이언트 플랫폼에서 Azure Stack 개발 키트 (ASDK) 리소스를 관리 하는 Azure 명령줄 인터페이스 (CLI)를 설정 하려면이 문서의 단계를 수행할 수 있습니다.

## <a name="prepare-for-azure-cli"></a>Azure CLI에 대 한 준비

개발 컴퓨터에서 Azure CLI를 사용 하려면 Azure Stack에 대 한 CA 루트 인증서를 해야 합니다. CLI 통해 리소스를 관리 하는 인증서를 사용 합니다.

 - **Azure Stack CA 루트 인증서** 는 ASDK 외부 워크스테이션에서 CLI를 사용 하는 경우 필요 합니다.  

 - **가상 머신 별칭 끝점** "UbuntuLTS" 또는 "Win2012Datacenter," Vm을 배포할 때 이미지 게시자, 제품, SKU 및 버전을 단일 매개 변수로 참조 하는 같은 별칭을 제공 합니다.  

다음 섹션에서는 이러한 값을 얻으려면 하는 방법에 설명 합니다.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Azure Stack CA 루트 인증서 내보내기

통합된 시스템을 사용 하는 경우 CA 루트 인증서를 내보낼 필요가 없습니다. CA 루트 인증서는 ASDK에 내보내기 해야 합니다.

ASDK 루트 인증서를 PEM 형식으로 내보내려면:

1. [Azure Stack에는 Windows VM을 만들](azure-stack-quick-windows-portal.md)합니다.

2. 컴퓨터에 로그인 하 고 관리자 권한 PowerShell 프롬프트를 연 후 다음 스크립트를 실행 합니다.

      ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }
      
    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
```

3. 로컬 컴퓨터에 인증서를 복사 합니다.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>가상 머신 별칭 끝점 설정

가상 컴퓨터 별칭 파일을 호스팅하는 공개적으로 액세스 가능한 끝점을 설정할 수 있습니다. 가상 머신 별칭 파일이 이미지에 대 한 일반 이름을 제공 하는 JSON 파일입니다. Azure CLI 매개 변수로 VM을 배포 하는 경우 이름을 사용 합니다.

1. 사용자 지정 이미지를 게시 하면 게시 중에 지정 된 게시자, 제품, SKU 및 버전 정보를 확인 합니다. Marketplace에서 이미지 인 경우 사용 하 여 정보를 볼 수 있습니다는 ```Get-AzureVMImage``` cmdlet.  

2. 다운로드 합니다 [샘플 파일](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) GitHub에서.

4. Azure Stack에서 저장소 계정을 만듭니다. 완료 되 면 blob 컨테이너를 만듭니다. 액세스 정책을 "공용"를 설정 합니다.  

3. 새 컨테이너에 JSON 파일을 업로드 합니다. 완료 되 면 blob의 URL을 볼 수 있습니다. Blob 이름을 선택한 다음 URL에서 blob 속성을 선택 합니다.

### <a name="install-or-ugrade-cli"></a>설치 또는 CLI

개발 워크스테이션에 로그인 하 고 CLI를 설치 합니다. Azure Stack에는 버전의 Azure CLI 2.0 이상이 필요합니다. 최신 버전의 API 프로필에는 현재 버전의 CLI 필요합니다.  에 설명 된 단계를 사용 하 여 CLI를 설치할 수는 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 문서. 설치가 성공 했는지 여부를 확인 하려면 터미널 또는 명령 프롬프트 창을 열고 다음 명령을 실행 합니다.

```azurecli
az --version
```

Azure CLI 및 컴퓨터에 설치 된 기타 종속 된 라이브러리의 버전에 표시 됩니다.

### <a name="install-python-on-windows"></a>Windows에 Python 설치

1. 설치할 [시스템에 Python 3](https://www.python.org/downloads/)합니다.

2. PIP를 업그레이드 합니다. PIP는 Python에 대 한 패키지 관리자입니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```PowerShell  
    python -m pip install --upgrade pip
    ```

3. 설치 합니다 **로** 모듈입니다. [로](https://pypi.org/project/certifi/) 모듈 및 TLS 호스트의 id를 확인 하는 동안 SSL 인증서를 신뢰할 수 있는지 유효성을 검사 하는 것에 대 한 루트 인증서의 컬렉션입니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```PowerShell
    pip install certifi
    ```

### <a name="install-python-on-linux"></a>Linux에서 Python을 설치 합니다.

1. Ubuntu 16.04 이미지는 Python 2.7 및 Python 3.5가 기본적으로 설치 되어 제공 됩니다. 다음 명령을 실행 하 여 Python 3 버전을 확인할 수 있습니다.

    ```bash  
    python3 --version
    ```

2. PIP를 업그레이드 합니다. PIP는 Python에 대 한 패키지 관리자입니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. 설치 합니다 **로** 모듈입니다. [로](https://pypi.org/project/certifi/) TLS 호스트의 id를 확인 하는 동안 SSL 인증서를 신뢰할 수 있는지를 확인 하는 루트 인증서의 컬렉션입니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```bash
    pip3 install certifi
    ```

### <a name="install-python-on-macos"></a>MacOS에 Python 설치

1. 설치할 [시스템에 Python 3](https://www.python.org/downloads/)합니다. Python 3.7 릴리스용 Python.org 다운로드에 대 한 두 가지 이진 설치 관리자 옵션을 제공합니다. 기본 변형 64 비트 전용 이며 이후 시스템과 10.9 (Mavericks) macOS에서 작동 합니다. 터미널을 열고 다음 명령을 입력 하 여 python 버전을 확인 합니다.

    ```bash  
    python3 --version
    ```

2. PIP를 업그레이드 합니다. PIP는 Python에 대 한 패키지 관리자입니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```bash  
    sudo -H pip3 install --upgrade pip
    ```

3. 설치 합니다 **로** 모듈입니다. [로](https://pypi.org/project/certifi/) 모듈 및 TLS 호스트의 id를 확인 하는 동안 SSL 인증서를 신뢰할 수 있는지 유효성을 검사 하는 것에 대 한 루트 인증서의 컬렉션입니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```bash
    pip3 install certifi
    ```

## <a name="windows-azure-ad"></a>Windows (Azure AD)

이 섹션에서는 안내 CLI 설정으로 id 관리 서비스를 사용 하 여 Azure AD를 사용 하는 Windows 컴퓨터에서 CLI를 사용 하는 경우.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack CA 루트 인증서를 신뢰 합니다.

ASDK를 사용 하는 경우에 원격 컴퓨터에 CA 루트 인증서를 신뢰 해야 합니다. Intregrated 시스템을 사용 하 여이 작업을 수행 해야 합니다.

Azure Stack CA 루트 인증서를 신뢰 하려면 기존 Python 인증서를 추가 합니다.

1. 컴퓨터에 인증서 위치를 찾습니다. 위치는 Python 설치에 따라 달라질 수 있습니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    인증서 위치를 기록해 둡니다. 예: `~/lib/python3.5/site-packages/certifi/cacert.pem` 특정 경로 설치 된 Python의 버전 및 OS에 따라 달라 집니다.

2. 기존 Python 인증서를 추가 하 여 Azure Stack CA 루트 인증서를 신뢰 합니다.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

1. Azure Stack 환경을 실행 하 여 등록 된 `az cloud register` 명령입니다.

    일부 시나리오에서는 직접 아웃 바운드 인터넷 연결은 SSL 가로채기를 적용 하는 방화벽 또는 프록시를 통해 라우팅됩니다. 이러한 경우에는 `az cloud register` 명령 "클라우드에서 끝점을 가져올 수 없습니다."와 같은 오류로 실패할 수 있습니다 이 오류를 해결 하려면 다음 환경 변수를 설정할 수 있습니다.

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. 사용자 환경에 등록 합니다. 실행 하는 경우 다음 매개 변수를 사용 하 여 `az cloud register`입니다.
    | 값 | 예 | 설명 |
    | --- | --- | --- |
    | 환경 이름 | AzureStackUser | 사용 하 여 `AzureStackUser` 사용자 환경에 대 한 합니다. 연산자 인 경우 지정 `AzureStackAdmin`합니다. |
    | 리소스 관리자 끝점 | https://management.local.azurestack.external | 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: `https://management.local.azurestack.external/` 합니다 **ResourceManagerUrl** 통합된 시스템의: `https://management.<region>.<fqdn>/` 에 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 통합된 시스템 끝점에 대 한 질문을 사용 하는 경우 귀하가 클라우드 운영자에 게 문의 합니다. |
    | 저장소 끝점 | local.azurestack.external | `local.azurestack.external` ASDK입니다. Intregrated 시스템, 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | Keyvalut 접미사 | .vault.local.azurestack.external | `.vault.local.azurestack.external` ASDK입니다. 통합된 시스템에 대 한 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | VM 이미지 별칭 문서 끝점 | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 가상 머신 이미지 별칭을 포함 하는 문서의 URI입니다. 자세한 내용은 [# # # 가상 머신 별칭 끝점 설정](#set-up-the-virtual-machine-aliases-endpoint)합니다. |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 다음 명령을 사용 하 여 활성 환경을 설정 합니다.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Azure Stack 특정 API 버전 프로필을 사용 하도록 사용자의 환경 구성을 업데이트 합니다. 구성 값을 업데이트 하려면 다음 명령을 실행 합니다.

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 빌드 전에 Azure Stack의 버전을 실행 하는 경우에 API 버전 프로필을 사용 해야 **2017-03-09-profile** API 버전 프로필 대신 **2018-03-01-하이브리드**합니다. 최신 버전의 Azure CLI를 사용 해야 합니다.
 
1. 사용 하 여 Azure Stack 환경에 로그인 합니다 `az login` 명령입니다. 로그인 할 수 있습니다 Azure Stack 환경에 사용자 또는으로 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)합니다. 

  - 으로 로그인을 *사용자*: 

    Username 및 password 내에서 직접 지정할 수 있습니다는 `az login` 명령, 또는 브라우저를 사용 하 여 인증 합니다. 사용자 계정에 multi-factor authentication 사용 하는 경우 후자를 수행 해야 합니다.

    ```azurecli
    az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
    ```

    > [!NOTE]
    > 사용자 계정에 multi-factor authentication 사용 하는 경우 사용할 수 있습니다 합니다 `az login` 제공 하지 않고 명령을 `-u` 매개 변수입니다. URL 및 인증을 사용 해야 하는 코드를 제공이 명령을 실행 합니다.

  - 으로 로그인을 *서비스 주체*: 
    
    로그인 하기 전에 [Azure portal 통해 서비스 주체를 만들려면](azure-stack-create-service-principals.md) 또는 CLI 역할을 할당 합니다. 이제 다음 명령을 사용 하 여 로그인 합니다.

    ```azurecli  
    az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
    ```

### <a name="test-the-connectivity"></a>연결 테스트

모든 항목을 사용 하 여 CLI를 사용 하 여 Azure Stack에서 리소스 만들기를 설정 합니다. 예를 들어, 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 머신 추가 수 있습니다. 다음 명령을 사용 하 여 "myresourcegroup 이라는" 이름의 리소스 그룹을 만듭니다.

```azurecli
az group create -n MyResourceGroup -l local
```

리소스 그룹 만들기를 완료 하는 경우 이전 명령은 새로 만든된 리소스의 다음 속성을 출력 합니다.

![리소스 그룹에는 출력 만들기](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

이 섹션에서는 안내 CLI 설정으로 id 관리 서비스를 사용 하 여 Active Directory Federated Services (AD FS)을 사용 하 고 Windows 컴퓨터에서 CLI를 사용 합니다.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack CA 루트 인증서를 신뢰 합니다.

ASDK를 사용 하는 경우에 원격 컴퓨터에 CA 루트 인증서를 신뢰 해야 합니다. Intregrated 시스템을 사용 하 여이 작업을 수행 해야 합니다.

1. 컴퓨터에 인증서 위치를 찾습니다. 위치는 Python 설치에 따라 달라질 수 있습니다. 명령 프롬프트 또는 PowerShell 프롬프트를 열고 다음 명령을 입력 합니다.

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    인증서 위치를 기록해 둡니다. 예: `~/lib/python3.5/site-packages/certifi/cacert.pem` 특정 경로 설치 된 Python의 버전 및 OS에 따라 달라 집니다.

2. 기존 Python 인증서를 추가 하 여 Azure Stack CA 루트 인증서를 신뢰 합니다.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

1. Azure Stack 환경을 실행 하 여 등록 된 `az cloud register` 명령입니다.

    일부 시나리오에서는 직접 아웃 바운드 인터넷 연결은 SSL 가로채기를 적용 하는 방화벽 또는 프록시를 통해 라우팅됩니다. 이러한 경우에는 `az cloud register` 명령 "클라우드에서 끝점을 가져올 수 없습니다."와 같은 오류로 실패할 수 있습니다 이 오류를 해결 하려면 다음 환경 변수를 설정할 수 있습니다.

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. 사용자 환경에 등록 합니다. 실행 하는 경우 다음 매개 변수를 사용 하 여 `az cloud register`입니다.
    | 값 | 예 | 설명 |
    | --- | --- | --- |
    | 환경 이름 | AzureStackUser | 사용 하 여 `AzureStackUser` 사용자 환경에 대 한 합니다. 연산자 인 경우 지정 `AzureStackAdmin`합니다. |
    | 리소스 관리자 끝점 | https://management.local.azurestack.external | 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: `https://management.local.azurestack.external/` 합니다 **ResourceManagerUrl** 통합된 시스템의: `https://management.<region>.<fqdn>/` 에 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 통합된 시스템 끝점에 대 한 질문을 사용 하는 경우 귀하가 클라우드 운영자에 게 문의 합니다. |
    | 저장소 끝점 | local.azurestack.external | `local.azurestack.external` ASDK입니다. Intregrated 시스템, 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | Keyvalut 접미사 | .vault.local.azurestack.external | `.vault.local.azurestack.external` ASDK입니다. 통합된 시스템에 대 한 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | VM 이미지 별칭 문서 끝점 | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 가상 머신 이미지 별칭을 포함 하는 문서의 URI입니다. 자세한 내용은 [# # # 가상 머신 별칭 끝점 설정](#set-up-the-virtual-machine-aliases-endpoint)합니다. |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. 다음 명령을 사용 하 여 활성 환경을 설정 합니다.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Azure Stack 특정 API 버전 프로필을 사용 하도록 사용자의 환경 구성을 업데이트 합니다. 구성 값을 업데이트 하려면 다음 명령을 실행 합니다.

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 빌드 전에 Azure Stack의 버전을 실행 하는 경우에 API 버전 프로필을 사용 해야 **2017-03-09-profile** API 버전 프로필 대신 **2018-03-01-하이브리드**합니다. 최신 버전의 Azure CLI를 사용 해야 합니다.

1. 사용 하 여 Azure Stack 환경에 로그인 합니다 `az login` 명령입니다. 로그인 할 수 있습니다 Azure Stack 환경에 사용자 또는으로 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)합니다. 

  - 으로 로그인을 *사용자*:

    Username 및 password 내에서 직접 지정할 수 있습니다는 `az login` 명령, 또는 브라우저를 사용 하 여 인증 합니다. 사용자 계정에 multi-factor authentication 사용 하는 경우 후자를 수행 해야 합니다.

    ```azurecli
    az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
    ```

    > [!NOTE]
    > 사용자 계정에 multi-factor authentication 사용 하는 경우 사용할 수 있습니다 합니다 `az login` 제공 하지 않고 명령을 `-u` 매개 변수입니다. URL 및 인증을 사용 해야 하는 코드를 제공이 명령을 실행 합니다.

  - 으로 로그인을 *서비스 주체*: 
    
    서비스 보안 주체 로그인에 사용 되는.pem 파일을 준비 합니다.

    보안 주체가 생성 된 위치를 클라이언트 컴퓨터에서 서비스 주체 인증서에 개인 키를 사용 하 여 pfx 있는 내보내기 `cert:\CurrentUser\My`, 이름을 주 서버는 같은 이름을 가진 인증서입니다.

    Pfx에서 pem (사용 하 여 OpenSSL 유틸리티를) 변환 합니다.

    CLI에 로그인 합니다.
  
    ```azurecli  
    az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
    ```

### <a name="test-the-connectivity"></a>연결 테스트

모든 항목을 사용 하 여 CLI를 사용 하 여 Azure Stack에서 리소스 만들기를 설정 합니다. 예를 들어, 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 머신 추가 수 있습니다. 다음 명령을 사용 하 여 "myresourcegroup 이라는" 이름의 리소스 그룹을 만듭니다.

```azurecli
az group create -n MyResourceGroup -l local
```

리소스 그룹 만들기를 완료 하는 경우 이전 명령은 새로 만든된 리소스의 다음 속성을 출력 합니다.

![리소스 그룹에는 출력 만들기](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

이 섹션에서는 안내 CLI 설정으로 id 관리 서비스를 사용 하 여 Azure AD를 사용 하는 Linux 컴퓨터에서 CLI를 사용 하는 경우.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack CA 루트 인증서를 신뢰 합니다.

ASDK를 사용 하는 경우에 원격 컴퓨터에 CA 루트 인증서를 신뢰 해야 합니다. Intregrated 시스템을 사용 하 여이 작업을 수행 해야 합니다.

기존 Python 인증서를 추가 하 여 Azure Stack CA 루트 인증서를 신뢰 합니다.

1. 컴퓨터에 인증서 위치를 찾습니다. 위치는 Python 설치에 따라 달라질 수 있습니다. Pip 및를 해야 합니다 [모듈을 설치](#install-python-on-linux)합니다. Bash 프롬프트에서 다음 Python 명령에 사용할 수 있습니다.

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    인증서 위치; 기록 예를 들어 `~/lib/python3.5/site-packages/certifi/cacert.pem`합니다. 특정 경로는 운영 체제에 설치 된 Python 버전에 따라 달라 집니다.

2. 인증서 경로 사용 하 여 다음 bash 명령은 실행 합니다.

  - 원격 Linux 컴퓨터:

    ```bash  
    sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
    ```

  - Azure Stack 환경 내에서 Linux 컴퓨터:

    ```bash  
    sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
    ```

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 단계를 사용 하 여 Azure Stack에 연결 합니다.

1. Azure Stack 환경을 실행 하 여 등록 된 `az cloud register` 명령입니다. 일부 시나리오에서는 직접 아웃 바운드 인터넷 연결은 SSL 가로채기를 적용 하는 방화벽 또는 프록시를 통해 라우팅됩니다. 이러한 경우에는 `az cloud register` 명령 "클라우드에서 끝점을 가져올 수 없습니다."와 같은 오류로 실패할 수 있습니다 이 오류를 해결 하려면 다음 환경 변수를 설정할 수 있습니다.

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. 사용자 환경에 등록 합니다. 실행 하는 경우 다음 매개 변수를 사용 하 여 `az cloud register`입니다.
    | 값 | 예 | 설명 |
    | --- | --- | --- |
    | 환경 이름 | AzureStackUser | 사용 하 여 `AzureStackUser` 사용자 환경에 대 한 합니다. 연산자 인 경우 지정 `AzureStackAdmin`합니다. |
    | 리소스 관리자 끝점 | https://management.local.azurestack.external | 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: `https://management.local.azurestack.external/` 합니다 **ResourceManagerUrl** 통합된 시스템의: `https://management.<region>.<fqdn>/` 에 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 통합된 시스템 끝점에 대 한 질문을 사용 하는 경우 귀하가 클라우드 운영자에 게 문의 합니다. |
    | 저장소 끝점 | local.azurestack.external | `local.azurestack.external` ASDK입니다. Intregrated 시스템, 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | Keyvalut 접미사 | .vault.local.azurestack.external | `.vault.local.azurestack.external` ASDK입니다. 통합된 시스템에 대 한 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | VM 이미지 별칭 문서 끝점 | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 가상 머신 이미지 별칭을 포함 하는 문서의 URI입니다. 자세한 내용은 [# # # 가상 머신 별칭 끝점 설정](#set-up-the-virtual-machine-aliases-endpoint)합니다. |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. 활성 상태의 환경을 설정 합니다. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Azure Stack 특정 API 버전 프로필을 사용 하도록 사용자의 환경 구성을 업데이트 합니다. 구성 값을 업데이트 하려면 다음 명령을 실행 합니다.

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 빌드 전에 Azure Stack의 버전을 실행 하는 경우에 API 버전 프로필을 사용 해야 **2017-03-09-profile** API 버전 프로필 대신 **2018-03-01-하이브리드**합니다. 최신 버전의 Azure CLI를 사용 해야 합니다.

5. 사용 하 여 Azure Stack 환경에 로그인 합니다 `az login` 명령입니다. 로그인 할 수 있습니다 Azure Stack 환경에 사용자 또는으로 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)합니다. 

    * 으로 로그인을 *사용자*:

    Username 및 password 내에서 직접 지정할 수 있습니다는 `az login` 명령, 또는 브라우저를 사용 하 여 인증 합니다. 사용자 계정에 multi-factor authentication 사용 하는 경우 후자를 수행 해야 합니다.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

    > [!NOTE]
    > 사용자 계정에 multi-factor authentication 사용 하는 경우 사용할 수 있습니다 합니다 `az login` 제공 하지 않고 명령을 `-u` 매개 변수입니다. URL 및 인증을 사용 해야 하는 코드를 제공이 명령을 실행 합니다.
   
    * 으로 로그인을 *서비스 주체*
    
    로그인 하기 전에 [Azure portal 통해 서비스 주체를 만들려면](azure-stack-create-service-principals.md) 또는 CLI 역할을 할당 합니다. 이제 다음 명령을 사용 하 여 로그인 합니다.

      ```azurecli  
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

### <a name="test-the-connectivity"></a>연결 테스트

모든 항목을 사용 하 여 CLI를 사용 하 여 Azure Stack에서 리소스 만들기를 설정 합니다. 예를 들어, 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 머신 추가 수 있습니다. 다음 명령을 사용 하 여 "myresourcegroup 이라는" 이름의 리소스 그룹을 만듭니다.

```azurecli
    az group create -n MyResourceGroup -l local
```

리소스 그룹 만들기를 완료 하는 경우 이전 명령은 새로 만든된 리소스의 다음 속성을 출력 합니다.

![리소스 그룹에는 출력 만들기](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

이 섹션에서는 안내 CLI 설정 관리 서비스로, Active Directory Federated Services (AD FS)를 사용 하는 Linux 컴퓨터에서 CLI를 사용 하는 경우.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack CA 루트 인증서를 신뢰 합니다.

ASDK를 사용 하는 경우에 원격 컴퓨터에 CA 루트 인증서를 신뢰 해야 합니다. Intregrated 시스템을 사용 하 여이 작업을 수행 해야 합니다.

기존 Python 인증서를 추가 하 여 Azure Stack CA 루트 인증서를 신뢰 합니다.

1. 컴퓨터에 인증서 위치를 찾습니다. 위치는 Python 설치에 따라 달라질 수 있습니다. Pip 및를 해야 합니다 [모듈을 설치](#install-python-on-linux)합니다. Bash 프롬프트에서 다음 Python 명령에 사용할 수 있습니다.

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    인증서 위치; 기록 예를 들어 `~/lib/python3.5/site-packages/certifi/cacert.pem`합니다. 특정 경로는 운영 체제에 설치 된 Python 버전에 따라 달라 집니다.

2. 인증서 경로 사용 하 여 다음 bash 명령은 실행 합니다.

  - 원격 Linux 컴퓨터:

    ```bash  
    sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
    ```

  - Azure Stack 환경 내에서 Linux 컴퓨터:

    ```bash  
    sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
    ```

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 단계를 사용 하 여 Azure Stack에 연결 합니다.

1. Azure Stack 환경을 실행 하 여 등록 된 `az cloud register` 명령입니다. 일부 시나리오에서는 직접 아웃 바운드 인터넷 연결은 SSL 가로채기를 적용 하는 방화벽 또는 프록시를 통해 라우팅됩니다. 이러한 경우에는 `az cloud register` 명령 "클라우드에서 끝점을 가져올 수 없습니다."와 같은 오류로 실패할 수 있습니다 이 오류를 해결 하려면 다음 환경 변수를 설정할 수 있습니다.

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. 사용자 환경에 등록 합니다. 실행 하는 경우 다음 매개 변수를 사용 하 여 `az cloud register`입니다.
    | 값 | 예 | 설명 |
    | --- | --- | --- |
    | 환경 이름 | AzureStackUser | 사용 하 여 `AzureStackUser` 사용자 환경에 대 한 합니다. 연산자 인 경우 지정 `AzureStackAdmin`합니다. |
    | 리소스 관리자 끝점 | https://management.local.azurestack.external | 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: `https://management.local.azurestack.external/` 합니다 **ResourceManagerUrl** 통합된 시스템의: `https://management.<region>.<fqdn>/` 에 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` 통합된 시스템 끝점에 대 한 질문을 사용 하는 경우 귀하가 클라우드 운영자에 게 문의 합니다. |
    | 저장소 끝점 | local.azurestack.external | `local.azurestack.external` ASDK입니다. Intregrated 시스템, 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | Keyvalut 접미사 | .vault.local.azurestack.external | `.vault.local.azurestack.external` ASDK입니다. 통합된 시스템에 대 한 시스템에 대 한 끝점을 사용 해야 합니다.  |
    | VM 이미지 별칭 문서 끝점 | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 가상 머신 이미지 별칭을 포함 하는 문서의 URI입니다. 자세한 내용은 [# # # 가상 머신 별칭 끝점 설정](#set-up-the-virtual-machine-aliases-endpoint)합니다. |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. 활성 상태의 환경을 설정 합니다. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Azure Stack 특정 API 버전 프로필을 사용 하도록 사용자의 환경 구성을 업데이트 합니다. 구성 값을 업데이트 하려면 다음 명령을 실행 합니다.

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >1808 빌드 전에 Azure Stack의 버전을 실행 하는 경우에 API 버전 프로필을 사용 해야 **2017-03-09-profile** API 버전 프로필 대신 **2018-03-01-하이브리드**합니다. 최신 버전의 Azure CLI를 사용 해야 합니다.

5. 사용 하 여 Azure Stack 환경에 로그인 합니다 `az login` 명령입니다. 로그인 할 수 있습니다 Azure Stack 환경에 사용자 또는으로 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)합니다. 

6. 서명하세요: 

  *  로 **사용자** 장치 코드를 사용 하 여 웹 브라우저를 사용 하 여:  

  ```azurecli  
    az login --use-device-code
  ```

  > [!NOTE]  
  >URL 및 인증을 사용 해야 하는 코드를 제공 명령을 실행 합니다.

  * 서비스 주체로:
        
    서비스 보안 주체 로그인에 사용 되는.pem 파일을 준비 합니다.

      * 보안 주체가 생성 된 위치를 클라이언트 컴퓨터에서 서비스 주체 인증서에 개인 키를 사용 하 여 pfx 있는 내보내기 `cert:\CurrentUser\My`, 이름을 주 서버는 같은 이름을 가진 인증서입니다.
  
      * Pfx에서 pem (사용 하 여 OpenSSL 유틸리티를) 변환 합니다.

    CLI에 로그인 합니다.

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>연결 테스트

모든 항목을 사용 하 여 CLI를 사용 하 여 Azure Stack에서 리소스 만들기를 설정 합니다. 예를 들어, 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 머신 추가 수 있습니다. 다음 명령을 사용 하 여 "myresourcegroup 이라는" 이름의 리소스 그룹을 만듭니다.

```azurecli
  az group create -n MyResourceGroup -l local
```

리소스 그룹 만들기를 완료 하는 경우 이전 명령은 새로 만든된 리소스의 다음 속성을 출력 합니다.

![리소스 그룹에는 출력 만들기](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>알려진 문제

Azure Stack에서 CLI를 사용 하는 경우 발견 된 문제 들입니다.

 - CLI 대화형 모드. 예를 들어를 `az interactive` 명령, Azure Stack에서 아직 지원 되지 않습니다.
 - Azure Stack에서 사용할 수 있는 가상 머신 이미지 목록을 사용 합니다 `az vm image list --all` 대신 명령을 `az vm image list` 명령입니다. 지정 된 `--all` 옵션을 사용 하면 Azure Stack 환경에서 사용할 수 있는 이미지만 반환 하는 응답 합니다.
 - Azure에서 사용할 수 있는 가상 머신 이미지 별칭 Azure Stack에 적용할 수 없습니다. 가상 머신 이미지를 사용 하는 경우에 전체 URN 매개 변수를 사용 해야 합니다 (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) 이미지 별칭 대신 합니다. 이 URN에서 파생 된 이미지 사양과 일치 해야 합니다는 `az vm images list` 명령입니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI을 사용하여 템플릿 배포](azure-stack-deploy-template-command-line.md)
- [Azure Stack 사용자 (운영자)에 대 한 Azure CLI를 사용 하도록 설정](../azure-stack-cli-admin.md)
- [사용자 권한 관리](azure-stack-manage-permissions.md) 
