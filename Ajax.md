# Asynchronous Javascript And XML & jquery 

[Documentation Jquery](https://jquery.com/)

1. L'objet **XMLHttpRequest** :
    - XMLHttpRequest peut être utilisé pour échanger des données avec un serveur Web en arrière-plan. Cela signifie qu'il est possible de mettre à jour des parties d'une page Web, sans recharger toute la page.
    - Pour créer un objet **XMLHttpRequest**
         `let variable = new XMLHttpRequest();`
    - Voir son contenu dans ma console : 
        `console.log(variable)`
    - **Les propriétés de l'objet** [voir doc](https://www.w3schools.com/js/js_ajax_http.asp)
        - ***onload*** : Définit une fonction à appeler lorsque la demande est recue (chargé)
        - ***onreadystatechange*** : Defiit une fonction a appeler lorsque la propriété **readyState** change 
        - ***readyState*** : Elle detient l'état de la requete XMLHttpRequest
            - 0 : requete non initialisée
            - 1 : connexion serveur établie 
            - 2 : requete recue
            - 3 : traitement de la requete
            - 4 : la requete est terminée et la réponse est prete 
        - ***responseText*** : renvois les données de la réponse sous forme d'une chaine de caractère 
        - ***responseXML*** : renvois les données de la réponse sous forme de données XML 
        - ***status*** : renvois le numéro d'état d'une demande 
            - 200 : OK 
            - 403 : interdit 
            - 404 : non trouvé 
            - [Voir liste conmplete des status](https://www.w3schools.com/tags/ref_httpmessages.asp)
        - ***statusText*** : renvois le status-text *("OK" ou "Not Found")*
2. 