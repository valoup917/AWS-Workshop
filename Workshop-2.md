Workshop 2 : Création d'API REST, Queues SQS et Fonctions Lambda
===========================================================================

Dans ce deuxième workshop, nous allons construire les bases d'une API REST sécurisée pour notre plateforme collaborative en utilisant **Amazon API Gateway** et **AWS Lambda**. Nous allons également introduire un système de queue avec **Amazon SQS** pour gérer des tâches asynchrones, comme les notifications d'actions utilisateur.

Objectifs
---------

*   Créer une API REST sécurisée avec **Amazon API Gateway**.
    
*   Utiliser **AWS Lambda** pour exécuter des fonctions sans serveur.
    
*   Configurer **Amazon SQS** pour gérer des queues de messages asynchrones.
    

Prérequis
---------

*   Avoir terminé le **Workshop 1** (authentification avec Cognito et base de données RDS).
    
*   Compte AWS avec accès aux services **API Gateway**, **Lambda**, et **SQS**.
    
*   Notions de base en API REST et en traitement asynchrone (facultatif).
    

Étapes du Workshop
------------------

### 1\. Création de la fonction Lambda pour gérer les projets

Nous allons commencer par créer une fonction Lambda qui gérera les actions de création et de modification de projets dans notre plateforme.

1.  Dans la console AWS, recherchez **Lambda** et sélectionnez **Create function**.
    
2.  Choisissez **Author from scratch** et nommez la fonction ManageProjects.
    
3.  Choisissez **Runtime** (ex. : Python 3.x ou Node.js).
    
4.  Attribuez un rôle IAM avec les permissions nécessaires pour accéder à **Amazon RDS** (utilisez le rôle créé dans le workshop 1 ou créez un nouveau rôle avec l'accès requis).
    
5.  Dans le code de la fonction, ajoutez une logique basique pour :
    
    *   Créer un projet en insérant des données dans la base de données RDS.
        
    *   Mettre à jour un projet existant.
        

Exemple de code Lambda (en Python) pour gérer la création d'un projet :

```python
import json
import pymysql

# Configuration de la connexion RDS
rds_host = "votre-hostname-rds"
db_user = "admin"
db_password = "votre-mot-de-passe"
db_name = "ProjetOpenSourceDB"

def lambda_handler(event, context):

    # Connexion à RDS
    connection = pymysql.connect(host=rds_host, user=db_user, password=db_password, database=db_name)

    # Récupérer les données de l'API
    body = json.loads(event['body'])
    action = body.get('action')
    project_name = body.get('name')
    description = body.get('description')

    try:
        with connection.cursor() as cursor:
            if action == "create":
                sql = "INSERT INTO projects (name, description) VALUES (%s, %s)"
                cursor.execute(sql, (project_name, description))
                connection.commit()
                return {"statusCode": 201, "body": json.dumps("Project created")}
            elif action == "update":
                project_id = body.get('id')
                sql = "UPDATE projects SET name=%s, description=%s WHERE id=%s"
                cursor.execute(sql, (project_name, description, project_id))
                connection.commit()
                return {"statusCode": 200, "body": json.dumps("Project updated")}
    finally:
        connection.close()

```

> **Note** : N'oubliez pas de configurer les informations d'identification RDS dans le code.

### 2\. Création de l’API REST avec Amazon API Gateway

1.  Dans la console AWS, recherchez **API Gateway** et sélectionnez **Create API**.
    
2.  Choisissez **REST API** et sélectionnez **Build**.
    
3.  Donnez un nom à l'API, par exemple ProjectManagementAPI.
    
4.  Créez une ressource /projects :
    
    *   Dans **Actions**, sélectionnez **Create Resource**.
        
5.  Ajoutez une méthode POST :
    
    *   Sélectionnez **POST** et choisissez **Lambda Function** comme intégration.
        
    *   Liez cette méthode à la fonction Lambda ManageProjects que vous avez créée.
        

> **Note** : Vous pouvez également configurer l'authentification Cognito sur l'API pour sécuriser l'accès.

### 3\. Configuration d'Amazon SQS pour la gestion des notifications

Nous allons utiliser Amazon SQS pour créer une queue où chaque action (par exemple, création de projet) déclenche une notification pour les utilisateurs concernés.

1.  Dans la console AWS, recherchez **SQS** et sélectionnez **Create Queue**.
    
2.  Choisissez le type de queue **Standard Queue** et nommez-la ProjectNotificationsQueue.
    
3.  Laissez les paramètres par défaut pour ce workshop.
    

### 4\. Connecter Lambda et SQS pour les notifications asynchrones

1.  Retournez dans la console Lambda, sélectionnez votre fonction ManageProjects.
    
2.  Dans l’onglet **Configuration**, allez dans **Triggers** et ajoutez **SQS**.
    
3.  Sélectionnez ProjectNotificationsQueue comme file d’attente source.
    
4.  Dans le code Lambda, modifiez la fonction pour envoyer un message à SQS lors de la création d’un projet.
    

Exemple de code pour envoyer un message SQS :
```python
import boto3

sqs = boto3.client("sqs")
queue_url = "votre-URL-SQS"


def send_notification(message_body):
    response = sqs.send_message(
        QueueUrl=queue_url, MessageBody=json.dumps(message_body)
    )
    return response

```
1.  Intégrez send\_notification dans la logique de création de projet pour envoyer une notification à chaque fois qu’un projet est créé ou mis à jour.
    

### 5\. Test de l'API et de SQS

1.  Dans la console **API Gateway**, utilisez l'option **Test** sur la méthode POST pour envoyer une requête de test.
    
2.  jsonCopier le code{ "action": "create", "name": "Awesome Project", "description": "This is an awesome project."}
    
3.  Vérifiez que le projet est créé dans votre base RDS et qu'un message est envoyé dans la file d’attente SQS.
    
4.  Vous pouvez consulter les messages SQS en retournant dans la console SQS et en cliquant sur ProjectNotificationsQueue.
    

Conclusion
----------

À la fin de ce workshop, vous avez construit une API REST pour gérer les projets, déclenchant des notifications asynchrones via SQS. Vous avez également exploré comment utiliser AWS Lambda pour écrire des fonctions serverless qui interagissent avec RDS et SQS.

Dans le prochain workshop, nous allons ajouter des fonctionnalités de stockage de fichiers avec **Amazon S3** et enrichir notre base de données avec **DynamoDB**.
