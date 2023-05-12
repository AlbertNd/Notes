# Utilisateion des asset 

[Documentation laravel](https://laravel.com/docs/10.x/vite)

- **Travailer sur du front avec css et javascript à l'aide de vite Laravel**
1. Comment est configurer ViTE
    - Voir dans `package.json`
        ``` 
            "laravel-vite-plugin" : *^......*,
            "vite":  *^......*
        ```
    - **Dans le fichier vite.config.js**
        -  C'est là ou se trouve la configuration de vite 
            - le pugin utilisé et le point d'entrée et l'autorefresh
                ```
                    plugins: [
                        laravel({
                            input: ['resources/css/app.css', 'resources/js/app.js'],
                            refresh: true,
                        }),
                    ],
                ```
2. Dans les fichier 
    1. Pour le **JS**
        - Dans les fichier `ressources/Js/app.js`
            - Fichier vide de base sauf qu'il importe deja `import './bootstrap';`
                - *(Il rajoute de la configuration a Axios)*


3. **Le chargement des fichier JS et CSS** 
    - Dans la view 
        - `@vite([le_chemin_de_la_source])`
            - *C'est les meme chemin que celle qui se trouve dans le* **plugins input du fichier vite.config.js** 
                - Pour le js : `resources/js/app.js`
                - Pour le Css : `resources/css/app.css`
4. **Voir pour la cas ou on developpe et le cas ou on publie.**
    - `npm run dev`
    - `npm build`