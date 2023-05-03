# Relation menu to many Utilisateur Categorie

[Documentation laravel 10](https://laravel.com/docs/10.x/eloquent-relationships#many-to-many)

- *Ici, on suppose que un utilisateur peux avoir plusieus catégories et que dans une categorie il peux y avoir plusieurs utilisateurs*
    1. On a donc une table **Utilisateurs** avec un id et ses autres colonnes 
    2. On a une table **Categories** avec un id et la colonne du nom de chaque catégorie 
    3. On a une table intermediaire **categorie_utilisateur**:
        - La nommination de cette table doit etre faite en minuscule et dans les deux nom dans un ordre alphabetique. 
        - La nomination des clef etrengere doit etre en minuscul et avoir un contrainte
        - **NB :** surtout pas au pluriel
    4. **NB:** ***Il n'y a pas de clef etrangere dans les deux tables Utilisateur et categorie***

1. **Creation de la table Utilisateurs** 
    1. Création du model et de la migration 
        - `php artisan make:model Utilisateur -m`
    2. Dans la migration ainsi creer
        - On rajoute les colonnes que l'on souhaite 
    3. Dans le model Utilisateur 
        1. On configure les protection fillable avec les champs de la table 
            - `protected $fillable = ['nom','prenom','.....']`
        2. On y configure la foction d'appelle pour l'autre table *(table Categorie)*
            -  On met en place la fontion ***BelongsToMany***
                ```
                    class Utilisateur extends Model
                    {
                        use HasFactory; 

                        protected $fillable = ['nom','prenom','....']
                        
                        public function Categorie(): BelongsToMany
                        {
                            return $this->belongsToMany(Categorie::class,'categorie_utilisateur','utilisateur_id','categorie_id');
                        }
                    }
                ```
    4. Creation d'un request et mettre en place les validations 
        - `php artisan make:request UtilisateurRequest`
        - ***En fonction des cas il est recommande de mettre aussi le renvois de l'input consernant les categorie et en provenance du formulaire mais en nullable***


2. **Creation de la table Categorie** 
    1. Création du model et de la migration 
        - `php artisan make:model Categorie -m`
    2. Dans la migration ainsi creer
        - On rejoute les colonnes que l'on souhaite *(Ici c'est juste le nom)* 
    3. Dans le model Categorie 
        1. On configure les protection fillable avec les champs de la table 
            - `protected $fillable = ['nom']`
        2. On y configure la foction d'appelle pour l'autre table *(table Utilisateur)*
            - On met en place la fontion ***BelongsToMany***
                ```
                    class Categorie extends Model
                    {
                        use HasFactory; 

                        protected $fillable = ['nom']
                        
                        public function Utilisateur(): BelongsToMany
                        {
                            return $this->belongsToMany(Utilisateur::class,'categorie_utilisateur','utilisateur_id','categorie_id');
                        }
                    }
                ```
        - ***Pas besoin d'un request entant donné que on les tire de la bd et dans le request de l'autre table***

3. **Creation de la table intermediaire** 
    1. ***On crée juste la table de migration et pas de model***
        - `php artisan make:migration create_categorie_utilisateur_table`
    2. Dans la table créer
        - On rajoute les clef étrangere des deux tables 
            - `$table -> foreignId('categorie_id')-> constrained() -> onDelete('cascade');`
            - `$table -> foreignId('ctilisateur_id')-> constrained() -> onDelete('cascade');`
4. **On lance la migration** 
    1. `php artisan migrate`
5. **Vérification dans la base de donnée**
6. On insert les donnée necessaire *(les cotégorie)* soit directement dans la base de donnée ou alors via les controller d'une des view *(voir dans relations one to many)* 
    -   ```
            Categories :: create([
                'Categorie' => 'B'
            ]);
            Categories:: create([
                'Categorie' => 'C'
            ]);
        ```
7. **dans le controlleur d'enregistrement** 
    1. Enregistrement dans la table des utilisateurs 
    2. Enregistrement dans la table intermediaire via les les catégorie 
    3. le message de redirection 

    ```
        public function enregistrerUtilisateur(UtilisateurRequest request){

            $utilisateur = Utilisteurs::create($request -> validated());
            $categorie = collect($request->input(key:'Categorie'));

            $Utilisateur ->Categorie()->sync($categorie); 

            return redirect()->route('...')->with('success',"Enregistrement reussis");       
        }  
    ```
8. **Dans la view du formulaire**  
    1. Dans le controller 
        - Injection des données requis dans la view 
        ```
            public function AffichForm(){
                return view('la view',[
                    'Categories'=> Categories::select('id','la colonne')-> get();
                ]);
            }
        ```
    2. Dans la view 
        - affichage des données 
        ```
            @foreach($Categories as $categorie)
                
                <div>
                    <input type="checkbox" name="Categorie[{{$categorie -> id}}]" id="" value="{{$categorie -> id}}" >
                    <label for="" class="text-xs font-thin font-serif font-extraligh uppercase text-teal-800">{{$categorie -> CategoriePermis}}</label>
                </div>
                
            @endforeach

        ```
9. **Affiche des information conjointe**