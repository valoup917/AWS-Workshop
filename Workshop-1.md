Workshop 1 : Authentification et Base de Données avec Amazon Cognito & Amazon RDS
=================================================================================

Bienvenue dans ce premier workshop ! Dans ce module, vous allez configurer l’authentification des utilisateurs avec **Amazon Cognito** et mettre en place une base de données **Amazon RDS** pour stocker les informations des utilisateurs de notre plateforme collaborative de gestion de projets open source. À la fin de ce workshop, vous aurez mis en place une authentification sécurisée et une base de données relationnelle prête à stocker les données des utilisateurs.

Objectifs
---------

*   Configurer un **pool d’utilisateurs** avec Amazon Cognito.
    
*   Mettre en place une **base de données MySQL** avec Amazon RDS.
    
*   Connecter Cognito et RDS pour stocker les informations utilisateur.
    

Prérequis
---------

*   Un compte AWS.
    
*   Accès à la **console AWS**.
    
*   Notion de bases de données relationnelles (MySQL).
    
*   Familiarité avec les concepts de base d’authentification (facultatif).
    

Étapes du Workshop
------------------

### 1\. Création d'un pool d’utilisateurs Amazon Cognito

Nous allons commencer par créer un pool d’utilisateurs pour gérer l’authentification.

1.  Connectez-vous à la **console AWS** et recherchez "Cognito" dans la barre de recherche.
    
2.  Sélectionnez **Manage User Pools** puis cliquez sur **Create a user pool**.
    
3.  Nommez votre pool (par exemple : UserPoolProjetOpenSource) et cliquez sur **Step through settings**.
    
4.  **Configuration des attributs utilisateur** :
    
    *   Allez dans **Attributes** et assurez-vous de sélectionner au moins email comme attribut requis.
        
5.  **Configurer les paramètres de sécurité** :
    
    *   Dans **Password policy**, définissez une politique de mot de passe (par exemple, avec majuscules, chiffres et caractères spéciaux).
        
6.  **Configurer l'inscription et la confirmation** :
    
    *   Dans **Sign-up and sign-in**, activez l’option pour permettre aux utilisateurs de s’inscrire avec leur adresse e-mail.
        
7.  **Configurer l’authentification multi-facteurs (facultatif)** :
    
    *   Vous pouvez choisir d’activer le MFA si vous souhaitez un niveau de sécurité supplémentaire.
        
8.  **Finaliser le pool** :
    
    *   Cliquez sur **Create Pool** pour finaliser la création.
        

> **Note** : Conservez l'ID du pool utilisateur pour les étapes suivantes.

### 2\. Créer une application cliente Cognito

1.  Dans votre pool d’utilisateurs nouvellement créé, allez dans **App clients** et cliquez sur **Add an app client**.
    
2.  Donnez un nom à votre client (par exemple, AppClientProjetOpenSource) et désactivez l’option de génération automatique d'un mot de passe temporaire.
    
3.  Cliquez sur **Create app client** pour finaliser.
    

> **Note** : Conservez l'ID du client, car vous en aurez besoin pour l'authentification.

### 3\. Configuration d'une base de données Amazon RDS

Nous allons maintenant configurer une base de données pour stocker les informations des utilisateurs.

1.  Retournez sur la **console AWS** et recherchez **RDS** dans la barre de recherche.
    
2.  Cliquez sur **Create database**.
    
3.  Sélectionnez **Standard create** et choisissez **MySQL** comme moteur de base de données.
    
4.  **Configuration de l'instance** :
    
    *   Choisissez une version récente de MySQL.
        
    *   Dans **Templates**, sélectionnez **Free Tier** si disponible.
        
5.  **Configurer les informations d'identification** :
    
    *   Définissez un nom d’utilisateur maître (ex. : admin) et un mot de passe maître.
        
6.  **Configurer la connectivité** :
    
    *   Dans **Public access**, sélectionnez **Yes** pour autoriser l'accès public (si nécessaire).
        
    *   Assurez-vous que votre instance est dans le même VPC que vos autres ressources.
        
7.  **Configurer les paramètres avancés** :
    
    *   Dans **Database options**, définissez un nom pour votre base de données initiale (ex. : ProjetOpenSourceDB).
        
8.  Cliquez sur **Create database** pour lancer le déploiement.
    

> **Note** : La création de la base de données peut prendre quelques minutes. Pendant ce temps, vous pouvez configurer les permissions IAM.

### 4\. Configuration des permissions IAM

Pour que Cognito puisse interagir avec RDS, nous allons configurer un rôle IAM.

1.  Allez dans **IAM** dans la console AWS, puis dans **Roles**.
    
2.  Cliquez sur **Create role** et sélectionnez **Cognito** comme service AWS qui va utiliser ce rôle.
    
3.  Attachez la politique **AmazonRDSFullAccess** pour permettre l'accès à RDS.
    
4.  Donnez un nom au rôle (ex. : CognitoRDSAccessRole) et finalisez la création.
    

### 5\. Relier Cognito et RDS

Une fois que Cognito et RDS sont configurés, nous allons intégrer ces deux services.

1.  Dans la console Cognito, retournez sur votre **pool d'utilisateurs** et sélectionnez **Identity Pool**.
    
2.  Associez l'ID de votre pool d'identités Cognito à votre pool d'utilisateurs pour créer un système d'authentification complet.
    
3.  Utilisez l’ID de votre client et l’ID de votre pool d’utilisateurs pour configurer les autorisations d'accès dans votre application.
    

### 6\. Tester l’authentification

Pour vous assurer que tout fonctionne correctement, effectuez les étapes suivantes :

1.  Utilisez l’interface Cognito pour créer un utilisateur de test (ou simulez une inscription).
    
2.  Testez la connexion avec cet utilisateur et vérifiez que les informations sont correctement stockées dans RDS.
    

Conclusion
----------

Félicitations ! Vous avez terminé le premier workshop. Vous avez mis en place une authentification sécurisée avec Amazon Cognito et une base de données relationnelle avec Amazon RDS pour stocker les informations de vos utilisateurs. Vous êtes maintenant prêts à intégrer ces éléments dans les prochaines étapes du projet.

Dans le prochain workshop, nous utiliserons ces identifiants pour créer des API sécurisées et ajouterons des fonctionnalités asynchrones avec Amazon SQS.

Ressources supplémentaires
--------------------------

*   [Documentation Amazon Cognito](https://docs.aws.amazon.com/cognito/index.html)
    
*   [Documentation Amazon RDS](https://docs.aws.amazon.com/rds/index.html)
