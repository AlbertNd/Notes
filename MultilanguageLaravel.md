## Localisation 

[Documentation](https://laravel.com/docs/10.x/localization)

1. Création du repertoir **Lang**
    - `php artisan lan:publish`
2. Dan sle repertoire **lang**
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
                    - Voir la igne 85 'loocale' => 'en' la mettre en 'fr'
3. faire un teste avec les routeur 
    -   ```
            Route::get('/test', function (){
                dd(App::getLocale());
            });
        ```
        - Dans l'urle de la page on /test : ce qui doit renvoyer le local configurer càd ici *fr*
    - Si revois est correcte, on test les setting de locale *en*:
        -   ```
                Route::get('/test', fonction (){
                    app::setLocale('en');
                    dd(App::getLocale());
                }); 
            ```
            - Dans l'urle de la page on /test : ce qui doit renvoyer le local configurer càd ici *en*

            - si renvois correcter ('en'), étape suisvant 
4. Creation de la views
    - Dans la vue les texte à traduire doit etre dans `{{__('text')}}`