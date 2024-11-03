Workshop 3 : Gestion des Fichiers avec Amazon S3 et Base de Données NoSQL avec DynamoDB
=======================================================================================

Dans ce troisième workshop, nous allons ajouter des fonctionnalités de stockage de fichiers et de métadonnées pour notre plateforme **CodeCraft**. Nous utiliserons **Amazon S3** pour gérer les fichiers et **DynamoDB** pour stocker les informations associées. Cela permet à chaque projet de disposer d'un espace de stockage dédié pour les documents et ressources (images, fichiers de documentation, etc.).

Objectifs
---------

*   Configurer un **bucket Amazon S3** pour le stockage de fichiers.
    
*   Mettre en place une table **DynamoDB** pour gérer les métadonnées des fichiers (nom, type, projet associé).
    
*   Créer une fonction **Lambda** pour gérer l'upload de fichiers et stocker les métadonnées.
    

Prérequis
---------

*   Avoir terminé les **Workshops 1 et 2**.
    
*   Compte AWS avec accès aux services **S3**, **DynamoDB**, et **Lambda**.
    
*   Notions de base en stockage de fichiers et en bases de données NoSQL (facultatif).
    

Étapes du Workshop
------------------

### 1\. Création d'un Bucket S3 pour le Stockage de Fichiers

Nous allons commencer par configurer un bucket S3 où les fichiers de chaque projet seront stockés.

1.  Dans la console AWS, recherchez **S3** et cliquez sur **Create bucket**.
    
2.  Donnez un nom unique au bucket (ex. : CodeCraft-project-files).
    
3.  Laissez les paramètres par défaut, mais assurez-vous que le **blocage de l'accès public** est activé pour des raisons de sécurité.
    
4.  Cliquez sur **Create bucket** pour finaliser.
    

### 2\. Création d'une Table DynamoDB pour les Métadonnées des Fichiers

Nous allons maintenant créer une table DynamoDB pour stocker les métadonnées des fichiers. Cette table permettra de suivre des informations telles que le nom du fichier, le type de fichier, le projet auquel il appartient, et la date d'ajout.

1.  Dans la console AWS, recherchez **DynamoDB** et cliquez sur **Create table**.
    
2.  Donnez un nom à la table (ex. : FileMetadata).
    
3.  Définissez la clé primaire :
    
    *   fileID (Type : String) - Un identifiant unique pour chaque fichier.
        
4.  Cliquez sur **Create table** pour finaliser la création.
    

> **Note** : DynamoDB est flexible. Si besoin, on peut ajouter des attributs plus tard pour gérer des informations supplémentaires.

### 3\. Création d'une Fonction Lambda pour l'Upload de Fichiers

Nous allons maintenant créer une fonction Lambda pour gérer l'upload des fichiers sur S3 et stocker les métadonnées dans DynamoDB.

1.  Retournez dans la console AWS **Lambda** et sélectionnez **Create function**.
    
2.  Choisissez **Author from scratch**, nommez la fonction UploadFile, et sélectionnez le runtime de votre choix (ex. : Python 3.x ou Node.js).
    
3.  Attribuez un rôle IAM avec les permissions pour accéder à **S3** et **DynamoDB**.
    
4.  Ajoutez le code suivant pour gérer l'upload sur S3 et l'enregistrement des métadonnées sur DynamoDB :
    

Exemple de code en Python :
```python
import json
import boto3
import uuid

s3_client = boto3.client('s3')
dynamodb_client = boto3.resource('dynamodb')
table = dynamodb_client.Table('FileMetadata')

BUCKET_NAME = 'CodeCraft-project-files'

def lambda_handler(event, context):
    file_content = event['file_content']  # Contenu du fichier
    file_name = event['file_name']        # Nom du fichier
    project_id = event['project_id']      # Projet associé
    file_type = event['file_type']        # Type de fichier (ex. image, pdf)
    
    # Générer un identifiant unique pour le fichier
    file_id = str(uuid.uuid4())
    s3_key = f"{project_id}/{file_name}"

    # Upload du fichier dans S3
    s3_client.put_object(Bucket=BUCKET_NAME, Key=s3_key, Body=file_content)

    # Sauvegarde des métadonnées dans DynamoDB
    table.put_item(
        Item={
            'fileID': file_id,
            'fileName': file_name,
            'projectID': project_id,
            'fileType': file_type,
            's3Key': s3_key
        }
    )

    return {
        'statusCode': 200,
        'body': json.dumps('File uploaded successfully')
    }

```

> **Note** : Ce code est un exemple de base. Les paramètres file\_content et file\_name devront être fournis via une API ou une interface front-end.

### 4\. Configuration d'API Gateway pour l’Upload de Fichiers

Pour permettre aux utilisateurs d'uploader des fichiers via une API REST, nous allons configurer un endpoint API Gateway qui déclenche la fonction Lambda.

1.  Dans la console **API Gateway**, accédez à l’API ProjectManagementAPI créée précédemment.
    
2.  Ajoutez une nouvelle **resource** /upload et créez une méthode POST.
    
3.  Sélectionnez **Lambda Function** comme intégration et liez la méthode à la fonction UploadFile.
    
4.  Déployez l'API pour rendre le nouvel endpoint accessible.
    

### 5\. Test de l'Upload de Fichiers et des Métadonnées

Vous pouvez maintenant tester l'upload de fichiers en appelant l'endpoint /upload de l'API avec un payload JSON. Utilisez un outil comme **Postman** ou **curl** pour envoyer une requête de test.

Exemple de payload JSON :

```json

{
    "file_content": "contenu du fichier en base64",
    "file_name": "guide.pdf",
    "project_id": "12345",
    "file_type": "pdf"
}

```

Vérifiez que :

*   Le fichier est bien stocké dans le bucket S3.
    
*   Les métadonnées du fichier sont enregistrées dans la table DynamoDB.
    

### 6\. Gestion des Permissions et de la Sécurité

Assurez-vous que les permissions S3 et DynamoDB de la fonction Lambda sont bien configurées pour éviter les accès non autorisés. Pour sécuriser davantage les uploads, vous pouvez restreindre l'accès à l'endpoint /upload à des utilisateurs authentifiés en configurant Cognito sur API Gateway.

Conclusion
----------

Félicitations ! Vous avez terminé le troisième workshop. Vous avez configuré Amazon S3 pour stocker des fichiers, une table DynamoDB pour gérer les métadonnées associées, et une fonction Lambda pour automatiser le processus d’upload.

Dans le prochain workshop, nous allons explorer les outils de **monitoring** avec **AWS CloudWatch** et **AWS X-Ray** pour mieux comprendre les performances de notre application.

Ressources supplémentaires
--------------------------

*   [Documentation Amazon S3](https://docs.aws.amazon.com/s3/index.html)
    
*   [Documentation DynamoDB](https://docs.aws.amazon.com/dynamodb/index.html)
    
*   [Documentation AWS Lambda](https://docs.aws.amazon.com/lambda/index.html)
