# ORM (Object relational mapping)
#### Communication avec un base des données

[Documentation](https://laravel.com/docs/5.0/eloquent)

1. Laravel supporte plusieurs base de données 
    - Voir les differents type de connection dans le fichier ***Config/database.php***
2. Pour la mise en place:
    1. Fichier **.env** *(si on ne change pas les parametres, qu'on laisse juste la ligne ***DB_CONNECTION=mysql*** il prendra les valeur par defaut pour la creation : vaut mieux faire comme d'hab)*
3. Création d'un **models** *(Permetant de communique avec la base de donnée)* et d'une **migration** *(Permettant la creation de la table et de rajouter des information dans la base de donnée)*
    - `php artisan make:model nom -m`
        - Le [models](https://laravel.com/docs/10.x/eloquent#generating-model-classes) sera créer dans le fichier **app/models**
            - Dans le fichier il y a **hasFactory** qui permetra de generer des données
            - *(Il y a rien a faire dans ce fichier, il permet juste de communiquer facilement avec la base des données)*
        - La **migration** créee via "**-m**" sera créer dans le dossier **Database/migrations** 
            - Dans ce fichier il y a **deux methodes**
                1. **up()**: explique comment generer les donnée correspondant à cette migration 
                    - [Documentation](https://laravel.com/docs/4.2/schema)
                        - Voir coc pour la configuration des colone si *unique, .....*
                2. **down()**: permet de faire l'index *(Dans le cas ou on souhaiter revenir en arriere c'est elle qui va supprimer la table)*
                ```
                    return new class extends Migration
                        {
                            /**
                            * Run the migrations.
                            */
                            public function up(): void
                            {
                                Schema::create('model_tests', function (Blueprint $table) {
                                    $table->id();
                                    $table->string('nom');
                                    $table->string('prenom');
                                    $table->string('titre');
                                    $table->timestamps();
                                });
                            }

                            /**
                            * Reverse the migrations.
                            */
                            public function down(): void
                            {
                                Schema::dropIfExists('model_tests');
                            }
                        };
                ```
4. Creation et generations des migration dans la base de donnée
    - `php artisan migrate` 
    - Voir dans la base de donnée si ca fonction bien. 

5. Tester l'introduiction des information dans la base de donnée 
    - Dans le dossier le fichier ***route/web.php*** 
        ```
            Route::get('/', function(){
                $test = new \App\Models\ModelTest(); // on peut inporte la methode pour avoir un code plus propre
                $test -> titre = 'mon titre'
                $test -> nom = 'ndizye'
                $test -> prenom = 'albert'
                $test -> save();

                return $test;     
            });
        ```
        - La methode **save()** permet de sauvegarder dans la base de donnée
    - On verifier dans la base de donnée pour voir si ca bien fonctionner et ensuite le lien de la route devrait rendre les information souhaité
    - On peut aussi utiliser la methode **create()**
        ```
            Route::get('/', function(){
                        $variable = \App\Models\ModelTest:create([
                            'titre' => 'mon titre'
                            'nom' => 'ndizye'
                            'prenom' => 'albert'
                        ]);
                    });

        ```
        - **Pour une creation sous forme de tableau il faut le preciser dans le model sinon il y aura une erreur**
            - **protectd $fillable[]** une protection qui permet de spécifier les champs remplissable càd qui ont l'autorisation d'etre remplir
            - il y a aussi **protected $guarded[]** une protection qui permet de spécifier les champs non remplissable càd qui n'ont pas l'autorisation d'etre remplir
        ```
        
            class ModelTest extends Model
                {
                    use HasFactory;

                    protected $fillable = ['titre','nom','prenom'];
                }
        ```

6. La récuperation des informations depuis la base de donnée.
    1. On va utiliser la **classe** du **Model** creer pour recupere les inforamtion. 
        ```
            Route::get('/', function(){
                    return \App\Models\ModelTest:all(): // la methode all() permet de recuperer l'ensemble des données
                });
        ```
        - Il y a differentes methodes interressant: 
            - **all()** permet de recuperer l'ensemble des données
            - **find(1)** permet de recupere les information de l'id que l'on place des l'argument de la fonction ici **1**
                - **findOrFail()** Si pas de donnée ca coupe tout (page 404)
                    - possibilité de rediriger en cas de faill
                        ```
                            $variable = \App\Models\ModelTest:findOrFail($titre);
                            if($variable -> titre != $titre){
                                return to_route('nom de la route',['titre' => $variable -> titre]);
                            }
                            return $variable;
                        ```
            - **paginate()** *(permet aussi de creer un API des informations)*
        - Ca va retourne, pour chaque donnée de la base un tableau avec les information contenu (id,nom,prenom,titre,...)
        - **NB:** *il est possible de spécifier les champs qe l'on souhaite voir afficher*
            - `all(['id','titre'])` :*il va retouner que les deux informations
    2. **Si on souhaite voir le type des retour recus**
        -  La fonction **dd()** Dump and Die: elle permet de debuguer une variable. 
        ```
            Route::get('/', function(){
                    $variable = \App\Models\ModelTest:all(['id','nom']);
                    dd($variable); 
                });
        ```
        - Il va s'arrete au niveau de la **dd** et le retour sera de type: 
            ```
                Illuminate\Database\Eloquent\Collection {#623 ▼ // routes/web.php:19
                #items: array:2 [▶]
                #escapeWhenCastingToString: false
                }
            ```
            - Elle renvois une [collection](https://laravel.com/docs/10.x/collections) avec les items et chaque items est un objct de type de la classe initialiser avec different information *(pour voir les information click sur le triangle)*
            - *on veut un champs d'une donnée par exemple la premier entrée*
                - `dd($variable[0]->prenom);` il retourne le prenom de la premiere entrée (ayant l'index 0)
7. Recupération via le [query bulder](https://laravel.com/docs/10.x/queries) permattant de concevoir des requettes
    - Conception des requettes spécifique en partant du **model** 
        - Si on veut des condition, des join, ... voir dans doc [query bulder](https://laravel.com/docs/10.x/queries)
        - Exemple recuperer les id > 1
        ```
            Route::get('/', function(){
                    $variable = \App\Models\ModelTest:where('id',>,0)->get();
                    
                    
                    dd($variable); 
                });
        ```
8. Pour modifier une donnée 
    - recuperer la donnée via son id, changer l'element souhaité et sauvearder à nouveau :
    ```
         Route::get('/', function(){
                    $variable = \App\Models\ModelTest:find(1);
                    $variable -> titre = 'autre titre';
                    $variable-> save();
                });

    ```
9. pour supprimer une donnée
    - recuperer la donnée via son id, supprimer l'element souhaité :
    ```
         Route::get('/', function(){
                    $variable = \App\Models\ModelTest:find(1);
                    $variable -> titre = 'autre titre';
                    $variable-> delete();
                });

    ```