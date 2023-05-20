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
        ```
            public function AfficheProduit(){
                $produits = Produit::all();

                return view('Produits.Articles',compact('produits'));
            }
        ```
    2. Dans la view 
        ```
            @extends('welcome')
            @section('Articles')

            <div class="container mx-auto p-10">
                <div class="flex space-x-8">
                    @foreach($produits as $produit)
                    <div>
                        <img src="{{$produit->image}}" alt="">
                        <h5 class="font-light text-lg">{{ $produit->nom}}</h5>
                        <p>
                            {{$produit -> prix}}
                        </p>
                    </div>
                    @endforeach
                </div>
                <div>
                    <form action="{{route('checkout')}}" method="post">
                        @csrf
                        <button class="px-2 py2 border border-gray-300">Checkout</button>
                    </form>
                </div>
            </div>

            @endsection
        ```
    3. La route pour l'etendrela view dans la view de base 
        - `Route::get('/',[ProduitController::class,'AfficheProduit']);`
    4. On check si tout fonctionne bien dans le view principale 

4. Creation du checkout 
    - Dans le ***ProduitController()*** 
        1. la fonction  ***checkout()***
            - L'acces à cette fonction doit etre une methode **post**
            - la route : `Route::post('/checkout',[[ProduitController::class,'checkout'])->name('checkout');`
        2. La generation de la session Stripe
            1. Installation du package Stripe 
                - `composer require stripe/stripe-php`
                    - Vérification dans le fichier `composer.json`
        3. documentation strip
            - Paiements en ligne
                - Accepter un paiement
                    - Dans la page *(voir Configurer Stripe)*
        4. Récuperation de la clef secrete