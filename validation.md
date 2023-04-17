# validation des données 
[Documentation](https://laravel.com/docs/10.x/validation)

1. Dans le controller, dans la fonction en rapport avec la view 
    1. Classe **validator** provenant du name space: **Illuminate\Support\Facades\validator**
    2. Methode **make()** : la methode prend deux parametres:
        1. Les données sous forme de tableau associatif `['titre' => '']`
            - le tableau sera la retour d'un ***$resquest -> all() ou quelque chose d'autre que le all()***
                - `Validator::make($request->all())`
        2. tableau representant les regles de validation. en clef il y a le nom de la propriete que l'on souhaite valider et en valeur les regles que l'on souhaite appliquer à la proprieté `['titre' => 'required|min:5|...']`
            - Pour les regles [voir documentation](https://laravel.com/docs/10.x/validation#available-validation-rules)
        3. Test pour voir ce que ca donne 
        ```
            use Illuminate\Support\Facades\validator;

            class test extends Controller
            {
                public function test(){

                    $validator = validator::make([
                        'titre' =>'replissage de test'
                    ],
                    [
                        'titre' =>'required' // peut etre mis sous forme d'un tableau ['reuired', 'min:5',..]
                    ]);
                    
                    dd($validator);    // pour voir le retour
                }
            }
        ```
        - Pour la fonction **dd()**
            - ***dd($variable -> fails());*** : Renvois un boolean pour dir si true (si ca a echoué donc regle non respectés) ou fals (c'est ok ) 
            - ***dd($variable -> errors());*** : Renvois une liste des message d'erreurs associées au probleme
            - ***dd($variable -> validated());*** : Renvois un tebaleu correspondant au données qui ont ete validées 
                - **NB**: ***Cette methode ne renvois que les valeurs qui ont ete valider par les regles de validation (donc les valeur du deuxieme tableau correspondant aux regles de val)*** si non il va declancher un erreur ou une page 404 o redirige vers la page precedente. (*voir la fonction si necessaire*)
2. Il y a une possibilité de le faire en dehors du controlleur dans un classe dediée.
    - `php artisan make:request nom_de_la_request`
    - Un fichier sera creer dans le dossier ***HTTP/request/nom_de_la_request.php***
        - Une methode **authorize()** qui permet de spécifier si l'utilisateur à le droit ou non d'accéder à la requete
            - Si pas de systeme d'autenthification on `return true;`
        - Une methode **rules()** qui va renvoyer un tableau permettant de definir les regles de validation. 
            - on met les regles de validation dans le retour de cette methode ce qui permet de spécifier que pour une page donnée, on a besoin que la requete ait au moins une propriété ***titre*** et le titre doit correspondre à tel regle 
                ```
                    public function rules():array
                    {
                        return [
                            'titre' => ['required','min:4']
                        ];
                    }
                ```
    - Dans le controlleur on aura la possiblité de rajouter un objet **request**
    ```
            class test extends Controller
            {
                public function test(nom_de_la_request $request){

                return view('la view',[
                    'article' => le_model :: paginate(1)  // ou autre methode
                ]);
                }
            }
    ```
    - **NB:** bien verifier que les regles sont respecter sinon erreur ou page vide (redirection en boucle ...) ou 404

    - **La methode prepare for validation**
        - Elle permet de preparer les données avant la validation 
        ```
            protected function prepareForValidation()
            {
                $this-> marge([
                    'le paramttre qui peut etre manquant' => 'ce qui va le remplacer par defaut'
                ])
            }
        ```
        - => Si on a un tritre et qu'on a pas tel ou tel, il faudrait que tu genere quelque chose par defaut
        ```
            protected function prepareForValidation()
            {
                $this-> marge([
                    'le paramttre qui peut etre manquant' => $this-> input('le parmetre manquant')?:_ Str::slug($this->input('titre'))
                    ]);
            }
        ```
        - => regarde si on a quelque chose et si il y a pas genere le a partir du titre par exemple
            - voir [helper String](https://laravel.com/docs/10.x/helpers) pour le manipulation sur les chaines de caractere
