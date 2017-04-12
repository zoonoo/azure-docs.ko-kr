---
title: "온-프레미스 데이터 게이트웨이 | Microsoft Docs"
description: "Azure의 Analysis Services 서버가 온-프레미스 데이터 원본에 연결되는 경우 온-프레미스 게이트웨이가 필요합니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 90584f60864589744888817ea71d0eb0d4d170ff
ms.lasthandoff: 11/17/2016


---
# <a name="on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이
온-프레미스 데이터 게이트웨이는 클라우드에서 온-프레미스 데이터 원본과 Azure Analysis Services 서버 간의 보안 데이터 전송을 제공하여 둘을 연결합니다.

게이트웨이는 네트워크의 컴퓨터에 설치됩니다. 게이트웨이는 Azure 구독에 있는 Azure Analysis Services 서버에 각각 설치되어야 합니다. 예를 들어 온-프레미스 데이터 원본에 연결된 Azure 구독에 두 개의 서버가 있는 경우 게이트웨이는 네트워크에 있는 두 개의 별도 컴퓨터에 설치되어야 합니다.

## <a name="requirements"></a>요구 사항
**최소 요구 사항:**

* .NET 4.5 Framework
* 64비트 버전의 Windows 7/Windows Server 2008 R2 이상

**권장:**

* 8 코어 CPU
* 8GB 메모리
* 64비트 버전의 Windows 2012 R2 이상

**중요 고려 사항:**

* 도메인 컨트롤러에 게이트웨이를 설치할 수 없습니다.
* 단일 컴퓨터에는 하나의 게이트웨이를 설치할 수 있습니다.
* 유지되고 대기 상태로 전환되지 않는 컴퓨터에 게이트웨이를 설치합니다. 컴퓨터가 켜져 있지 않은 경우 Azure Analysis Services 서버는 온-프레미스 데이터 원본에 연결하여 데이터를 새로 고칠 수 없습니다.
* 네트워크에 무선으로 연결된 컴퓨터에 게이트웨이를 설치하지 않습니다. 성능이 감소될 수 있습니다.
* 이미 구성된 게이트웨이의 서버 이름을 변경하려면 새 게이트웨이를 다시 설치하고 구성해야 합니다.
* 경우에 따라 SQL Server Native Client(SQLNCLI11)와 같은 네이티브 공급자를 사용하여 데이터 원본에 연결하는 테이블 형식 모델은 오류를 반환할 수 있습니다. 자세한 내용은 참조 [데이터 원본 연결](analysis-services-datasource.md)을 참조하세요.

## <a name="supported-on-premises-data-sources"></a>지원되는 온-프레미스 데이터 원본
미리 보기의 경우 게이트웨이는 Azure Analysis Services 서버와 다음 온-프레미스 데이터 원본 간의 연결을 지원합니다.

* SQL Server
* SQL 데이터 웨어하우스
* APS
* Oracle
* Teradata

## <a name="download"></a>다운로드
 [게이트웨이 다운로드](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>설치 및 구성
1. 설치를 실행합니다.
2. 설치 위치를 선택하고 사용 조건에 동의합니다.
3. Azure에 로그인합니다.
4. Azure Analysis Server 이름을 지정합니다. 게이트웨이 당 하나의 서버만 지정할 수 있습니다. **구성**을 클릭하여 계속할 수 있습니다.

    ![Azure에 로그인](./media/analysis-services-gateway/aas-gateway-configure-server.png)

## <a name="how-it-works"></a>작동 방법
게이트웨이는 조직의 네트워크에 있는 컴퓨터에서 Windows 서비스인 **온-프레미스 데이터 게이트웨이**로 실행됩니다. Azure Analysis Services와 함께 사용하기 위해 설치한 게이트웨이는 Power BI와 같은 다른 서비스에 사용되는 동일한 게이트웨이에 기반하지만 구성 방법에는 몇 가지 차이점이 있습니다.

![작동 방법](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

다음과 같은 쿼리 및 데이터 흐름 작업:

1. 쿼리는 온-프레미스 데이터 원본의 암호화된 자격 증명을 사용하여 클라우드 서비스에서 생성합니다. 그런 다음 처리할 게이트웨이의 큐로 전송됩니다.
2. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/)에 대한 요청을 보냅니다.
3. 온-프레미스 데이터 게이트웨이가 보류 중인 요청을 위해 Azure 서비스 버스를 폴링합니다.
4. 게이트웨이가 쿼리를 가져오고, 자격 증명의 암호를 해독하고, 해당 자격 증명으로 데이터 원본에 연결합니다.
5. 게이트웨이가 실행을 위해 데이터 원본에 쿼리를 보냅니다.
6. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 클라우드 서비스로 전송됩니다.

## <a name="windows-service-account"></a>Windows 서비스 계정
온-프레미스 데이터 게이트웨이는 Windows 서비스 로그온 자격 증명에 대해 *NT SERVICE\PBIEgwService*를 사용하도록 구성됩니다. 기본적으로 게이트웨이를 설치하는 컴퓨터의 컨텍스트에서 서비스로 로그온할 권리가 있습니다. 이 자격 증명은 온-프레미스 데이터 원본 또는 Azure 계정에 연결하는 데 사용되는 동일한 계정이 아닙니다.  

인증으로 인해 프록시 서버와 함께 문제가 발생하는 경우 Windows 서비스 계정을 도메인 사용자 또는 관리되는 서비스 계정으로 변경할 수도 있습니다.

## <a name="ports"></a>포트
게이트웨이는 Azure 서비스 버스에 대한 아웃바운드 연결을 만듭니다. 아웃바운드 포트 TCP 443(기본값), 5671, 5672, 9350-9354에서 통신합니다.  게이트웨이에는 인바운드 포트가 필요하지 않습니다.

방화벽에서 사용자 데이터 영역에 대한 IP 주소를 허용 목록에 추가하는 것이 좋습니다. [Microsoft Azure 데이터 센터 IP 목록](https://www.microsoft.com/download/details.aspx?id=41653)을 다운로드할 수 있습니다. 이 목록은 매주 업데이트됩니다.

> [!NOTE]
> Azure 데이터 센터 IP 목록에 나열된 IP 주소는 CIDR 표기법으로 작성됩니다. 예를 들어, 10.0.0.0/24는 10.0.0.0-10.0.0.24를 의미하지 않습니다. [CIDR 표기법](http://whatismyipaddress.com/cidr)에 대해 자세히 알아보세요.
>
>

다음은 게이트웨이에서 사용하는 정규화된 도메인 이름입니다.

| 도메인 이름 | 아웃바운드 포트 | 설명 |
| --- | --- | --- |
| *.powerbi.com |80 |설치 프로그램을 다운로드하는 데 사용되는 HTTP입니다. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |AMQP(고급 메시지 큐 프로토콜) |
| *.servicebus.windows.net |443, 9350-9354 |TCP의 서비스 버스 릴레이에 대한 수신기(액세스 제어 토큰 획득에 443 필요) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Power BI 서비스에서 게이트웨이에 연결할 수 없는 경우 인터넷 연결을 테스트하는 데 사용합니다. |
| *.microsoftonline-p.com |443 |구성에 따라 인증에 사용합니다. |

### <a name="forcing-https-communication-with-azure-service-bus"></a>Azure Service Bus와의 HTTPS 통신 강제 적용
TCP 대신 HTTPS를 사용하여 Azure Service Bus와 통신하도록 게이트웨이 강제할 수 있지만 성능이 크게 저하될 수 있습니다. *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* 파일을 수정해야 합니다. 값을 `AutoDetect`에서 `Https`로 변경합니다. 이 파일은 기본적으로 *C:\Program Files\On-premises data gateway*에 위치합니다.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>문제 해결
내부적으로 Azure Analysis Services를 온-프레미스 데이터 원본에 연결하는 데 사용되는 온-프레미스 데이터 게이트웨이는 Power BI에 사용되는 게이트웨이와 동일합니다.

게이트웨이를 설치하고 구성하는 데 문제가 있는 경우 [Power BI 게이트웨이 문제 해결](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/)을 참조하세요. 방화벽에 문제가 발생하는 경우 방화벽 또는 프록시 섹션을 참조하세요.

게이트웨이 프록시에 문제가 발생하는 경우 [Power BI 게이트웨이에 대한 프록시 설정 구성](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Analysis Services 관리](analysis-services-manage.md)
* [Azure Analysis Services에서 데이터 가져오기](analysis-services-connect.md)

