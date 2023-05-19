# Developpement d'une solution de paiement a l'aide de [strip](https://stripe.com/fr-be) 

1. [Configuration d'un systeme de d'authentification](https://github.com/AlbertNd/Notes/blob/main/Authentification.md)
    - *(Je pense que cella va permettre d'avoir une session mais ca reste a confirmer)*

2. **S'authentifier avec un compte créer**
    - *(Il est important d'etre autentifier avant de commencer la canfiguration de stripe)*
3. **Connection au compte [stripe](https://stripe.com/fr-be)**
    1. Création d'un produit ***(Se mettre en mode test)***
        - Produit => ajouter un produit 
            - configuration du produit 
    2. [Sur la page d'**Accueil**](https://dashboard.stripe.com/test/dashboard) 
        - Afficher la documentation 
    3. [Dans la documentation](https://stripe.com/docs/payments?payments=popular)
        1. Stripe Checkout UI
            - [Démarrage rapide](https://stripe.com/docs/checkout/quickstart)
4. **Configuration du serveur** 
    1. `composer require stripe/strip-php`
        - Vérification dans le fichier `composer.json`
            - `"stripe/stripe-php": "^10.13"`
    2. Création d'un controller StripeController
        - `php artisan make:controller StripeController`
    3. Dans le controller StripeController 
        1. Création de la fonction ***AfficheProduit()***
            - Elle retourne une view avec:
                - le prix definis dans la création du produit dans strip 
                - un formulaire 
                    - method ="post"
                    - @csrf 
                    - boutton submit
            ```
                @extends('welcome')

                @section('abonement')
                <div class="container mx-auto flex justify-center p-10">
                    <div>
                        <section>
                            <div class="product">
                                <img src="https://i.imgur.com/EHyR2nP.png" alt="The cover of Stubborn Attachments" />
                                <div class="description">
                                    <h3>Stubborn Attachments</h3>
                                    <h5>$20.00</h5>
                                </div>
                            </div>
                            <form action="" method="post">
                                @csrf
                                <button type="submit" id="checkout-button">Checkout</button>
                            </form>
                        </section>

                    </div>
                </div>
                @endsection
            ```
        2. Création d'une fonction ***postStripe()***
            - les arguments de la fonction `(Request $request)`
            - le contenu de la fonction
                1. appel au fichier ***autoload.php***
                2. definition de la clef secrete de Strip *(mettre dans le fichier .env)*
                3. Definir le type de l'entete *(Voir comment le faire avec laravel)*
                4. Definition du domaine *(mettre dans le fichier .env)*
                5. Définition du prix *(mettre dans le fichier .env)*
                5. Création de la session checkout
                    - *Voir les arguments à configurer et comment en fonction des besoins*
                6. Rediriger vers l'url de paiement. 
        ```
           public function postStrip(Request $request)
            {
                require_once '../vendor/autoload.php';

                Stripe::setApiKey(env('STRIPE_SECRET'));

                header('Content-Type: application/json');

                $Domaine = env('DOMAIN');

                $prix = en('Prix');

                $checkout_session = \Stripe\Checkout\Session::create([
                    'line_items' => [[
                        # Provide the exact Price ID (e.g. pr_1234) of the product you want to sell
                        'price' => $prix,
                        'quantity' => 1,
                    ]],
                    'mode' => 'payment',
                    'success_url' => $Domaine . 'success',
                    'cancel_url' => $Domaine . 'cancel',
                    'automatic_tax' => [
                        'enabled' => true,
                    ],
                ]);
            
                return redirect($checkout_session->url);
            } 
        ``` 
        3. Création d'une fonction ***TransactionSuccess()**
            - Elle retourne une view avec un message de transaction reussis
                - *voir ptre comment l'ameliorer* 
        4. Création d'une fonction ***TransactionCancel()***
            - Elle retourne une view avec un message de transaction annuler
                - *voir ptre comment l'ameliorer* 
5. creation des routes 
    1. affichage de des produit 
        - `Route::get('/produit',[StripeController::class,'AfficheProduit'])->name('produit');`
    2. recuperation des infos du produit 
        - `Route::post('/produit',[StripeController::class,'postStripe']);`
    3. affiche de message de succes ou d'annulation 
        - `Route::get('/success',[StripeController::class,'succeTransaction'])->name('success');`
        - `Route::get('/cancel',[StripeController::class,'cancelTransaction'])->name('cancel');`
6. **Configuration du webhook**
    1. Developpeurs 
        - webhooks
            - Ajouter un endpoint ou si deja une on ajouter un écouteur local 
    2. Suivre la procedure 
        - pas ou blier que avec **Linux** : ***./stripe login*** 