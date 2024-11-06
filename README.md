# Workshop Semaine 1 : Configuration de l'Infrastructure Réseau, Base de Données, Authentification et Backend Serverless

## Objectifs
Dans ce premier workshop, nous allons configurer l'infrastructure réseau, la base de données, l'authentification utilisateur et la fonction backend de notre application cloud-native. Les objectifs sont :
1. Configurer un VPC avec des sous-réseaux publics et privés.
2. Mettre en place des Security Groups pour sécuriser les différents services.
3. Créer une base de données DynamoDB pour stocker les informations des to-do, utilisateurs et transactions.
4. Configurer Cognito pour gérer l'authentification utilisateur.
5. Mettre en place une fonction Lambda pour ajouter des enregistrements dans DynamoDB.

---

## Prérequis
- **Compte AWS** avec permissions administratives.
- **AWS CLI** installé et configuré localement.
- **Accès à l'interface de gestion AWS** pour effectuer certaines configurations.

---

## Étapes

### 1. Création du VPC et des sous-réseaux

#### a. Créer un VPC
1. Dans la console AWS, accédez à **VPC > Your VPCs**.
2. Cliquez sur **Create VPC**.
   - **Nom du VPC** : `todo-app-vpc`.
   - **IPv4 CIDR block** : `10.0.0.0/16`.
3. Cliquez sur **Create** pour créer le VPC.

#### b. Créer des sous-réseaux publics et privés
1. Dans la section **Subnets**, cliquez sur **Create subnet**.
2. Sélectionnez le **VPC `todo-app-vpc`** créé précédemment.
3. Configurez deux sous-réseaux :
   - **Public Subnet** :
     - **Nom** : `todo-app-public-subnet`.
     - **CIDR block** : `10.0.1.0/24`.
     - **Availability Zone** : Choisissez une AZ (e.g., `us-east-1a`).
   - **Private Subnet** :
     - **Nom** : `todo-app-private-subnet`.
     - **CIDR block** : `10.0.2.0/24`.
     - **Availability Zone** : Choisissez une autre AZ (e.g., `us-east-1b`).
4. Cliquez sur **Create subnet** pour finaliser.

#### c. Configurer une Table de routage pour le sous-réseau public
1. Allez dans **Route Tables** > **Create route table**.
2. Associez la table au **VPC `todo-app-vpc`** et nommez-la `public-route-table`.
3. Ajoutez une route pour permettre le trafic Internet :
   - **Destination** : `0.0.0.0/0`.
   - **Target** : Sélectionnez **Internet Gateway** (créez-en une si nécessaire).
4. Associez cette table au sous-réseau public `todo-app-public-subnet`.

---

### 2. Configuration des Security Groups

#### a. Créer un Security Group pour DynamoDB (pour une sécurité supplémentaire)
1. Allez dans **Security Groups** > **Create Security Group**.
2. Configurez les paramètres :
   - **Nom** : `todo-app-dynamodb-sg`.
   - **Description** : Security group pour la base de données DynamoDB.
   - **VPC** : Sélectionnez `todo-app-vpc`.
3. Ajoutez une règle d'entrée pour autoriser l’accès depuis le sous-réseau privé uniquement :
   - **Type** : HTTP ou autre protocole nécessaire.
   - **Source** : `10.0.2.0/24` (CIDR du sous-réseau privé).
4. Cliquez sur **Create Security Group**.

#### b. Créer un Security Group pour le sous-réseau public
1. Répétez les étapes pour créer un Security Group pour les instances ou autres services dans le sous-réseau public.
   - **Nom** : `todo-app-public-sg`.
   - **Description** : Security group pour les services publics (e.g., accès Internet).
   - **VPC** : `todo-app-vpc`.
2. Configurez les règles d'entrée et de sortie selon les besoins de l’application front-end.

---

### 3. Création de la base de données DynamoDB

1. Allez dans **DynamoDB > Tables** et cliquez sur **Create table**.
2. Configurez les détails de la table :
   - **Table name** : `todo-app-tasks`.
   - **Partition key** : `userId` (String).
   - **Sort key** : `taskId` (String).
3. Activez la réplication et le provisionnement selon les besoins.

---

### 4. Configuration de Cognito pour l'authentification des utilisateurs

1. Allez dans **Cognito > Manage User Pools** et cliquez sur **Create a user pool**.
2. Entrez un **Nom** pour le pool d’utilisateurs, par exemple `todo-app-user-pool`.
3. Dans **How do you want your end users to sign in?** sélectionnez **Email**.
4. Configurez les paramètres de mot de passe et d’autres options selon vos besoins.
5. Cliquez sur **Create pool**.
6. Notez l'**ID du pool d'utilisateurs** et configurez un **App client** pour permettre l’authentification via votre application.

---

### 5. Création d'une fonction Lambda pour ajouter des enregistrements dans DynamoDB

1. Accédez à **Lambda > Create function** dans la console AWS.
2. Choisissez **Author from scratch** et configurez les options suivantes :
   - **Nom** : `todo-app-add-task`.
   - **Runtime** : Sélectionnez le runtime de votre choix, par exemple Node.js ou Python.
   - **Role** : Créez un rôle IAM avec des permissions pour écrire dans DynamoDB (accès à la table `todo-app-tasks`).
3. Une fois la fonction créée, ajoutez un code qui enregistre des éléments dans DynamoDB en fonction des données entrées.
4. Configurez un **trigger** Cognito pour permettre aux utilisateurs authentifiés d'appeler la fonction Lambda.
5. Testez la fonction dans la console Lambda pour vérifier qu'elle peut ajouter un enregistrement dans DynamoDB.

---

### 6. Sécuriser l'accès à la fonction Lambda et à DynamoDB

- Vérifiez que le rôle IAM associé à la fonction Lambda a des permissions spécifiques pour accéder à la table `todo-app-tasks` dans DynamoDB.
- Utilisez les **Security Groups** configurés précédemment pour restreindre l'accès à DynamoDB.
- Assurez-vous que seuls les utilisateurs authentifiés via Cognito peuvent invoquer la fonction Lambda.

---

## Vérification de la configuration

1. **Tester la connexion à la base de données** depuis la fonction Lambda pour vérifier la configuration réseau et l'accès sécurisé.
2. **Vérifier l'authentification** en utilisant l’application front-end pour se connecter avec Cognito, puis appeler la fonction Lambda pour ajouter une tâche dans la base de données.
3. Utilisez **AWS CLI** ou l'interface de gestion AWS pour consulter les logs et confirmer que chaque étape fonctionne comme prévu.
```
