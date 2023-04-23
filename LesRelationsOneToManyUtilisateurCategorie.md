# Les relations One to Many utilisateurs-categories

[Documentation](https://laravel.com/docs/5.4/eloquent-relationships)

#### Le cas 
```
    On part du principe ou il y a une tables utilisateurs et ensuite une table catégories 
        - On a deux categories : Chauffeurs et Autres 
        - un utilisateur ne peut apartenir que a une catégories 
            => C'est une relation "one to many" (Càd un catégorie peut avoir plusieurs utilisateurs si Catégorie chaffeur plusieurs chauffeur et si categorie autre plusieur utilisateur autre)
                => Il faut donc une clef etrangere dans la table utilisateur
```
1. Table utilisateur
    1. Formulaire de la table
        - @csrf
        - message erreur
        - boucle (voir plus loin dans dans les notes)
    2. Connection database
    3. model et migration
        - Dans la migration de la table vaut mieux ne pas mettre la colonne que l'on souhaite avoir comme clef etrengere
        - Dans le model on `protectd $fillable = ['les colonnes']` (pas oublier de rendre fillable celle de la table catégorie une fois qu'elle sera créer)
    4. fichier request et validation
        - `php artisan make:request UtilisateurReq` (attention au choi du nom pour ne pas se melanger apres)
        - dans le fichier 
            - ***return true***
            - Les regles de validation (pas oublier de mettre une regle pour la colonne de la table categorie par apres)
    5. Controller et fonction
        - Fonction d'affichge de la view
        - Fonction de stockage des données (avec la redirection)
            - avec l'argument de la classe request
            - un dd() pour voir le retour (en fonction de l'avancement on le met en commentaire)
            - appel du model et de sa fonction create() pour enregistre les données dans la base de donnée 
                - `$variable = Utilisateur(c'est le model)::create($request ->validated());` 
            - la redirection 
                - `return redirect()->route('.....')->with('success',"le message en cas de success");
        - retour dans le formulaire pour l'affichage des message de succes et d'erreur en fonction des regles de validation; 
            - success : `@if(session('success')) {{session('success')}} @endif`
            - erreur : `@error('nom de l'input') {{$message}} @enderror`
    6. Les routes 
        - Route d'affichage ***get()***
        - Route de stockage ***post()***
    7. Test de l'insertion, si message success ok voir dans la base de donnée
    8. Affichage des données dans la view 
        - dans la fonction d'affichage `return view('la view',['Utilisateur' => le model::all()]);`
            - all() : pour tous les données 
            - select('...','...')->get() : pour spécifier les donnée souhaité
        - dans la view `@foreach($Utilisateur as $utilisateur) {{ $utilisateur -> nom de la colonne}} @endforeach`
            - **NB** ***Bien mettre dans la bonne vieu***
    

2. La table Categorie 
    1. model et migration
        - Dans la migration on ajouter la colonnes du nom des categories
        - Dans cette meme migration : Creation de la clef etrangere et ses contraintes dans la table des utilisateur ***(Bien verifier l'annatation de la table  dans la base de donnée)***
            -   ```
                    Schema::table('utilisateur', function(Blueprint $table)){
                        $table -> foreignIdFor(Categorie::class)->constrained()->cascadeOnDelete();
                    }
                ```
            - on peut mettre un fonction de suppression pour au cas ou il y a n rollback à faire *(mais ca donne a chaque fois des erreurs donc c'est a revoir)*
                - Dans la fonction **down()** juste en dessus de celle qui supprime la table catégorie
                ```
                    Schema::table('utilisateur', function(Blueprint $table){
                        $table -> dropForeignIdFor(Categorie::class);
                     });
                ```
        - Dans le model, on rend la variable `protected $fillable =['...'];`
        - Dans  **le model de la table utilisateur** ajout d'une fonction belogsTo() 
            - Juste en dessous de la protectd $fillable 
                ```
                    public function Categorie(){
                        return $this->belongsTo(le model categorie ::class);
                    }
                ```
        - On lance la migration. 
            - **NB** *il y aura probablement des erreur lier au relation dans la base de donnée. soit suivre les instruction soit supprimer les table et relancer (j'ai fit un roll back qui a effacer la table utilisateur ensuite j'ai efface celle de de categorie et ai relance la migration)*
        - Vérifier si la clef etrengere à bien ete créer dans la table utilisateur
    2. Insertion des catégories dans la table de categories 
        - Dans le controller, prendre une fonction qui renvois a une view et de preference celle qui sort la liste des utilisateur et mettre temporairement la fonction creat du model Categorie
        ```
            le model :: create([
                'NomCategorie' => 'Chauffeurs'
            ]);
            le model :: create([
                'NomCategorie' => 'Autres'
            ]);
        ``` 
    3. se rendre sur le lien pour lancer la fonction et aller verifier dans la table categorie de la base de donnée si l'insertion est bien ok *(si ok on commanter les fonction de creation car on risque d'en avoir besoin)*
    4. Dans le fichier request , rendre la colonne categorie_id fillable et lui attribuer une regles de telle sorte que elle soit requise et de plus il faut sont id exiter dans la table categorie
        - `'categorie_id' => ['required','exists:categories,id']` 
    5. Dans le model Utilisateur mettre rajouter la conne categorie_id dans les fillable.
    6. Appel aux id de la table categorie dans la selection du formulaire
        - Dans la fonction affichage du fomulaire, faire introduire les id et le Nom de categorie 
        ```
            return view('la view',[
                'Utilisateur' => Categorie::select('id','NomCategorie')->get()
            ]);
        ``` 
    7. Dans la view à la section des selecte 
    ```
        <select name="categories_id" id="">

                <option value="">Choisissez votre status</option>
                
                    @foreach($Utilisateur as $utilisateur)
                    
                        <option value="{{$utilisateur -> id}}"> 
                            
                            {{$utilisateur -> NomCategorie}}
                        
                        </option>
                    
                    @endforeach

        </select>
    ```
