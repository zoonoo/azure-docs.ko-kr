---
title: "Microsoft Azure Cloud Services FAQ에 대한 구성 및 관리 문제 | Microsoft Docs"
description: "이 문서는 Microsoft Azure Cloud Services의 구성 및 관리에 대한 질문과 대답을 나열합니다."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7d7676be26a8b68ab9fda18388e2b8cd5ed5f60b
ms.contentlocale: ko-kr
ms.lasthandoff: 06/15/2017

---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services의 구성 및 관리 문제: FAQ(질문과 대답)

이 문서는 [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)의 구성 및 관리 문제에 대한 질문과 대답을 포함합니다. 크기 정보는 [클라우드 서비스 VM 크기 페이지](cloud-services-sizes-specs.md) 를 참조할 수도 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>"nosniff"를 내 웹 사이트에 추가하려면 어떻게 할까요?
클라이언트가 MIME 형식을 스니핑하지 않도록 하려면 *web.config* 파일에 설정을 추가합니다.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

IIS에서 설정으로도 추가할 수 있습니다. [일반적인 시작 작업](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) 문서의 다음 명령을 사용합니다.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>웹 역할에 IIS를 사용자 지정하려면 어떻게 할까요?
[일반적인 시작 작업](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) 문서의 IIS 시작 스크립트를 사용합니다.

## <a name="i-cannot-scale-beyond-x-instances"></a>X 인스턴스 이상 확장할 수 없습니다.
사용자의 Azure 구독은 사용할 수 있는 코어 수를 제한합니다. 사용 가능한 모든 코어를 사용하는 경우 크기 조정은 작동하지 않습니다. 예를 들어 100개의 코어 제한이 있으면 클라우드 서비스에 대한 100개의 A1 크기 가상 컴퓨터 인스턴스나 50개의 A2 크기 가상 컴퓨터 인스턴스를 생성할 수 있습니다.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Cloud Services에 역할 기반 액세스를 구현하려면 어떻게 할까요?
Cloud Services는 Azure Resource Manager 기반 서비스가 아니므로 RBAC(역할 기반 액세스 제어) 모델을 지원하지 않습니다.

[Azure RBAC와 클래식 구독 관리자 비교](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators)를 참조하세요.

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer에 유휴 시간 제한을 설정하려면 어떻게 할까요?
다음과 같이 서비스 정의(csdef) 파일에서 시간 제한을 지정할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
자세한 정보는 [새로 만들기: Azure Load Balancer에 구성 가능한 유휴 시간 제한](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/)을 참조하세요.

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Microsoft 내부 엔지니어는 허가 없이 클라우드 서비스 인스턴스에 RDP할 수 있나요?
Microsoft에서는 소유자 또는 지정된 사용자의 서면 승인(전자 메일 또는 기타 서면 통신)없이 내부 엔지니어가 클라우드 서비스에 RDP하도록 허용하지 않는 엄격한 프로세스를 따릅니다.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>내 클라우드 서비스 SSL 인증서의 인증서 체인이 완료되지 않은 이유는 무엇인가요?
고객은 리프 인증서가 아닌 전체 인증서 체인(리프 인증서, 중간 인증서 및 루트 인증서)을 설치하는 것이 좋습니다. 방금 리프 인증서를 설치한 경우 Windows CTL을 탐색하여 인증서 체인을 빌드하기 위해 Windows를 사용합니다. Windows가 인증서의 유효성을 검사할 때 Azure 또는 Windows 업데이트에서 일시적인 네트워크 또는 DNS 문제가 발생하는 경우 인증서는 잘못된 것으로 간주될 수 있습니다. 전체 인증서 체인을 설치하여 이 문제를 방지할 수 있습니다. [체인된 SSL 인증서를 설치하는 방법](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/)의 블로그는 이 작업을 수행하는 방법을 보여줍니다.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>클라우드 서비스에 고정 IP 주소를 연결하려면 어떻게 할까요?
고정 IP 주소를 설정하려면 예약된 IP 만들어야 합니다. 이 예약된 IP는 새 클라우드 서비스 또는 기존 배포에 연결할 수 있습니다. 자세한 내용은 다음과 같은 문서를 참조하세요.
* [예약된 IP 주소를 만드는 방법](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [기존 클라우드 서비스의 IP 주소 예약](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [예약된 IP를 새 클라우드 서비스에 연결](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [실행 중인 배포에 예약된 IP 연결](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [서비스 구성 파일을 사용하여 클라우드 서비스에 예약된 IP 연결](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>클라우드 서비스에 대한 할당량 제한은 무엇인가요?
[서비스 특정 제한](../azure-subscription-service-limits.md#subscription-limits)을 참조하세요.

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>클라우드 서비스 VM의 드라이브에 사용 가능한 디스크 공간이 거의 표시되지 않는 이유는 무엇인가요?
이 동작은 정상적이며 응용 프로그램에 문제가 발생하지 않아야 합니다. 저널은 Azure PaaS VM에서 %uproot% 드라이브에 설정되며 이 기능은 기본적으로 파일이 차지하는 공간의 두 배를 사용합니다. 그러나 이를 사소하게 만드는 몇 가지 사항이 있습니다.

%approot% 드라이브 크기는 <.cspkg 크기 + 최대 저널 크기 + 여유 공간의 여백> 또는 1.5GB 중 더 큰 값으로 계산됩니다. VM의 크기는 이 계산과 관련이 없습니다. (VM 크기는 임시 C: 드라이브의 크기에 영향을 줍니다.) 

%approot% 드라이브에 작성하도록 지원되지 않습니다. Azure VM에 작성하는 경우 임시 LocalStorage 리소스에서 수행해야 합니다(또는 Blob Storage, Azure Files 등과 같은 다른 옵션). 따라서 %approot% 폴더에서 사용 가능한 공간의 크기는 의미가 없습니다. 응용 프로그램을 %approot% 드라이브에 작성하는지 확실하지 않은 경우 몇 일 동안 서비스를 실행한 다음 "이전" 및 "이후" 크기를 비교할 수 있습니다. 

Azure에서는 %approot% 드라이브에 아무 것도 작성하지 않습니다. .cspkg에서 VHD가 생성되고 Azure VM에 탑재되면 이 드라이브에 작성될 수 있는 유일한 항목은 사용자의 응용 프로그램입니다. 

저널 설정을 구성할 수 없으므로 끌 수 없습니다.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Azure 기본 IPS/IDS와 DDoS에서 제공하는 기능이란?
Azure에는 위협으로부터 보호하기 위해 데이터 센터 실제 서버에 있는 IP/ID가 있습니다. 또한 고객은 웹 응용 프로그램 방화벽, 네트워크 방화벽, 맬웨어 방지 프로그램, 침입 감지 및 방지 시스템(IDS/IPS) 등과 같은 타사 보안 솔루션을 배포할 수 있습니다. 자세한 내용은 [데이터 및 자산 보호 및 글로벌 보안 표준 준수](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)를 참조하세요.

Microsoft에서는 서버, 네트워크 및 응용 프로그램을 지속적으로 모니터링하여 위협을 감지합니다. Azure의 다면적 위협 관리 접근 방식은 침입 감지, DDoS(Distributed Denial-of-Service) 공격 방지, 침투 테스트, 동작 분석, 이상 감지 및 Machine Learning을 사용하여 방어를 지속적으로 강화하고 위험을 줄입니다. Azure용 Microsoft 맬웨어 방지 프로그램은 Azure Cloud Services 및 가상 컴퓨터를 보호합니다. 웹 응용 프로그램 방화벽, 네트워크 방화벽, 맬웨어 방지 프로그램, 침입 감지 및 방지 시스템(IDS/IPS) 등과 같은 타사 보안 솔루션을 배포하는 옵션이 있습니다.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>IIS에서 로그 디렉터리에 작성을 중지하는 이유는 무엇인가요?
로그 디렉터리에 작성할 로컬 저장소 할당량을 모두 사용했습니다. 이를 수정하기 위해 다음 세 가지 중 하나를 수행할 수 있습니다.
* IIS에 진단을 사용하도록 설정하고 진단을 주기적으로 Blob Storage에 이동합니다.
* 로깅 디렉터리에서 로그 파일을 수동으로 제거합니다.
* 로컬 리소스에 대한 할당량 제한을 늘립니다.

자세한 내용은 다음 문서를 참조하세요.
* [Azure Storage에서 진단 데이터 저장 및 보기](cloud-services-dotnet-diagnostics-storage.md)
* [클라우드 서비스에서 IIS 로그 작성 중지](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>"확장을 위한 Windows Azure Tools 암호화 인증서"의 목적은 무엇인가요?
이러한 인증서는 클라우드 서비스에 확장명을 추가할 때마다 자동으로 생성됩니다. 가장 일반적으로 WAD 확장 또는 RDP 확장이지만 맬웨어 방지 또는 로그 수집기 확장과 같은 다른 항목일 수도 있습니다. 이러한 인증서는 확장을 위해 개인 구성을 암호화하고 암호 해독하는 경우에만 사용됩니다. 만료 날짜를 확인하지 않으므로 인증서가 만료되는지는 중요하지 않습니다. 

이러한 인증서를 무시할 수 있습니다. 인증서를 정리하려면 모두 삭제할 수 있습니다. Azure에서 사용 중인 인증서를 삭제하려면 오류가 throw됩니다.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>"RDP"하지 않고 인스턴스에서 CSR(인증서 서명 요청)을 생성하려면 어떻게 할까요?
다음 지침 문서를 참조하세요.

>[WAWS(Windows Azure 웹 사이트)를 사용하기 위해 인증서 가져오기](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR은 텍스트 파일입니다. 인증서를 궁극적으로 사용하는 컴퓨터에서 만들어서는 안됩니다. 이 문서가 App Service를 위해 작성되었지만 CSR 생성은 일반적이며 Cloud Services에도 적용됩니다.

