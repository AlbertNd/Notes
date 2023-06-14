# Laravel Ajax Jquery 
#### Recherche de pays pars rapport aux pays 

##### [voir documentation W3schools (AJAX)](https://www.w3schools.com/xml/ajax_intro.asp)

1. Création de la base de donnée, des model, controller et migration des tables pays et villes *(NB : relation one to many)*
    1. La base données mysql ..... connection à la base de données 
    2. **model, controller et migration**
        1. **Pays** 
            - `php artisan make:model Pays -mc`
            - **Dans la migration** 
            ```
                Schema::create('pays', function (Blueprint $table) {
                    $table->id();
                    $table->string('name');
                    $table->timestamps();
                });
            ```
            - **Dans le model**
            ```
                protected $fillable = ['name'];
                public function ville():HasMany {
                    return $this -> hasMany(Ville::class);
                }
            ```
        2. **Ville** 
            - `php artisan make:model Ville -mc`
            - **Dans la migration** 
            ```
                 Schema::create('villes', function (Blueprint $table) {
                    $table->id();
                    $table->foreignId('pays_id')->constrained();
                    $table->string('name')-> nullable();
                    $table->timestamps();
                });
            ```
            - **Dans le model**
            ```
                protected $fillable = ['name', 'pays_id']; 
                public function pays():BelongsTo {
                    return $this -> belongsTo(Pays::class);
                }   
            ``` 
        3. Lancer la migration 
            - `php artisan migrate`
        4. insertion des donnée dans la base de donnée soit via la methode sedder et factory soit manuellement
2. Création des views, Création du fichier JS dans resources/js,
    1. **La view de base** 
        - bien mettre la resources/js/jquery.js
            - Bien la configure dans le fichier ***vite.config.js***
            ```
                laravel({
                    input: ['resources/css/app.css', 'resources/js/app.js', 'resources/js/jquery.js'],
                    refresh: true,
                }),
            ```
        - Pas oublier de mettre le **CDN** de jquery tout en bas de la balise body
            ` <script src="https://code.jquery.com/jquery-3.7.0.min.js"></script>`
    
    ```
        <!doctype html>
        <html>

        <head>
            <meta charset="utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            @vite(['resources/css/app.css','resources/js/jquery.js'])
        </head>

        <body>
            <div>
                @yield('recherche')
            </div>
            <script src="https://code.jquery.com/jquery-3.7.0.min.js"></script>
        </body>

        </html>
    ```
    2. **La view recherche (Extenstion de la view de base)** 
    
        ```
            @extends('welcome')

            @section('recherche')

            <div class="container mx-auto p-5">
                <form>
                    @csrf
                    <!--La liste des pays --> 
                        <div class="w-full">
                            <select name="selectPays" id="selectPays" class="w-full p-2 bg-white border border-gray-300">
                                @foreach($pays as $pays)
                                    <option value="{{$pays -> id}}">{{$pays->name}}</option>
                                @endforeach
                            </select>
                        </div>
                    <!-- Le boutton de recherche --> 
                        <button id="recherche" class="m-3 px-2 py-2 text-white border border-gray-300 bg-green-500 hover:bg-green-600 rounded">Rechercher les villes du pays</button>
                    <!-- le tableau d'affichage des villles --> 
                    <table id="tableVille" class="hidden">
                        <!--Titre tableau --> 
                        <thead>
                            <th>Les villes </th>
                        </thead>
                        <!--le corps du tableau --> 
                        <tbody id="listVille">
                        </tbody>
                    </table>
                </form>
            </div>
            @endsection
        ```
3. Configuration du controller et de la route pour les Pays 
    1. **PaysController** *(Etant donnée que les pays sont au niveau le plus haut de la dependance, c'est dans ce controller qu'on va injecter les donnée directement dans la view et pour les autre ce sera via les configuration d'AJAX)*

    ```
         public function afficheFormulaireRecherche()
            {
                $pays = Pays::all();
                return view('recherche',['pays' => $pays]);
            }
    ```
    2. **la route pour les pays (get)** 
    `Route::get('/recherche',[PaysController::class,'afficheFormulaireRecherche']);`
4. Configuration du controller et de la route pour les villes 
    1. **VillController** *(Etant donnée que c'est le second niveau il ne renvois pas directement dans la view)*
        - Il faut recuperer l'ID du pays en liens avec  
    ```
        public function RecuperationVille(Request $request){
            $ville =Ville::where('pays_id',$request->selectPays)-> get();
            return $ville;
        }
    ```
    2. **La route pour les villes (post)** 
    `Route::post('/recherche',[VilleController::class,'RecuperationVille'])->name('rechercheData');`
5. Configuration de la requette AJAX dans le fichier jquery.js
    ```
            $(function(){

            // création d'evenement click pour le boutton recherche 
            
            $(document).on('click','#recherche', function(event){
            
                //Eviter le rafrechissement de la table 
            
                event.preventDefault();

                //stockage des valeur de du select 

                var selectPays = $('#selectPays').val();

                // recuperation de la valeur du token csrf

                var _token = $('input[type="hidden"]').attr('value');

                // appele de la requete AJAX 

                $.ajax({
            
                    //L'url sous le format des name route laravel 
            
                    uploadUrl : "{{route('rechercheData')}}",
            
                    // Recuperation des donnée du select 
            
                    data:{
                        selectPays,
            
                        // le token 
            
                        _token
                    },
            
                    // la forme de sorties des données 
            
                    dataType : "json",
            
                    // la methode 
            
                    method : "POST",
            
                    // la fonction en cas de succes de la requette AJAX
            
                    success : function(data){
            
                        // Le petit console log pour vérifier si tout est bon 
            
                        //console.log(data);
            
                        // prealablement penser a vider la liste des villes a chaque 
                        requet sinon on a des listes qui ne s'effacent pas apres chaque action du boutton recherche 
                        
                        $('#listVille').html('');
                        
                        // l'affichage du tableau des villes qui est masque par defaut 
                        
                        $('#tableVille').removeClass('hidden');
                        
                        // Boucle sur les données pour la table 
                        
                        for (let i = 0; i < data.length; i++) {
                        
                            // ajouts des balises (pas oublier le nom de la colone souhaité : name "pour le nom des villes")
                        
                            $('#listVille').append('<tr><td>'+data[i].name+'<tr><td>')
                        }
                    }
                })
            })
        });
    ```



