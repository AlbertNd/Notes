# Les controlleurs 
1. **Les controllers**: ce sont des classes qui ont pour but de regrouper des fonctions contenant de la logique
    - Il est recommander de créer des controllers qui correspondent au domaine ou au model sur le quel ils intervennent (Affichage, formulaire ....);
        - `php artisan make:controller nom_du_domaine`
    - Le fichier créer va se trouver dans le dossier **HTTP/Controllers**
        - C'est classe qui extends Controller qui lui meme est une classe créer par laravel est qui elle est l'extends de BaseController de LARAVEL qui elle meme provient du routting.
    - ***NB: l'ordre des parametre est ici important pour les route ayant des parametre comme langue, id ...*** on peut aussi typer les retour des focntions
2. Dans les routes 
    - On passe à la route un tableau contenant d'abord le nom du controller qui est une classe et le nom de la fonction.
    ```
        Route::get('/',[controllerNom::class,'fonction'])->name('nom');
    ```
3. **Il est possible de groupe les liens de route qui on le meme controller et appartenant au meme domaine**
    ```
        Route::prefix('/blog')->name('blog')->controller(mon_controller::class)->group(function(){
            Route::get('/','la fonction à appeler dans le controlleur')->name('nom de la fonction');
        })
    ```