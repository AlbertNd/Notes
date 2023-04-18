# le routing 

[Documentation laravel](https://laravel.com/docs/10.x/routing)

1. Dans le fichier ***web.php*** 
    1. On va avoir ue classe qui se situe dans le name space ***use Illuminate\Suppor\Facades\Route**
    2. Un route 
        ```
            Route::get('/', function(){
                return view('welcome');
            });
        ```
        - Elle repond à la methode **get** du nagivateur 
            - Un permier parametre **'/'**; L'URL càd la racine 
            - Une fonction **function(){ ...}** permetant qu'explique commment repondre
            - NB: ppour voir les retours de la fonction : ***inspect => Réseau => document => Content-Type***
                - La fonction peut retourner :
                    - une chaine de carctere : `return 'blablaba'`;
                    - Un tableau :`return ["article"=>"article 1"]` *(le retour sera de type json)*
                    - Un lien : `return => '/racine/...`
                        - Apres avoir vu la suite ci dessous   
                        ```
                            Route::get('/racine',function(Request $request){
                                return [
                                    "lien" => \route('nom de la route',['parametre'=>'article', 'id'=>12]),
                                ]
                            })
                        ```
                - La fonction peut recuperer un paramettre dans l'URL *(avant on utiliser le parametre g$_GET['...'])*
                    - On inject à la fonction un paramettre **Request**qui va permettre de recupere les informations sur la requete 
                    ```
                        use Illuminate\Http\Request;

                        function(Request $request) {
                            return $request -> ce qu'ont souhaite retourner et qui vient de l'URL
                        }
                    ```
                    - On peut recuperer un route, le chemin , methode ***all()*** , methode ***input()*** .....
2. Avoir des URL propres 
    1. Pour les paramettre dynamique aui se trouvent apres la racine
    ```
        Route::get('/racine/{parametre}-{id}', function(){
            return 'blabalabl';
        });
    ```
    -  Les paramettre en accolade veut dire que l'on aura un paramettre dinamique
    2. Pour recuperer les paramettre 
    ```
        "Dans l'URL : localhost:8000/racine/parametre-id" 

        Route::get('/racine/{paramettre}-{id}' function(string $parametre, string $id){
            return [
                "parametre" => $parametre,
                "id" => $id
            ];
        })->where([
            'id' => '[0-9]+', "Expression reguliere pour les nombres à repeter plusieur fois +"
            'parametre' => '[a-z0-9\-]+' "Expression reguliere pour les nombre et les lettre et enplus accepter les "-" à repeter plusieurs fois + "
        ]

        );
    ```
    - Les deux paramettres seront typé sous forme de chaine de caractere et correspondront au niveau de leur nom au nom des paramettre que l'on a mis dans l'URL.
    - La methode **where()** permet de spécifier des contraintes sur les parametres *(si on veut que id soit que des nombre ...)*
    3. Si on souhaite recuperer des information sur l'URL
    ```
        "Dans L'URL : localhost:8000/racine/parametre-id?nom=albert"

        Route::get('/racine/{paramettre}-{id}',function(string $parametre, string $id, Request $request){

        return [
                "parametre" => $parametre,
                "id" => $id,
                "nom" => $request -> input('nom'),
            ];
        })->where([
            'id' => '[0-9]+', "Expression reguliere pour les nombres à repeter plusieur fois +"
            'parametre' => '[a-z0-9\-]+' "Expression reguliere pour les nombre et les lettre et enplus accepter les "-" à repeter plusieurs fois + "
        ]

        );
    ```
    - On utilise les paramettre **Request** en troisieme parametre **Dans le retour "input('')"**
    - ***NB: L'ordre n'a pas d'importance***
3. Nommage des liens (des routes)
    ```
         Route::get('/', function(){
                return view('welcome');
            })-> name('nom du lien ou des la route)';
    ```                        
4. Si on souhaite lister l'ensemble des route 
    - `php artisan route:list`
        - A l'affichage : Methode, URL (avec ses paramettre si il y en a), et Nom  
5. Il est possible de regouper plusieur route lorsqu'ils ont des points communs 
    - La meme racine ....
    ```
        Route::prefix('/racine')->group(function(){
            "Ici on definis les routes sans preciser la racine mais on y met le "/" pour direqu ec'est la page racine" 
            Route::get('/'.....)....;
        })
    ```
    - On peut aussi les nommer comme ca elles auront toute le meme nom 
    ```
        Route::prefix('/racine')->name('nom')->group(function(){
            
            Route::get('/'.....)-> name('nom de la route sans le prefixer')....;
        })

        "Au lising des route elles auront les prefixe de nom general en plus de leur nom propre"

    ```
6. #### Le model binding
[Documentations](https://laravel.com/docs/10.x/routing#route-model-binding)
    - On a la possibilité de demander à LARAVEL de prerecuperer les informations lorsqu'on a une route spécifique
    - LARAVEL a la capacité que lorsqu'il voit un parametre qui correspond a un model de se dire que lorsque on lui demande ce paramettre ce qu'il doit trouver un model qui a un parametre qui correspond à ce qui est passé
        - Ce qui permet de pas utiliser la fonction **findOrFail()** car directement, dans la cas d'un id on peut reperer l'article qui correspond
            - Si c'est un qui n'existe pas il retourn un **failed**