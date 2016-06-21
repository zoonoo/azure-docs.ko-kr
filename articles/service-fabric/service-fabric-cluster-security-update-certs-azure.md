<properties
   pageTitle="Azure 클러스터에 대한 인증서 업데이트 | Microsoft Azure"
   description="Azure 주요 자격 증명 모음에 새 인증서를 업로드하고 기존 서비스 패브릭 클러스터에 대한 인증서를 업데이트하거나 제거하는 방법에 대해 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Azure에서 서비스 패브릭 클러스터에 대한 인증서 추가 또는 제거

클러스터를 만드는 동안 인증서 보안을 구성할 때 서비스 패브릭을 사용하여 기본 인증서와 보조 인증서를 지정할 수 있습니다. 기본적으로 생성 시 지정한 인증서가 주 인증서입니다. 클러스터를 만든 후 새 인증서를 보조 인증서로 추가하거나 기존 인증서를 제거할 수 있습니다. 서비스 패브릭에서 X.509 인증서를 사용하는 방법에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 읽어보세요.

>[AZURE.NOTE] 보안 클러스터의 경우 항상 적어도 하나의 유효한(취소되지 않거나 만료되지 않은) 주 또는 보조 인증서 배포가 필요하며 그렇지 않으면 클러스터에 액세스할 수 없습니다.

## 보조 인증서 추가
다른 인증서를 보조 인증서로 추가하려면 해당 인증서를 Azure 주요 자격 증명 모음에 업로드하고 클러스터의 VM에 배포해야 합니다. 자세한 내용은 [고객 관리 주요 자격 증명 모음에서 VM에 인증서 배포](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)를 참조하세요.

1. [주요 자격 증명 모음에 X.509 인증서 업로드](service-fabric-cluster-azure-secure-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)
2. [Azure 포털](https://portal.azure.com/)에 로그인하고 이 인증서를 추가하려는 클러스터 리소스로 이동합니다.
3. **설정**아래에서 인증서 설정을 클릭하고 보조 인증서 지문을 입력합니다.
4. **Save**를 클릭합니다. 배포가 시작되고 해당 배포가 완료되면 주 또는 보조 인증서를 사용하여 클러스터에서 관리 작업을 수행할 수 있습니다.

![포털의 인증서 지문 스크린 샷][SecurityConfigurations_02]

## 인증서 제거
다음은 클러스터에서 사용하지 않도록 오래된 인증서를 제거하는 프로세스입니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인하고 클러스터의 보안 설정으로 이동합니다.
2. 인증서 중 하나를 제거합니다.
3. **저장**을 클릭하여 새 배포를 시작합니다. 배포가 완료되면 제거한 인증서는 클러스터에 연결하는 데 더 이상 사용할 수 없습니다.

## 다음 단계
클러스터 관리에 대한 자세한 내용은 다음 문서를 읽어보세요.

- [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
- [클라이언트에 대한 역할 기반 액세스 설정](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0608_2016-->