# Relation menu to many Utilisateur Categorie

[Documentation laravel 10](https://laravel.com/docs/10.x/eloquent-relationships#many-to-many)

- *Ici, on suppose que un utilisateur peux avoir plusieus catégories et que dans une categorie il peux y avoir plusieurs utilisateurs*
    1. On a donc une table **Utilisateurs** avec un id et et ses autres colonnes 
    2. On a une table **Categories** avec un id et la colonne du nom de chaque catégorie 
    3. On a une table intermediaire **Categorie_Utilisateur** *(La nomenclature veut que l'appellation soit dans l'ordre alphabetique des des nom des tables associées)* qui relie les utilisateurs et les catégories
        - Cette table va contenir un ***Utilisateur_id*** et un ***Categorie_id*** et pas d'**Id** pour cette table

    **NB:** ***Il n'y a pas de clef etrangere dans les deux tables Utilisateur et categorie***
1. Creation de la table Utilisateurs 
    1. Création du model et de la migration 
        - `php artisan make:model Utilisateur -m`
    2. Dans la migration ainsi creer
        - On rejoute les colonnes que l'on souhaite 
    3. Dans le model Utilisateur 
        1. On configure les protection fillable avec les champs de la table 
            - `protected $fillable = ['nom','prenom','.....']`
        2. On met en place la fontion ***BelongsToMany***
            -   ```
                    class Utilisateur extends Model
                    {
                        use HasFactory; 

                        protected $fillable = ['nom','prenom','....']
                        
                        public function roles(): BelongsToMany
                        {
                            return $this->belongsToMany(Categorie::class,'Categorie_Utilisateur','Utilisateur_id','Categorie_id');
                        }
                    }
                ```
        3. Creation d'un request et mettre en place les validations
1. Creation de la table Categorie 
    1. Création du model et de la migration 
        - `php artisan make:model Categorie -m`
    2. Dans la migration ainsi creer
        - On rejoute les colonnes que l'on souhaite *(Ici c'est juste le nom)* 
    3. Dans le model Categorie 
        1. On configure les protection fillable avec les champs de la table 
            - `protected $fillable = ['nom']`
        2. On met en place la fontion ***BelongsToMany***
            -   ```
                    class Categorie extends Model
                    {
                        use HasFactory; 

                        protected $fillable = ['nom']
                        
                        public function Utilisateur(): BelongsToMany
                        {
                            return $this->belongsToMany(Utilisateur::class,'Categorie_Utilisateur','Utilisateur_id','Categorie_id');
                        }
                    }
                ```
        3. Creation d'un request et mettre en place les validations
2. Creation de la table intermediaire 
    1. ***On crée juste la migration et pas de model***
        - `php artisan make:migration Categorie_Utilisateur`
    2. Dans la migration créer
        - On rajoute les clef étrangere des deux tables 
            - `$table -> foreignId('Categorie_id')-> constrained() -> onDelete('cascade');`
            - `$table -> foreignId('Utilisateur_id')-> constrained() -> onDelete('cascade');`
3. On fait la migration 
    1. `php artisan migrate`
4. Vérification dans la base de donnée
5. On insert les donnée necessaire *(les cotégorie)* soit directement dans la base de donnée ou alors via les controller d'une des view *(voir dans relations one to many)* 

6. Recuperation des donnée 
```
    use App\Models\User;
 
    $user = User::find(1);
    
    foreach ($user->roles as $role) {
        echo $role->pivot->created_at;
    }
```
 
