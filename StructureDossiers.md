# Structure des dossiers LARAVEL
1. Dossier **App**: se trouve à la racine du projet de l'application LARAVEL
    1. Le fichier **composer.json** : C'est là que LARAVEL fait la liaison avec le namesapce **App**
        -   ```
                "autoload": {
                        "psr-4": {
                            "App\\": "app/",
                            "Database\\Factories\\": "database/factories/",
                            "Database\\Seeders\\": "database/seeders/"
                        }
                    },
            ```
            - Le name space sera relier au dossier **App**
    2. Dans le dossier **App** : il y a plusieur dossiers qui correspondent à des classes préconsus par LARAVEL. parmis ceux ci: 
        1. Dossier **config**:
            - Il contient la configuration des differents elements de LARAVEL 
                - **mail.php** : pour la configuration des systemes mailing 
                - **cache.php** : Pour configurer le cache 
                - ...
        2. Dossier **database**:
            - **Factories**: Contient des classes qui vont permettre d'initialiser des information à rentrer dans la base de données
            - **migration**: Des classe qui vont permettre de decrire la structure de la base de donnée
            - **seeders** : Des classes qui vont permettre de remplir la base de donnée ***(Utile pour la phase de test)***
        3. Dossier **public**:
            - C'est le dossier va servir de racine pour l'application. *(Dans le serveur web c'est ce dossier qui est la racine de l'application)* et tout ce qui est dans ce dossier sera publiquement accessible.
            - Le fichier **index.php**: Qui sera le veritable point d'entré de LARAVEL
                - Ce fichier charge le necessaire (css, js,...) et initiamiser l'application
        4. Dossier **resource**
        5. Dossier **Route**
            - pour la partie API
            - Pour la partie Channel
            - Pour la partie Console 
            - Pour la partie Web
        6. Dossier **storage** : Si l'application permet aux utilisateur d'envoyer des miniatures, pour les articles, c'est dans ce dossier qu'on va mettre ca, c'est aussi là qu'on mettra le cache pour la partie framework
        7. Dossier **test**: va contenir les tests fonctionnels, unitaire ...
        8. Dossier **vendor**
2. Le ficier **.env**: permet de parametrer le fonctionnement de l'application
3. Le fichier **.env.exemple**: est utile lorsque l'on veut reinitialiser la configuration du fishier **.env**
4. Le fichier **artisan** : Permet d'executer des commandes
    - `php artisan` pour voir les differentes commandes possible