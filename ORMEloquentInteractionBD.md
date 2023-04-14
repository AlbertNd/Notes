# ORM (Object relational mapping)
#### Communication avec un base des données

[Documentation](https://laravel.com/docs/5.0/eloquent)

1. Laravel supporte plusieurs base de données 
    - Voir les differents type de connection dans le fichier ***Config/database.php***
2. Pour la mise en place:
    1. Fichier **.env** *(si on ne change pas les parametres, qu'on laisse juste la ligne ***DB_CONNECTION=mysql*** il prendra les valeur par defaut pour la creation : vaut mieux faire comme d'hab)*
3. Création d'une table et ses information *(On utilise le systeme de migration de laravel)*
    1. `php artisan make:migration Nom_de_la_migration` : Création d'une [migration](https://laravel.com/docs/5.0/migrations) que va permettre de rajouter des informations dans la base des données 
        - Le fichier sera créer dans le dossier ***Database/migrations***
            - Dans ce fichier il y a **deux methodes**
                1. **up()**: explique comment generer les donnée correspondant à cette migration 
                2. **down()**: permet de faire l'index *(Dans le cas ou on souhaiter revenir en arriere c'est elle qui va supprimer la table)*
    2. Dans la methodes **up()**
        - [Documentation](https://laravel.com/docs/4.2/schema)
    3. `php artisan migrate` creation et generation des migrations 
4. Récuperation des informations 
    1. Création d'un [models](https://laravel.com/docs/10.x/eloquent#generating-model-classes)
        - `php artisan make:model nom` : il est recommander de mettre le meme nom que la table
        - **NB: Il est possible de créer le model et les migration en meme temps :** `php artisan make:model nom -m`
    2. Le fichier sera créer dans **app/models**
        - Dans le fichier il y a **hasFactory** qui permetra de generer des données
        - Il y a rien a faire dans ce fichier, il permet juste de communiquer facilement avec la base des données 
5. Test en exemple : 
    - Dans le dossier le fichier ***route/web.php*** 
        ```
            Route::get('/', function(){
                $post = new \App\Models\post(); // on peut inporte la methode pour avoir un code plus propre
                $post -> title = 'mon titre'
                $post -> nom = 'ndizye'
                $post -> prenom = 'albert'
                $post -> save();


                
            });
        ```
        - La methode **save()** permet de sauvegarder dans la base de donnée