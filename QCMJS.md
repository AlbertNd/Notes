# QCM en HTML et javascript

1. Creation d'un formulaire
    1. une balise input en boutton radio
        - des id qui doivent etre differents
        - le name doit etre le meme pour les option portant sur la meme question 
        - une valeur differente pour chaque car il va faloir definir la bonne reponse 
    2. une label pour laquelle 
        - for = id  
2. Test dans la console 
    - `a = document.getElementsByName('Le_name_donnée')`
        - Il retourne nombre d'elements que contient la variable "a" ainsi definis
            - `a.lenght;` permet d'avoir le nombre total d'element pour faire la boucle (3 reponses possible, ou 4, ....)
        - `a[0]` pour avoir l'id 0 
        - `a[0].checked`: pour savoir si il est a ete cocher *(retourne True ou False)*
        - `a[0].value` : on recupe la valeur 
3. On rajoute un button "resultat"
4. une div ou autre pour le message du resultat 

5. le script javascript
    1. Definir l'element boutton
        - `var boutonElement = document.getElementById("Id_du_boutton");`
        - `boutonElement.addEventListener("click", la_fonction);` : quant il y a un click appelle cette fonction 
    2. Tout en haut de l'element boutton 
        - `var res = document.getElementById("resultat");`: recherche dans le documant ce qui correspond à resultat, pour afficher ce qui est dans la div ou autre et correspondant au message du resultat
    3. Definir la fonction 
        -   ```
                function la_fonction(){
                    var nombreQuestion = 2;
                    var question ;
                    var score = 0;
                    for(var j = 1; j < nombreQuestion + 1; j++){

                    question = document.getElementsById("Q"+J);
                    for(var i=0; i < question.length; i++){
                        if (question[i].checked){
                            score = score + Number(question[i].value) // parce que la valeur est un string on la transforme en nombre 
                            }
                        }
                    }
                    res.texttContent = score;
                }
            
            ```