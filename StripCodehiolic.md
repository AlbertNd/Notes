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
        3. documentation stripe
            - Paiements en ligne
                - Accepter un paiement
                    - Dans la page *(voir Configurer Stripe)*
        4. Récuperation de la clef secrete
            - page d'acceuille sur stripe. 
    - **Le contenu de la fonction** [Voir dans la doc stripe](https://stripe.com/docs/payments/accept-a-payment)
        1. appel au fichier autoload.php 
            - `require '../vendor/autoload.php';` *(les ../ en fonction du placement du fichier)*
        2. Création d'un nouveau client 
            - `$stripe = new \Stripe\StripeClient(env('clefSecrete'));`
        3. Selection des l'ensemble des tous les produits de la base de donnée. 
            - `$produits = Produit::all();`
        4. Definition d'une variable tableau vide et qui contiendra l'ensemble des informations sur les produit 
            - `$lineItems = [];`
        5. Definition d'un variable qui va sommer l'ensemble des prix *(initialisé a zéro)*
            - `$prixTotal = 0`
        6. Boucle sur l'ensemble des produits de la base de donnée et les mettres dans la variable **lineItems** 
            ```
                foreach($produits as $produit){
                $prixTotal = $produit -> prix;
                $lineItems[] =[
                        'price_data' => [
                          'currency' => 'usd',
                          'product_data' => [
                            'name' => $produit -> nom,
                            'images' => [$produit -> image]
                          ],
                          'unit_amount' => $produit -> prix *100,
                        ],
                        'quantity' => 1, // ici ce sont les quantité par produits si on met 2 chaque prix sera doublé
                    ];
                }
            ```
        7. Creation de la checkout session 
            - La configuration des urls des message de success et cancel 
        ```
                $checkout_session = $stripe->checkout->sessions->create([
                'line_items' => $lineItems,
                'mode' => 'payment',
                'success_url' => route('success',[],true),
                'cancel_url' => route('cancel',[],true),
            ]);
        ```
        8. Insertion des données de retour session dans la table des commande de la base de donnée 
        ```
            $commande = new Commande();
            $commande -> status = 'unpaid';
            $commande -> prixTotal =  $prixTotal;
            $commande -> session_id = $checkout_session->id;
            $commande -> save();
        ```
        9. Definition de la redirection 
            - `return redirect($checkout_session->url);`
        10. le recap de la fonction 
        ```
             public function checkout(){
                $stripe = new StripeClient(env('SecretApiKey'));

                // selection de tous les produits 

                $produits = Produit::all();

                    $lineItems = [];
                    $prixTotal = 0;

                    foreach($produits as $produit){
                        $prixTotal = $produit -> prix;
                        $lineItems[] =[
                                'price_data' => [
                                'currency' => 'usd',
                                'product_data' => [
                                    'name' => $produit -> nom,
                                    'images' => [$produit -> image]
                                ],
                                'unit_amount' => $produit -> prix *100,
                                ],
                                'quantity' => 1,
                        ];
                    }

                $checkout_session = $stripe->checkout->sessions->create([
                    'line_items' => $lineItems,
                    'mode' => 'payment',
                    'success_url' => route('success',[],true),
                    'cancel_url' => route('cancel',[],true),
                ]);
                
                $commande = new Commande();
                $commande -> status = 'unpaid';
                $commande -> prixTotal =  $prixTotal;
                $commande -> session_id = $checkout_session->id;
                $commande -> save();


                return redirect($checkout_session->url);
            }
        ```
5. Creation des fonctions de success 

    ```
        public function success(){
            return view('Checkout.success')
        }
    ```
    - a ce stade , ne sais pas dire d'ou vient cette redirection dans le retour coté client. de plus, tout le monde peut aller sur cette url et avoir le message contenu dans la view 
        - Il y a un possibilité de personnaliser le message de success [voir doc](https://stripe.com/docs/payments/checkout/custom-success-page)
            - Modifier l'URL de confirmation de paiement : 
                - `'success_url' => "http://yoursite.com/order/success?session_id={CHECKOUT_SESSION_ID}",`
        - **Dans la $checkout_session :**
            - `'success_url' => route('success',[],true)."?session_id={CHECKOUT_SESSION_ID}",`
                - *(à chaque fois que elle genere une session et que il diriger l'utilisateur sur la page de success prendre la ***CHECKOUT_SESSION_ID*** et passe la dans l'url)*
        - **On modifier la fonction qui retourne la page de succes**
            - [voir doc](https://stripe.com/docs/payments/checkout/custom-success-page)
                - Créer votre page de confirmation de paiement: 
                    - `$session = $stripe->checkout->sessions->retrieve($_GET['session_id']);`
                    - `$customer = $stripe->customers->retrieve($session->customer);`
        ```
            public function success(Request $request){
            
            Stripe::setApikey(env('SecretApiKey'));

            $sessionId= $request -> get('session.id');

            $session = $stripe->checkout->sessions->retrieve($sessionId);
            $customer = $stripe->customers->retrieve($session->customer);

            return view('Checkout.success')
            }
        ```

