---
title: "CLI를 사용하여 Service Fabric 클러스터와 상호 작용 | Microsoft Docs"
description: "Azure CLI를 사용하여 Service Fabric 클러스터와 상호 작용하는 방법"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 615e7ea84aae45f384edb671a28e4ff98b4ade3a
ms.openlocfilehash: d61b7a9c8199b15c8bb24e7146ea93a2f67fb0a7
ms.lasthandoff: 02/17/2017


---
# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Azure CLI를 사용하여 Service Fabric 클러스터와 상호 작용
Linux에서 Azure CLI를 사용하여 Linux 컴퓨터에서 Service Fabric 클러스터와 상호 작용할 수 있습니다.

첫 번째 단계는 git 리포지토리에서 최신 버전의 CLI를 가져오고 다음 명령을 사용하여 경로에서 설정하는 것입니다.

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

지원하는 각 명령에 대해 도움말을 보려면 명령의 이름을 입력하면 됩니다. 명령에 대해서는 자동 완성이 지원됩니다. 예를 들어 다음 명령은 모든 응용 프로그램 명령에 대한 도움말을 제공합니다. 

```sh
 azure servicefabric application 
```

다음 예와 같이 특정 명령으로 추가 필터링할 수 있습니다.

```sh
 azure servicefabric application  create
```

CLI에서 자동 완성을 사용하려면 다음 명령을 실행합니다.

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

다음 명령은 클러스터에 연결하고 클러스터에 있는 노드를 보여줍니다.

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

명명된 매개 변수를 사용하고 매개 변수가 어떤 기능이 있는지 알아보려면 명령 뒤에 --help를 입력하면 됩니다. 예:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

**클러스터에 포함되지 않은**컴퓨터에서 여러 컴퓨터 클러스터에 연결하는 경우 다음 명령을 사용합니다.

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

PublicIPorFQDN 태그를 실제 IP 또는 FQDN으로 적절하게 바꿉니다. **클러스터에 포함된**컴퓨터에서 여러 컴퓨터 클러스터에 연결하는 경우 다음 명령을 사용합니다.

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

PowerShell 또는 CLI를 사용하여 Azure 포털을 통해 만든 Linux Service Fabric 클러스터와 상호 작용할 수 있습니다. 

> [!WARNING]
> 이러한 클러스터는 안전하지 않으므로 클러스터 매니페스트에서 공용 IP 주소를 추가하여 one-box를 열 수 있습니다.

## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Azure CLI를 사용하여 Service Fabric 클러스터에 연결
다음 Azure CLI 명령은 보안 클러스터에 연결하는 방법을 설명합니다. 인증서 세부 정보는 클러스터 노드의 인증서와 일치해야 합니다.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

인증서에 인증 기관(CA)이 있는 경우 다음 예와 같이 --ca-cert-path 매개 변수를 추가해야 합니다. 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
여러 CA가 있는 경우 구분 기호로 쉼표를 사용합니다.

인증서에 있는 일반 이름이 연결 끝점과 일치하지 않는 경우 다음 명령에 표시된 것처럼 `--strict-ssl-false` 매개 변수를 사용하여 확인을 바이패스할 수 있습니다. 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false 
```

CA 확인을 건너뛰려면 다음 명령에 표시된 것처럼 --reject-unauthorized-false 매개 변수를 추가하면 됩니다. 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

연결 후에는 클러스터와 상호 작용하기 위해 다른 CLI 명령을 실행할 수 있습니다. 

## <a name="deploying-your-service-fabric-application"></a>서비스 패브릭 응용 프로그램 배포
다음 명령을 실행하여 서비스 패브릭 응용 프로그램을 복사, 등록 및 시작합니다.

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>응용 프로그램 업그레이드
이 과정은 [Windows의 과정](service-fabric-application-upgrade-tutorial-powershell.md)과 유사합니다.

프로젝트 루트 디렉터리에서 응용 프로그램을 빌드, 복사, 등록 및 만듭니다. 응용 프로그램 인스턴스가 fabric:/MySFApp으로 명명된 경우 형식은 MySFApp이고 명령은 다음과 같습니다.

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

응용 프로그램을 변경하고 수정된 서비스를 다시 빌드합니다.  수정된 서비스의 매니페스트 파일(ServiceManifest.xml)을 업데이트된 버전의 서비스(및 필요에 따라 코드, 구성 또는 데이터)로 업데이트합니다. 또한 응용 프로그램의 매니페스트(ApplicationManifest.xml)를 응용 프로그램에 대한 업데이트된 버전 번호와 수정된 서비스로 수정합니다.  이제 다음 명령을 사용하여 업데이트된 응용 프로그램을 복사 및 등록합니다.

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

이제, 다음 명령을 사용하여 응용 프로그램 업그레이드를 시작할 수 있습니다.

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

SFX를 사용하여 응용 프로그램 업그레이드를 모니터링할 수 있습니다. 몇 분 후 응용 프로그램이 업데이트됩니다.  업데이트된 앱에서 오류를 시도해보고 서비스 패브릭의 자동 롤백 기능을 확인해볼 수도 있습니다.

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>PEM에서 PFX로 또는 그 반대로 변환

다른 환경에 있을 수 있는 보안 클러스터에 액세스하여 로컬 컴퓨터(Windows 또는 Linux)에 인증서를 설치해야 할 수 있습니다. 예를 들어 Windows 컴퓨터에서 보안된 Linux 클러스터를 액세스하거나 그 반대로 액세스하는 동안, 인증서를 PFX에서 PEM으로 또는 그 반대로 변환해야 할 수 있습니다. 

PEM 파일에서 PFX 파일로 변환하려면 다음 명령을 사용합니다.

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

PFX 파일에서 PEM 파일로 변환하려면 다음 명령을 사용합니다.

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

자세한 내용은 [OpenSSL 설명서](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)를 참조하세요.

<a id="troubleshooting"></a>
## <a name="troubleshooting"></a>문제 해결
### <a name="copying-of-the-application-package-does-not-succeed"></a>응용 프로그램 패키지 복사 실패
`openssh` 가 설치되어 있는지 확인합니다. 기본적으로 Ubuntu Desktop에는 설치되어 있지 않습니다. 다음 명령을 사용하여 설치합니다.

```
 sudo apt-get install openssh-server openssh-client**
```

문제가 지속되면 다음 명령으로 **sshd_config** 파일을 변경하여 ssh에 대해 PAM을 사용하지 않도록 해보세요.

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

그래도 문제가 지속되면 다음 명령을 실행하여 ssh 세션 수를 늘려 보세요.

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
ssh 인증을 위한 키 사용(암호 아님)이 아직 지원되지 않으므로(플랫폼에서는 패키지를 복사하는 데 ssh를 사용하므로) 대신 암호 인증을 사용합니다.



## <a name="next-steps"></a>다음 단계
개발 환경을 설정하고 Service Fabric 응용 프로그램을 Linux 클러스터에 배포합니다.


