---
title: Usar o JavaScript para arquivos & ACLs no Azure Data Lake Storage Gen2 (versão prévia)
description: Use o armazenamento do Azure Data Lake biblioteca de cliente para JavaScript para gerenciar diretórios e listas de controle de acesso (ACL) de arquivos e diretórios em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154862"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Usar o JavaScript para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2 (versão prévia)

Este artigo mostra como usar o JavaScript para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado. 

> [!IMPORTANT]
> A biblioteca JavaScript que está em destaque neste artigo está atualmente em visualização pública.

[Pacote (Gerenciador de pacotes de nó)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [fornecer comentários](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem o namespace hierárquico (HNS) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Se você estiver usando esse pacote em um aplicativo node. js, precisará do node. js 8.0.0 ou superior.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale Data Lake biblioteca de cliente para JavaScript abrindo uma janela de terminal e, em seguida, digitando o comando a seguir.

```javascript
npm install @azure/storage-file-datalake
```

Importe o pacote de `storage-file-datalake` colocando essa instrução na parte superior do seu arquivo de código. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Conectar-se à conta 

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. A maneira mais fácil de obter um é usar uma chave de conta. 

Este exemplo cria uma instância do **DataLakeServiceClient** usando uma chave de conta.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Esse método de autorização funciona apenas para aplicativos node. js. Se você planeja executar seu código em um navegador, você pode autorizar usando Azure Active Directory (AD). Para obter diretrizes sobre como fazer isso, consulte o arquivo de [armazenamento do Azure data Lake biblioteca de cliente para](https://www.npmjs.com/package/@azure/storage-file-datalake) arquivos Leiame do JavaScript. 

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos atua como um contêiner para seus arquivos. Você pode criar uma obtendo uma instância de **FileSystemClient** e, em seguida, chamando o método **FileSystemClient. Create** .

Este exemplo cria um sistema de arquivos chamado `my-file-system`. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório obtendo uma instância de **DirectoryClient** e, em seguida, chamando o método **DirectoryClient. Create** .

Este exemplo adiciona um diretório chamado `my-directory` a um sistema de arquivos. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório chamando o método **DirectoryClient. Rename** . Passe o caminho do diretório desejado de um parâmetro. 

Este exemplo renomeia um subdiretório para o nome `my-directory-renamed`.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Este exemplo move um diretório chamado `my-directory-renamed` para um subdiretório de um diretório chamado `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório chamando o método **DirectoryClient. Delete** .

Este exemplo exclui um diretório chamado `my-directory`.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Gerenciar uma ACL de diretório

Este exemplo obtém e define a ACL de um diretório chamado `my-directory`. Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte [controle de acesso em Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Primeiro, leia um arquivo. Este exemplo usa o módulo `fs` node. js. Em seguida, crie uma referência de arquivo no diretório de destino criando uma instância de **fileclient** e, em seguida, chamando o método **fileclient. Create** . Carregue um arquivo chamando o método **fileclient. Append** . Certifique-se de concluir o carregamento chamando o método **fileclient. Flush** .

Este exemplo carrega um arquivo de texto em um diretório chamado `my-directory`. '

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Gerenciar uma ACL de arquivo

Este exemplo obtém e define a ACL de um arquivo chamado `upload-file.txt`. Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte [controle de acesso em Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Primeiro, crie uma instância de **FileSystemClient** que representa o arquivo que você deseja baixar. Use o método **FileSystemClient. Read** para ler o arquivo. Em seguida, grave o arquivo. Este exemplo usa o módulo `fs` do node. js para fazer isso. 

> [!NOTE]
> Esse método de baixar um arquivo funciona apenas para aplicativos node. js. Se você planeja executar seu código em um navegador, consulte o arquivo de Leiame [biblioteca de cliente do data Lake do armazenamento do Azure para JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) para obter um exemplo de como fazer isso em um navegador. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Este exemplo, imprime os nomes de cada diretório e arquivo que está localizado em um diretório chamado `my-directory`.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Confira também

* [Pacote (Gerenciador de pacotes do nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)