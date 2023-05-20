1. Création et connection d'une base de données 
    - Lancer la migration 
2. Création des model et migration pour les produits et les commandes
    1. Produit 
        1. On va créer directement : une migration , un factory, un seeder et un controller 
            - `php artisan make:model Produit -mfsc`
        2. Dans la migration :
            ```
                public function up(): void
                {
                    Schema::create('produits', function (Blueprint $table) {
                        $table->id();
                        $table -> string('nom', 255);
                        $table -> decimal('prix',6,2);
                        $table -> string('image',1024)->nullable();
                        $table->timestamps();
                    });
                }
            ```
        3. Création des produits *simulés par laravel*
            - Dans ***database\factories\ProduitFactory.php***  
                ```
                     public function definition(): array
                    {
                        return [
                            'nom' => fake() -> text,
                            'prix' => fake() -> randomFloat(2,100,1000),
                            'image' => fake() -> imageUrl
                        ];
                    }
                ``` 
        4. Seeder la table de produit avec *(3 produits)*
            - Dans ***database\seeders\ProduitSeeder.php***
                ```
                    public function run(): void
                    {
                        Produit::factory(3)->create();
                    }
                ```  
        5. Dans ***database\seeders\DatabaseSeeder.php***
            ```
                public function run(): void
                {
                    
                    $this->call([
                        ProduitSeeder::class
                    ]);
                    
                }
            ```
        6. Lancer la migration `php artisan migrate`
        7. Lancer le seeder `php artisan db:seed`

    2. Commande 
        - `php artisan make:model Commande -m`
        ```
            public function up(): void
            {
                Schema::create('commandes', function (Blueprint $table) {
                    $table->id();
                    $table -> string('status');
                    $table -> decimal('prix-total',6,2);
                    $table -> string('session_id');
                    $table->timestamps();
                });
            }
        ```
    3. lancer la migration 

3. L'affichage des produit dans une view 
    1. Dans le ***ProduitController()*** 
    