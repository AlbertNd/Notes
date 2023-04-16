# Les fichier blades 
[documantion](https://laravel.com/docs/10.x/blade)

1. #### @extends(''), @section(''), @yield('')
    - Dans le fichier de base càd la vue qui va acceuillir toute les section *(placer le fichier sur la racine)*
        - ***@yield('nom de la section que l'on voir dans un div ou autres')***
        ```
                <div class="container mx-auto">

                    <h1>Le contenu de la page base </h1>

                    @yield('Nom donnée à la section de l'autre fichier') // le contenu de l'autre fichier sera afficher ici 

                </div>


        ```
    - Dans le fichier dans lequel contient la section souhaité *(placer dans un dossier des vues)*
        -   ```
                @extends('le nom du fichier de base')

                @section('nom de la section')

                    ce que l'on veut voir dans le fichier de base 
                
                @endsection
            ```
            - On vient de créer une etendu du fichier de base et un section dans cette etendu. cette section sera integrer dans le fichier de base à l'endroit voulu ***@yield('...')***
    - **Attention:** ***On fait appel au fichier contenant l'extention (@extends('fichier de base'), @section()...)***
        ```
            Route::get('/', function(){
                return view('Nom_du_Dossier.Nom_de_la_view');
            })
        ```
2. #### Pour les titre des pages et autre, il est aussi possible de les rendre dynamique via le meme procedé: 
    -   ```
                @extends('le nom du fier de base')
                
                @section('titre','le titre souhaité')

                @section('nom de la section')

                    ce que l'on veut voir dans le fichier de base 
                
                @endsection
        ```
    - Dans le fichier de base 
        -   ```
                <head>
                    <title>@yield('titre')</title>
                </head>
                
            ```
3. #### Recuperaion des informations dans la view
    - Il y a une maniere sur d'afficher du contenu avec LARAVEL grace a des ***{{ 'le contenu' }}***
        - Les double accollade permet aussi de faire de l'echapement ce qui permet de ne pas avoir des probleme d'injection des gens malin. *(si on injecte un code HTMLou JS avec balise .. il sera repris commeun texte html)* 
    - **Il y a les racourci**
        - [Boucles foreach](https://laravel.com/docs/10.x/blade#loops) : ***@foreach...***
        - [Les condition](https://laravel.com/docs/10.x/blade#if-statements) : ***@if(true)*** afficher ca ***@else*** .... ***@endif***  
    - **@dump(['...'])**
        - Permet, comme **dd()** de montre les information sur le ce qui est afficher, ou est ce que ca ete chargé etc...
    - **@php...@endphp**
        - Permet de mettre le code classique PHP : `@php $demo = 'albert'; @endphp`
    1.  Affichage des informations en liste 
        1. Dans les controlleur 
            - Lorsque on appelle la vue, il y a aussi une possiblité de lui donner des variable 
                ```
                    public function test(){
        
                        return('la view',[
                            'variable' => \App\Models\le_model::all();
                        ])
                    }
                ```
            - Dans la view 
                - On peut dumper la variable : `@dump($variable)`
                - une boucle foreach
                
                ```
                    @foreach($variable as $variable)

                        <div>
                            <h2>{{ $variable -> titre}} </h2>

                            <p>{{ $variable -> nom}} </p>
                        
                        </div>


                    @endforeach

                ```
4. #### Recuperation du nom d'une route 
    - `@dump(request()->route()->getName())`
5. #### rajouter la classe active si la route correspond au nom que l'on veut
    - sur un a de lien par exemple, dans la classe ="`@if(request()->route()->getName()) === 'nom souhaité') active @endif`
6. #### Voir [@class](https://laravel.com/docs/10.x/blade#conditional-classes) pour des classe dynamique
7. #### @include('')