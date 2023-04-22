# Model binding 
1. ***Il faut absolument avoir un model qui porte le meme nom qu'une variable***
    ```
        use App\Models\User;

        //La route 
        Route::get('/users/{user}', function (User $user) {
            return $user->email;
        });
    ```
    - Lorsque on souhaite customise la clef de rechercher pour qu'il rechercher en fonction du paramtre choisis 
    ```
        use App\Models\Post;
        
        // La route 
        Route::get('/posts/{post:slug}', function (Post $post) {
            return $post;
        });
    ```