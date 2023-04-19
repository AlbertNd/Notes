# Creation de formulaire pour creér modifier et supprimer 

#### Creation

1. Creation de la vue et de sa configuration. 
    1. Creation de la view dans le dossier prevue pour 
        ```
            <div class="px-20">
                <form action="" method="post">
                @csrf // Pas oublier cette directive
                    <div>
                    <label for="nom">Nom</label>
                    <input type="text" name="nom" id="" class="border border-gray-300 py-5">
                    </div>
                    <div>
                    <label for="titre">Nom</label>
                    <input type="text" name="titre" id="" class="border border-gray-300 py-5">
                    </div>
                    <div>
                    <label for="message">message</label>
                    <textarea name="message" id="" cols="30" rows="10" class="border border-gray-300"></textarea>
                    </div>
                    <button type="submit" class="border border-gray-300 px-2 py-4">enyover</button>
                </form>
            </div>
        ```
2. Creation du controlleur et **des fonctions d'appeller à cette view et celle de stockage (avec resuest) des donnée provenant de la view (post)**
        ```
            use Illuminate\Http\Request;
            
            class Formulaire extends Controller
                {
                    public function creer(){
                        return view('Formulaire.Formulaire');
                    }

                    public function stockData(Request $request){
                        dd($request -> all()); // pour voir le retour 
                    }

                }
        ```
3. Creation des routes pour les deux fonction 
    ```
        Route::get('/formulaire',[Formulaire::class,'creer'])->name('creer');
        Route::post('/formulaire',[Formulaire::class,'stockData'])-> name('stockData');
    ```
4. On test le retour de la fonction **dd()**
5. Mettre en place le model et  la migration pour la comunication avec la base de donnée. 
        - Voir document [ORMEloquant](https://github.com/AlbertNd/Notes/blob/main/ORMEloquentInteractionBD.md)
6. Dans le **controlleur** 
    1. completer la fonction ***stockage()***
    2. redirection de l'utilisateur *(vers la page que l'ont veut)* avec un message (methode ***with()***) 

        ```
             use Illuminate\Http\Request;
            
            class Formulaire extends Controller
                {
                    public function creer(){
                        return view('Formulaire.Formulaire');
                    }

                    public function stockData(Request $request){
                        $variable = le_model::creat([
                            'nom' => $request ->input('nom'),
                            'titre' => $request ->input('titre'),
                            'message' => $request ->input('message')
                        ]);
                    return redirect()->route('lien souhaité')->with('succes','le message à afficher')
                    }

                }
        ```
    3. le message de succes dans la view:
        ```
            @if(session('success'))
             <div class="alert">
                {{session('success')}}
            </div>
            @endif     
        ```
7. #### Mise en place d'un sisteme de validation des données 
    1. `php artisan make:request nom_de_la_request`
        - Voir document [validation](https://github.com/AlbertNd/Notes/blob/main/validation.md)
    2. On reecris la plus proprement la fonction stokage()
        ```
             public function stockData(nom_de_la_request $request){
                        $variable = le_model::create($request -> validated());
                    return redirect()->route('lien souhaité')->with('succes','le message à afficher')
                    }
        ```
8. affichage d'erreur de validation sur le formulaire lorsque l'utilisateur ne respecte pas les regle de validation
```
    <div>
         <input type="text" name="nom" id="" class="border border-gray-300 py-5">
         @error('le name de l'input')
            {{message}}
        @enderror
    </div>
```

#### Modification 
1. Creation des routes ***(get et post)***
    - `Route::get('/{id_de_l'article}/modifier',[Formulaire::class,'A_modifier'])->name('A_modifier');`
    - `Route::get('/{id_de_l'article}/modifier',[Formulaire::class,'modifier'])->name('modifier');`
2. Dans le controlleur 
```
    public function A_modifier(Article $article) // dans l'argu de la fonction voir model binding (pour la recuperation de l'article voulu)
    {
        return view('dossier.modif',[
            'article' => $article
        ]);
    }
    public function modifier(nom_de_la_request $request, nom_de_la_request $request){
        $variable = le_model::update($request -> validated);
        return redirect()->route('lien souhaité')->with('succes','le message à afficher')
        
        }
```
3. Creation de la vue d'édition
    - reprende le code du formulaire 
        1. change de titre : @section('titre')
        2. pour la `value="{{old('titre',$articles->titre)}}"` on recupere le titre pour le mettre dans l'argument à afficher 
    - **Attention**  si il y a des champs pour lequel il y a une vallidation unique :
        - mettre dans les regles de validation 
            - Rule::unique('le parametre d'entré')->ignore($this->route()->parametre('parametre de modification')) 

#### Creation d'une vue de formulaire commune
1. Creation d'un formlaire commun dans un chier ex: *form.blade.php*
2. On y rajoute tout le code du formulaire souhaité pour les deux view ***(creation et modification)***
3. Faire de **@include('')** dans les deux view à la palce de la ou devrait etre le code du formulaire 
    ```
        @extends('welcome')

        @section('titre','creation')

        @section('Formulaire')
            @include('')
        @endsection
    ```
    - **On change de titre en fonction de la view
