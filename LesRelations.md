# Les relations 

[Documentation](https://laravel.com/docs/5.4/eloquent-relationships)

1. Cas de creation des catégories 
    1. Creation du model et le migration des categories 
        - `php artisan make:modle nom_du_model -m`
    2. se rendre dans la migration 
        - On rajoute un champs dans la table pour le nom des catégories **NomCategorie**
2. Associations des categorie aux article. *(On part du principe ou un article ne peut avoir qu'une seule catégorie)*
    - On est dans une relation ***1-N*** 
        - **Il faut une clef etrangere dans la table des article = Catégorie_id**
    1. ***Partant du principe ou la table article à deja ete créee, il faut modifier la table article***
        - Ceci peut etre fait dans la migration de la Catégorie `schema::table(...)`
        ```
            schema::create('categorie',function(Blueprint $table){
                ....
            });

            schema::table('article', function(Blueprint $table){
                $table -> dropForeignIdFor(\App\Models\Categories::class)->contrained()->cascadeOnDelete();
            })
            .
            .
            .
            public function down(): void
            {
                schema:: .......('categorie');

                schema::table('articles', function(bleuprint $table){
                    $table -> dropForeignIdFor(\App\Models\Categories::class);
                });
            }
        ```
        - La fonction `schema::table(...)` permet de recuperer une table et de la mofier elle prend les meme paramettre que celle de la fonction ***create()*** au dessus mais avec le nom de la table souhaité
        - La fonction `foreignIdFor()` en lui passant le model, va permettre de rajouter une clef etranger dans la tables des articles, on lui rajoute une contrainte ***constrained()*** et ***cascadeOnDelete()*** 
    2. On lance la migration 
        - Faire un **rollback** si on a deja des choses dans la table articles ou alors se rendre dans la base de donnée et supprimer tout puis relance la migrations
            - *Ou rajouter une fonction ***nullable()*** pour affirmer que les article deja present n'ont pas besoin d'avoir des categorie* 
    3. Verification dans la base de donnée pour voir si tout est bon 
        1. Rendre le champs categorie fillable
            - Se rendre dans le model ctegorie 
                -  `$fillable = ['NomCategorie']`
        2. Au niveau du model Article (Pour pouvoir recuperer la categorie associer à l'article)
            - Creation d'une fonction qui return la fonction ***belongsTo()*** permattant de dire que l'article appartien à une categorie et en paramettre on met le model de la categorie 
            ```
                public function categories(){
                    return $this-> belongsTo(\App\Models\categorie::class);
                }
            ```
            - Si le nommage est propre laravel devinera directement la clef etrangere et les autre informations 
            - Pour tester la recueration de la categorie 
            ```
                $article = Article :: find(1);
                dd($article-> categorie->NomCategorie);
            ```