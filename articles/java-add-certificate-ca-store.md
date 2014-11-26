<properties linkid="develop-java-how-to-add-a-certificate" urlDisplayName="Add a Cert to the CA Store" pageTitle="Add a certificate to the Java CA store - Azure" metaKeywords="Azure Twilio Java, Twilio Java Certificate, Azure Service Bus Certificate" description="Learn how to add a certificate authority (CA) certificate to the Java CA certificate (cacerts) store for Twilio service or Azure Service Bus." metaCanonical="" services="" documentationCenter="Java" title="Adding a Certificate to the Java CA Certificates Store" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Java CA 인증서 저장소에 인증서 추가

다음 단계는 Java CA 인증서(cacerts) 저장소에 CA(인증 기관) 인증서를 추가하는 방법을 보여 줍니다. 사용된 예제는 Twilio 서비스에 필요한 CA 인증서용입니다. 토픽의 뒷부분에 제공된 정보는 Azure 서비스 버스용 CA 인증서를 설치하는 방법을 설명합니다.

JDK를 압축하고 Azure 프로젝트의 **approot** 폴더에 추가하기 전에 keytool을 사용하여 CA 인증서를 추가하거나 keytool을 사용하여 인증서를 추가하는 Azure 시작 작업을 실행할 수 있습니다. 이 예제에서는 JDK를 압축하기 전에 CA 인증서를 추가한다고 가정합니다. 또한 예제에서는 특정 CA 인증서가 사용되지만 다른 CA 인증서를 얻고 cacerts 저장소로 가져오는 단계도 이와 유사합니다.

## cacerts 저장소에 인증서를 추가하려면

1.  JDK의 **jdk\\jre\\lib\\security** 폴더로 설정된 명령 프롬프트에서 다음을 실행하여 설치된 인증서를 확인합니다.

    `keytool -list -keystore cacerts`

    저장소 암호를 묻는 메시지가 표시됩니다. 기본 암호는 **changeit**입니다. 암호를 변경하려면 <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>에서 keytool 설명서를 참조하세요. 이 예제에서는 MD5 지문 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4가 포함된 인증서가 나열되지 않아서 해당 인증서를 가져오려 한다고 가정합니다(이 특정 인증서는 Twilio API 서비스에 필요함).
2.  [GeoTrust 루트 인증서][GeoTrust 루트 인증서](영문)에 나열된 인증서 목록에서 인증서를 얻습니다. 일련 번호가 35:DE:F4:CF인 인증서 링크를 마우스 오른쪽 단추로 클릭하고 **jdk\\jre\\lib\\security** 폴더에 저장합니다. 이 예제에서는 **Equifax\_Secure\_Certificate\_Authority.cer**이라는 파일에 저장되었습니다.
3.  다음 명령을 통해 인증서를 가져옵니다.

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    이 인증서를 신뢰하라는 메시지가 표시될 경우 인증서에 MD5 지문 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4가 포함되어 있으면 **y**를 입력하여 응답합니다.

4.  다음 명령을 실행하여 CA 인증서를 가져왔는지 확인합니다.

    `keytool -list -keystore cacerts`

5.  JDK를 압축하고 Azure 프로젝트의 **approot** 폴더에 추가합니다.

keytool에 대한 자세한 내용은 <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>을 참조하세요.

# Azure 루트 인증서

Azure 서비스(예: Azure 서비스 버스)를 사용하는 응용 프로그램은 Baltimore CyberTrust Root 인증서를 신뢰해야 합니다. 2013년 4월 15일부터 Azure는 GTE CyberTrust Global Root에서 Baltimore CyberTrust Root로 마이그레이션을 시작했습니다. 이 마이그레이션을 완료하는 데 몇 달이 걸렸습니다.

Baltimore 인증서가 cacerts 저장소에 이미 설치되어 있을 수도 있으므로 먼저 **keytool -list** 명령을 실행하여 인증서가 있는지 확인해야 합니다.

Baltimore CyberTrust Root를 추가해야 하는 경우 일련 번호 02:00:00:b9 및 SHA1 지문 d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74가 포함되어 있습니다. <https://cacert.omniroot.com/bc2025.crt>에서 다운로드하고 확장명이 **.cer**인 로컬 파일에 저장한 후 위와 같이 **keytool**을 사용하여 가져올 수 있습니다.

Azure에서 사용되는 루트 인증서에 대한 자세한 내용은 [Azure 루트 인증서 마이그레이션][Azure 루트 인증서 마이그레이션](영문)을 참조하십시오.

  [GeoTrust 루트 인증서]: http://www.geotrust.com/resources/root-certificates/
  [Azure 루트 인증서 마이그레이션]: http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx
