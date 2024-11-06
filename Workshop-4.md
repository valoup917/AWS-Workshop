Workshop 4 : Monitoring et Tracing avec AWS CloudWatch et AWS X-Ray
===================================================================

Dans ce quatrième workshop, nous allons implémenter des outils de monitoring et de tracing pour notre application. Ces outils permettront de suivre la performance des API, de détecter les goulots d'étranglement et de diagnostiquer les erreurs. Nous utiliserons **AWS CloudWatch** pour la surveillance en temps réel et **AWS X-Ray** pour le tracing des requêtes dans les différents services.

Objectifs
---------

*   Configurer **AWS CloudWatch** pour le monitoring des logs et des métriques de l’application.
    
*   Utiliser **AWS X-Ray** pour tracer les requêtes API et visualiser les dépendances des services.
    
*   Créer des **alertes CloudWatch** pour suivre les erreurs et anomalies.
    

Prérequis
---------

*   Avoir terminé les **Workshops 1, 2 et 3**.
    
*   Compte AWS avec accès aux services **CloudWatch**, **X-Ray**, **API Gateway** et **Lambda**.
    
*   Notions de base sur la surveillance et les logs.
    

Étapes du Workshop
------------------

### 1\. Activer X-Ray sur les Fonctions Lambda

Nous allons commencer par activer AWS X-Ray sur les fonctions Lambda pour capturer des informations de tracing.

1.  Dans la console AWS, recherchez **Lambda** et accédez à une fonction que vous avez créée précédemment (par exemple, ManageProjects ou UploadFile).
    
2.  Allez dans l’onglet **Configuration** de la fonction et sélectionnez **Monitoring and operations tools**.
    
3.  Dans la section **AWS X-Ray**, activez le **tracing** en cochant "Active tracing".
    
4.  Cliquez sur **Save** pour sauvegarder les modifications.
    

> **Répétez cette opération pour chaque fonction Lambda** de votre application afin d'activer le tracing sur toutes les parties de votre architecture.

### 2\. Activer X-Ray sur Amazon API Gateway

Pour tracer les requêtes qui transitent par l'API, nous devons également activer AWS X-Ray sur API Gateway.

1.  Dans la console AWS, accédez à **API Gateway** et sélectionnez l'API que vous avez créée (par exemple, ProjectManagementAPI).
    
2.  Dans le menu **Stages**, sélectionnez un stage de déploiement (par exemple, prod ou dev).
    
3.  Dans la section **Settings**, activez **Enable X-Ray Tracing**.
    
4.  Cliquez sur **Save Changes** pour activer le tracing.
    

### 3\. Visualiser les Traces avec AWS X-Ray

AWS X-Ray capture maintenant les traces des requêtes qui transitent entre les services. Pour visualiser ces traces :

1.  Allez dans la console AWS et recherchez **X-Ray**.
    
2.  Dans le menu, sélectionnez **Traces** pour visualiser les traces capturées.
    
3.  Cliquez sur une trace pour explorer les détails, notamment les requêtes réussies, les erreurs, les latences, et la façon dont les services sont reliés.
    

Vous verrez une carte de service (service map) qui montre les dépendances entre les différents services (API Gateway, Lambda, DynamoDB, etc.), ce qui vous permet de repérer rapidement les éventuels goulots d'étranglement.

### 4\. Configurer des Logs et des Dashboards avec CloudWatch

Nous allons maintenant configurer CloudWatch pour surveiller les logs de notre application et créer un dashboard pour visualiser les métriques clés.

#### a) Surveillance des Logs

1.  Retournez dans la console AWS **Lambda**.
    
2.  Dans la section **Monitoring**, vous pouvez consulter les logs CloudWatch de chaque fonction Lambda.
    
3.  Cliquez sur **View logs in CloudWatch** pour accéder aux logs détaillés de la fonction.
    
4.  Répétez cette opération pour chaque fonction Lambda pour accéder à leurs logs respectifs.
    

#### b) Création d'un Dashboard CloudWatch

1.  Dans la console AWS, accédez à **CloudWatch** et sélectionnez **Dashboards** dans le menu de gauche.
    
2.  Cliquez sur **Create dashboard** et donnez un nom à votre dashboard (par exemple : AppMonitoringDashboard).
    
3.  Sélectionnez **Add widget** pour ajouter des graphiques de surveillance :
    
    *   **Métriques Lambda** : Ajoutez des graphiques pour suivre la latence, le nombre d'invocations, et le taux d'erreur des fonctions Lambda.
        
    *   **API Gateway** : Suivez le nombre de requêtes et les erreurs 4xx/5xx.
        
4.  Cliquez sur **Add widget** à chaque fois pour sauvegarder les modifications.
    

Votre dashboard vous permettra de surveiller la santé et les performances de votre application en temps réel.

### 5\. Créer des Alertes avec CloudWatch

Pour être notifié en cas de problème, nous allons créer des alertes CloudWatch qui s'activent si certains seuils sont atteints (par exemple, taux d'erreur élevé ou latence excessive).

1.  Dans **CloudWatch**, accédez à **Alarms** dans le menu de gauche et sélectionnez **Create alarm**.
    
2.  Sélectionnez une métrique :
    
    *   Allez dans **Browse** pour trouver les métriques de **Lambda** ou **API Gateway**.
        
    *   Choisissez par exemple la **latence** ou le **taux d'erreur** d'une fonction Lambda spécifique.
        
3.  Configurez le seuil de l’alerte (par exemple, taux d’erreur > 5% ou latence > 2 secondes).
    
4.  Sélectionnez **Actions** pour recevoir une notification, par exemple via **SNS** (Simple Notification Service) pour envoyer des emails.
    
5.  Donnez un nom à l'alerte et créez-la.
    

### 6\. Test des Fonctionnalités de Monitoring et de Tracing

Pour vérifier que tout fonctionne correctement, effectuez les étapes suivantes :

1.  **Envoyez des requêtes** à votre API et consultez les logs et traces dans CloudWatch et X-Ray.
    
2.  **Simulez des erreurs** (par exemple, en envoyant des données incorrectes) pour déclencher les alertes CloudWatch et vérifier la capture des erreurs dans X-Ray.
    
3.  **Vérifiez les notifications** SNS si vous avez configuré une alerte pour recevoir des emails en cas de problème.
    

Conclusion
----------

À la fin de ce workshop, vous aurez mis en place une surveillance avancée de votre application avec AWS CloudWatch et X-Ray. Vous pouvez désormais suivre les performances, diagnostiquer les problèmes en temps réel, et être alerté si des anomalies surviennent.

Dans le prochain workshop, nous allons relier l'interface front-end de l'application au back-end en utilisant les APIs que vous avez créées.

Ressources supplémentaires
--------------------------

*   [Documentation AWS CloudWatch](https://docs.aws.amazon.com/cloudwatch/index.html)
    
*   [Documentation AWS X-Ray](https://docs.aws.amazon.com/xray/index.html)
