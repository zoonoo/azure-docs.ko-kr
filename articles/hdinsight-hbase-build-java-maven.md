<properties title="Maven을 사용하여 HBase 응용 프로그램 빌드" pageTitle="Maven을 사용하여 HBase 응용 프로그램 빌드" description="Apache Maven을 사용하여 Java 기반 Apache HBase 응용 프로그램을 빌드한 다음 Azure HDInsight에 배포하는 방법에 대해 알아봅니다." metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="larryfr" />

## Maven을 통해 HDInsight(Hadoop)와 함께 HBase를 사용하는 Java 응용 프로그램 작성

Apache Maven을 사용하여 Java로 [Apache HBase][Apache HBase] 응용 프로그램을 만들어 빌드하는 방법을 알아봅니다. 그런 다음 Azure HDInsight(Hadoop)에서 응용 프로그램을 사용합니다.

[Maven][Maven]은 Java 프로젝트용 소프트웨어, 문서화 및 보고를 빌드할 수 있는 소프트웨어 프로젝트 관리 및 종합 도구입니다. 이 문서에서는 Maven을 사용하여 Azure HDInsight 클러스터에서 HBase 테이블을 만들고, 쿼리하고, 삭제하는 기본 Java 응용 프로그램을 만드는 방법을 알아봅니다.

## 요구 사항

-   [Java 플랫폼 JDK][Java 플랫폼 JDK](영문) 7 이상

-   [Maven][Maven]

-   [Azure HDInsight 클러스터 및 HBase][Azure HDInsight 클러스터 및 HBase]

## 프로젝트 만들기

1.  개발 환경의 명령줄에서 프로젝트를 만들 위치로 디렉터리를 변경합니다. 예를 들면 `cd code\hdinsight`와 같습니다.

2.  Maven과 함께 설치되는 **mvn** 명령을 사용하여 프로젝트용 스캐폴딩을 생성합니다.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    이 명령은 현재 디렉터리에 새 디렉터리를 만들며, 이름은 **artifactID** 매개 변수로 지정됩니다(이 예제에서는 **hbaseapp**). 이 디렉터리는 다음 항목을 포함합니다.

    -   **pom.xml** - 프로젝트 개체 모델([POM][POM])은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.

    -   **src** - **main\\java\\com\\microsoft\\examples** 디렉터리를 포함하는 디렉터리이며 여기서 응용 프로그램을 작성합니다.

3.  **src\\test\\java\\com\\microsoft\\examples\\apptest.java** 파일은 이 예제에서 사용되지 않으므로 이 파일을 삭제합니다.

## 프로젝트 개체 모델 업데이트

1.  **pom.xml** 파일을 편집하고 `<dependencies>` 섹션 내에 다음을 추가합니다.

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    이 코드를 통해 Maven은 프로젝트에 **hbase-client** 버전 **0.98.4-hadoop2**가 필요하다는 것을 인식합니다. 컴파일 시간에 이 파일이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 리포지토리 검색][Maven 리포지토리 검색]을 사용하여 이 종속성에 대한 자세한 정보를 볼 수 있습니다.

2.  **pom.xml** 파일에 다음을 추가합니다. 이 코드는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어 `</dependencies>`와 `</project>` 사이에 있어야 합니다.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
            <resource>
              <directory>${basedir}/conf</directory>
              <filtering>false</filtering>
              <includes>
                <include>hbase-site.xml</include>
              </includes>
            </resource>
          </resources>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>       
          </plugins>
        </build>

    이 코드는 HBase에 대한 구성 정보를 포함하는 리소스(**conf\\hbase-site.xml**)를 구성합니다.

    > [WACOM.NOTE] 또한 코드를 통해 구성 값을 설정할 수도 있습니다. 그 방법에 대해서는 아래 **CreateTable** 예제의 설명을 참조하세요.

    또한 이 코드는 Maven으로 빌드된 JAR에서 라이선스 중복을 방지하는 데 사용되는 [maven-shade-plugin][maven-shade-plugin]을 구성합니다. 이 플러그 인을 사용하는 이유는 중복 라이선스 파일이 HDInsight 클러스터에서 런타임으로 오류를 일으키기 때문입니다. `ApacheLicenseResourceTransformer` 구현과 함께 maven-shade-plugin을 사용하면 이 오류가 방지됩니다.

    또한 maven-shade-plugin은 응용 프로그램에 필요한 모든 종속성을 포함하는 uberjar(또는 fatjar)도 생성합니다.

3.  **pom.xml** 파일을 저장합니다.

4.  **hbaseapp** 디렉터리에 **conf**라는 새 디렉터리를 만듭니다. **conf** 디렉터리에, **hbase-site.xml**이라는 새 파일을 만들고 다음을 그 내용으로 사용합니다.

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
         * Copyright 2010 The Apache Software Foundation
         *
         * Licensed to the Apache Software Foundation (ASF) under one
         * or more contributor license agreements.  See the NOTICE file
         * distributed with this work for additional information
         * regarding copyright ownership.  The ASF licenses this file
         * to you under the Apache License, Version 2.0 (the
         * "License"); you may not use this file except in compliance
         * with the License.  You may obtain a copy of the License at
         *
         *     http://www.apache.org/licenses/LICENSE-2.0
         *
         * Unless required by applicable law or agreed to in writing, software
         * distributed under the License is distributed on an "AS IS" BASIS,
         * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
         * See the License for the specific language governing permissions and
         * limitations under the License.
         */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    이 파일은 HDInsight 클러스터용 HBase 구성을 로드하는 데 사용됩니다.

    > [WACOM.NOTE] 이 파일은 최소 크기의 hbase-site.xml 파일로, HDInsight 클러스터용 완전한 최소 설정을 포함합니다. HDInsight에 사용되는 hbase-site.xml 구성 파일의 전체 버전에 대해서는 [HDInsight 클러스터에 대한 원격 데스크톱][HDInsight 클러스터에 대한 원격 데스크톱]을 참조하세요. hbase-site.xml 파일은 C:\\apps\\dist\\hbase-\<version number\>-hadoop2\\conf 디렉터리에 있습니다. 파일 경로의 버전 번호 부분은 HBase가 클러스터에서 업데이트될 때 변경됩니다.

5.  **hbase-site.xml** 파일을 저장합니다.

## 응용 프로그램 만들기

1.  **hbaseapp\\src\\com\\microsoft\\examples** 디렉터리로 이동하여 app.java\_\_ 파일 이름을 **CreateTable.java**로 바꿉니다.

2.  **CreateTable.java** 파일을 열고 기존 내용을 다음으로 바꿉니다.

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            //   in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    이 코드는 **CreateTable** 클래스이며, **people**이라는 테이블을 만들고 미리 정의된 사용자로 채웁니다.

3.  **CreateTable.java** 파일을 저장합니다.

4.  **hbaseapp\\com\\microsoft\\examples** 디렉터리에서 **SearchByEmail.java**라는 새로운 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    **SearchByEmail** 클래스를 사용하여 메일 주소로 행을 쿼리할 수 있습니다. 정규식 필터를 사용하므로, 이 클래스를 사용할 때 문자열 또는 정규식을 제공할 수 있습니다.

5.  **SearchByEmail.java** 파일을 저장합니다.

6.  **hbaseapp\\com\\microsoft\\examples** 디렉터리에서 **DeleteTable.java**라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    이 클래스는 먼저 **CreateTable** 클래스로 생성된 테이블을 비활성화한 후 제거하여 이 예외를 정리하는 데에만 사용됩니다.

7.  **CreateTable.java** 파일을 저장합니다.

## 응용 프로그램 빌드 및 패키지화

1.  명령 프롬프트를 열고 **hbaseapp** 디렉터리로 이동합니다.

2.  다음 명령을 사용하여 응용 프로그램을 포함하는 JAR을 빌드합니다.

        mvn clean package

    이 코드는 이전 빌드 아티팩트를 정리하고, 아직 설치되지 않은 모든 종속성을 다운로드한 후 응용 프로그램을 빌드 및 패키지화합니다.

3.  명령이 완료되면 **hbaseapp\\target** 디렉터리에 **hbaseapp-1.0-SNAPSHOT.jar**이라는 파일이 포함됩니다.

    > [WACOM.NOTE] **hbaseapp-1.0-SNAPSHOT.jar** 파일은 응용 프로그램을 실행하는 데 필요한 모든 종속성을 포함하는 uberjar(또는 fatjar)입니다.

## JAR 업로드 및 작업 시작

> [WACOM.NOTE] [HDInsight에서 Hadoop 작업용 데이터 업로드][HDInsight에서 Hadoop 작업용 데이터 업로드]에서 설명한 대로 HDInsight 클러스터에 파일을 업로드하는 방법은 많습니다. 아래 단계에서는 [Azure PowerShell][Azure PowerShell]을 사용합니다.

1.  [Azure PowerShell][Azure PowerShell]을 설치 및 구성한 후 **hbase-runner.psm1**이라는 새 파일을 만듭니다. 이 파일의 내용으로 다음을 사용합니다.

        <#
        .SYNOPSIS
            Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
            Copies a file from a local directory to the blob container for
            the HDInsight cluster.
        .EXAMPLE
            Start-HBaseExample -className "com.microsoft.examples.CreateTable"
                -clusterName "MyHDInsightCluster"

        .EXAMPLE
            Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
                -clusterName "MyHDInsightCluster"
                -emailRegex "contoso.com"

        .EXAMPLE
            Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
                -clusterName "MyHDInsightCluster"
                -emailRegex "^r" -showErr
        #>

        function Start-HBaseExample {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The class to run
                [Parameter(Mandatory = $true)]
                [String]$className,

                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,

                #Only used when using SearchByEmail
                [Parameter(Mandatory = $false)]
                [String]$emailRegex,

                #Use if you want to see stderr output
                [Parameter(Mandatory = $false)]
                [Switch]$showErr
            )

            Set-StrictMode -Version 3

            # Is the Azure module installed?
            FindAzure

            # The JAR
            $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

            # The job definition
            $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
              -JarFile $jarFile `
              -ClassName $className `
              -Arguments $emailRegex

            # Get the job output
            $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
            Write-Host "Wait for the job to complete ..." -ForegroundColor Green
            Wait-AzureHDInsightJob -Job $job
            if($showErr)
            {
                Write-Host "STDERR"
                Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
            }
            Write-Host "Display the standard output ..." -ForegroundColor Green
            Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
        }

        <#
        .SYNOPSIS
            Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
            Copies a file from a local directory to the blob container for
            the HDInsight cluster.
        .EXAMPLE
            Add-HDInsightFile -localPath "C:\temp\data.txt"
                -destinationPath "example/data/data.txt"
                -ClusterName "MyHDInsightCluster"
        .EXAMPLE
            Add-HDInsightFile -localPath "C:\temp\data.txt"
                -destinationPath "example/data/data.txt"
                -ClusterName "MyHDInsightCluster"
                -Container "MyContainer"
        #>

        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,

                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,

                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,

                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )

            Set-StrictMode -Version 3

            # Is the Azure module installed?
            FindAzure

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
                                        -Container $storage.container `
                                        -Context $storage.context
        }

        function FindAzure {
            # Is the Azure module installed?
            if (-not(Get-Module -ListAvailable Azure))
            {
                throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/ko-kr/documentation/articles/install-configure-powershell/"
            }

            # Is there an active Azure subscription?
            $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureHDInsightCluster -name $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            # Get the primary storage account information
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
            $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
            # Build the hash of storage account name/keys
            $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
            foreach($account in $hdi.StorageAccounts)
            {
                $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
            }
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $hdi.DefaultStorageAccount.StorageContainerName
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccounts = $storageAccounts

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    이 파일에는 다음 두 모듈이 포함됩니다.

    -   **Add-HDInsightFile** - HDInsight에 파일을 업로드하는 데 사용합니다.

    -   **Start-HBaseExample** - 이전에 생성한 클래스를 실행하는 데 사용합니다.

2.  **hbase-runner.psm1** 파일을 저장합니다.

3.  새 Azure PowerShell 창을 열고, **hbaseapp** 디렉터리로 이동한 후 다음 명령을 실행합니다.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    이전에 만든 **hbase-runner.psm1** 파일의 위치로 경로를 변경합니다. 그러면 이 PowerShell 세션에 대한 모듈이 등록됩니다.

4.  다음 명령을 사용하여 HDInsight 클러스터에 **hbaseapp-1.0-SNAPSHOT.jar**을 업로드합니다.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    **hdinsightclustername**을 HDInsight 클러스터의 이름으로 바꿉니다. 이 명령은 HDInsight 클러스터용 기본 저장소의 **example/jars** 위치로 **hbaseapp-1.0-SNAPSHOT.jar**을 업로드합니다.

5.  파일을 업로드한 후 다음 명령으로 **hbaseapp**을 사용하여 새 테이블을 만듭니다.

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    **hdinsightclustername**을 HDInsight 클러스터의 이름으로 바꿉니다.

    이 명령은 HDInsight 클러스터에 **people**이라는 새 테이블을 만듭니다.

6.  테이블에서 항목을 검색하려면 다음 명령을 사용합니다.

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    **hdinsightclustername**을 HDInsight 클러스터의 이름으로 바꿉니다.

    이 명령은 SearchByEmail 클래스를 사용하여 열 패밀리 **contactinformation**의 **email** 열이 **contoso.com** 문자열을 포함하는 행을 검색합니다. 다음과 같은 결과가 표시됩니다.

        Rae Schroeder - rae@contoso.com - ID: 032439814
        Franklin Holtz - franklin@contoso.com - ID: 229772110
        Gabriela Ingram - gabriela@contoso.com - ID: 655336201

    `-emailRegex` 값에 **fabrikam.com**을 사용하면 메일 필드에 **fabrikam.com**을 포함하는 사용자가 반환됩니다. 이 검색은 정규식 기반 필터를 사용하여 구현되므로, **^r**과 같은 정규식을 입력할 수 있습니다. 이 정규식은 메일이 'r' 문자로 시작하는 항목을 반환합니다.

## 테이블 삭제

예제를 완료하면 PowerShell 세션에서 다음 명령을 사용하여 이 예제에 사용된 **people** 테이블을 삭제합니다.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

**hdinsightclustername**을 HDInsight 클러스터의 이름으로 바꿉니다.

## 문제 해결

### **Start-HBaseExample**을 사용할 경우 결과가 없거나 예기치 않은 결과가 표시됨

`-showErr` 매개 변수를 사용하여 작업을 실행하는 동안 생성된 STDERR을 표시합니다.

  [Apache HBase]: http://hbase.apache.org/
  [Maven]: http://maven.apache.org/
  [Java 플랫폼 JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Azure HDInsight 클러스터 및 HBase]: /ko-kr/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [Maven 리포지토리 검색]: http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [HDInsight 클러스터에 대한 원격 데스크톱]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [HDInsight에서 Hadoop 작업용 데이터 업로드]: /ko-kr/documentation/articles/hdinsight-upload-data/
  [Azure PowerShell]: /ko-kr/documentation/articles/install-configure-powershell/
