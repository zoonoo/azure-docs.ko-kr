<properties 
	pageTitle="DocumentDB를 사용하여 Node.js 웹 응용 프로그램 작성 | Azure" 
	description="Microsoft Azure DocumentDB를 사용하여 Azure에 호스트된 Node.js 응용 프로그램에 데이터를 저장하고 액세스하는 방법에 대해 알아봅니다." 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395783175">DocumentDB를 사용하여 Node.js 응용 프로그램 작성</a>

<a name="_Toc395783175">

이 자습서에서는 Azure DocumentDB 서비스를 사용하여 Azure 웹 사이트에 호스트된
Node.js Express 응용 프로그램에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다.

이 자습서에서는 이전에 Node.js를 사용한 경험이 있다고 가정합니다.

다음 내용을 배웁니다.

· Visual Studio용 Node.js 도구를 사용하는 방법

· documentdb npm 모듈을 사용하여 Azure DocumentDB 서비스 작업을 수행하는 방법

· Azure 웹 사이트에 웹 응용 프로그램을 배포하는 방법

이 자습서를 따라 작업을 만들고, 검색하고,
완료할 수 있는 간단한 웹 기반 작업 관리 응용 프로그램을
작성합니다. 작업은 Azure DocumentDB에 JSON 문서로
저장됩니다.

![대체 텍스트][0]

## <a name="_Toc395783176">필수 조건</a>

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지
확인해야 합니다.

[Node.js][Node.js] 버전 v0.10.29 이상

[Git][Git]

[Visual Studio 2013][Visual Studio 2013] 업데이트 3

[Visual Studio용 Node.js 도구][Visual Studio용 Node.js 도구]

**참고:** 이 자습서에서 Visual Studio를 사용하여 Node.js 프로젝트를 작성,
디버그 및 배포하는 동안 원하는 편집기를 사용하여 Node.js
프로젝트를 실행하는 일반적인 방식으로 선택한 플랫폼에서
직접 Node.js를 실행할 수 있습니다. 그런 다음 [Azure CLI][Azure CLI] 도구를 사용하여
Azure 웹 사이트에 응용 프로그램을 배포할 수 있습니다.

# <a name="_Toc395637761">DocumentDB 데이터베이스 계정 만들기</a>

Azure에서 DocumentDB 데이터베이스 계정을 프로비전하려면 [Azure관리 포털][Azure관리 포털]을 열고
홈페이지에서 Azure 갤러리 타일을 클릭하거나 화면 왼쪽 아래에 있는 "+"를 클릭합니다.

![대체 텍스트][1]

Azure 갤러리가 열리며, 여기서 사용 가능한 많은 Azure 서비스를
선택할 수 있습니다. 갤러리의 범주 목록에서 "데이터, 저장소 및
백업"을 선택합니다.

![대체 텍스트][2]

여기서 Azure DocumentDB에 대한 옵션을 선택합니다.

![대체 텍스트][3]

그런 다음 화면 맨 아래에서 "만들기"를 선택합니다.

![대체 텍스트][4]

"새 DocumentDB" 블레이드가 열립니다. 여기서 새 계정의
이름, 지역, 배율, 리소스 그룹 및 기타 설정을
지정할 수 있습니다.

![대체 텍스트][5]

계정의 값을 제공한 후 "만들기"를 클릭하면 프로비저닝 프로세스에서
데이터베이스 계정을 만들기 시작합니다.
프로비저닝 프로세스가 완료되면 포털의 알림 영역에
알림이 표시되며 시작 화면의 타일(만들도록
선택한 경우)이 완료된 작업을 표시하도록
변경됩니다.

![대체 텍스트][6]

프로비저닝이 완료된 후 시작 화면에서 DocumentDB 타일을
클릭하면 새로 만든 이 DocumentDB 계정의 기본 블레이드가
표시됩니다.

![대체 텍스트][7]
![대체 텍스트][8]

"키" 단추를 사용하여 끝점 URL과 기본 키에 액세스한 후
다음에 만들 웹 응용 프로그램에서 해당 값을 사용하므로
클립보드로 복사하여 유용하게 유지합니다.

## <a name="_Toc395783178">새 Node.js 응용 프로그램 만들기</a>

Visual Studio에서 파일 – 새 프로젝트를 선택하고 "기본 Microsoft Azure Express 응용 프로그램"을 만드는 옵션을 선택합니다.

![대체 텍스트][9]

기본 Express 응용 프로그램이 만들어집니다. "종속성을
설치"하라는 메시지가 표시되면 "예"를 선택합니다. 새 Express 응용 프로그램에
필요한 모든 npm 패키지가 설치됩니다.

이 작업이 완료되면 솔루션 탐색기가
다음과 같이 표시됩니다.

![대체 텍스트][10]

Express, Jade 및 Stylus가 설치되어 있음을 보여 줍니다.

Visual Studio에서 F5 키를 누르면 프로젝트가 작성되고
Node.js가 시작된 다음 "Hello World"에 해당하는 Express 항목이 포함된
브라우저가 표시됩니다.

![대체 텍스트][11]

## <a name="_Toc395783179">추가 모듈 설치</a>

**package.json** 파일은 프로젝트의 루트에 만들어진
파일 중 하나입니다. 이 파일에는 Express 응용 프로그램에 필요한
추가 모듈 목록이 포함되어 있습니다. 나중에 이 응용 프로그램을
Azure 웹 사이트에 배포하는 경우 이 파일을 사용하여 응용 프로그램을
지원하기 위해 Azure에 설치해야 할 모듈을 결정합니다.

![대체 텍스트][12]

이 자습서를 위해 패키지 두 개를 더 설치해야 합니다.

솔루션 탐색기에서 "npm"을 마우스 오른쪽 단추로 클릭하고 "npm
패키지 설치"를 선택합니다.

![대체 텍스트][13]

"새 npm 패키지 설치" 대화 상자에서 **nconf**를 입력하여 모듈을
검색합니다. 이 모듈은 응용 프로그램이 구성 파일에서 데이터베이스
구성 값을 읽는 데 사용됩니다.

![대체 텍스트][14]

최종적으로, **documentdb**를 검색하여 Azure DocumentDB 모듈을
동일한 방식으로 설치합니다. 이 모듈에서 중요한 모든 DocumentDB 기능이
수행됩니다.

이 추가 모듈 두 개와 종속성을 설치하면
솔루션 탐색기의 **npm** 모듈 아래에
표시됩니다.

![대체 텍스트][15]

응용 프로그램의 **package.json** 파일을 신속하게 확인할 때
추가 모듈이 표시되어야 합니다. 이 파일은 나중에
응용 프로그램을 실행할 때 다운로드 및 설치해야 하는
패키지를 Azure에 알립니다.

필요한 경우 아래 예제와 유사하도록 package.json 파일을 편집합니다.

![대체 텍스트][16]

이렇게 하면 응용 프로그램이 추가 모듈에 종속됨을 노드(및 나중에 Azure)에
알립니다.

# <a name="_Toc395783180">node 응용 프로그램에서 DocumentDB 서비스 사용</a>

초기 설정 및 구성이 모두 완료되었으므로 이제 Azure DocumentDB를 사용하여 일부 코드를 작성하겠습니다. 먼저 방금 만든 Express 응용 프로그램의 루트에 있는 **app.js**를 편집합니다. 파일에서 다음 줄을 찾습니다.

    app.get('/', routes.index);
    app.get('/users', user.list);

다음 두 줄로 바꿉니다.

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

이렇게 하면 다음에 만들 폼에서 기본 GET 및 POST 메서드를 처리하는 방법을 응용 프로그램에 알립니다.

이제 **routes** 폴더 아래에 있는 **index.js** 파일을 찾습니다. 편집기에서 이 파일을 열고 파일에 있는 모든 코드를 삭제합니다.

파일 맨 위에 다음을 추가합니다.

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

이렇게 하면 사용하려는 모듈(**documentdb** 및 **nconf**)이 정의됩니다.

**nconf**는 데이터베이스 연결 문자열과 같은 구성 값을 코드에 인라인으로 배치하는 대신 외부 파일에서 로드할 수 있는 모듈입니다. nconf는 기본적으로 해당 구성을 위해 **config.json**을 찾습니다.

계속해서 프로젝트의 루트(app.js와 동일한 위치)에 **config.json**이라는 빈 텍스트 파일을 만듭니다.

새 **config.json** 파일을 열고 해당 DocumentDB 끝점에 적절하게 다음 값을 입력합니다. HOST 및 MASTER\_KEY 값을 올바르게 설정해야 합니다.

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

이제 **index.js**로 돌아가서 마지막 줄 뒤에 다음 줄을 추가하여 실제로 구성 파일을 읽고 페이지 수준 변수에 값을 저장합니다.

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

이 작업이 완료되면 계속해서 **index.js**에 다음 코드를 추가합니다.

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

이것은 경로를 정의할 때 **app.js**의 앞부분에서 응용 프로그램이 사용하도록 지정한 두 함수입니다. GET이 인덱스 뷰에 도달하면 **exports.index** 함수가 실행되고, 마찬가지로 POST가 인덱스 뷰에 수신되면 **exports.createOfUpdateItem** 함수가 실행됩니다.

두 함수는 작성할 응용 프로그램의 모든 작업을 수행하지만 순전히 코드를 읽고 따르기 쉽도록 만들기 위해 다른 함수를 호출합니다. 계속해서 **index.js** 파일에 다음 코드를 추가합니다. 이 코드에는 위의 두 함수가 사용하는 모든 메서드 및 모든 DocumentDB 호출이 포함되어 있습니다. 나중에 각 함수를 자세히 살펴보겠습니다.

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** – 폼에서 전달된 항목 ID를 기준으로 데이터베이스의 문서를 업데이트합니다. 이 ID를 사용하여 DocumentDB에 대해 ***readDocument*** 메서드를 실행하고 저장된 특정 문서를 읽습니다. 그런 다음 문서의 "completed" 특성을 true로 변경하여 이제 완료되었음을 나타내고 계속해서 데이터베이스의 문서를 바꿉니다.

**getItem –** ***queryDocuments***를 사용하여 항목의 id 속성으로 데이터베이스에서 단일 항목을 가져옵니다.

**createItem** – ***createDocument*** 메서드를 사용하여 폼에 입력된 항목 이름 및 항목 범주를 기준으로 데이터베이스에 새 문서를 만듭니다. 또한 completed 플래그를 false로 설정하여 이 항목이 아직 완료되지 않았음을 나타냅니다.

**listItems** – ***queryDocuments***를 사용하여 컬렉션에서 아직 완료되지 않았거나 completed=false인 모든 문서를 찾습니다. ANSI-SQL을 기반으로 하는 DocumentDB 쿼리 문법을 사용하여 익숙하지만 강력한 쿼리 기능을 보여 줍니다.

**readOrCreateDatabase** – ***queryDatabases***를 사용하여 이 이름을 가진 데이터베이스가 이미 있는지 확인합니다. 찾을 수 없는 경우 ***createDatabase***를 사용하여 지정된 끝점(구성 파일)에 제공된 식별자(구성 파일)로 새 데이터베이스를 만듭니다.

**readOrCreateCollection** – readOrCreateDatabase와 마찬가지로 이 메서드는 먼저 제공된 식별자를 가진 컬렉션을 찾으려고 하며, 있을 경우 해당 컬렉션이 반환되고 없을 경우 새로 만듭니다.

이 샘플 응용 프로그램에서 DocumentDB를 조작하는 데 필요한 모든 코드가 완료되었습니다.

이제 사용자가 실제로 응용 프로그램을 조작할 수 있도록 사용자 인터페이스를 작성하겠습니다. 만든 Express 응용 프로그램은 **Jade**를 뷰 엔진으로 사용합니다. Jade에 대한 자세한 내용은 <http://jade-lang.com/>을 참조하세요.

**views** 폴더에 있는 **layout.jade** 파일을 열고 파일 내용을 다음으로 바꿉니다.

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

이렇게 하면 응용 프로그램에 대한 일부 HTML을 렌더링하고 콘텐츠 페이지의 레이아웃을 제공할 수 있는 "**content**"라는 "**block**"을 만들도록 **Jade** 엔진에 알립니다.

이제 index.js와 연결된 뷰인 **index.jade** 파일을 열고 파일 내용을 다음으로 바꿉니다.

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

이렇게 하면 레이아웃이 확장되고 앞에서 layout.jade 파일에 있던
"block content" 자리 표시자의 콘텐츠가 제공됩니다.

이 레이아웃에서 HTML 폼 두 개를 만들었습니다. 첫 번째 폼에는 데이터 테이블과
항목을 업데이트할 수 있는 단추가 포함되어 있습니다. 두 번째 폼에는
일부 입력 필드와 새 항목을 만들 수 있는 단추가 포함되어 있습니다.

두 단추 중 하나를 클릭하면 **index.js** 파일에 만들어진 함수인
**createOrUpdateItem**(즉, 폼 POST가 수행됨)이
호출됩니다. 이 페이지를 직접 로드하면(즉, GET이
수행됨) **index** 함수가 실행됩니다.

응용 프로그램이 작동하는 데 필요한 모든 작업이 완료되었습니다.

</h1>
<a name="_Toc395783181">로컬에서 응용 프로그램 실행</a>
========================================================

로컬 컴퓨터에서 응용 프로그램을 테스트하려면 Visual Studio에서 F5 키를 누릅니다.
앱이 작성되고 Node.js가 시작되며 아래 그림과 같이 빈 페이지가 표시된
브라우저가 시작됩니다.

![대체 텍스트][17]

1\. 항목, 항목 이름 및 범주에 대해 제공된 필드를 사용하여
정보를 입력하고 **항목 추가**를 클릭합니다.

2\. 페이지가 업데이트되어 새로 만든 항목을 ToDo List에
표시합니다.

![대체 텍스트][18]

3\. 작업을 완료하려면 완료 열의 확인란을 선택하고
**작업 업데이트**를 클릭하면 됩니다.

</h1>
<a name="_Toc395783182">Azure 웹 사이트에 응용 프로그램 배포</a>
=====================================================================

Visual Studio용 Node.js 도구가 설치되어 있으면 몇 개의 단계만 수행하여
쉽게 Azure에 배포할 수 있습니다.

프로젝트를 마우스 오른쪽 단추로 클릭하고 "게시"를 선택합니다.

![대체 텍스트][19]

게시 마법사를 따라 Azure 웹 사이트에 필요한
구성을 제공합니다. 마법사에서 업데이트할 기존 웹 사이트를
선택하거나 새 웹 사이트를 만들 수 있습니다.

필요한 구성을 제공한 후 "게시"를 누르기만 하면 됩니다.

![대체 텍스트][20]

게시 마법사를 따라 Azure 웹 사이트에 필요한
구성을 제공합니다. 마법사에서 업데이트할 기존 웹 사이트를
선택하거나 새 웹 사이트를 만들 수 있습니다.

필요한 구성을 제공한 후 "게시"를 누르기만 하면 됩니다.

그러면 Visual Studio가 Azure 구독에 연결하고
이 Node.js 응용 프로그램을 게시합니다.

몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를 완료하고
브라우저를 시작하며, Azure에서 실행되는 작업 내용을
확인할 수 있습니다.

# <a name="_Toc395783183"></a> <a name="_Toc395637775">결론</a>

축하합니다. 지금까지 Azure DocumentDB를 사용하여 첫 Node.js Express
웹 응용 프로그램을 작성하고 Azure 웹 사이트에 게시했습니다.

[여기][여기]서 전체 참조 응용 프로그램의 소스 코드를 다운로드할 수 있습니다.

</h1>

  [0]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/vstudio/cc136611.aspx
  [Visual Studio용 Node.js 도구]: https://nodejstools.codeplex.com/
  [Azure CLI]: http://azure.microsoft.com/documentation/articles/xplat-cli/
  [Azure관리 포털]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [7]: ./media/documentdb-nodejs-application/image8.png
  [8]: ./media/documentdb-nodejs-application/image9.png
  [9]: ./media/documentdb-nodejs-application/image10.png
  [10]: ./media/documentdb-nodejs-application/image11.png
  [11]: ./media/documentdb-nodejs-application/image12.png
  [12]: ./media/documentdb-nodejs-application/image13.png
  [13]: ./media/documentdb-nodejs-application/image14.png
  [14]: ./media/documentdb-nodejs-application/image15.png
  [15]: ./media/documentdb-nodejs-application/image16.png
  [16]: ./media/documentdb-nodejs-application/image17.png
  [17]: ./media/documentdb-nodejs-application/image18.png
  [18]: ./media/documentdb-nodejs-application/image19.png
  [19]: ./media/documentdb-nodejs-application/image20.png
  [20]: ./media/documentdb-nodejs-application/image21.png
  [여기]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409

<!--HONumber=46--> 
