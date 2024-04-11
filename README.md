### Overview
---
You can serve static content (HTML, CSS, JavaScript, and image files) directly from a storage container named $web. Hosting your content in Azure Storage enables you to use serverless architectures that include Azure Functions and other Platform-as-a-service (PaaS) services. Azure Storage static website hosting is a great option in cases where you don't require a web server to render content.

### Key Aspects
---
N/D

### Environment
---
- Microsoft Azure Portal
  - Valid Subscription
  - Azure Cloud Shell
 
### Actions
---
[ On Azure Cloud Shell # PowerShell ]

Prepare the environment
- Create an Azure Resource Group
```azurecli
az group create --location "westeurope" --name "MyResourceGroup"
```
- Create an Azure Storage Account
```azurecli
az storage account create `
--name mystorageaccount20240410 `
--resource-group MyResourceGroup `
--location "westeurope" `
--sku Standard_LRS
```
- Enable the static website on the Azure Storage Account
```azurecli
az storage blob service-properties update `
--account-name "mystorageaccount20240410" `
--static-website `
--404-document "404.html" `
--index-document "index.html"
```
Create the HTML files
- Create Index.html
```azurecli
Code Index.html
#
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Index</title>
</head>
<body>
    <h1>Index</h1>
</body>
</html>
```
- Create 404.html
```azurecli
Code 404.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>404</title>
</head>
<body>
    <h1>404</h1>
</body>
</html>
```
Define settings
- Grant permission of “Storage Blob Data Owner”
```azurecli
az role assignment create `                                                             
--assignee 67338a79-b421-4e74-91bf-f0ce129cc220 `
--role "Storage Blob Data Owner" `                        
--scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount20240410/blobServices/default
```
- Set the blob storage to public
```azurecli
az storage account update `
--name "mystorageaccount20240410" `
--allow-blob-public-access true        
```
```azurecli
az storage container set-permission ` 
--name '$web' `
--public-access blob `
--account-name "mystorageaccount20240410"            
```
Upload the HTML files on Azure Storage Account # Blob Storage
- Upload Index.html
```azurecli
az storage blob upload `
 --account-name "mystorageaccount20240410" `
 --name "index.html" `
 --file "index.html" `
 --type block  `
 --auth-mode login `
 --container-name '$web'
```
- Upload 404.html
```azurecli
az storage blob upload `
 --account-name "mystorageaccount20240410" `
 --name "404.html" `
 --file "404.html" `
 --type block  `
 --auth-mode login `
 --container-name '$web'
```
Test the implementation
```azurecli
Invoke-WebRequest -Uri 'https://mystorageaccount20240410.blob.core.windows.net/$web/index.html'
```

### Media
---
![image](https://github.com/ViCunha/Lab-Azure-AzureStorageAccount-BlobStorage-StaticHTML/assets/65992033/60e1f94d-82af-4442-95c9-0f735908ac9f)
---
![image](https://github.com/ViCunha/Lab-Azure-AzureStorageAccount-BlobStorage-StaticHTML/assets/65992033/15c1ea2e-7bd8-41b7-8977-c49d59eef23f)
---
![image](https://github.com/ViCunha/Lab-Azure-AzureStorageAccount-BlobStorage-StaticHTML/assets/65992033/b50f7192-4414-44cd-80e9-c33fbc60f30a)
---
![image](https://github.com/ViCunha/Lab-Azure-AzureStorageAccount-BlobStorage-StaticHTML/assets/65992033/a71cc09b-053f-4fe2-b67d-f231307e48cd)


### References
---
- [Discover static website hosting in Azure Storage - Training | Microsoft Learn](https://learn.microsoft.com/en-us/training/modules/explore-azure-blob-storage/5-blob-storage-static-website)
