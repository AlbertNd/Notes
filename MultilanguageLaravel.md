## Localisation 

[Documentation](https://laravel.com/docs/10.x/localization)

##### Nb surout ne pas oubier de faire attention au cas de d'affichage responsive

1. Création du repertoir **Lang**
    - `php artisan lan:publish`
2. Dans le repertoire **lang**
    1. Création du fichier **.json** de la lanque souhaité 
        - exemple : pour l'anglais **en.json**
            -   ```
                    {
                        "du francais à traduire":"la traduction en anglais",
                        ...
                    }
                ```
    2. Sachant que la langue par defaut est l'anglais.
        - Faire les manipulation necessaire: 
            1. Dans le fichier **config/app.php** 
                - Si alangue par defaut est le francais 
                    - Voir la ligne 85 'loocale' => 'en' la mettre en 'fr'
3. faire un teste avec les routeur 
    -   ```
            Route::get('/test', function (){
                dd(App::getLocale());
            });
        ```
        - Dans l'urle de la page on /test : ce qui doit renvoyer le local configurer càd ici *fr*
    - Si le renvois est correcte, on test les setting de locale *en*:
        -   ```
                Route::get('/test', fonction (){
                    app::setLocale('en');
                    if(App::isLocale('en')){
                         dd(App::getLocale());
                    }
                   
                }); 
            ```
            - Dans l'urle de la page on /test : ce qui doit renvoyer le local configurer càd ici *en*
 
4. Création de l'echangeur de langue
    1. Creation d'un **groupe de routage**, et d'une **redirection de l'URL**, afin de permettre de partager sur une nombre de routage sans avoir à definir les attributs sur chaque routage individuellement. 
        ```
            Route::redirect('/','/en');

            Route::group(['prefix' => '{language}'],        
                function (){

                Route::get('/', function () {
                    return view('welcome');
                });

            });
        ```
        - Mon astuce 
        ```
            Route::redirect('/','/en');

                Route::group(['prefix' => '{language}'], function (){

                Route::get('/', function () {
                    return view('welcome');
                });
                Route::get('/welcome', function () {
                    return view('welcome');
                })->name('welcome');

            });
        ```
    2. Definir une **middleware** pour inspecter et filtrer les requetes HTTP qui entrent dans l'application.
        1. `php artisan make:middleware SetLanguage`
        2. Configuration: 
            - dans le fichier **app/Http/Middlware/SetLaguage.php**
                - on y met le language qui est un paramettre de l'URL

                ```
                    class SetLanguage
                        {
                            /**
                            * Handle an incoming request.
                            *
                            * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
                            */
                            public function handle(Request $request, Closure $next): Response
                            {
                                \App::setLocale($request -> language);
                                
                                return $next($request);
                            }
                        }
                ```
                - Le configurer dans le **Kernel.php**
                ```
                  protected $middlewareGroups = [
                    'web' => [
                        \App\Http\Middleware\EncryptCookies::class,
                        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
                        \Illuminate\Session\Middleware\StartSession::class,
                        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
                        \App\Http\Middleware\VerifyCsrfToken::class,
                        \Illuminate\Routing\Middleware\SubstituteBindings::class,


                        \App\Http\Middleware\SetLanguage::class,
                    ],
                  ]
                ```
5. Dans la view, le texte a traduire daoit se trouver dans `{{__('texte')}}`
    - ***NB: le textes qui ne trouve pas dans cette configuration ne vont pas apparetre à l'affichage***
6. **Dans la vieuw, pour les liens avec routeur il faut rajouter le paramettre.**
    - `<a href="{{ route('nom_de_la_route',app()->getLocale())}}>`

7. Les liens d'echangeur de langue
    - **NB: Avoir bien créer les routes et les avoir nomé sinon il y a erreur** 
    - `<a href="{{ route(Route::currentRouteName(),'en')}}> anglais </a>`
    - `<a href="{{ route(Route::currentRouteName(),'fr')}}> francais </a>`