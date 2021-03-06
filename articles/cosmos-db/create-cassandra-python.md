---
title: 'Início Rápido: API do Cassandra com Python – Azure Cosmos DB'
description: Este guia de início rápido mostra como usar a API do Apache Cassandra do Azure Cosmos DB para criar um aplicativo de perfil com Python.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 0b432653c452b6763e746f61b86e881c9cee62cc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134634"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Início Rápido: compilar um aplicativo do Cassandra com o SDK do Python e o Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Neste início rápido, você cria uma conta de API do Cassandra do Azure Cosmos DB e usa um aplicativo Cassandra Python clonado no GitHub para criar um banco de dados e contêiner Cassandra. O Azure Cosmos DB é um serviço de banco de dados multimodelo que permite criar e consultar rapidamente bancos de dados de documentos, tabelas, pares chave-valor e grafo com funcionalidades de escala horizontal e distribuição global.

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ou então [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure.
- [Python 2.7.14+ ou 3.4+](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Driver do Python para Apache Cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos clonar um aplicativo da API do Cassandra do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando. Crie uma pasta chamada `git-samples`. Em seguida, feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como o código cria os recursos de banco de dados,examine os snippets a seguir. Os snippets de código são todos obtidos do arquivo *pyquickstart.py*. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

* Os valores de nome de usuário e senha são definidos usando a página de cadeia de conexão no portal do Microsoft Azure. O `path\to\cert` fornece um caminho para um X509 certificado. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* O `cluster` é inicializado com informações do contactPoint. O contactPoint é recuperado do Portal do Azure.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* O `cluster` se conecta à API Cassandra do Azure Cosmos DB.

    ```python
    session = cluster.connect()
    ```

* Um novo keyspace é criado.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Uma nova tabela é criada.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Entidades de chave/valor são inseridas.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Consulta para obter todos os pares chave-valor.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Consulta para obter uma chave-valor.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. A cadeia de conexão permite que seu aplicativo se comunique com o banco de dados hospedado.

1. Em sua conta do Azure Cosmos DB no [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**. 

    Use o ![botão Copiar](./media/create-cassandra-python/copy.png) no lado direito da tela para copiar o valor superior, o PONTO DE CONTATO.

    ![Exibir e copiar nome de usuário, senha e ponto de contato de acesso na folha de cadeia de conexão no Portal do Azure](./media/create-cassandra-python/keys.png)

2. Abra o arquivo *config.py*. 

3. Cole o valor PONTO DE CONTATO do portal sobre `<FILLME>` na linha 10.

    Agora a linha 10 deve ser semelhante a 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

4. Copie o valor NOME DE USUÁRIO do portal e cole-o sobre `<FILLME>` na linha 6.

    Agora a linha 6 deve ser semelhante a 

    `'username': 'cosmos-db-quickstart',`
    
5. Copie o valor SENHA do portal e cole-o sobre `<FILLME>` na linha 8.

    Agora a linha 8 deve ser semelhante a

    `'password' = '2Ggkr662ifxz2Mg==`';`

6. Salve o arquivo *config.py*.
    
## <a name="use-the-x509-certificate"></a>Use o certificado X509

1. Baixe o certificado de Baltimore CyberTrust Root localmente do [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Renomeie o arquivo usando a extensão de arquivo *.cer*.

   O certificado tem o número de série `02:00:00:b9` e a impressão digital SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Abra o *pyquickstart.py* e altere o `path\to\cert` para apontar para seu novo certificado.

3. Salve o *pyquickstart.py*.

## <a name="run-the-python-app"></a>Execute o aplicativo script Python

1. Use o comando cd no terminal git para mudar para a pasta- `azure-cosmos-db-cassandra-python-getting-started`. 

2. Execute os seguintes comandos para instalar os módulos necessários:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Execute o seguinte comando para iniciar o aplicativo do Python:

    ```
    python pyquickstart.py
    ```

3. Verifique se os resultados estão conforme o esperado na linha de comando.

    Pressione CTRL+C para interromper a execução do programa e feche a janela do console. 

    ![Exibir e verificar a saída](./media/create-cassandra-python/output.png)
    
4. No portal do Azure, abra **Data Explorer** para consultar, modificar e trabalhar com esses novos dados. 

    ![Exibir os dados no Data Explorer](./media/create-cassandra-python/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB com a API do Cassandra e executar um aplicativo Cassandra Python que cria um banco de dados e contêiner Cassandra. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)

