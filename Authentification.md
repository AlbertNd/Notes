# Authentification

1. Lorsqu'on installe LARAVEL, il vient avec un systeme d'utilisateur deja preconfigurer.
    1. Pour le model et la migration   
        - Les models : **User**
            - C'est l'extention de la classe ***Authenticable*** qui permet de gerer des methodes liées à l'authentification
            - Il y a un $fillable pour les champs 
            - ....
        - Dans les migrations :  
            - Une migration de creation de la table user 
                - Avec differents champs lié à un systeme d'authentification (nom, mail, mot de pass)
        - **NB :** *Il y a donc rien à faire en termr de model et de migration*
2. Création du pemier utilisateur
    1. Dans le controller
        - Créer un utilisateur dans une fonction qui mene sur une des pages *(page d'acceuil)*
            - On va utiliser le model ***User*** et demander de creer un nouvel utilisateur
            ```
                public function AfficherPage(){
                    User::create([
                        'name' => 'albert',
                        'email' => 'albert@gmail.com'
                        'password' => Hash::make('mot_de_pass')
                    ])

                    return view('la_view_de_la_page');
                }
            ```
            - ***Hash::make()*** : Le mot de passe doit etre hashé, càd qu'on ne doit pas mettre le mot de passe dans la base de donnée telle quel ar ca represente un probleme en terme de securité
                - La class **Hash** provient du name space ***Illuminete\support\Facades***
                - La methode **make()** prend en argument une valeur qui va genere un hash qui correspond
        - On reactualise la page de la wiew et l'utilisateur devrait etre créer dans la base de donnée
3. Le formulaire de connection de l'utilisateur 
    1. Creation du controlleur associé
        - `php artisan make:controller AuthController`
    2. Création d'une route qui permet à l'utilisateur de se connecter 
        - `Route::get('/login',[AuthController::class,'login']) -> name('auth.login');`
    3. Configuration du controller 
        ```
            public function login(){
                return view('auth.login');
            }
        ```
    4. Création de la view auth.login
        - L'etendre à la page souhaité ...
        - Creation du formulaire de connection 
            - action :`{{route('auth.login')}}`
            - method : `post`
            - @csrf
            - Champs email *(Si on veut recuperer l'ancienne valeur : value="{{old('email')}}")
            - Champs mot de pass *(Champs de type password)*
            - Button 
            - Message erreur pour les champs 
    5. Creation d'une route pour la soumission du formulaire
        - `Route::post('/login',[AuthController::class,'doLogin'])`
    6. Création de la fonction doLogin dans le controller
        1. Creation du request de validation 
            - `php artisan make:request LoginRequest`
                - return true
                - regle de validation 
                    - email => required et en forma email ....
                    - password => required et un minimum de caractere ..... 
        2. Configuration de la fonction  
        ```
            public function login(){
                return view('auth.login');
            }

            public function doLogin(LoginRequest $request){

            }
        ```
    7. Test de progression 
        - Les message d'erreur du fomulaire *(Avec les chmps vides)*
        - Les champs avec les bonnes informations (information renseigné plus haut dans la creation de l'utilisateur)et envoyer pour voir si il y a pas d'erreur dans le code *(ici ca envois vers une page vide puisque le controller n'a pas de page à renvoyer)*
    8. Completer la fonction doLogin() avec les informations de connection 
        ```
            public function doLogin(LoginRequest $request){
                $credentials = $request -> validated();

                Auth::attempt($credentials); // on peut faire un dd(Auth::attempt($credentials)) pour voir ce que ca retourne 
            }
        ```
        - ***$credentials*** : simple varible de recuperations des informations validée qui seront sous forme de tableau avec un clef email et un mot de pass
        - La class **Auth** provient du name space ***Illuminete\support\Facades*** : elle permet d'authentifier l'utilisateur
            - Elle a une methode **attempt()**
            - Elle a une methode **login()** peut permettre de loguer un utilisateur récuperé directement dans la base de donnée et de le connecter 
                - `Auth::login(User::find(2))`
            - Elle a une methode **loginUsingId()** qui permet de connecter un utilisateur arbitrairement à partir d'un id
        - La methode **attempt()** qui va prendre en paramettre le tableau ***$credentials***
            - Le **dd()**, sur cette fonction, retourne ***true*** si les infos sont correct et ***fals*** dans le cas contraire
            - **Cette methode permet aussi de sauvegarder l'utilisateur en session**
                - On lui met une condtion de disant que si l'utilisateur à bien ete connecté à va utiliser la session et de generer la session 
                    - Soit par la methode globale: `session()->regenerate();` *(non récommandée)*
                    - Ou alors: depuis la requete :`$request ->session()->regenerate();` *(recommandé car pour le test elle permettra de simuler des sessions)*
                - Une fois que la session est regenerer, on redirige l'utilisateur vers la page souhaiter
                    - Soit par la methode ***route()*** : `return redirect()->route('Nom_de_la_route');`
                    - Soit par la methode ***intended()*** : `return reidrect()->intended(route('Nom_de_la_routte'));` *(récommandé)*
                        - ***celle ci va permettre de rediriger l'utilisateur vers la route qu'il avait demandé à l'origine et et si il y a pas de route à l'origine, il va sur le chemin spécifier dans la route***
                            - *voir session dans le debug: il sauvegarde toujours l'url precednt* 
                    - Si l'utilisateur s'est trompé on le redirige vers la partie connection avec un message lui indicant l'erreur
                        -`return to_route('Auth.login')->withErrors(['email' => "Email invalide"]);` 
                    - Si on veut qu'il conserve le mail encode on rajoute la fonction ***onlyInput('email')*** ou ***withInput()***
        ```
            public function doLogin(LoginRequest $request){
                $credentials = $request -> validated();

                if(Auth::attempt($credentials)){
                    $request -> session() -> regenerate();

                    return redirect()->intended(route('Nom_de_la_route'));
                }; 
                
                return to_route('Auth.login')->withErrors(['email' => "email pas valide"])->onlyInput('email');
            }
        ```
    9. test de la progression 
        - .....
        - Si on veut savoir si l'utiisateur est connecté **dd(Auth::user());** dans la fonction d'affichage d'une page 
            - Dans resultat du **dd()** voir attributses pour les informations de l'utilisateur qui ont ete sauvegardé en session
3. Voir la connection au niveau de la view (nom d'utilisateur et la bar de deconnection)
    1. Dans la view souhaité *(en generale la bar de navigation)*
        - Les directive **@auth** ... **@endauth** 
            - C'est l'equivalent d'une condition ***if()***, sauf qu'elle vérifie si l'utilisateur est connecté
        - Les directives inverse **@guest** ... **@endguest** vérifier un utilisateur qui n'est pas connecté
    
    ```
        <div>
            @auth
                {{\Illuminate\Support\Facades\Auth::user()->name}} // afficher le nom du connecté
            @enauth

            @guest
                <a href="{{route('auth.login')}}"> se connecter </a> // le lien qui s'affiche lorsu'on est pas connecter et qui mene vers la page de connection
            @endguest
        </div>
    ```
4. Le lien de deconnection 
    - On pourrait penser a avoir un lien logOut, le probleme est que si on fait un et que quelqu'un le partage sur un systeme de chat instantané et que je je click dessus ca va me deconnecter ***A creuser par rapport au projet***
        - En general lorsuq'on a des liens qui font des actions, on va les mettre dans un formulaire
    1. Création d'un formulaire qui permet de creer le button ***se deconnecter***
        - action= `"{{ route('auth.logout')}}"`
        - method = `"post"`
        - Simulation d'une fausse methode delete :`@method('delete)`
        - @csrf
        - button de deconnection 
        ```
        <div>
            @auth
                {{\Illuminate\Support\Facades\Auth::user()->name}} // afficher le nom du connecté

                <form action="{{ route('auth.logout')}}" method="post">
                
                    @method('delete')

                    @csrf
                    
                    <button> se deconnecter </button>
                
                </form>
            @enauth

            @guest
                <a href="{{route('auth.login')}}"> se connecter </a> // le lien qui s'affiche lorsu'on est pas connecter et qui mene vers la page de connection
            @endguest
    2. Creation de l'action logout et rediriger l'utilisateur 
        - Dans le controlleur 
        ```
            public function logOut(){
                Auth::logout();

                return to_route('auth.login');
            }
        ```
    3. Definir la route 
        - `Route::delete('/logout',[AuthController::class,'logOut']) -> name('auth.logout');`
5. La securisation des liens avec le middelware
    1. Voir dans kernel pour un appercu 
        - Plus bas il y a des midellware avec des alias ***protected $middlewareAlias = [ ...]*** que l'on peut activer si on le souhaite
            - ***auth*** qui permet de dire que l'utilisateur doit etre identifier pour acceder à une certaine requete
            - ***guest*** : il faut que l'utilisateur ne soit pas connecté, et aussi si tu es connecter tu peux par exemple ne pas revoir le formulaire de conncetion ....
    2. Dans les route 
        - on rajoute la fonction middleware avec en argument l'alias 
            - `.... -> name('...')-> middleware('auth');`
    3. **Attention** ***en cas d'autentification Ne pas oublier de changer la route dans **Authentificat**  du middelware et changer en y mettant le nom donnée a la route d'autentification***
        1. dossier kernel
        2. Alias middleware : ***auth*** 
        3. ctrl + click sur *Authentificate*
        - `return $request -> expectsJson()? null : route('login');` 