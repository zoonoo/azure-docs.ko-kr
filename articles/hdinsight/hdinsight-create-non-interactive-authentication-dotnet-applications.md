---
title: "비대화형 인증 .NET HDInsight 응용 프로그램 만들기 | Microsoft 문서"
description: "비대화형 인증 .NET HDInsight 응용 프로그램을 만드는 방법을 알아봅니다."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a423975e8a091183154217678706817694f3e346
ms.openlocfilehash: d5256250d6d3a6d7df3a90ae4a0801af131b830e


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>비대화형 인증 .NET HDInsight 응용 프로그램 만들기
응용 프로그램의 고유한 ID(비대화형) 또는 응용 프로그램에 로그인한 사용자의 ID(대화형)로 .NET Azure HDInsight 응용 프로그램을 실행할 수 있습니다. 대화형 응용 프로그램의 샘플은 [Azure HDInsight에 연결](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)을 참조하세요. 이 문서에서는 비대화형 인증 .NET 응용 프로그램을 만들어 Azure에 연결하고 HDInsight를 관리하는 방법을 보여 줍니다.

비 대화형.NET 응용 프로그램에서 다음 항목이 필요합니다.

* Azure 구독 테넌트 ID(즉, 디렉터리 ID) [테넌트 ID 가져오기](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)를 참조하세요.
* Azure Directory 응용 프로그램 클라이언트 ID [Active Directory 응용 프로그램 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-active-directory-application)와 [응용 프로그램 ID 가져오기](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)를 참조하세요.
* Azure Directory 응용 프로그램 비밀 키 [응용 프로그램 인증 키 가져오기](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
* HDInsight 클러스터. [시작 자습서](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.



## <a name="assign-ad-application-to-role"></a>역할에 AD 응용 프로그램 할당
작업 수행 권한을 부여하려면 응용 프로그램을 [역할](../active-directory/role-based-access-built-in-roles.md)에 할당해야 합니다. 구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다. 이 자습서에서는 리소스 그룹 수준에서 범위를 설정합니다. 자세한 정보는 [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../active-directory/role-based-access-control-configure.md)를 참조하세요.

**AD 응용 프로그램에 소유자 역할을 추가하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **리소스 그룹** 을 클릭합니다.
3. 이 자습서의 뒷부분에서 Hive 쿼리를 실행할 HDInsight 클러스터가 포함된 리소스 그룹을 클릭합니다. 너무 많은 리소스 그룹이 있는 경우 필터를 사용할 수 있습니다.
4. 리소스 그룹 메뉴에서 **액세스 제어(IAM)**를 클릭합니다.
5. **사용자** 블레이드에서 **추가**를 클릭합니다.
6. 지침에 따라 마지막 절차에서 만든 AD 응용 프로그램에 **소유자** 를 추가합니다. 성공적으로 완료하면 소유자 역할이 있는 사용자가 블레이드에 나열된 응용 프로그램이 표시됩니다.

## <a name="develop-hdinsight-client-application"></a>HDInsight 클라이언트 응용 프로그램 개발

1. C# 콘솔 응용 프로그램을 만듭니다.
2. 다음 NuGet 패키지를 추가합니다.

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. 다음 코드 샘플을 사용합니다.

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        
        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
                private static void Main(string[] args)
                {
                    var key = new SecureString();
                    foreach (char c in secretKey) { key.AppendChar(c); }

                    var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    var results = _hdiManagementClient.Clusters.List();
                    foreach (var name in results.Clusters)
                    {
                        Console.WriteLine("Cluster Name: " + name.Name);
                        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                        Console.WriteLine("\t Cluster location: " + name.Location);
                        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                    }
                    Console.WriteLine("Press Enter to continue");
                    Console.ReadLine();
                }

                /// Get the access token for a service principal and provided key                
                public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
                {
                    var authFactory = new AuthenticationFactory();
                    var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    var accessToken =
                        authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

## <a name="next-steps"></a>다음 단계
* [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Azure Resource Manager를 사용하여 서비스 주체 인증](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)



<!--HONumber=Dec16_HO3-->


