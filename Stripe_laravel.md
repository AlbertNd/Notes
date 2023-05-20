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
    1. `composer require stripe/stripe-php`
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
                    - **@csrf** *(à ne sourtout pas oublier)*
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
    1. Dans le controller 
        - Création d'une fonction ***webhok()***
            - *(à ce stade elle est vide, car elle va permettre d'alimenter une table de la db, elle sert juste de test)*
    2. Creation de la route 
        - `Route::post('/webhook',[StripeController::class,'webhook']);`
    2. ***Desactiver la verification du*** **csrf** ***le liens*** **/webhook**
        1. Dans le fichier ***app\Http\Middleware\VerifyCsrfToken.php***
        ```
            protected $except = [
                /webhook
            ];
        ```
    3. Developpeurs 
        - webhooks
            - *Ajouter un endpoint ou si deja une on ajouter un écouteur local* 
        - Click sur Telecharger l'interface de commande 
            - Choix du moteur d'exploitation 
                - Pour ma part **linux** 
                    1. telechargement du lien et decopression `tar -xvf le_fichier`
                    2. Aller dans le fichier decompressé et ***./stripe login***
                        - `Press Enter to open the browser or visit https://dashboard.stripe.com/stripecli/confirm_auth?t=JEM4KyWiS0CGMGYaFKooHitEMdY9p816 (^C to quit)` 
                    3. Click sur enter 
                        - Une page web va s'ouvrir avec un message d'authorisation *on autorise l'accès*
                            - Verifier si ce type de code `relish-dazzle-awe-aver` sur la page correspond a celui qui est dans l'invité de commande
                                - si oui : *on autorise l'accès* 
                        - Dans le terminal : `Done! The Stripe CLI is configured for artbeltrans.be with account id acct_1N71ORK1WbMuURXh` *(avec un id du compte)* et le message ci dessous:
                            - `Please note: this key will expire after 90 days, at which point you'll need to re-authenticate.`
    4. Transferer les evenements au webhook 
        - Dans l'invité de commande dans le dossier de telechargement il y a toujours le decompresse de stripe: 
            - En fonction de notre ecouteur local et du lien webhook:
                - `./stripe listen --forward-to localhost:8000/webhook`
                - Click enter 
                    - la signature secrete: `> Ready! You are using Stripe API Version [2022-11-15]. Your webhook signing secret is whsec_be4ced880ec7a0db0a35f61984b97e3fdd47108589ddcc484320a42aecc7a6c4 (^C to quit)`
                        - *on aura besoin de cette clef de signature*
                    - Vérifier dans stripe si c'est au vert 
    5. Le declencheur d'evenement: 
        - Copier `stripe trigger payment_intent.succeeded`
        - Au mieux: 
            - Ouvrir une autre terminal et se rendre dans le dossier de decompression 
        - dans le nouvel terminal:
            - `./stripe trigger payment_intent.succeeded`
            - Click Enter 
                - Aller voir l'output sur l'autre terminal *(parfois il faut clicker sur Enter)*
7. Recuperation des données de retour et stockage dans la db 
    1. Creation de la table de recupération des données dans la db 
        - Les champs que je trouve utiles:
            1. **id_stripe** *Il s'agit de l'id de l'object*
            2. **nom**
            3. **email**
            4. **montant**
            5. **devise**
            6. **type**
        - création du model de la table et de sa migration 
            - `php artisan make:model stripeData -m`
        - Dansla migration 
        ```
             public function up(): void
                {
                    Schema::create('stripe_data', function (Blueprint $table) {
                        $table->id();
                        $table->string('id_stripe'); 
                        $table->string('nom');
                        $table->string('email');
                        $table->integer('montant');
                        $table->string('devise');
                        $table->string('type');
                        $table->timestamps();
                    });
                }
        ```
        - Dans le model
        - Dans la methode webhook() du controller 
            - Appel du des request
            - Condition de vérification du type **payment_intent.created** *(Etant donnée que c'est celle qu'il crée en dernier)*
            - un ***try{}*** and ***catch(\Exeption $e){ return $e->getMessage();}*** 
        ```
             public function webhook(Request $request){
                if($request -> type === 'payment_intent.created'){
                    try{

                        stripeData::create([
                        'id_stripe' => $request -> data['object']['id'],
                        'nom' => $request -> data['object']['name'],
                        'email' => $request -> data['object']['receipt_email'],
                        'montant' => $request -> data['object']['amount'],
                        'devise' => $request -> data['object']['currency'],
                        'type'=> $request -> type,
                        ]);

                    }catch(\Exception $e){
                        return $e->getMessage();
                    }
                }

                return 'ok';
            }
        ```