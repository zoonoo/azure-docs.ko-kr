---
title: "Azure Cloud Services 역할 FAQ | Microsoft Docs"
description: "Azure Cloud Services에 대한 질문과 대답입니다. 인증서, 웹 역할 및 작업자 역할에 대한 몇 가지 일반적인 질문의 답변이 제공됩니다."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 7287cb1709b7c863cd046edfb995e23455398ec2
ms.lasthandoff: 03/25/2017


---
# <a name="cloud-services-faq"></a>클라우드 서비스 FAQ
이 문서는 Microsoft Azure 클라우드 서비스에 대한 일부 자주 묻는 질문을 답변합니다. 또한 일반적인 Azure 가격 책정 및 지원 정보는 [Azure 지원 FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) 를 방문할 수 있습니다. 크기 정보는 [클라우드 서비스 VM 크기 페이지](cloud-services-sizes-specs.md) 를 참조할 수도 있습니다.

## <a name="certificates"></a>인증서
### <a name="where-should-i-install-my-certificate"></a>어디에 내 인증서를 설치해야 하나요?
* **내**  
  개인 키를 포함한 응용 프로그램 인증서(\*.pfx, \*.p12)
* **CA**  
  모든 중간 인증서를 이 저장소(정책 및 하위 CA)로 이동시킵니다.
* **루트**  
  루트 CA 저장소이므로 기본 루트 CA 인증서를 여기로 이동시켜야 합니다.

### <a name="i-cant-remove-expired-certificate"></a>만료된 인증서를 제거할 수 없습니다.
Azure에서는 사용 중인 인증서를 제거할 수 없습니다. 인증서를 사용하는 배포를 삭제하거나 다른 또는 갱신된 인증서로 배포를 업데이트해야 합니다.

### <a name="delete-an-expired-certificate"></a>만료된 인증서 삭제
인증서를 사용하지 않으면 [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell cmdlet을 사용하여 인증서를 제거할 수 있습니다.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>확장명이 Windows Azure 서비스 관리라는 인증서가 만료되었습니다.
이러한 인증서는 원격 데스크톱 확장과 같은 클라우드 서비스에 확장명을 추가할 때마다 생성됩니다. 이러한 인증서는 확장명의 개인 구성을 암호화하고 암호 해독하는 경우에만 사용됩니다. 이러한 인증서가 만료되는 경우 중요하지 않습니다. 만료 날짜를 확인하지 않습니다.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>삭제한 인증서가 다시 나타납니다.
대개 Visual Studio와 같은 사용 중인 도구 때문에 계속 다시 나타납니다. 인증서를 사용하는 도구에 다시 연결할 때마다 다시 Azure에 업로드됩니다.

### <a name="my-certificates-keep-disappearing"></a>내 인증서가 자꾸 없어집니다.
가상 컴퓨터 인스턴스가 재활용되면 모든 로컬 변경 내용이 손실됩니다. [시작 태스크](cloud-services-startup-tasks.md) 를 사용하여 역할이 시작될 때마다 가상 컴퓨터에 인증서를 설치합니다.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>포털에서 내 관리 인증서를 찾을 수 없습니다.
[관리 인증서](../azure-api-management-certs.md) Azure 클래식 포털에만 사용할 수 있습니다. 현재의 Azure 포털에서는 관리 인증서를 사용하지 않습니다. 

### <a name="how-can-i-disable-a-management-certificate"></a>어떻게 하면 관리 인증서를 사용하지 않도록 설정할 수 있습니까?
[관리 인증서](../azure-api-management-certs.md) 는 사용하지 않도록 설정할 수 없습니다. 더 이상 사용되게 하지 않으려면 Azure 클래식 포털을 통해 삭제합니다.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>특정 IP 주소에 대한 SSL 인증서를 만들려면 어떻게 해야 합니까?
[인증서 만들기 자습서](cloud-services-certs-create.md)에 나오는 지시를 따릅니다. IP 주소를 DNS 이름으로 사용합니다.

## <a name="security"></a>보안
### <a name="disable-ssl-30"></a>SSL 3.0 사용 안 함
SSL 3.0을 사용하지 않도록 설정하고 TLS 보안을 사용하려면 https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/ 블로그 게시물에 설명되어 있는 시작 태스크를 만듭니다.

### <a name="add-nosniff-to-your-website"></a>웹 사이트에 **nosniff** 추가
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

### <a name="customize-iis-for-a-web-role"></a>웹 역할에 대해 IIS 사용자 지정
[일반적인 시작 작업](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) 문서의 IIS 시작 스크립트를 사용합니다.

## <a name="scaling"></a>확장
### <a name="i-cannot-scale-beyond-x-instances"></a>X 인스턴스 이상 확장할 수 없습니다.
사용자의 Azure 구독은 사용할 수 있는 코어 수를 제한합니다. 사용 가능한 모든 코어를 사용하는 경우 크기 조정은 작동하지 않습니다. 예를 들어 100개의 코어 제한이 있으면 클라우드 서비스에 대한 100개의 A1 크기 가상 컴퓨터 인스턴스나 50개의 A2 크기 가상 컴퓨터 인스턴스를 생성할 수 있습니다.

## <a name="networking"></a>네트워킹
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>여러 VIP 클라우드 서비스에서 IP를 예약할 수 없습니다.
우선, IP를 예약하려고 하는 가상 컴퓨터 인스턴스가 켜져 있는지 확인합니다. 다음으로, 스테이징 및 프로덕션 배포 모두에 대해 예약된 IP를 사용해야 합니다. **않습니다** .

## <a name="remote-desktop"></a>원격 데스크톱
### <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>NSG가 있을 때 원격 데스크톱을 어떻게 수행하나요?
포트 **20000**을 전달하는 NSG에 규칙을 추가합니다.

