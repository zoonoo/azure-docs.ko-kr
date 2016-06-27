<properties
   pageTitle="보안 개인 클러스터에 연결 | Microsoft Azure"
   description="이 문서에서는 클라이언트와 클러스터 사이 뿐만 아니라 독립 실행형 클러스터 또는 개인 클러스터 내에서 통신을 보호하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# 증서를 사용하여 독립 실행형 Windows 클러스터 보안

이 문서에서는 독립 실행형 Windows 클러스터의 다양한 노드 간에 통신을 보호하는 방법 뿐만 아니라 X.509 인증서를 사용하여 이 클러스터에 연결된 클라이언트를 인증하는 방법을 설명합니다. 이렇게 하면 권한이 있는 사용자만 클러스터 및 배포된 응용 프로그램에 액세스할 수 있으며 관리 작업을 수행할 수 있습니다. 인증서 보안은 클러스터가 만들어지기 전에 클러스터에서 사용되어야 합니다. 노드 간 보안, 클라이언트-노드 보안 및 역할 기반 액세스 제어에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

## 어떤 인증서가 필요한가요?

처음에 클러스터의 노드 중 하나에 [독립 실행형 클러스터 패키지를 다운로드](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)합니다. 다운로드한 패키지에서 **ClusterConfig.X509.json** 파일을 찾을 수 있습니다. *편집* 모드로 파일을 열고 **속성** 탭의 **보안**을 위한 섹션을 검토합니다.

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

이 섹션에서는 독립 실행형 Windows 클러스터를 보호하기 위한 모든 인증서를 설명합니다. **ClusterCredentialType** 및 **ServerCredentialType** 값을 *X509*로 설정하여 인증서 기반 보안을 활성화합니다.

>[AZURE.NOTE] [지문](https://en.wikipedia.org/wiki/Public_key_fingerprint)은 인증서의 기본 ID입니다. [인증서의 지문을 검색하는 방법](https://msdn.microsoft.com/library/ms734695(v=vs.110).aspx)을 참고하여 만든 인증서의 지문을 확인합니다.

다음 테이블에서는 클러스터 설치에 필요한 실제 인증서를 나열합니다.

|**CertificateInformation 설정**|**설명**|
|-----------------------|--------------------------|
|ClusterCertificate|이 인증서는 클러스터에서 노드 간의 통신을 보호해야 합니다. 작동하지 않는 경우 두 개의 다른 인증서, 기본 및 보조 인증서를 사용할 수 있습니다. **지문** 섹션에서 기본 인증서의 지문 및 **ThumbprintSecondary** 변수에서 보조 인증서의 지문을 설정합니다.|
|ServerCertificate|이 인증서가 클러스터에 연결하려고 시도할 때 클라이언트에 표시됩니다. 편의상 *ClusterCertificate* 및 *ServerCertificate*에 동일한 인증서를 사용하도록 선택할 수 있습니다. 작동하지 않는 경우 두 개의 다른 서버 인증서, 기본 및 보조 인증서를 사용할 수 있습니다. **지문** 섹션에서 기본 인증서의 지문 및 **ThumbprintSecondary** 변수에서 보조 인증서의 지문을 설정합니다. |
|ClientCertificateThumbprints|인증된 클라이언트에 설치하려는 인증서의 집합입니다. 실행되는 클러스터와 응용 프로그램에 대한 액세스를 허용하려는 컴퓨터에 다양한 클라이언트 인증서가 설치되었을 수도 있습니다. **CertificateThumbprint** 변수에서 각 인증서의 지문을 설정합니다. **IsAdmin**을 *true*로 설정한 경우 이 인증서가 설치된 클라이언트는 클러스터에 대한 다양한 관리 작업을 수행할 수 있습니다. **IsAdmin**을 *false*로 설정한 경우 클러스터에서 실행되는 응용 프로그램에만 액세스할 수 있습니다.|
|ClientCertificateCommonNames|**CertificateCommonName**에 대한 첫 번째 클라이언트 인증서의 일반 이름을 설정합니다. **CertificateIssuerThumbprint**는 이 인증서의 발급자 지문입니다. [인증서 사용](https://msdn.microsoft.com/library/ms731899(v=vs.110).aspx)을 참고하여 일반 이름 및 발급자에 대해 자세히 알아 봅니다.|


## 인증서를 설치합니다.

클러스터 간의 통신을 보호하려면 클러스터 노드에 대한 X.509 인증서를 먼저 가져와야 합니다. 또한 권한이 있는 컴퓨터/사용자에게 이 클러스터에 연결을 제한하려면 이러한 잠재적 클라이언트 컴퓨터에 인증서를 가져와서 설치해야 합니다. X.509 인증서를 가져오는 단계는 [인증서를 가져오는 방법](https://msdn.microsoft.com/library/aa702761.aspx)을 참고하세요. **.pfx** 인증서를 만들어서 개인 키를 저장하도록 해야 합니다. 또는 Azure 구독을 보유한 경우 [X.509 인증서 획득](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts) 섹션을 따라 인증서 만들 수 있습니다. 이 인증서를 만들면 다음 단계를 사용하여 클러스터 노드에 설치할 수 있습니다. 이 단계에서는 노드가 최신 Windows PowerShell 3.x에 설치되었다고 가정합니다. 클러스터와 서버 인증서 및 각 항목에 대한 모든 보조 인증서의 경우 각 노드에서 이러한 단계를 반복해야 합니다.

- .pfx 파일을 노드에 복사합니다.

- 관리자 권한으로 PowerShell 창을 열고 다음 명령을 입력합니다.
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

*$password*를 이 인증서를 만드는 데 사용한 암호로 바꿉니다. $PfxFilePath를 이 노드에 복사된 .pfx의 전체 경로로 바꿉니다.

- 서비스 패브릭 프로세스가 다음 스크립트를 실행하여 사용할 수 있도록 이 인증서에 액세스 제어를 설정합니다.

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

또한 다음 단계를 사용하여 클러스터에 대한 액세스를 허용하려는 컴퓨터에 클라이언트 인증서를 설치합니다.


## 다음 단계

ClusterConfig.X509.json 파일의 **보안** 섹션을 구성한 후에 [클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md#createcluster) 섹션을 진행하여 노드를 구성하고 독립 실행형 클러스터를 만들 수 있습니다. 클러스터를 만드는 동안 **ClusterConfig.X509.json** 파일을 사용하도록 합니다. 예를 들어, 명령은 다음과 같을 수 있습니다.

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


보안 독립 실행형 Windows 클러스터를 성공적으로 실행하고 여기에 연결할 인증된 클라이언트를 설정했다면 [PowerShell을 사용하여 보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) 섹션을 따라 연결합니다.

<!---HONumber=AcomDC_0615_2016-->